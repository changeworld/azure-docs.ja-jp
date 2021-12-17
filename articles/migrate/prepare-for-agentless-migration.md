---
title: Azure Migrate でのエージェントレス移行のためにマシンを準備する
description: Azure Migrate でのエージェントレス移行のためにオンプレミスのマシンを準備する方法について学習します。
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: 1765b671f09dc90506636cc643337e55957c95cd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084636"
---
# <a name="prepare-for-vmware-agentless-migration"></a>VMware のエージェントレス移行の準備をする

この記事では、Azure Migrate: Server Migration ツールを使用して、[エージェントレスの移行方法により VMware VM を Azure に移行する](./tutorial-migrate-vmware.md)ときに実行される変更の概要を説明します。

オンプレミスの VM を Azure に移行する前に、VM を Azure 対応準備するためにいくつかの変更が必要になる場合があります。 これらの変更は、移行された VM が Azure で正常に起動され、Azure VM への接続を確立できるようにするために重要です。
以下で示す Linux および Windows オペレーティング システムのバージョンについては、これらの構成の変更は Azure Migrate によって自動的に処理されます。 このプロセスは "*ハイドレーション*" と呼ばれます。

**ハイドレーションでサポートされているオペレーティング システムのバージョン**

- Windows Server 2008 以降
- Red Hat Enterprise Linux 8.x、7.9、7.8、7.7、7.6、7.5、7.4、7.0、6.x
- CentOS 8.x、7.7、7.6、7.5、7.4、6.x
- SUSE Linux Enterprise Server 15 SP0、15 SP1、12
- Ubuntu 20.04、19.04、19.10、18.04LTS、16.04LTS、14.04LTS
- Ubuntu 18.04LTS、16.04LTS
- Debian 9、8、7
- Oracle Linux 6、7.7、7.7-CI

また、上の一覧にないオペレーティング システムのバージョンについては、この記事を使用して、Azure に移行するための VM の準備を手動で行うこともできます。 このような変更には、だいたい次のものがあります。

- 必要なドライバーの存在を検証する
- シリアル コンソールを有効にする
- ネットワークの設定を構成する
- VM ゲスト エージェントをインストールする

## <a name="hydration-process"></a>ハイドレーション プロセス

