---
title: Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする | Microsoft Docs
description: Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: sedusch
ms.openlocfilehash: 9ce95bcf15d0186c1baea3df407d0fc0c4200f45
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115478"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance

Azure で Pacemaker クラスターをセットアップする場合、2 つの選択肢があります。 1 つはフェンス エージェントを使う方法です。フェンス エージェントは、障害が発生したノードを Azure API で再起動させる役割を担います。もう 1 つは、SBD デバイスを使う方法です。

SBD デバイスを使う場合、iSCSI ターゲット サーバーとして機能しつつ SBD デバイスを提供する仮想マシンが 1 つ追加で必要となります。 ただし、この iSCSI ターゲット サーバーは他の Pacemaker クラスターと共有することができます。 SBD デバイスを使う利点は、フェールオーバー時間を短縮できることです。また、SBD デバイスをオンプレミスで使う限り、Pacemaker クラスターの運用方法に変更を加える必要は一切ありません。 SBD フェンスを使う場合でも、iSCSI ターゲット サーバーが利用できなくなった場合のバックアップ フェンス メカニズムとして Azure フェンス エージェントを使うことができます。

仮想マシンを 1 つ追加で用意することが予算的に難しい場合は、Azure Fence エージェントを使うこともできます。 その場合の欠点は、リソースの停止に失敗した場合や、クラスター ノードが互いに通信できない状態に陥った場合、フェールオーバーに 10 分から 15 分かかることです。

