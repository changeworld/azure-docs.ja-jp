---
title: Azure Virtual Machines (VM) 上での SAP HANA システム レプリケーションのセットアップ | Microsoft Docs
description: Azure Virtual Machines (VM) 上の SAP HANA の高可用性を実現する。
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/24/2018
ms.author: sedusch
ms.openlocfilehash: 5bc578d617edd093a3b7eec7903209bfdb9ebfce
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Azure Virtual Machines (VM) 上の SAP HANA の高可用性 | Microsoft Docs

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

オンプレミスでは、HANA システム レプリケーションまたは共有記憶域を使用して、SAP HANA の高可用性を実現できます。
Azure 上では、Azure VM HANA システム レプリケーションが現在サポートされている唯一の高可用性機能です。 SAP HANA レプリケーション は、1 つのプライマリ ノードと、少なくとも 1 つのセカンダリ ノードで構成されています。 プライマリ ノードのデータに対する変更は、セカンダリ ノードに同期的または非同期的にレプリケートされます。

この記事では、仮想マシンのデプロイおよび構成方法、クラスター フレームワークのインストール方法、SAP HANA システム レプリケーションのインストールおよび構成方法について説明します。
サンプルの構成では、インストールのコマンドで、インスタンス番号として 03、HANA システム ID として HN1 が使用されています。

はじめに、次の SAP Note およびガイドを確認してください

* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン
* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2205917]: SUSE Linux Enterprise Server for SAP Applications 向けの推奨の OS 設定が記載されています。
* SAP Note [1944799]: SUSE Linux Enterprise Server for SAP Applications の SAP HANA ガイドラインが記載されています。
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1984787]: SUSE Linux Enterprise Server 12 に関する一般情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]に関する記事
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ (この記事)][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]に関する記事
* [SAP HANA SR Performance Optimized Scenario (SAP HANA SR パフォーマンス最適化シナリオ)][suse-hana-ha-guide] このガイドには、SAP HANA システム レプリケーションをオンプレミスでセットアップするときに必要なすべての情報が記載されています。 このガイドをベースラインとして使用します。

## <a name="overview"></a>概要

高可用性を実現するために、SAP HANA は 2 台の仮想マシンにインストールされます。 データは、HANA システム レプリケーションを使用してレプリケートされます。

