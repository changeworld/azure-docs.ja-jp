---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 04/30/2018
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 4fe1f2ad4bad9d670094bbb4eed188baf28108ea
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2018
---
# <a name="write-accelerator"></a>書き込みアクセラレータ
書き込みアクセラレータは、専用の Azure Managed Disks がある Premium Storage 上の M シリーズ仮想マシン (VM) 用のディスク機能です。 名前が示すように、この機能の目的は、Azure Premium Storage に対する書き込みの I/O 待機時間を短縮することです。 書き込みアクセラレータは、最新のデータベース用のパフォーマンスの高い方法でログ ファイルの更新をディスクに永続化する必要がある場合に最適です。

書き込みアクセラレータは、パブリック クラウド内の M シリーズ VM に一般提供されています。

## <a name="planning-for-using-write-accelerator"></a>書き込みアクセラレータを使用するための計画
書き込みアクセラレータは、DBMS のトランザクション ログまたは再実行ログを含むボリュームに対して使う必要があります。 書き込みアクセラレータはログ ディスクに使用するように最適化されているので、DBMS のデータ ボリュームに使用することはお勧めしません。

書き込みアクセラレータは、[Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) との連携でのみ動作します。 


> [!IMPORTANT]
> 複数の Azure Premium Storage ディスクで構成され、Windows のディスクまたはボリューム マネージャー、Windows 記憶域スペース、Windows スケールアウト ファイル サーバー (SOFS)、Linux LVM、または MDADM を使ってストライピングされている既存のボリュームで、書き込みアクセラレータを有効または無効にする場合は、ボリュームを構成するすべてのディスクに対し、異なる手順で書き込みアクセラレータを有効または無効にする必要があります。 **そのような構成で書き込みアクセラレータを有効または無効にする前に、Azure VM をシャットダウンしてください**。 


> [!IMPORTANT]
> Windows のディスクまたはボリューム マネージャー、Windows 記憶域スペース、Windows スケールアウト ファイル サーバー (SOFS)、Linux LVM、または MDADM を使って複数のディスクで構成されるボリュームの一部ではない既存の Azure ディスクに対して書き込みアクセラレータを有効にするには、Azure ディスクにアクセスしているワークロードをシャットダウンする必要があります。 Azure ディスクを使っているデータベース アプリケーションをシャットダウンをする必要があります。

> [!IMPORTANT]
> VM のオペレーティング システム ディスクで書き込みアクセラレータを有効にすると、VM が再起動します。 

SAP 関連の VM 構成では、OS ディスクで書き込みアクセラレータを有効にする必要はありません

### <a name="restrictions-when-using-write-accelerator"></a>書き込みアクセラレータを使用するときの制限事項
Azure ディスク/VHD で書き込みアクセラレータを使うときは、次の制限が適用されます。

- Premium ディスクのキャッシュを 'None' または 'Read Only' に設定する必要があります。 他のすべてのキャッシュ モードはサポートされていません。
- 書き込みアクセラレータを有効にしたディスクでのスナップショットは、まだサポートされていません。 この制限により、仮想マシンのすべてのディスクのアプリケーション整合スナップショットを実行する Azure Backup サービスの機能はブロックされます。
- 高速化パスは、I/O サイズが小さい場合 (<=32KiB) にのみ使用されます。 データが一括で読み込まれたり、ストレージに保存される前に複数の DBMS のトランザクション ログ バッファの大部分が入力されるようなワークロードの状況では、ディクスに書き込まれる I/O で高速化パスが使用される機会はありません。

書き込みアクセラレータでサポートできる VM ごとの Azure Premium Storage VHD には制限があります。 現在の制限は次のとおりです。

| VM の SKU | 書き込みアクセラレータ ディスクの数 | VM あたりの書き込みアクセラレータ ディスクの IOPS |
| --- | --- | --- |
| M128ms | 16 | 8000 |
| M128s | 16 | 8000 |
| M64ms | 8 | 4000 |
| M64s | 8 | 4000 | 

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>特定のディスクでの書き込みアクセラレータの有効化
以降のセクションでは、Azure Premium Storage の VHD で書き込みアクセラレータを有効にする方法を示します。


### <a name="prerequisites"></a>前提条件
現時点では、書き込みアクセラレータの使用には次の前提条件が適用されます。