移行された VM が Azure で正常に機能するよう、移行の前に、VM の構成にいくつかの変更を行う必要があります。 Azure Migrate では、これらの構成の変更は "*ハイドレーション*" プロセスによって処理されます。 ハイドレーション プロセスは、上で示した Azure でサポートされるオペレーティング システムのバージョンに対してのみ実行されます。 上の一覧にない他のオペレーティング システムのバージョンについては、移行する前に、必要な変更を手動で実行することが必要な場合があります。 必要な変更を行わずに VM を移行すると、VM が起動しない、または移行された VM に接続できないおそれがあります。 次の図は、Azure Migrate によって実行されるハイドレーション プロセスを示したものです。

 [![ハイドレーションの手順](./media/concepts-prepare-vmware-agentless-migration/hydration-process-inline.png)](./media/concepts-prepare-vmware-agentless-migration/hydration-process-expanded.png#lightbox)

ユーザーが "*テスト移行*" または "*移行*" を開始すると、Azure Migrate によってハイドレーション プロセスが実行され、オンプレミスの VM が Azure に移行できるように準備されます。
ハイドレーション プロセスをセットアップするため、Azure Migrate によって一時的な Azure VM が作成され、ソース VM のディスクがアタッチされて、ソース VM を Azure 用に準備するための変更が行われます。 一時的な Azure VM は、最終的な移行済み VM が作成される前に、移行プロセスの間に作成される中間 VM です。 一時的な VM は、Marketplace の OS イメージの 1 つを使用して、同種の OS (Windows または Linux) で作成されます。 オンプレミスの VM で Windows が実行されている場合は、変更を実行するために、オンプレミスの VM のオペレーティング システム ディスクが、一時的な VM にデータ ディスクとしてアタッチされます。 Linux サーバーの場合は、オンプレミスの VM にアタッチされているすべてのディスクが、一時的な Azure VM にデータ ディスクとしてアタッチされます。

Azure Migrate により、一時的な VM をホストするためのネットワーク インターフェイス、新しい仮想ネットワーク、サブネット、ネットワーク セキュリティ グループ (NSG) が作成されます。 これらのリソースは、お客様のサブスクリプションに作成されます。 競合するポリシーのためにネットワーク成果物を作成できない場合は、Azure Migrate によって、レプリケーション ターゲットの設定オプションの一部として提供される仮想ネットワークとサブネットで、一時的な Azure VM の作成が試みられます。

仮想マシンが作成された後、Azure Migrate により、Azure Virtual Machine REST API を使用して、一時的な VM で[カスタム スクリプト拡張機能](../virtual-machines/extensions/custom-script-windows.md)が起動されます。 カスタム スクリプト拡張機能ユーティリティにより、一時的な Azure VM にアタッチされたオンプレミスの VM ディスクで、Azure の準備に必要な構成が含まれる準備スクリプトが実行されます。 準備スクリプトは、Azure Migrate によって所有されているストレージ アカウントからダウンロードされます。 スクリプトを実行するために一時的な Azure VM が Azure Migrate のストレージ アカウントにアクセスできるよう、仮想ネットワークのネットワーク セキュリティ グループの規則が構成されます。

 ![移行の手順](./media/concepts-vmware-agentless-migration/migration-steps.png)

## <a name="changes-performed-during-the-hydration-process"></a>ハイドレーション プロセスの間に行われる変更

準備スクリプトにより、移行されるソース VM の OS の種類に基づいて、次の変更が実行されます。 このセクションをガイドとして使用し、ハイドレーションでサポートされていないオペレーティング システムの移行用に VM を手動で準備することもできます。

### <a name="changes-performed-on-windows-servers"></a>Windows サーバーで行われる変更

1. **Windows OS ボリュームを検出して準備する**

   関連する構成の変更を実行する前に、準備スクリプトによって、正しい OS ディスクが移行用に選択されているかどうかが検証されます。 準備スクリプトにより、システムで認識できるすべてのアタッチされたボリュームが走査され、SYSTEM レジストリ ハイブ ファイル パスが調べられて、ソース OS のボリュームが検索されます。

   このステップでは、次のアクションが実行されます。
   - 一時的な VM にアタッチされている OS ディスク上の各パーティションがマウントされます。

   - パーティションがマウントされた後、\Windows\System32\Config\System でレジストリ ファイルが検索されます。
   - ファイルが見つからない場合、そのパーティションはマウント解除され、適切なパーティションで検索が続行されます。
   - ファイルがどのパーティションにも存在しない場合は、正しくない OS ディスクが選択されているか、OS ディスクが破損しているおそれがあることを示します。 Azure Migrate の移行プロセスは、適切なエラーで失敗します。

   >[!NOTE]
   >移行用にサーバーを手動で準備している場合、このステップは関係ありません。

1. **起動と接続に関連する変更を行う**

   ソース OS ボリューム ファイルが検出された後、準備スクリプトによって、SYSTEM レジストリ ハイブが一時的な Azure VM のレジストリ エディターに読み込まれ、VM が確実に起動および接続されるようにするため、次の変更が行われます。 OS のバージョンがハイドレーションでサポートされていない場合は、これらの設定を手動で構成する必要があります。

   1. **必要なドライバーの存在を検証する**
      
      必要なドライバーがインストールされていて、**ブート開始** 時に読み込まれるように設定されていることを確認します。 これらの Windows ドライバーにより、サーバーはハードウェアおよび接続されている他のデバイスと通信できるようになります。

      - IntelIde.sys
      - Atapi
      - Storfit
      - Storvsc
      - VMbus

   1. **記憶域ネットワーク (SAN) のポリシーを "すべてをオンライン" に設定する**

      これにより、Azure VM 内の Windows ボリュームで、オンプレミスの VM と同じドライブ文字の割り当てが使用されるようになります。 既定では、一時的なストレージとして使用するドライブ D: が Azure VM に割り当てられます。 このドライブが割り当てられると、アタッチされている他のストレージ ドライブに割り当てられている文字が 1 文字ずつ後ろにずれることになります。 この自動的な割り当てを回避し、次に空いているドライブ文字が Azure によって一時ボリュームに確実に割り当てられるようにするには、記憶域ネットワーク (SAN) のポリシーを "すべてをオンライン" に設定します。

      この設定を手動で構成するには:

      - オンプレミスのサーバーで、昇格された特権を使用してコマンド プロンプトを開き、「**diskpart**」と入力します。

        ![手動で構成](./media/concepts-prepare-vmware-agentless-migration/command-prompt-diskpart.png)

      - 「SAN」と入力します。 ゲスト オペレーティング システムのドライブ文字が維持されていない場合には、Offline All または Offline Shared が返されます。

      - DISKPART プロンプトで、「SAN Policy=OnlineAll」と入力します。 この設定により、ディスクがオンラインになり、両方のディスクの読み取りと書き込みが可能になります。

        ![管理者コマンド プロンプトでの diskpart オンライン ポリシー](./media/concepts-prepare-vmware-agentless-migration/diskpart-online-policy.png)

1. **DHCP の開始の種類を設定する**

   準備スクリプトでは、DHCP サービスの開始の種類も "自動" に設定されます。 これにより、移行された VM で、移行後に IP アドレスを取得して接続を確立できるようになります。 DHCP サービスが構成されていて、状態が実行中であることを確認します。

    ![DHCP の開始の種類の設定](./media/concepts-prepare-vmware-agentless-migration/get-service-dhcp.png)

   DHCP のスタートアップの設定を手動で編集するには、Windows PowerShell で次の例を実行します。

   ```
   Get-Service -Name Dhcp
   Where-Object StartType -ne Automatic
   Set-Service -StartupType Automatic
   ```

1. **VMware Tools を無効にする**

   "VMware Tools" サービスは、Azure の VM には必要ないので、存在する場合は、そのスタートアップの種類を無効にします。

   >[!NOTE]                        
   >Windows Server 2003 VM に接続するには、Hyper-V 統合サービスを Azure VM にインストールする必要があります。 既定では、Windows Server 2003 コンピューターにはインストールされていません。 こちらの[記事](./prepare-windows-server-2003-migration.md)を参照し、移行用にインストールして準備します。

1. **Windows Azure ゲスト エージェントをインストールする**

    Azure Migrate により、Microsoft Azure 仮想マシン エージェント (VM エージェント) のインストールが試みられます。これは、仮想マシン (VM) と Azure ファブリック コントローラーのやり取りを管理する、セキュリティで保護された軽量のプロセスです。 VM エージェントの主要な役割は、Azure 仮想マシン拡張機能を有効にして実行することであり、これにより、ソフトウェアのインストールや構成など、VM のデプロイ後の構成が可能になります。 Windows Server 2008 R2 以降のバージョンには、Azure Migrate により、Windows VM エージェントが自動的にインストールされます。

    Windows インストーラー パッケージを使用して、手動で Windows VM エージェントをインストールできます。 手動で Windows VM エージェントをインストールするには、[VM エージェント インストーラーをダウンロードします](https://go.microsoft.com/fwlink/?LinkID=394789)。 [GitHub の Windows IaaS VM エージェントのリリース](https://github.com/Azure/WindowsVMAgent/releases)で、特定のバージョンを検索することもできます。 VM エージェントは、Windows Server 2008 (64 ビット) 以降でサポートされます。

    Azure VM エージェントが正常にインストールされたことを確認するには、タスク マネージャーを開いて **[詳細]** タブを選択し、*WindowsAzureGuestAgent.exe* というプロセス名を探します。 このプロセスが存在する場合は、VM エージェントがインストールされています。 [PowerShell を使用して VM エージェントを検出する](../virtual-machines/extensions/agent-windows.md#powershell)こともできます。

    ![Azure VM エージェントの正常なインストール](./media/concepts-prepare-vmware-agentless-migration/installation-azure-vm-agent.png)

    上記の変更が行われた後、システム パーティションがアンロードされます。 これで、VM を移行する準備ができました。
    [Windows サーバーに関する変更の詳細については、こちらを参照してください。](../virtual-machines/windows/prepare-for-upload-vhd-image.md)

### <a name="changes-performed-on-linux-servers"></a>Linux サーバーで行われる変更

1. **Linux OS パーティションを検出してマウントする**  

   関連する構成の変更を実行する前に、準備スクリプトによって、正しい OS ディスクが移行用に選択されているかどうかが検証されます。 スクリプトにより、すべてのパーティション、その UUID、マウント ポイントに関する情報が収集されます。 スクリプトによって、認識されるすべてのパーティションが走査され、/boot および /root パーティションが見つけられます。

   このステップでは、次のアクションが実行されます。

   - /root パーティションを検出します。
     - 認識される各パーティションがマウントされて、etc/fstab が検索されます。
      - fstab ファイルが見つからない場合、そのパーティションはマウント解除され、適切なパーティションで検索が続行されます。
      - fstab ファイルが見つかった場合は、fstab の内容が読み取られて、ルート デバイスが特定され、ベース マウント ポイントとしてマウントされます。
   - /boot と他のシステム パーティションを検出します。
     - fstab の内容を使用して、/boot が別のパーティションであるかどうかが判断されます。 別のパーティションの場合は、fstab の内容からブート パーティションのデバイス名が取得されるか、ブート フラグを持つパーティションが検索されます。
     - 次に、スクリプトによって、/boot と、chroot jail に必要なルート ファイルシステム ツリーを構築するために必要な "/mnt/azure_sms_root" 上の他のパーティションが、検出されてマウントされます。 他の必要なパーティションとしては、/boot/grub/menu.lst、/boot/grub/grub.conf、/boot/grub2/grub.cfg、/boot/grub/grub.cfg、/boot/efi (UEFI ブート用)、/var、/lib、/etc、/usr などがあります。

1. **OS のバージョンを検出する**

   ルート パーティションが検出された後、スクリプトにより、以下のファイルを使用して、Linux オペレーティング システムのディストリビューションとバージョンが特定されます。

   - RHEL/CentOS: etc/redhat-release
   - OL: etc/oracle-release
   - SLES: etc/SuSE-release
   - Ubuntu: etc/lsb-release
   - Debian: etc/debian_version

1. **Hyper-V Linux Integration Services をインストールし、カーネル イメージを再生成する**  

   次のステップでは、カーネル イメージが検査され、初期 RAM ディスクに必要な Hyper-V ドライバー (**hv_vmbus、hv_storvsc、hv_netvsc**) が含まれるように、Linux の init イメージがリビルドされます。 init イメージをリビルドすることにより、Azure で確実に VM が起動します。

   Azure は、Hyper-V ハイパーバイザー上で実行されます。 そのため、Linux が Azure で実行されるには特定のカーネル モジュールが必要です。 Linux イメージを準備するには、少なくとも hv_vmbus と hv_storvsc のカーネル モジュールを初期 RAM ディスクで使用できるように、initrd をリビルドする必要があります。 initrd または initramfs イメージの再構築のためのメカニズムは、ディストリビューションによって異なる場合があります。 適切な手順については、使用しているディストリビューションのドキュメントまたはサポートを参照してください。 一例として、mkinitrd ユーティリティを使用した initrd のリビルドを次に示します。

   1. システムにインストールされているカーネルの一覧を検索します (/lib/modules)
   1. モジュールごとに、Hyper-V ドライバーが既に含まれているかどうかを調べます。
   1. いずれかのドライバーがない場合は、必要なドライバーを追加し、対応するカーネル バージョンのイメージを再生成します。

      >[!NOTE]
      >Ubuntu および Debian の VM には Hyper-V ドライバーが既定で組み込まれているため、このステップが適用されない場合があります。 [変更に関する詳細については、こちらを参照してください。](../virtual-machines/linux/create-upload-generic.md#installing-kernel-modules-without-hyper-v)

      Initrd のリビルドに関して説明する例

      - 既存の initrd イメージをバックアップします

        ```
        cd /boot
        sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
        ```

      - hv_vmbus と hv_storvsc のカーネル モジュールを使用して initrd を再構築します。

        ```
          sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
        ```
   Linux ディストリビューションの新しいバージョンには、ほとんどの場合、既定でこれが含まれています。 上で示したものを除くすべてのバージョンについて、これが含まれていない場合は、前述の手順を使用して手動でインストールします。

1. **Azure シリアル コンソールのログ記録を有効にする**

   その後、スクリプトによって、Azure シリアル コンソール ログを有効にするように変更されます。 コンソール ログを有効にすると、Azure VM での問題のトラブルシューティングに役立ちます。 Linux 用 Azure シリアル コンソールの詳細については、「[Linux 用 Azure シリアル コンソール - Virtual Machines | Microsoft Docs](/troubleshoot/azure/virtual-machines/serial-console-linux)」を参照してください。

   GRUB または GRUB2 のカーネル ブート ラインを変更して次のパラメーターを含め、すべてのコンソール メッセージが最初のシリアル ポートに送信されるようにします。 これらのメッセージは、Azure Support での問題のデバッグに役立ちます。

   ```
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
   ```

   また、次のパラメーターがある場合、それらを削除することをお勧めします。

   ```
   rhgb quiet crashkernel=auto
   ```
    具体的な変更については、[こちらの記事を参照してください](../virtual-machines/linux/create-upload-generic.md#general-linux-system-requirements)。

1. **接続に関するネットワークの変更**

   OS のバージョンに基づいて、スクリプトにより、移行された VM への接続に必要なネットワークの変更が行われます。 それらの変更を次に示します。

   1. udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Azure で仮想マシンを複製するときに問題の原因となります。

      RedHat サーバーの例

      ```console
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
      ```

   1. 必要に応じて、ネットワーク マネージャーを削除します。 一部の OS バージョンでは、ネットワーク マネージャーが Azure Linux エージェントと干渉する場合があります。 RedHat および Ubuntu のディストリビューションを実行しているサーバーでは、これらの変更を行うことをお勧めします。

   1. 次のコマンドを実行して、このパッケージをアンインストールします。
    
      RedHat サーバーの例

      ```console
         # sudo rpm -e --nodeps NetworkManager
      ```

   1. 既存の NIC の設定をバックアップし、DHCP の設定を使用して eth0 NIC 構成ファイルを作成します。 これを行うため、スクリプトによって /etc/sysconfig/network-scripts/ifcfg-eth0 ファイルが作成または編集されて、次のテキストが追加されます。

      RedHat サーバーの例

      ```config
         DEVICE=eth0
         ONBOOT=yes
         BOOTPROTO=dhcp
         TYPE=Ethernet
         USERCTL=no
         PEERDNS=yes
         IPV6INIT=no
         PERSISTENT_DHCLIENT=yes
         NM_CONTROLLED=yes
      ```

   1. 次のように、/sysconfig/network ファイルをリセットします。

      RedHat サーバーの例

      ```config
         NETWORKING=yes
         HOSTNAME=localhost.localdomain
      ```

1. **fstab の検証**

    Azure Migrate により、fstab ファイルのエントリが検証され、必要に応じて、fstab のエントリが永続的なボリューム識別子 UUID に置き換えられます。 これにより、アタッチされているシステムに関係なく、ドライブおよびパーティションの名前が一定に維持されます。

   - デバイス名が標準的なデバイス名 (/dev/sdb1 など) の場合は、次のようになります。
     - ルート パーティションまたはブート パーティションの場合は、UUID に置き換えられます。
     - パーティションが、同じディスク上の標準パーティションとしてのルート パーティションまたはブート パーティションと共存している場合は、それが UUID に置き換えられます。
   - デバイス名が UUID/LABEL/LV の場合は、変更は行われません。
   - ネットワーク デバイス (nfs、cifs、smbfs など) の場合は、スクリプトによってエントリがコメント化されます。 それにアクセスするには、同じものをコメント解除して、Azure VM を再起動します。

1. **Linux Azure ゲスト エージェントをインストールする**

    Azure Migrate により、Microsoft Azure Linux エージェント (waagent) のインストールが試みられます。これは、Linux と FreeBSD のプロビジョニングおよび VM と Azure ファブリック コントローラーのやり取りを管理する、セキュリティで保護された軽量のプロセスです。  Linux エージェントにより Linux と FreeBSD IaaS のデプロイで有効になる機能の詳細については、[こちら](../virtual-machines/extensions/agent-linux.md)を参照してください。

    Linux VM エージェントをインストールするために[必要なパッケージ](../virtual-machines/extensions/agent-linux.md#requirements)の一覧を確認します。 Azure Migrate は、VMware 移行のエージェントレスの方法を使用しているときに、RHEL 8.x/7.x/6.x, CentOS 8.x/7.x/6.x, Ubuntu 14.04/16.04/18.04/19.04/19.10/20.04, SUSE 15 SP0/15 SP1/12、Debian 9/8/7、および Oracle 7 用に Linux VM エージェントを自動的にインストールします。 他の OS バージョンについては、[Linux エージェントの手動インストール手順](../virtual-machines/extensions/agent-linux.md#installation)に従ってください。

    Azure Linux エージェントのサービス状態を検証するためのコマンドを使用して、それが実行されていることを確認できます。 サービス名は **walinuxagent** や **waagent** のようになります。
    ハイドレーションの変更が完了すると、スクリプトによって、マウントされているすべてのパーティションのマウントが解除され、ボリューム グループが非アクティブ化された後、デバイスがフラッシュされます。

   ```
    $ vgchange -an <vg-name>
    $ blockdev –flushbufs <disk-device-name>
   ```

   [Linux サーバーの変更に関する詳細を参照してください。](../virtual-machines/linux/create-upload-generic.md)

### <a name="clean-up-the-temporary-vm"></a>一時的な VM をクリーンアップする

必要な変更が済むと、Azure Migrate によって一時的な VM が停止され、アタッチされている OS ディスク (およびデータ ディスク) が解放されます。 これにより、"*ハイドレーション プロセス*" の終了がマークされます。     

その後、変更された OS ディスクと、レプリケートされたデータを含むデータ ディスクが複製されます。 ターゲット リージョンに新しい仮想マシンが作成され、仮想ネットワーク、サブネット、複製されたディスクが仮想マシンにアタッチされます。 これにより、移行プロセスの完了がマークされます。

## <a name="learn-more"></a>詳細情報

- [Azure への移行のためにオンプレミスのマシンを準備する](./prepare-for-migration.md)
