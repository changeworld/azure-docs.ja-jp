---
title: Windows および Linux IaaS VM の Azure Disk Encryption | Microsoft Docs
description: この記事は、Windows および Linux IaaS VM 用の Microsoft Azure Disk Encryption に関する付録です。
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 98bbcb84-8e6c-4eb2-8490-c2a0c67aad79
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: cf3e9ce055219bccb44c19fd8e77fe39c938c968
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392599"
---
# <a name="appendix-for-azure-disk-encryption"></a>Azure Disk Encryption に関する付録 
この記事は、[IaaS VM 用 Azure Disk Encryption](azure-security-disk-encryption-overview.md) の付録です。 コンテキストを理解するために、まず、IaaS VM 用の Azure Disk Encryption に関する記事をお読みください。 この記事では、事前に暗号化された VHD を準備する方法とその他のタスクについて説明します。

## <a name="connect-to-your-subscription"></a>サブスクリプションへの接続
続行する前に、[前提条件](azure-security-disk-encryption-prerequisites.md)に関する記事を確認してください。 すべての前提条件が満たされていることを確認したら、次のコマンドレットを実行して、ご利用のサブスクリプションに接続します。

### <a name="bkmk_ConnectPSH"></a> PowerShell を使用してサブスクリプションに接続する

1. Azure PowerShell セッションを開始し、次のコマンドを使用して Azure アカウントにサインインします。

     ```powershell
     Connect-AzureRmAccount 
     ```
2. 複数のサブスクリプションがあり、使用するサブスクリプションを指定する場合は、次のように入力して自分のアカウントのサブスクリプションを表示します。
     
     ```powershell
     Get-AzureRmSubscription
     ```
3. 使用するサブスクリプションを指定するには、次のように入力します。
 
     ```powershell
      Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. 構成されているサブスクリプションが正しいことを確認するには、次のように入力します。
     
     ```powershell
     Get-AzureRmSubscription
     ```
5. 必要に応じて、[Connect-AzureAD](/powershell/module/azuread/connect-azuread) を使用して Azure AD に接続します。
     
     ```powershell
     Connect-AzureAD
     ```
6. Azure Disk Encryption コマンドレットがインストールされていることを確認するには、次のように入力します。
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. 必要に応じて、「[Azure PowerShell の使用に関するページ](/powershell/azure/get-started-azureps)」と「[AzureAD](/powershell/module/azuread)」を確認します。

### <a name="bkmk_ConnectCLI"></a> Azure CLI を使用してサブスクリプションに接続する

1. [az login](/cli/azure/authenticate-azure-cli#interactive-log-in) を使用して、Azure にログインします。 
     
     ```azurecli
     az login
     ```

2. ログインするテナントを選択するには、以下を使用します。
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. 複数のサブスクリプションがあり、特定のサブスクリプションを指定する場合は、[az account list](/cli/azure/account#az-account-list) を使用してサブスクリプションの一覧を表示し、[az account set](/cli/azure/account#az-account-set) を使用して指定します。
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. インストールされているバージョンを確認します。
     
     ```azurecli
        az --version
     ``` 

5. 必要に応じて、「[Azure CLI 2.0 を使ってみる](/cli/azure/get-started-with-azure-cli)」を確認します。 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Azure Disk Encryption 用の PowerShell スクリプトのサンプル 

- **サブスクリプション内の暗号化された VM をすべて一覧表示する**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzureRmVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **キー コンテナー内の VM を暗号化するために使用されるディスクの暗号化シークレットをすべて一覧表示する** 

     ```azurepowershell-interactive
     Get-AzureKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

## <a name="resource-manager-templates"></a>Resource Manager テンプレート

- [Key Vault を作成します](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) 
 
