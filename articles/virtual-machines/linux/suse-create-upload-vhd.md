---
title: Azure 上での SUSE Linux VHD の作成とアップロード
description: SUSE Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 276f5f4542ecea42c665764b8c4e5f66f2531126
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552713"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Azure 用の SLES または openSUSE 仮想マシンの準備


この記事では、既に SUSE または openSUSE Linux オペレーティング システムを仮想ハード ディスクにインストールしていることを前提にしています。 .vhd ファイルを作成するツールは、Hyper-V のような仮想化ソリューションなど複数あります。 詳細については、「 [Hyper-V の役割のインストールと仮想マシンの構成](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))」を参照してください。

## <a name="sles--opensuse-installation-notes"></a>SLES/openSUSE のインストールに関する注記
* Azure で Linux を準備する際のその他のヒントについては、「 [Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes) 」も参照してください。
* VHDX 形式は Azure ではサポートされていません。サポートされるのは **固定 VHD** のみです。  Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。
* Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。 必要な場合は、[LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) または [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) をデータ ディスク上で使用できます。
* OS ディスクにスワップ パーティションを構成しないでください。 Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。  このことに関する詳細については、次の手順を参照してください。
* Azure の VHD の仮想サイズはすべて、1 MB にアラインメントさせる必要があります。 未フォーマット ディスクから VHD に変換するときに、変換する前の未フォーマット ディスクのサイズが 1 MB の倍数であることを確認する必要があります。 詳細については、[Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes)のセクションを参照してください。

## <a name="use-suse-studio"></a>SUSE Studio を使用する
[SUSE Studio](https://studioexpress.opensuse.org/) を使用すると、Azure および Hyper-V 用の SLES イメージと openSUSE イメージを簡単に作成、管理できます。 独自の SLES イメージや openSUSE イメージをカスタマイズする場合はこの方法をお勧めします。

SUSE では、独自の VHD を構築する代わりに、[VM Depot](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf)に SLES の BYOS (Bring Your Own Subscription) イメージを発行することもできます。

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>Azure 用に SUSE Linux Enterprise Server を準備する
1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。
2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。
3. SUSE Linux Enterprise システムを登録すると、更新プログラムをダウンロードしてパッケージをインストールできるようになります。
4. 最新の更新ログラムでシステムを更新します。

    ```console
    # sudo zypper update
    ```
    
5. Azure Linux エージェントと cloud-init をインストールします。

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. waagent と cloud-init がブート時に起動されるようにします。

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. waagent と cloud-Init の構成を更新します。

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. コンソール ログがシリアル ポートに送信されるように /etc/default/grub ファイルを編集してから、grub2-mkconfig -o /boot/grub2/grub.cfg を使用して主要な構成ファイルを更新します。

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。
    
9. UUID (by-uuid) を使用して、/etc/fstab ファイルでディスクが参照されるようにします。
         
10. udev ルールを編集して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となる可能性があります。

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. "/etc/sysconfig/network/dhcp" ファイルを編集して、次のように `DHCLIENT_SET_HOSTNAME` パラメーターを変更することをお勧めします。

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. "/etc/sudoers" で、次の行をコメント アウトするか削除する必要があります (ある場合)。

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。 通常これが既定です。

14. スワップの構成
 
    オペレーティング システム ディスクにスワップ領域を作成しないでください。

    以前は、Azure で仮想マシンがプロビジョニングされた後に、仮想マシンに接続されたローカル リソース ディスクを使用してスワップ領域を自動的に構成するために、Azure Linux エージェントが使用されていました。 しかし、これは cloud-init によって処理されるようになったので、Linux エージェントを使用して、スワップ ファイルを作成するリソース ディスクをフォーマット **しないでください**。`/etc/waagent.conf` で次のパラメーターを適切に変更します。

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    スワップのマウント、フォーマット、作成を行う場合は、次のいずれかの方法を使用できます。
    * VM を作成するたびに、cloud-init 構成としてこれを渡します。
    * VM が作成されるたびに、これを実行するイメージに組み込まれている cloud-init ディレクティブを使用します。

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```

15. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

    ```console
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```
16. Hyper-V マネージャーで **[アクション]、[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

---
## <a name="prepare-opensuse-131"></a>openSUSE 13.1 以上の準備
1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。
2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。
3. シェルでコマンド "`zypper lr`" を実行します。 このコマンドから次のような出力が返された場合、リポジトリは予想どおりに構成されているため、調整は不要です (バージョン番号が異なる場合があります)。

   | # | エイリアス                 | 名前                  | Enabled | 更新
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | はい     | はい
   | 2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | はい     | はい
   | 3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | はい     | はい

    コマンドによって "No repositories defined..." が返された場合は、次のコマンドを実行してこれらのリポジトリを追加します。

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
    # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
    ```

    "`zypper lr`" コマンドをもう一度実行してリポジトリが追加されたことを確認できます。 更新したリポジトリのいずれかが有効になっていない場合は、次のコマンドを使用して有効にします。

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. カーネルを最新のバージョンに更新します。

    ```console
    # sudo zypper up kernel-default
    ```

    または、次のように、すべての最新のパッチでシステムを更新します。

    ```console
    # sudo zypper update
    ```

5. Azure Linux エージェントをインストールします。

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで "/boot/grub/menu.lst" を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 また、カーネルのブート行に次のパラメーターがある場合は削除します。

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. "/etc/sysconfig/network/dhcp" ファイルを編集して、次のように `DHCLIENT_SET_HOSTNAME` パラメーターを変更することをお勧めします。

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **重要:** "/etc/sudoers" で、次の行をコメント アウトするか削除する必要があります (ある場合)。

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。
10. OS ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは *一時* ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. 起動時に Azure Linux エージェントが実行されるようにします。

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Hyper-V マネージャーで **[アクション]、[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

## <a name="next-steps"></a>次のステップ
これで、SUSE Linux 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。 .vhd ファイルを Azure に初めてアップロードする場合は、「[Create a Linux VM from a custom disk (カスタム ディスクから Linux VM を作成する)](upload-vhd.md#option-1-upload-a-vhd)」を参照してください。