![SLES における Pacemaker の概要](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Linux Pacemaker クラスター ノードおよび SBD デバイスを計画およびデプロイする場合、関係する VM と SBD デバイスをホストする VM が [NVA](https://azure.microsoft.com/solutions/network-appliances/) などのその他のデバイスを通過しないようにすることが、完全なクラスター構成の全体的な信頼性に必要です。 このようにしないと、NVA の問題とメンテナンス イベントがクラスター構成全体の安定性と信頼性に負の影響を与える可能性があります。 このような障害を回避するには、Linux Pacemaker クラスター ノードおよび SBD デバイスを計画およびデプロイする場合に、クラスター化されたノードと NVA や類似のデバイスを介した SBD デバイスなどの間のトラフィックをルーティングする、NVA のルーティング規則または[ユーザー定義のルーティング規則](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)を定義しないようにする必要があります。 
>


## <a name="sbd-fencing"></a>SBD フェンス

SBD デバイスをフェンスに使用する場合は、次の手順に従ってください。

### <a name="set-up-an-iscsi-target-server"></a>iSCSI ターゲット サーバーのセットアップ

まだ iSCSI ターゲット仮想マシンを作成していない場合は、先に iSCSI ターゲット仮想マシンを作成する必要があります。 iSCSI ターゲット サーバーは、複数の Pacemaker クラスターと共有することができます。

1. 新しい SLES 12 SP1 以降の仮想マシンをデプロイし、そこに ssh で接続します。 サイズの大きいマシンは必要ありません。 Standard_E2s_v3 または Standard_D2s_v3 などの仮想マシン サイズで十分です。

1. SLES を更新します

   <pre><code>
   sudo zypper update
   </code></pre>

1. パッケージの削除

   targetcli および SLES 12 SP3 に関する既知の問題を回避するには、次のパッケージをアンインストールします。 見つからないパッケージに関するエラーは無視できます
   
   <pre><code>
   sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>
   
1. iSCSI ターゲット パッケージをインストールします

   <pre><code>
   sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. iSCSI ターゲット サービスを有効にします

   <pre><code>   
   sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>iSCSI ターゲット サーバーに iSCSI デバイスを作成する

このクラスターに使用できる新しいデータ ディスクを iSCSI ターゲット仮想マシンにアタッチします。 このデータ ディスクは 1 GB 程度のサイズでかまいませんが、[単一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) の特典を利用するためには、Premium Storage アカウントまたは Premium 管理ディスク上に配置する必要があります。

**iSCSI ターゲット VM** 上で以下のコマンドを実行し、新しいクラスターに使用する iSCSI ディスクを作成してください。 次の例で、**cl1** は新しいクラスターの識別に使用されます。また、**prod-cl1-0** と **prod-cl1-1** はクラスター ノードのホスト名です。 これらは、実際のクラスター ノードのホスト名に置き換えてください。

<pre><code>
# List all data disks with the following command
sudo ls -al /dev/disk/azure/scsi1/

# total 0
# drwxr-xr-x 2 root root  80 Mar 26 14:42 .
# drwxr-xr-x 3 root root 160 Mar 26 14:42 ..
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun0 -> ../../../<b>sdc</b>
# lrwxrwxrwx 1 root root  12 Mar 26 14:42 lun1 -> ../../../sdd

# Then use the disk name to list the disk id
sudo ls -l /dev/disk/by-id/scsi-* | grep sdc

# lrwxrwxrwx 1 root root  9 Mar 26 14:42 /dev/disk/by-id/scsi-14d53465420202020a50923c92babda40974bef49ae8828f0 -> ../../sdc
# lrwxrwxrwx 1 root root  9 Mar 26 14:42 <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0 -> ../../sdc</b>

# Use the data disk that you attached for this cluster to create a new backstore
sudo targetcli backstores/block create <b>cl1</b> <b>/dev/disk/by-id/scsi-360022480a50923c92babef49ae8828f0</b>

sudo targetcli iscsi/ create iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/luns/ create /backstores/block/<b>cl1</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-0.local:prod-cl1-0</b>
sudo targetcli iscsi/iqn.2006-04.<b>cl1</b>.local:<b>cl1</b>/tpg1/acls/ create iqn.2006-04.<b>prod-cl1-1.local:prod-cl1-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

### <a name="set-up-sbd-device"></a>SBD デバイスをセットアップする

最後の手順で作成した iSCSI デバイスにクラスターから接続します。
以下のコマンドは、作成する新しいクラスターの各ノード上で実行します。
次の各手順の先頭には、**[A]** - 全ノードが該当、**[1]** - ノード 1 のみ該当、**[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** iSCSI デバイスに接続します

   まず、iSCSI サービスと SBD サービスを有効にします。

   <pre><code>
   sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** 1 つ目のノードでイニシエーターの名前を変更します

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   iSCSI ターゲット サーバーに iSCSI デバイスを作成したときに使った ACL に合わせてファイルの内容を変更します

   <pre><code>   
   InitiatorName=<b>iqn.2006-04.prod-cl1-0.local:prod-cl1-0</b>
   </code></pre>

1. **[2]** 2 つ目のノードでイニシエーターの名前を変更します

   <pre><code>
   sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   iSCSI ターゲット サーバーに iSCSI デバイスを作成したときに使った ACL に合わせてファイルの内容を変更します

   <pre><code>
   InitiatorName=<b>iqn.2006-04.prod-cl1-1.local:prod-cl1-1</b>
   </code></pre>

1. **[A]** iSCSI サービスを再起動します

   ここで iSCSI サービスを再起動すると、変更が適用されます
   
   <pre><code>
   sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   iSCSI デバイスを接続します。 下の例の 10.0.0.17 は iSCSI ターゲット サーバーの IP アドレス、3260 は既定のポートです。 <b>iqn.2006-04.cl1.local:cl1</b> は、1 つ目のコマンドを実行したときに一覧表示されるターゲットの名前です。

   <pre><code>
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>
   
   sudo iscsiadm -m node -T <b>iqn.2006-04.cl1.local:cl1</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   iSCSI デバイスが利用可能な状態であることを確認し、デバイス名を書き留めておいてください (次の例では /dev/sde)

   <pre><code>
   lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  cl1              4.0   /dev/sde</b>
   </code></pre>

   ここで、iSCSI デバイスの ID を取得します。

   <pre><code>
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   </code></pre>

   このコマンドを実行すると、3 つのデバイス ID が一覧表示されます。 上の例の scsi-3 で始まる ID (下記) の使用をお勧めします
   
   **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**

1. **[1]** SBD デバイスを作成します

   iSCSI デバイスのデバイス ID を使用して、1 つ目のクラスター ノードに新しい SBD デバイスを作成します。

   <pre><code>
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 10 -4 20 create
   </code></pre>

1. **[A]** SBD の構成を調整します

   SBD の構成ファイルを開きます

   <pre><code>
   sudo vi /etc/sysconfig/sbd
   </code></pre>

   SBD デバイスのプロパティを変更し、Pacemaker の統合を有効にして、SBD の起動モードを変更します。

   <pre><code>
   [...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   </code></pre>

   softdog 構成ファイルを作成します

   <pre><code>
   echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   モジュールを読み込みます

   <pre><code>
   sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>クラスターのインストール

次の各手順の先頭には、**[A]** - 全ノードが該当、**[1]** - ノード 1 のみ該当、**[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** SLES を更新します

   <pre><code>
   sudo zypper update
   </code></pre>

1. **[1]** SSH アクセスを有効にします

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

2. **[2]** SSH アクセスを有効にします

   <pre><code>
   sudo ssh-keygen
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key   
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** SSH アクセスを有効にします

   <pre><code>
   # insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** フェンス エージェントをインストールします
   
   <pre><code>
   sudo zypper install fence-agents
   </code></pre>

1. **[A]** ホスト名解決を設定します   

   DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます。 /etc/hosts を使用する利点は、単一障害点になる可能性もある DNS からクラスターを独立させられることです。

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します   
   
   <pre><code>
   # IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** クラスターをインストールします
   
   <pre><code>
   sudo ha-cluster-init
   
   # Do you want to continue anyway? [y/N] -> y
   # Network address to bind to (for example: 192.168.1.0) [10.79.227.0] -> Press ENTER
   # Multicast address (for example: 239.x.x.x) [239.174.218.125] -> Press ENTER
   # Multicast port [5405] -> Press ENTER
   # Do you wish to configure an administration IP? [y/N] -> N
   </code></pre>

1. **[2]** クラスターにノードを追加します
   
   <pre><code> 
   sudo ha-cluster-join
   
   # Do you want to continue anyway? [y/N] -> y
   # IP address or hostname of existing node (for example: 192.168.1.1) [] -> IP address of node 1 for example 10.0.0.14
   # /root/.ssh/id_rsa already exists - overwrite? [y/N] N
   </code></pre>

1. **[A]** hacluster のパスワードを同じパスワードに変更します

   <pre><code> 
   sudo passwd hacluster
   </code></pre>

1. **[A]** 他のトランスポートを使用したり、ノードリストを追加したりするために corosync を構成します。 これを構成しないと、クラスターは機能しません。
   
   <pre><code> 
   sudo vi /etc/corosync/corosync.conf   
   </code></pre>

   値が無いか、異なる場合は、次の太字の内容をファイルに追加します。 トークンを 30000 に変更してメモリ保持メンテナンスを可能にします。 詳細については、[Linux の場合はこちらの記事][virtual-machines-linux-maintenance]、[Windows の場合はこちらの記事][virtual-machines-windows-maintenance]をご覧ください。
   
   <pre><code> 
   [...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      6000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   その後、corosync サービスを再起動します

   <pre><code>
   sudo service corosync restart
   </code></pre>

1. **[1]** Pacemaker の既定の設定を変更します

   <pre><code>
   sudo crm configure rsc_defaults resource-stickiness="1"   
   </code></pre>

## <a name="create-stonith-device"></a>STONITH デバイスの作成

STONITH デバイスは、サービス プリンシパルを使用して Microsoft Azure を承認します。 サービス プリンシパルを作成するには、次に手順に従います。

1. <https://portal.azure.com> に移動します
1. [Azure Active Directory] ブレードを開きます  
   [プロパティ] に移動し、ディレクトリ ID をメモします。 これは、**テナント ID** です。
1. [アプリの登録] を選択します
1. [追加] をクリックします。
1. 名前を入力して、アプリケーションの種類に [Web アプリ/API] を選択し、サインオン URL (例: http://localhost)) を入力します。その後、[作成] をクリックします
1. サインオン URL は使用されず、任意の有効な URL を指定することができます
1. 新しいアプリを選択し、[設定] タブで [キー] をクリックします
1. 新しいキーの説明を入力し、[Never expires] \(有効期限なし) を選択して [保存] をクリックします
1. 値をメモします。 この値は、サービス プリンシパルの**パスワード**として使用します
1. アプリケーション ID をメモします。 これは、サービス プリンシパルのユーザー名 (下記の手順の**ログイン ID**) として使用します

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** フェンス エージェントのカスタム ロールを作成する

既定では、サービス プリンシパルには、Azure のリソースにアクセスする権限はありません。 クラスターのすべての仮想マシンを開始および停止 (割り当て解除) する権限を、サービス プリンシパルに付与する必要があります。 まだカスタム ロールを作成していない場合は、[PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) または [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role) を使って作成してください

入力ファイルには次の内容を使用します。 実際のサブスクリプションに合わせて内容を調整する必要があります。つまり、c276fc76-9cd4-44c9-99a7-4fd71546436e and e91d47c4-76f3-4271-a796-21b4ecfe3624 は、ご利用のサブスクリプションの ID に置き換えます。 ご利用のサブスクリプションが 1 つしかない場合は、AssignableScopes の 2 つ目のエントリは削除してください。

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]** サービス プリンシパルにカスタム ロールを割り当てる

最後の章で作成したカスタム ロール "Linux Fence Agent Role" をサービス プリンシパルに割り当てます。 所有者ロールは今後使わないでください。

1. https://portal.azure.com に移動します
1. [All resources] \(すべてのリソース) ブレードを開きます
1. 1 つ目のクラスター ノードの仮想マシンを選択します
1. [アクセス制御 (IAM)] を選択します
1. [追加] をクリックします。
1. "Linux Fence Agent Role" ロールを選択します
1. 上記で作成したアプリケーションの名前を入力します
1. [OK] をクリックします

2 つ目のクラスター ノードについても上記の手順を繰り返します。

### <a name="1-create-the-stonith-devices"></a>**[1]** STONITH デバイスを作成します

仮想マシンのアクセス許可を編集したあとで、クラスターの STONITH デバイスを構成できます。

<pre><code>
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure property stonith-timeout=900

sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

</code></pre>

### <a name="1-create-fence-topology-for-sbd-fencing"></a>**[1]** SBD フェンスのフェンス トポロジを作成する

SBD デバイスを使う場合でも、iSCSI ターゲット サーバーが利用できなくなった場合のバックアップとして、Azure フェンス エージェントを使うことをお勧めします。

<pre><code>
sudo crm configure fencing_topology \
  stonith-sbd rsc_st_azure

</code></pre>
### **[1]** STONITH デバイスの使用を有効にします

<pre><code>
sudo crm configure property stonith-enabled=true 
</code></pre>


## <a name="next-steps"></a>次の手順
* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。
