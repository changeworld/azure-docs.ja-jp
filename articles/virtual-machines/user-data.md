---
title: Azure 仮想マシンのユーザー データ
description: お客様がプロビジョニング時にスクリプトまたは他のメタデータを Azure 仮想マシンに挿入できるようにします。
services: virtual-machines-linux
author: ningk
manager: rogardle
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/30/2021
ms.author: ningk
ms.reviewer: azmetadata
ms.openlocfilehash: d149ead5cd45b55c846852f92342e6bbc56ba3cf
ms.sourcegitcommit: 19dfdfa85e92c6a34933bdd54a7c94e8b00eacfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109665308"
---
# <a name="user-data-for-azure-virtual-machine"></a>Azure 仮想マシンのユーザー データ 

ユーザー データを使用すると、独自のスクリプトまたはメタデータを仮想マシンに渡すことができます。

## <a name="what-is-user-data"></a>"ユーザー データ" とは

ユーザー データは、プロビジョニング時に Azure 仮想マシンに挿入されるスクリプトまたは他のメタデータのセットです。 仮想マシン上のどのアプリケーションも、プロビジョニング後に Azure Instance Metadata Service (IMDS) からユーザー データにアクセスできます。 

ユーザー データは、[カスタム データ](./custom-data.md)の新しいバージョンであり、次のようなさらなる利点があります。

* ユーザー データは、プロビジョニング後に Azure Instance Metadata Service (IMDS) から取得できます。

* ユーザー データは永続的です。 これは VM の有効期間中に使用できます。

* VM を停止または再起動することなく、VM の外部からユーザー データを更新できます。

* ユーザー データのクエリは、$expand オプション付きの GET VM/VMSS API を介して実行できます。

 さらに、ユーザー データがプロビジョニング時に追加されていない場合でも、プロビジョニング後に追加できます。

**セキュリティの警告**

> [!WARNING]
> ユーザー データは暗号化されないため、VM 上の任意のプロセスでこのデータのクエリを実行できます。 ユーザー データに機密情報を格納しないでください。

新しいユーザー データ機能を使用するには、必ず最新の Azure Resource Manager API を入手してください。 コンテンツは、API に渡される前に base64 でエンコードする必要があります。 そのサイズは 64 KB を超えることはできません。

## <a name="create-user-data-for-azure-vmvmss"></a>Azure VM または VMSS のユーザー データを作成する

**新しい VM の作成時のユーザー データの追加**

[こちらの Azure Resource Manager テンプレート](https://aka.ms/ImdsUserDataArmTemplate)を使用して、ユーザー データを含む新しい VM を作成します。
REST API を使用する場合、単一 VM に対しては、VM を作成する PUT 要求と共に "properties" セクションに "UserData" を追加します。

```json
{
  "name": "testVM",
  "location": "West US",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_A1"
    },
    "storageProfile": {
      "osDisk": {
        "osType": "Windows",
        "name": "osDisk",
        "createOption": "Attach",
        "vhd": {
          "uri": "http://myaccount.blob.core.windows.net/container/directory/blob.vhd"
        }
      }
    },
    "userData": "c2FtcGxlIHVzZXJEYXRh",
    "networkProfile": { "networkInterfaces" : [ { "name" : "nic1" } ] },
  }
}
```

**新しい仮想マシン スケール セットを作成する際のユーザー データの追加**

REST API を使用して、仮想マシン スケール セットの作成時に PUT 要求と共に "virtualMachineProfile" セクションに "UserData" を追加します。
```json
{
  "location": "West US",
  "sku": {
    "name": "Standard_A1",
    "capacity": 1
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "userData": "VXNlckRhdGE=",
      "osProfile": {
        "computerNamePrefix": "TestVM",
        "adminUsername": "TestUserName",
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "timeZone": "Dateline Standard Time"
        }
      },
      "storageProfile": {
        "osDisk": {
          "createOption": "FromImage",
          "caching": "ReadOnly"
        },
        "imageReference": {
          "publisher": "publisher",
          "offer": "offer",
          "sku": "sku",
          "version": "1.2.3"
        }
      },
      "networkProfile": {"networkInterfaceConfigurations":[{"name":"nicconfig1","properties":{"ipConfigurations":[{"name":"ip1","properties":{"subnet":{"id":"vmssSubnet0"}}}]}}]},
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "https://crputest.blob.core.windows.net"
        }
      }
    },
    "provisioningState": 0,
    "overprovision": false,
    "uniqueId": "00000000-0000-0000-0000-000000000000"
  }
}
```


## <a name="retrieving-user-data"></a>ユーザー データの取得

VM 内で実行されているアプリケーションは、IMDS エンドポイントからユーザー データを取得できます。 詳細については、[こちらの IMDS サンプル コード](./linux/instance-metadata-service.md?tabs=linux#get-user-data)を参照してください。

お客様は、\$expand=userData エンドポイントを使用して REST API を介してユーザー データの既存の値を取得できます (要求本文は空のままでもかまいません)。

単一 VM:

`GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachines/{VMName}?$expand=userData"`

仮想マシン スケール セット:

`GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMSSName}?$expand=userData"`

仮想マシン スケール セット VM:

` GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMSSName}/virtualmachines/{vmss instance id}?$expand=userData" `

## <a name="updating-user-data"></a>ユーザー データの更新

REST API を使用すると、通常の PUT または PATCH 要求を使用してユーザー データを更新できます。 ユーザー データは、VM を停止または再起動しなくても更新されます。

`PUT
"/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/ virtualMachines/{VMName}
`

`PATCH
"/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/ virtualMachines/{VMName}
`

これらの要求の VM.Properties には、次のように目的の UserData フィールドが含まれている必要があります。

```json
"properties": {
        "hardwareProfile": {
          "vmSize": "Standard_D1_v2"
        },
        "storageProfile": {
          "imageReference": {
            "sku": "2016-Datacenter",
            "publisher": "MicrosoftWindowsServer",
            "version": "latest",
            "offer": "WindowsServer"
          },
          "osDisk": {
            "caching": "ReadWrite",
            "managedDisk": {
              "storageAccountType": "Standard_LRS"
            },
            "name": "vmOSdisk",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
              "properties": {
                "primary": true
              }
            }
          ]
        },
        "osProfile": {
          "adminUsername": "{your-username}",
          "computerName": "{vm-name}",
          "adminPassword": "{your-password}"
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
            "storageUri": "http://{existing-storage-account-name}.blob.core.windows.net",
            "enabled": true
          }
        },
        "userData": "U29tZSBDdXN0b20gRGF0YQ=="
      } 
```
> [!NOTE]
> このケースで "userData" に空の文字列を渡すと、ユーザー データは削除されます。

## <a name="user-data-and-custom-data"></a>ユーザー データとカスタム データ

カスタム データは、現在と同じように引き続き機能します。 IMDS からカスタム データを取得できないことに注意してください。 

## <a name="adding-user-data-to-an-existing-vm"></a>既存の VM へのユーザー データの追加 

既存の VM または VMSS にユーザー データがない場合でも、「[ユーザー データの更新](#updating-user-data)」セクションで説明されているように、更新コマンドを使用して、この VM にユーザー データを追加できます。 必ず最新バージョンの Azure Resource Manager API にアップグレードしてください。

## <a name="next-steps"></a>次のステップ 
 
[Azure Instance Metadata Service](./linux/instance-metadata-service.md) を試し、そのエンドポイントから VM インスタンスのメタデータとユーザー データを取得する方法について確認します。 