- [Marketplace から作成された新しい IaaS Windows VM でディスク暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - このテンプレートでは、Windows Server 2012 ギャラリー イメージを使用する、暗号化された Windows VM が新規に作成されます。

- [完全なディスク暗号化を使用した RHEL 7.2 のデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)
    - このテンプレートでは、30 GB の暗号化された OS ドライブと、/mnt/raidencrypted にマウントされている 200 GB の RAID-0 アレイがある、完全に暗号化された RHEL 7.2 VM が Azure で作成されます。 サポートされている Linux サーバー ディストリビューションについては、[FAQ](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) に関する記事を参照してください。 

- [Windows または Linux 用に事前に暗号化された VHD でディスク暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)

- [既存または実行中の IaaS Windows VM でディスク暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [既存または実行中の IaaS Linux VM でディスク暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrt-running-linux-vm)    

- [実行中の Windows IaaS でディスク暗号化を無効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [実行中の Linux VM で暗号化を無効にする](https://aka.ms/decrypt-linuxvm) 
    - Linux VM 用のデータ ボリュームでのみ、暗号化を無効にすることができます。 

- [事前に暗号化された VHD/ストレージ BLOB から、新しい暗号化されたマネージド ディスクを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - 事前に暗号化された VHD とその対応する暗号化設定が用意されている、新しい暗号化されたマネージド ディスクが作成されます

- [ギャラリー イメージから新しい暗号化された Windows IaaS マネージド ディスク VM を作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - このテンプレートでは、Windows Server 2012 ギャラリー イメージを使用して、マネージド ディスクを含む新しい暗号化された Windows VM が作成されます。
    

## <a name="bkmk_preWin"></a> 事前に暗号化された Windows VHD を準備する
以下のセクションに示すのは、事前に暗号化された Windows VHD を準備し、それを Azure IaaS 内の暗号化された VHD としてデプロイするために必要な情報です。 Azure Site Recovery や Azure 上に新しい Windows VM (VHD) を準備し、それらを起動する際には、これらの情報を使用してください。 VHD を準備してアップロードする方法の詳細については、「[汎用化した VHD をアップロードして Azure で新しい VM を作成する](../virtual-machines/windows/upload-generalized-managed.md)」を参照してください。

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>グループ ポリシーを更新して非 TPM で OS を保護できるようにする
**[ローカル コンピューター ポリシー]** > **[コンピューターの構成]** > **[管理用テンプレート]** > **[Windows コンポーネント]** の下にある、**[BitLocker ドライブ暗号化]** という BitLocker グループ ポリシー設定を構成します。 以下の図に示すように、**[オペレーティング システムのドライブ]** > **[スタートアップ時に追加の認証を要求する]** > **[互換性のある TPM が装備されていない BitLocker を許可する]** の順に選択して、この設定を変更します。

![Azure での Microsoft マルウェア対策](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>BitLocker 機能コンポーネントのインストール
Windows Server 2012 以降の場合は、次のコマンドを使用します。

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Windows Server 2008 R2 の場合は、次のコマンドを使用します。

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>`bdehdcfg` を使用して BitLocker の OS ボリュームを準備する
OS のパーティションを圧縮して、BitLocker 用にコンピューターを準備するには、必要に応じて、[bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment#using-bitlocker-to-encrypt-volumescommand) を実行します。

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>BitLocker を使用して OS ボリュームを保護する
ブート ボリュームでの暗号化を、外部キーの保護機能を使用して有効化するには、[`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) コマンドを使用します。 また、外部ドライブまたは外部ボリューム上に外部キー (.bek ファイル) を配置します。 システム/ブート ボリュームでの暗号化は、次回のリブート後に有効になります。

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> BitLocker を使用して外部キーを取得する場合は、そのためのデータ/リソース VHD を別個に使用して VM を準備してください。

## <a name="bkmk_LinuxRunning"></a> 実行中の Linux VM での OS ドライブの暗号化

### <a name="prerequisites-for-os-disk-encryption"></a>OS ディスクを暗号化するための前提条件

* VM は、「[Azure Disk Encryption に関する FAQ](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)」に記載されている OS ディスクの暗号化と互換性のあるディストリビューションを使っている必要があります。 
* VM は、Azure Resource Manager で Marketplace イメージから作成する必要があります。
* 少なくとも 4 GB の RAM を持つAzure VM (推奨するサイズは 7 GB)。
* (RHEL と CentOS については) SELinux を無効にします。 SELinux を無効にする方法については、 VM で「[SELinux User's and Administrator's Guide (SELinux ユーザーおよび管理者用ガイド)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux)」の「4.4.2. Disabling SELinux (SELinux の無効化)」をご覧ください。
* SELinux を無効にしたら、少なくとも 1 回、VM を再起動してください。

### <a name="steps"></a>手順
1. 上記のディストリビューションのいずれかを使用して、VM を作成します。

 CentOS 7.2 では、OS ディスクの暗号化は、特別なイメージを使用してサポートされます。 このイメージを使用するには、VM を作成するときに、SKU として "7.2n" を指定します。

 ```powershell
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. ニーズに応じて VM を構成します。 すべてのドライブ (OS およびデータ) を暗号化する場合は、/etc/fstab にデータ ドライブを指定してマウントできるようにする必要があります。

 > [!NOTE]
 > /etc/fstab にデータ ドライブを指定するには、ブロック デバイス名 (たとえば、/dev/sdb1 など) ではなく、UUID=... を使用してください。 暗号化中には、ドライブの順序が VM 上で変更されます。 VM がブロック デバイスの特定の順序に依存している場合、暗号化後のマウントは失敗します。

3. SSH セッションからサインアウトします。

4. OS を暗号化するには、暗号化を有効にするときに、volumeType として **All** または **OS** を指定します。

 > [!NOTE]
 > `systemd` サービスとして実行されているすべてのユーザー スペースのプロセスは、`SIGKILL` によって強制終了されます。 VM を再起動してください。 実行中の VM での OS ディスクの暗号化を有効にする場合は、VM のダウンタイムを計画してください。

5. [次のセクション](#monitoring-os-encryption-progress)の指示に従って、暗号化の進行状況を定期的に監視します。

6. Get-AzureRmVmDiskEncryptionStatus で "VMRestartPending" が表示されたら、VM にサインインするか、ポータル、PowerShell、または CLI を使用して VM を再起動します。
    ```powershell
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
再起動する前に、VM の[ブート診断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)を保存することをお勧めします。

## <a name="monitoring-os-encryption-progress"></a>OS 暗号化の進行状況の監視
OS 暗号化の進行状況を監視するには、次の 3 つの方法があります。

* `Get-AzureRmVmDiskEncryptionStatus` コマンドレットを使用して、ProgressMessage フィールドを確認する。
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 VM が "OS disk encryption started (OS ディスクの暗号化が開始されました)" 状態に達するまで、Premium ストレージを使用する VM でおよそ 40 分から 50 分かかります。

 WALinuxAgent での[問題 #388](https://github.com/Azure/WALinuxAgent/issues/388) により、一部のディストリビューションでは、`OsVolumeEncrypted` と `DataVolumesEncrypted` は `Unknown` と表示されます。 WALinuxAgent バージョン 2.1.5 以降では、この問題は自動的に修正されます。 出力に `Unknown` が含まれている場合は、Azure リソース エクスプローラーを使用して、ディスクの暗号化状態を確認できます。

 [Azure リソース エクスプローラー](https://resources.azure.com/)に移動し、左側の選択パネルで次の階層を展開します。

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 InstanceView で、画面を下にスクロールして、ドライブの暗号化の状態を表示します。

 ![VM インスタンス ビュー](./media/azure-security-disk-encryption/vm-instanceview.png)

* [ブート診断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)を調べる。 ADE 拡張機能のメッセージには、頭に `[AzureDiskEncryption]` が付きます。

* SSH を使用して VM にサインインし、拡張機能ログを取得する。

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 OS 暗号化の進行中は、VM にサインインしないでください。 ログのコピーは、他の 2 つの方法が失敗した場合にのみ行ってください。

## <a name="bkmk_preLinux"></a> 事前に暗号化された Linux VHD を準備する
事前に暗号化された VHD の準備は、ディストリビューションによって異なる場合があります。 ここでは、[Ubuntu 16](#bkmk_Ubuntu)、[openSUSE 13.2](#bkmk_openSUSE)、および [CentOS 7](#bkmk_CentOS) の準備例を示します。 

### <a name="bkmk_Ubuntu"></a> Ubuntu 16
次の手順に従って、ディストリビューションのインストール時に暗号化を構成します。

1. ディスクをパーティション分割するときに **[Configure encrypted volumes]** を選択します。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. 独立したブート ドライブを作成します。このドライブは暗号化しません。 ルート ドライブを暗号化します。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. パスフレーズを指定します。 これは、キー コンテナーにアップロードしたパスフレーズです。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. パーティション分割を終了します。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. VM を起動し、パスフレーズの入力を求められたら、手順 3 で指定したパスフレーズを入力します。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. [こちらの手順](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/)に従って、VM を Azure にアップロードするための準備をします。 最後の手順 (VM のプロビジョニング解除) はまだ実行しないでください。

次の手順を行って、Azure で使用する暗号化を構成します。

1. 以下のスクリプトのコンテンツを使用して、/usr/local/sbin/azure_crypt_key.sh の下にファイルを作成します。 KeyFileName に注意してください。これは、Azure によって使用されるパスフレーズ ファイル名です。

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. */etc/crypttab* の crypt 構成を変更します。 次のようになります。
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Windows で *azure_crypt_key.sh* を編集し、それを Linux にコピーした場合は、`dos2unix /usr/local/sbin/azure_crypt_key.sh` を実行します。

4. 実行可能アクセス許可をスクリプトに追加します。
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. */etc/initramfs-tools/modules* を編集し、次の行を追加します。
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. `update-initramfs -u -k all` を実行して initramfs を更新し、`keyscript` を有効にします。

7. これで、VM をプロビジョニング解除できるようになります。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. 次の手順に進み、Azure にご利用の VHD をアップロードします。

### <a name="bkmk_openSUSE"></a>  openSUSE 13.2
ディストリビューションのインストール時に暗号化を構成するには、次の手順を行います。
1. ディスクをパーティション分割するときに、**[ボリューム グループの暗号化]** を選択し、パスワードを入力します。 これは、Key Vault にアップロードするパスワードです。

 ![openSUSE 13.2 のセットアップ](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. パスワードを使用して VM を起動します。

 ![openSUSE 13.2 のセットアップ](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. 「[Azure 用の SLES または openSUSE 仮想マシンの準備](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131)」の手順に従って、VM を Azure にアップロードするための準備をします。 最後の手順 (VM のプロビジョニング解除) はまだ実行しないでください。

Azure で使用する暗号化を構成するには、次の手順を行います。
1. /etc/dracut.conf を編集し、次の行を追加します。
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. ファイル /usr/lib/dracut/modules.d/90crypt/module-setup.sh の末尾付近にある以下の行をコメント アウトします。
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh ファイルの先頭に次の行を追加し、
 ```
    DRACUT_SYSTEMD=0
 ```
次の記述のすべての出現箇所を
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
を次のように変更します。
```
    if [ 1 ]; then
```
4. /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh を編集し、それを "# Open LUKS device" に追加します。

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. `/usr/sbin/dracut -f -v` を実行して initrd を更新します。

6. これで、VM のプロビジョニングを解除し、Azure にご利用の VHD をアップロードできるようになります。

### <a name="bkmk_CentOS"></a> CentOS 7
ディストリビューションのインストール時に暗号化を構成するには、次の手順を行います。
1. ディスクをパーティション分割するときに **[Encrypt my data]** を選択します。

 ![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. ルート パーティションで **[Encrypt]** が選択されていることを確認します。

 ![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. パスフレーズを指定します。 これは、キー コンテナーにアップロードするパスフレーズです。

 ![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. VM を起動し、パスフレーズの入力を求められたら、手順 3 で指定したパスフレーズを入力します。

 ![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. 「[Azure 用の CentOS ベースの仮想マシンの準備](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70)」の「CentOS 7.0+」の手順に従って、VM を Azure にアップロードするための準備をします。 最後の手順 (VM のプロビジョニング解除) はまだ実行しないでください。

6. これで、VM のプロビジョニングを解除し、Azure にご利用の VHD をアップロードできるようになります。

Azure で使用する暗号化を構成するには、次の手順を行います。

1. /etc/dracut.conf を編集し、次の行を追加します。
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. ファイル /usr/lib/dracut/modules.d/90crypt/module-setup.sh の末尾付近にある以下の行をコメント アウトします。
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh ファイルの先頭に次の行を追加し、
```
    DRACUT_SYSTEMD=0
```
次の記述のすべての出現箇所を
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
to
```
    if [ 1 ]; then
```
4. /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh を編集し、これを "# Open LUKS device" の後に追加します。
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. “/usr/sbin/dracut -f -v” を実行して initrd を更新します。

![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a> 暗号化された VHD を Azure ストレージ アカウントにアップロードする
BitLocker 暗号化または DM-Crypt 暗号化を有効にしたら、ローカルで暗号化された VHD をストレージ アカウントにアップロードする必要があります。
```powershell
    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a> 事前に暗号化された VM 用のシークレットを自分のキー コンテナーにアップロードする
前に取得したディスク暗号化シークレットを、Key Vault にシークレットとしてアップロードする必要があります。 Key Vault では、ディスク暗号化だけではなく、Azure AD クライアントに対するアクセス許可も有効になっている必要があります。

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a> KEK で暗号化されないディスク暗号化シークレット
Key Vault でシークレットをセットアップするには、[Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) を使用します。 Windows 仮想マシンがある場合は、bek ファイルが base64 文字列としてエンコードされ、その後、`Set-AzureKeyVaultSecret` コマンドレットを使用して Key Vault にアップロードされます。 Linux の場合は、パスフレーズが base64 文字列としてエンコードされ、その後 Key Vault にアップロードされます。 また、Key Vault でシークレットを作成する際には、以下のタグが設定されます。

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


[KEK を使用せずに OS ディスクをアタッチする](#bkmk_URLnoKEK)場合は、次の手順で `$secretUrl` を使用します。

### <a name="bkmk_SecretKEK"></a> KEK で暗号化されるディスク暗号化シークレット
必要であれば、シークレットを Key Vault にアップロードする前に、キー暗号化キーを使用してシークレットを暗号化できます。 最初にキー暗号化キーを使用してシークレットを暗号化するには、ラップ [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) を使用します。 このラップ操作の出力は、base64 URL エンコードされた文字列です。これは、[`Set-AzureKeyVaultSecret`](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) コマンドレットを使用してシークレットとしてアップロードできます。

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

[KEK を使用して OS ディスクをアタッチする](#BKMK_URLKEK)場合は、次の手順で `$KeyEncryptionKey` と `$secretUrl` を使用します。

##  <a name="bkmk_SecretURL"></a> OS ディスクをアタッチするときにシークレット URL を指定する

###  <a name="bkmk_URLnoKEK"></a> KEK を使用しない
OS ディスクをアタッチする際に、`$secretUrl` を渡す必要があります。 この URL は、「KEK で暗号化されないディスク暗号化シークレット」セクションで生成されたものです。
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="bkmk_URLKEK"></a> KEK を使用する
OS ディスクをアタッチする際に、`$KeyEncryptionKey` と `$secretUrl` を渡します。 この URL は、「KEK で暗号化されるディスク暗号化シークレット」セクションで生成されたものです。
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