![SAP HANA の高可用性の概要](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA SR セットアップでは、専用の仮想ホスト名と仮想 IP アドレスが使用されます。 Azure では、仮想 IP アドレスを使用するためにロード バランサーが必要になります。 ロード バランサーの構成を次に示します。

* フロントエンドの構成
  * IP アドレス 10.0.0.13 (hn1-db)
* バックエンドの構成
  * HANA システム レプリケーションに含める必要のあるすべての仮想マシンのプライマリ ネットワーク インターフェイスに接続済み
* プローブ ポート
  * ポート 62503
* 負荷分散規則
  * 30313 TCP
  * 30315 TCP
  * 30317 TCP

## <a name="deploying-linux"></a>Linux のデプロイ

SAP HANA のリソース エージェントは、SUSE Linux Enterprise Server for SAP Applications に含まれています。
Azure Marketplace には、SUSE Linux Enterprise Server for SAP Applications 12 のイメージが含まれており、新しい仮想マシンのデプロイに使用できます。

### <a name="deploy-with-template"></a>テンプレートを使用したデプロイ
GitHub にあるいずれかのクイック スタート テンプレートを使用して、必要なすべてのリソースをデプロイできます。 テンプレートでは、仮想マシン、ロード バランサー、可用性セットなどをデプロイできます。テンプレートをデプロイするには、次の手順に従います。

1. Azure Portal で、[データベース テンプレート][template-multisid-db]または[集約型テンプレート][template-converged]を開きます。 
   データベース テンプレートでは、データベースの負荷分散規則しか作成できませんが、集約型テンプレートでは、ASCS/SCS および ERS (Linux のみ) のインスタンスの負荷分散規則も作成できます。 SAP NetWeaver ベースのシステムをインストールして、同じコンピューターに ASCS/SCS インスタンスもインストールする場合は、[集約型テンプレート][template-converged]を使用します。
1. 次のパラメーターを入力します
    1. SAP システム ID  
       インストールする SAP システムの SAP システム ID を入力します。 この ID は、デプロイされるリソースのプレフィックスとして使用されます。
    1. スタックの種類 (集約型テンプレートを使用する場合のみ適用可能)   
       SAP NetWeaver のスタックの種類を選択します
    1. OS の種類  
       いずれかの Linux ディストリビューションを選択します。 この例では、SLES 12 を選択します。
    1. データベースの種類  
       HANA を選択します
    1. Sap System Size (SAP システムのサイズ)  
       新しいシステムで提供する SAPS の量。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
    1. システムの可用性  
       [HA] を選択します
    1. [管理ユーザー名] と[管理パスワード]  
       コンピューターへのログオンで使用できる新しいユーザーが作成されます。
    1. New Or Existing Subnet (新規または既存のサブネット)  
       新しい仮想ネットワークとサブネットを作成するか、既存のサブネットを使用するかを決定します。 オンプレミス ネットワークに接続している仮想ネットワークが既にある場合は、既存のものを選択します。
    1. サブネット ID  
    仮想マシンを接続するサブネットの ID。 仮想マシンをオンプレミス ネットワークに接続するには、VPN または Express Route 仮想ネットワークのサブネットを選択します。 通常、ID は /subscriptions/`<subscription ID`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`> のようになります。

### <a name="manual-deployment"></a>手動によるデプロイ

1. リソース グループを作成します
1. 仮想ネットワークを作成します
1. 可用性セットを作成します  
   更新ドメインの最大数を設定します
1. ロード バランサー (内部) を作成します  
   2 番目に作成された VNet を選択します
1. 仮想マシン 1 を作成します  
   SLES4SAP 12 SP1 以上を使用します。この例では、SLES4SAP 12 SP2 イメージ (https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM) を使用します  
   SLES for SAP 12 SP2 (Premium)  
   前に作成された可用性セットを選択します  
1. 仮想マシン 2 を作成します  
   SLES4SAP 12 SP1 以上を使用します。この例では、SLES4SAP 12 SP1 BYOS イメージ (https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM) を使用します  
   SLES for SAP 12 SP2 (Premium)  
   前に作成された可用性セットを選択します  
1. データ ディスクを追加します
1. ロード バランサーを構成します
    1. フロントエンド IP プールの作成
        1. ロード バランサーを開き、[フロントエンド IP プール] を選択して [追加] をクリックします
        1. 新規のフロントエンド IP プールの名前を入力します (例: hana-frontend)
        1. 割り当てを静的に設定し、IP アドレスを入力します (例: **10.0.0.13**)
        1. [OK] をクリックします
        1. 新規のフロントエンド IP プールが作成されたら、その IP アドレスをメモします
    1. バックエンド プールの作成
        1. ロード バランサーを開き、[バックエンド プール] を選択して [追加] をクリックします
        1. 新規のバックエンド プールの名前を入力します (例: hana-backend)
        1. [仮想マシンの追加] をクリックします
        1. 前の手順で作成した可用性セットを選択します
        1. SAP HANA クラスターの仮想マシンを選択します
        1. [OK] をクリックします
    1. 正常性プローブの作成
        1. ロード バランサーを開き、[正常性プローブ] を選択して [追加] をクリックします
        1. 新しい正常性プローブの名前を入力します (例: hana-hp)
        1. プロトコルに TCP、ポートに 625**03** を選択し、[間隔] は 5、[異常] のしきい値は 2 のままにしておきます
        1. [OK] をクリックします
    1. SAP HANA 1.0: 負荷分散規則の作成
        1. ロード バランサーを開き、[負荷分散規則] を選択して [追加] をクリックします
        1. 新しい負荷分散規則の名前を入力します (例: hana-lb-3**03**15)
        1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: hana-frontend)
        1. プロトコルは TCP のままにし、ポートに「3**03**15」を入力します
        1. アイドル タイムアウトを 30 分に増やします
        1. **Floating IP を有効にします**
        1. [OK] をクリックします
        1. ポート 3**03**17 に対しても上記の手順を繰り返します
    1. SAP HANA 2.0: システム データベースの負荷分散規則の作成
        1. ロード バランサーを開き、[負荷分散規則] を選択して [追加] をクリックします
        1. 新しいロード バランサー規則の名前を入力します (例: hana-lb-3**03**13)
        1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: hana-frontend)
        1. プロトコルは TCP のままにし、ポートに「3**03**13」を入力します
        1. アイドル タイムアウトを 30 分に増やします
        1. **Floating IP を有効にします**
        1. [OK] をクリックします
        1. ポート 3**03**14 に対しても上記の手順を繰り返します
    1. SAP HANA 2.0: 最初のテナント データベースの負荷分散規則の作成
        1. ロード バランサーを開き、[負荷分散規則] を選択して [追加] をクリックします
        1. 新しいロード バランサー規則の名前を入力します (例: hana-lb-3**03**40)
        1. 前の手順で作成したフロントエンド IP アドレス、バックエンド プール、正常性プローブを選択します (例: hana-frontend)
        1. プロトコルは TCP のままにし、ポートに「3**03**40」を入力します
        1. アイドル タイムアウトを 30 分に増やします
        1. **Floating IP を有効にします**
        1. [OK] をクリックします
        1. ポート 3**03**41 および 3**03**42 に対して、上記の手順を繰り返します

SAP HANA に必要なポートについて詳しくは、[SAP HANA テナント データベース](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) ガイドの[テナント データベースへの接続](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html)に関する章または [SAP Note 2388694][2388694] をご覧ください。


## <a name="create-pacemaker-cluster"></a>Pacemaker クラスターの作成

「[Setting up Pacemaker on SUSE Linux Enterprise Server in Azure (Azure で SUSE Linux Enterprise Server に Pacemaker を設定する)](high-availability-guide-suse-pacemaker.md)」の手順に従って、この HANA サーバーに対して基本的な Pacemaker クラスターを作成します。 SAP HANA および SAP NetWeaver (A) SCS に対して同じ Pacemaker クラスターを使用することもできます。

## <a name="installing-sap-hana"></a>SAP HANA のインストール

次の各手順の先頭には、**[A]** - Pacemaker クラスターの全ノードが該当、**[1]** - ノード 1 のみ該当、**[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** ディスク レイアウトをセットアップします
    1. LVM  
       
       一般に、データ ファイルとログ ファイルを格納するボリュームには LVM を使用することをお勧めします。 次の例は、仮想マシンに 4 つのデータ ディスクがアタッチされていて、これを使用して 2 つのボリュームを作成するということを前提としています。

       使用可能なすべてのディスクを表示します
       
       <pre><code>
       ls /dev/disk/azure/scsi1/lun*
       </code></pre>
       
       出力例
       
       ```
       /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
       ```
       
       使用するすべてのディスクの物理ボリュームを作成します。    
       <pre><code>
       sudo pvcreate /dev/disk/azure/scsi1/lun0
       sudo pvcreate /dev/disk/azure/scsi1/lun1
       sudo pvcreate /dev/disk/azure/scsi1/lun2
       sudo pvcreate /dev/disk/azure/scsi1/lun3
       </code></pre>

       データ ファイルのボリューム グループ、ログ ファイルのボリューム グループ、SAP HANA の共有ディレクトリのボリューム グループを作成します

       <pre><code>
       sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
       sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
       sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
       </code></pre>
       
       論理ボリュームを作成します

       <pre><code>
       sudo lvcreate -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
       sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
       sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
       sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       </code></pre>
       
       マウント ディレクトリを作成し、すべての論理ボリュームの UUID をコピーします
       
       <pre><code>
       sudo mkdir -p /hana/data/<b>HN1</b>
       sudo mkdir -p /hana/log/<b>HN1</b>
       sudo mkdir -p /hana/shared/<b>HN1</b>
       # write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       sudo blkid
       </code></pre>
       
       3 つの論理ボリュームの fstab エントリを作成します
       
       <pre><code>
       sudo vi /etc/fstab
       </code></pre>
       
       この行を /etc/fstab に挿入します
       
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
       </code></pre>
       
       新しいボリュームをマウントします
       
       <pre><code>
       sudo mount -a
       </code></pre>
    
    1. プレーン ディスク  
       デモ システムの場合、ご自身の HANA のデータとログ ファイルを 1 つのディスクに配置することができます。 次のコマンドで、/dev/disk/azure/scsi1/lun0 にパーティションを作成し、xfs でフォーマットします。

       <pre><code>
       sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
       sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
       
       # write down the ID of /dev/disk/azure/scsi1/lun0-part1
       sudo /sbin/blkid
       sudo vi /etc/fstab
       </code></pre>

       この行を /etc/fstab に挿入します
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
       </code></pre>

       ターゲット ディレクトリを作成してディスクをマウントします。

       <pre><code>
       sudo mkdir /hana
       sudo mount -a
       </code></pre>

1. **[A]** すべてのホストにホスト名解決をセットアップします  
    DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます
    ```bash
    sudo vi /etc/hosts
    ```
    次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します    
    
   <pre><code>
   <b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** HANA HA パッケージをインストールします  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

SAP HANA システム レプリケーションをインストールするには、SAP HANA SR パフォーマンス最適化シナリオ ガイド (https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/) の 4 章に従います。

1. **[A]** HANA DVD から hdblcm を実行します
    * Choose installation (インストールの選択) -> 1
    * Select additional components for installation (追加でインストールするコンポーネントの選択) -> 1
    * Enter Installation Path [/hana/shared] \(インストールのパス (/hana/shared) の入力): -> ENTER
    * Enter Local Host Name [..] \(ローカルのホスト名 (..) の入力): -> ENTER
    * Do you want to add additional hosts to the system? (システムに別のホストを追加しますか?)  (y/n) [n]: -> ENTER
    * Enter SAP HANA System ID (SAP HANA のシステム ID を入力): <SID of HANA e.g. HN1>
    * Enter Instance Number [00] \(インスタンス番号 (00) の入力):   
  HANA のインスタンス番号です。 Azure テンプレートを使用した場合、または手動デプロイを行った場合は、「03」を使用します
    * Select Database Mode / Enter Index [1] \(データベース モードの選択/インデックス (1) の入力): -> ENTER
    * Select System Usage / Enter Index [4] \(システム使用率の選択/インデックス (4) の入力):  
  システムの使用率を選択します
    * Enter Location of Data Volumes [/hana/data/HN1] \(データ ボリュームの場所 (/hana/data/HN1) の入力): -> ENTER
    * Enter Location of Log Volumes [/hana/data/HN1] \(データ ボリュームの場所 (/hana/log/HN1) の入力): -> ENTER
    * Restrict maximum memory allocation? (メモリの最大割り当てを制限しますか?)  [n]: -> ENTER
    * Enter Certificate Host Name For Host '...' [...]\(ホスト '...' の証明書のホスト名 (...) を入力\): -> ENTER
    * Enter SAP Host Agent User (sapadm) Password (SAP ホスト エージェントのユーザー (sapadm) パスワードを入力):
    * Confirm SAP Host Agent User (sapadm) Password (SAP ホスト エージェントのユーザー (sapadm) パスワードを確認):
    * Enter System Administrator (hdbadm) Password (システム管理者 (hdbadm) のパスワードを入力):
    * Confirm System Administrator (hdbadm) Password (システム管理者 (hdbadm) のパスワードを確認):
    * Enter System Administrator Home Directory [/usr/sap/HN1/home] \(システム管理者のホーム ディレクトリ (/usr/sap/HN1/home) を入力): -> ENTER
    * Enter System Administrator Login Shell [/bin/sh] \(システム管理者のログイン シェル (/bin/sh) を入力): -> ENTER
    * Enter System Administrator User ID [1001] \(システム管理者のユーザー ID (1001) を入力): -> ENTER
    * Enter ID of User Group (sapsys) [79] \(ユーザー グループ (sapsys) の ID (79) を入力): -> ENTER
    * Enter Database User (SYSTEM) Password (データベース ユーザー (SYSTEM) のパスワードを入力):
    * Confirm Database User (SYSTEM) Password (データベース ユーザー (SYSTEM) のパスワードを確認):
    * Restart system after machine reboot? (コンピューターの再起動後にシステムを再起動しますか?)  [n]: -> ENTER
    * Do you want to continue? (続行してもよろしいですか?)  (y/n):   
  内容を確認し、「y」を入力して続行します

1. **[A]** SAP Host Agent をアップグレードします  
  [SAP ソフトウェアセンター][sap-swcenter]から最新の SAP Host Agent アーカイブをダウンロードし、次のコマンドを実行してエージェントをアップグレードします。 アーカイブのパスを置き換えて、ダウンロードしたファイルを示すようにします。
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0 システム レプリケーションの構成

次の各手順の先頭には、**[A]** - Pacemaker クラスターの全ノードが該当、**[1]** - ノード 1 のみ該当、**[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[1]** テナント データベースを作成します

   SAP HANA 2.0 または MDC を使用している場合は、ご自身の SAP NetWeaver システムに対してテナント データベースを作成します。 NW1 をご自身の SAP システムの SID に置き換えます。

   `<hanasid`>adm としてログインし、次のコマンドを実行します

   <pre><code>
   hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** 最初のノードでシステム レプリケーションを構成します
   
   `<hanasid`>adm としてログインし、データベースをバックアップします

   <pre><code>
   hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   システム PKI ファイルをセカンダリにコピーします

   <pre><code>
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   プライマリ サイトを作成します。

   <pre><code>
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** 2 番目のノードでシステム レプリケーションを構成します
    
    2 番目のノードを登録して、システム レプリケーションを開始します。 `<hanasid`>adm としてログインし、次のコマンドを実行します

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1.0 システム レプリケーションの構成

1. **[1]** 必要なユーザーを作成します

    root としてログインし、次のコマンドを実行します。 太字の文字列 (HANA システム ID: HN1、インスタンス番号: 03 ) を、ご自身の SAP HANA のインストールの値に置き換えてください。

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. **[A]** キーストア エントリを作成します
   
    root としてログインし、次のコマンドを実行して、新しいキーストア エントリを作成します。

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>

1. **[1]** データベースをバックアップします

   root としてログインし、データベースをバックアップします

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   マルチテナント インストールを使用する場合は、テナント データベースもバックアップします

   <pre><code>   
   hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** 最初のノードでシステム レプリケーションを構成します
    
    `<hanasid`>adm としてログインし、プライマリ サイトを作成します。

    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>

1. **[2]** セカンダリ ノードでシステム レプリケーションを構成します。

    `<hanasid`>adm としてログインし、セカンダリ サイトを登録します。

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA クラスター リソースの作成

   最初に、HANA トポロジを作成します。 Pacemaker クラスター ノードのいずれかで、次のコマンドを実行します。
   
   <pre><code>
   sudo crm configure property maintenance-mode=true

   # replace the bold string with your instance number and HANA system ID
   
   sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
     operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"
   
   sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
   </code></pre>
   
   次に、HANA リソースを作成します。
   
   <pre><code>
   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
      
   sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
   
   sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
   
   sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
     meta target-role="Started" is-managed="true" \
     operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10s" timeout="20s" \
     params ip="<b>10.0.0.13</b>"
   
   sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>
   
   sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  
   
   sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
   
   # Cleanup the HANA resources. The HANA resources might have failed because of a known issue.
   sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

   sudo crm configure property maintenance-mode=false
   </code></pre>

   クラスターの状態が正常であることと、すべてのリソースが起動されていることを確認します。 リソースがどのノードで実行されているかは重要ではありません。

   <pre><code>
   sudo crm_mon -r
   
   # Online: [ hn1-db-0 hn1-db-1 ]
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): Started hn1-db-0
   # rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
   # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
   #     Started: [ hn1-db-0 hn1-db-1 ]
   # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
   #     Masters: [ hn1-db-0 ]
   #     Slaves: [ hn1-db-1 ]
   # Resource Group: g_ip_HN1_HDB03
   #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
   #     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

### <a name="test-cluster-setup"></a>クラスターのセットアップのテスト
この章では、セットアップをテストする方法について説明します。 テストはすべて root で行い、仮想マシン hn1-db-0 で SAP HANA マスターが実行されていることを前提とします。

#### <a name="fencing-test"></a>フェンスのテスト

フェンス エージェントのセットアップをテストするには、ノード hn1-db-0 でネットワーク インターフェイスを無効にします。

<pre><code>
sudo ifdown eth0
</code></pre>

クラスターの構成によって、仮想マシンが再起動するか停止します。
stonith-action を off に設定すると、仮想マシンが停止し、実行中の仮想マシンにリソースが移行します。

AUTOMATED_REGISTER を false に設定した場合、仮想マシンを再起動すると、SAP HANA リソースがセカンダリとしての起動に失敗します。 その場合は、次のコマンドを実行して HANA のインスタンスをセカンダリとして構成してください。

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>手動フェールオーバーのテスト

手動フェールオーバーをテストするには、ノード hn1-db-0 で Pacemaker サービスを停止します。
<pre><code>
service pacemaker stop
</code></pre>

フェールオーバー後、サービスを再度開始できます。 AUTOMATED_REGISTER を false に設定した場合、hn1-db-0 の SAP HANA リソースがセカンダリとしての起動に失敗します。 その場合は、次のコマンドを実行して HANA のインスタンスをセカンダリとして構成してください。

<pre><code>
service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-migration"></a>移行のテスト

SAP HANA マスター ノードは、次のコマンドを実行すると移行できます
<pre><code>
crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
crm resource migrate g_ip_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

AUTOMATED_REGISTER を false に設定した場合、この一連のコマンドを実行すると、SAP HANA マスター ノードおよび仮想 IP アドレスを含むグループが hn1-db-1 に移行します。
hn1-db-0 の SAP HANA リソースは、セカンダリとしての起動に失敗します。 その場合は、次のコマンドを実行して HANA のインスタンスをセカンダリとして構成してください。

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

移行では場所の制約が作成されますが、これは再度削除する必要があります。

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HN1</b>_HDB<b>03</b> g_ip_<b>HN1</b>_HDB<b>03</b> role=Started inf: <b>hn1-db-1</b>
</code></pre>

また、セカンダリ ノードのリソースの状態をクリーンアップする必要があります

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

## <a name="next-steps"></a>次の手順
* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。 