- Azure 書き込みアクセラレータを適用するディスクは、Premium Storage 上の [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) である必要があります。
- M シリーズの VM を使用する必要があります

### <a name="enabling-through-power-shell"></a>PowerShell で有効にする
バージョン 5.5.0 の Azure PowerShell モジュールには、特定の Azure Premium Storage ディスクに対する書き込みアクセラレータの有効化または無効化に関連するコマンドレットの変更が含まれます。
書き込みアクセラレータによってサポートされるディスクを有効化または展開するため、次の PowerShell コマンドが変更され、書き込みアクセラレータのパラメーターを受け取るように拡張されています。

新しいスイッチ パラメーター "WriteAccelerator" が、次のコマンドレットに追加されています。 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

パラメーターを指定しないと、プロパティは false に設定され、書き込みアクセラレータによってサポートされていないディスクが展開されます。

新しいスイッチ パラメーター "OsDiskWriteAccelerator" が、次のコマンドレットに追加されています。 

- Set-AzureRmVmssStorageProfile

パラメーターを指定しないと、プロパティは false に設定され、書き込みアクセラレータを利用しないディスクが提供されます。

新しい省略可能なブール値 (null 非許容) パラメーター "OsDiskWriteAccelerator" が、次のコマンドレットに追加されています。 

- Update-AzureRmVM
- Update-AzureRmVmss

ディスクでの Azure 書き込みアクセラレータのサポートを制御するには、$true または $false を指定します。

コマンドの例を次に示します。

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

以下のセクションで示すように、2 つの主要なシナリオをスクリプト化できます。

#### <a name="adding-a-new-disk-supported-by-write-accelerator"></a>書き込みアクセラレータでサポートされる新しいディスクを追加する
このスクリプトを使うと、新しいディスクを VM に追加することができます。 このスクリプトで作成されるディスクは、書き込みアクセラレータを使うようになります。

```

# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
特定の展開に合わせて、VM、ディスク、リソース グループの各名前と、ディスクのサイズおよび LunID を、調整する必要があります。


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>既存の Azure ディスクで Azure 書き込みアクセラレータを有効にする
既存のディスクで書き込みアクセラレータを有効にする必要がある場合は、次のスクリプトを使ってタスクを実行できます。

```

#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

VM、ディスク、リソース グループの各名前を調整する必要があります。 上記のスクリプトでは、$newstatus の値を "$true" に設定して、既存のディスクに書き込みアクセラレータを追加します。 値 "$false" を使うと、特定のディスクの書き込みアクセラレータが無効になります。

> [!Note]
> 上記のスクリプトを実行すると、指定されているディスクがデタッチされ、ディスクに対して書き込みアクセラレータが有効になり、再度ディスクがアタッチされます。

### <a name="enabling-through-azure-portal"></a>Azure Portal を使用して有効にする

ディスク キャッシュ設定を指定するポータルを使用して書き込みアクセラレータを有効にすることができます。 

![Azure Portal 上の書き込みアクセラレータ](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)


### <a name="enabling-through-rest-apis"></a>REST API で有効にする
Azure REST API を使って展開するには、Azure armclient をインストールする必要があります。

#### <a name="install-armclient"></a>armclient をインストールする

armclient を実行するには、Chocolatey を使ってインストールする必要があります。 cmd.exe または PowerShell でインストールできます。 これらのコマンドは、[管理者として実行] を使って管理者特権で実行します。

cmd.exe を使って、次のコマンドを実行します。

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

PowerShell では次のようにします。

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

cmd.exe または PowerShell で次のコマンドを使って armclient をインストールできるようになります

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>現在の VM 構成を取得する
ディスク構成の属性を変更するには、最初に、JSON ファイルの現在の構成を取得する必要があります。 現在の構成は、次のコマンドを実行することによって取得できます。

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
JSON ファイルのファイル名など、"<<   >>" 内のテキストを実際のデータに置き換えます。

出力は次のようになります。

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

次に、JSON ファイルを更新して、"log1" という名前のディスクで書き込みアクセラレータを有効にします。 この手順は、JSON ファイルでディスクのキャッシュ エントリの後に次の属性を追加することによって実行できます。 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

その後、このコマンドで既存の展開を更新します。

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

出力は次のようになります。 1 つのディスクで書き込みアクセラレータが有効になっていることがわかります。

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

変更すると、それ以降、ドライブは書き込みアクセラレータでサポートされるようになります。

 
