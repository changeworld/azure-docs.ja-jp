---
title: Linux の暗号化の状態を確認する方法
description: この記事では、プラットフォームおよび OS レベルの暗号化の状態を確認する手順について説明します。
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123419"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Linux の暗号化の状態を確認する方法 

**このシナリオは、ADE のデュアルパス拡張機能とシングルパス拡張機能を対象としています。**  
このドキュメントのスコープは、各種の方法を使用して仮想マシンの暗号化状態を確認することです。

### <a name="environment"></a>環境

- Linux ディストリビューション

### <a name="procedure"></a>手順

仮想マシンは、デュアルパスまたはシングルパスを使用して暗号化されています。

暗号化の状態は、暗号化中または暗号化後にさまざまな方法を使用して確認することができます。

>[!NOTE] 
>このドキュメントでは、いたるところで変数が使用されています。適宜、実際の値に置き換えてください。

### <a name="verification"></a>検証

確認作業は、ポータル、PowerShell、AZ CLI から行えるほか、VM OS 側から行うこともできます。 

この確認は、特定の VM にアタッチされたディスクをチェックすることで実行できます。 

また、ディスクがアタッチされているかどうかに関係なく、各ディスクについて個別に暗号化の設定を照会する方法もあります。

以降、各種の確認方法を見ていきましょう。

## <a name="using-the-portal"></a>ポータルの使用

Azure portal の拡張機能セクションをチェックして暗号化の状態を確認します。

**[拡張機能]** セクションには、ADE 拡張機能が一覧表示されます。 

それをクリックして **[status message]\(ステータス メッセージ\)** を見ると、現在の暗号化の状態がわかります。

![ポータルのチェック (1)](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

拡張機能の一覧には、対応する ADE 拡張機能のバージョンが表示されます。 バージョン 0.x は ADE デュアルパスに、バージョン 1.x は ADE シングルパスに対応します。

拡張機能をクリックし、 *[詳細な状態の表示]* をクリックすると、さらに詳しい情報が得られます。

暗号化プロセスの詳しい状態は、JSON 形式で表示されます。

![ポータルのチェック (2)](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![ポータルのチェック (3)](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

暗号化の状態は、 **[ディスク]** セクションを見て確認することもできます。

![ポータルのチェック (4)](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> この状態は、ディスクに暗号化の設定がスタンプされていることを意味するものであって、実際に OS レベルで暗号化されているという意味ではありません。 設計上、ディスクは最初にスタンプされ、後から暗号化されます。 暗号化プロセスに失敗した場合、ディスクはスタンプされているものの暗号化されていない状態になります。 実際にディスクが暗号化されているかどうかを確かめるために、OS レベルで各ディスクの暗号化をダブルチェックしてください。

## <a name="using-powershell"></a>PowerShell の使用

暗号化された VM の**全体的**な暗号化の状態は、次の PowerShell コマンドを使用して確認できます。

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![PowerShell でのチェック (1)](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

暗号化の設定を各ディスクから個別にキャプチャするには、次の PowerShell コマンドを使用します。

### <a name="single-pass"></a>シングルパス
シングルパスの場合、暗号化の設定は各ディスク (OS とデータ) にスタンプされます。シングルパスでの OS ディスクの暗号化設定は、次のようにしてキャプチャできます。

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![OS の確認 (シングル パス 01)](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

ディスクに暗号化の設定がスタンプされていない場合、次のように出力は空になります。

![OS の暗号化の設定 (2)](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

データ ディスクの暗号化の設定は、次のようにしてキャプチャします。

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![データの確認 (シングル パス 001)](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>デュアルパス
デュアル パスでは、暗号化の設定が、個々のディスクではなく VM モデルにスタンプされます。

暗号化の設定がデュアルパスでスタンプされていることを確認するには、次のコマンドを使用します。

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![デュアル パスの確認 (PowerShell 1)](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>アタッチされていないディスク

VM にアタッチされていないディスクの暗号化の設定を確認します。

### <a name="managed-disks"></a>マネージド ディスク
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="using-az-cli"></a>AZ CLI の使用

暗号化された VM の**全体的**な暗号化の状態は、次の AZ CLI コマンドを使用して確認できます。

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![全体確認 (CLI 使用) ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>シングル パス
暗号化の設定を各ディスクから個別に確認するには、次の AZ CLI コマンドを使用します。

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![データ暗号化設定](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> ディスクに暗号化の設定がスタンプされていない場合、"Disk is not encrypted" (ディスクは暗号化されていません) と表示されます。

詳細な状態と暗号化の設定は次のとおりです。

OS ディスク:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

データ ディスク:

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![データ シングル CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>デュアル パス

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![全体確認 (デュアル、CLI 使用)](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) 暗号化の設定は、OS ディスクの VM モデル ストレージ プロファイルで確認することもできます。

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![VM プロファイルの確認 (デュアル、CLI 使用) ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>アタッチされていないディスク

VM にアタッチされていないディスクの暗号化の設定を確認します。

### <a name="managed-disks"></a>マネージド ディスク

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>非管理対象ディスク

アンマネージド ディスクは、Azure Storage アカウントでページ BLOB として格納された VHD ファイルです。

特定のディスクの詳細を把握するには、次の情報を指定する必要があります。

ディスクが含まれるストレージ アカウントの ID。
対象となる特定のストレージ アカウントの接続文字列。
ディスクを格納するコンテナーの名前。
ディスク名。

次のコマンドは、すべてのストレージ アカウントの ID を一覧表示するものです。

```bash
az storage account list --query [].[id] -o tsv
```
ストレージ アカウントの ID が、次の形式で一覧表示されます。

/subscriptions/\<サブスクリプション ID>/resourceGroups/\<リソース グループ名>/providers/Microsoft.Storage/storageAccounts/\<ストレージ アカウント名>

適切な ID を選択して変数に格納します。
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
接続文字列です。

次のコマンドは、ある特定のストレージ アカウントの接続文字列を取得して変数に格納するものです。

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

コンテナーの名前。

次のコマンドは、ストレージ アカウントにあるすべてのコンテナーを一覧表示するものです。
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
ディスクに使用されるコンテナーには通常、"vhds" という名前が付けられます。

そのコンテナー名を変数に格納します。 
```bash
ContainerName="name of the container"
```

ディスク名。

特定のコンテナーにあるすべての BLOB を一覧表示するには、次のコマンドを使用します。
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
照会したいディスクを選んでその名前を変数に格納します。
```bash
DiskName="diskname.vhd"
```
ディスク暗号化の設定を照会します。
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>OS から
データ ディスクのパーティションが暗号化されている (なおかつ OS ディスクが暗号化されていない) かどうかを確認します。

暗号化されているパーティションまたはディスクは、**crypt** タイプとして表示されます。暗号化されていないパーティションまたはディスクは、**part または disk** タイプとして表示されます。

``` bash
lsblk
```

![OS Crypt レイヤー ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

以下の "lsblk" バリアントを使用すれば、さらに詳しい情報を取得できます。 

拡張機能によってマウントされた **crypt** タイプ レイヤーが表示されます。

"**crypto\_LUKS FSTYPE**" の通常のディスクと論理ボリュームの例を次に示します。

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![OS Crypt レイヤー (2)](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

さらに、データ ディスクにキーが読み込まれているかどうかを確認することもできます。

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

どの dm デバイスが crypt として表示されるかを調べることもできます。

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>次の手順

- [Azure Disk Encryption のトラブルシューティング](disk-encryption-troubleshooting.md)
