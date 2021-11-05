---
title: ARM テンプレートによりプライベート リンクを使用して Media Services とストレージ アカウントを作成する
titleSuffix: Azure Media Services
description: VNet へのプライベート リンクを使用して、Media Services アカウントとストレージ アカウントを作成します。 Azure Resource Manager (ARM) テンプレートでは、両方のプライベート リンクの DNS も設定します。 最後に、テンプレートにより VM が作成され、ユーザーがプライベート リンクを試すことができます。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 6182fd0772d4e669a42f2895518139ab2748ae61
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091255"
---
# <a name="create-a-media-services-and-storage-account-with-a-private-link-using-an-arm-template"></a>ARM テンプレートによりプライベート リンクを使用して Media Services とストレージ アカウントを作成する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

VNet へのプライベート リンクを使用して、Media Services アカウントとストレージ アカウントを作成します。 Azure Resource Manager (ARM) テンプレートでは、両方のプライベート リンクの DNS も設定します。 最後に、テンプレートにより VM が作成され、ユーザーがプライベート リンクを試すことができます。

## <a name="prerequisites"></a>前提条件

[クイック スタート:Azure portal を使用して ARM テンプレートを作成およびデプロイする](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)を確認する。

## <a name="limitations"></a>制限事項

- Media Services の場合、テンプレートはキー配信用の Private Link のみを設定します。
- VM に対してネットワーク セキュリティ グループは作成されません。
- ストレージ アカウントまたはキーの配信用にネットワーク アクセスの制御は構成されません。

テンプレートによって以下が作成されます。

- Media Services アカウントとストレージ アカウント (通常どおり)
- サブネットを持つ VNet
- Media Services アカウントとストレージ アカウントの両方に対して以下が作成されます。
  - プライベート エンドポイント
  - プライベート DNS ゾーン
  - リンク間のリンク (プライベート DNS ゾーンを VNet に接続するため)
  - プライベート DNS ゾーン グループ (プライベート DNS ゾーンで DNS レコードの自動作成をトリガーするため)
- VM (パブリック IP アドレスとネットワーク インターフェイスが関連付けられている)

[!INCLUDE [Azure Policy Media Services](includes/security-azure-policy-private-links.md)]

## <a name="azure-resource-manager-arm-template-for-private-link"></a>プライベート リンク用の Azure Resource Manager (ARM) テンプレート

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "vmAdminUsername": {
      "type": "string"
    },
    "vmAdminPassword": {
      "type": "secureString"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D2_v3"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "storageAccountName": {
      "type": "string"
    },
    "mediaServicesAccountName": {
      "type": "string"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-01-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
    },
    {
      "type": "Microsoft.Media/mediaservices",
      "apiVersion": "2020-05-01",
      "name": "[parameters('mediaServicesAccountName')]",
      "location": "[parameters('location')]",
      "properties": {
        "storageAccounts": [
          {
            "type": "Primary",
            "id": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
          }
        ]
      },
      "identity": {
        "type": "SystemAssigned"
      },
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-08-01",
      "name": "myVnet",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/16"
          ]
        },
        "subnets": [
          {
            "name": "mySubnet",
            "properties": {
              "addressPrefix": "10.0.0.0/24",
              "privateEndpointNetworkPolicies": "Disabled"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/privateEndpoints",
      "apiVersion": "2020-08-01",
      "name": "storagePrivateEndpoint",
      "location": "[parameters('location')]",
      "properties": {
        "subnet": {
          "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
        },
        "privateLinkServiceConnections": [
          {
            "name": "storagePrivateEndpointConnection",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
              "groupIds": [
                "blob"
              ]
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateDnsZones",
      "apiVersion": "2020-06-01",
      "name": "privatelink.blob.core.windows.net",
      "location": "global"
    },
    {
      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
      "apiVersion": "2020-06-01",
      "name": "[format('{0}/storageDnsZoneLink', 'privatelink.blob.core.windows.net')]",
      "location": "global",
      "properties": {
        "registrationEnabled": false,
        "virtualNetwork": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
      "apiVersion": "2020-08-01",
      "name": "[format('{0}/storagePrivateDnsZoneGroup', 'storagePrivateEndpoint')]",
      "properties": {
        "privateDnsZoneConfigs": [
          {
            "name": "config1",
            "properties": {
              "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]"
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]",
        "[resourceId('Microsoft.Network/privateEndpoints', 'storagePrivateEndpoint')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints",
      "apiVersion": "2020-08-01",
      "name": "mediaServicesPrivateEndpoint",
      "location": "[parameters('location')]",
      "properties": {
        "subnet": {
          "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
        },
        "privateLinkServiceConnections": [
          {
            "name": "mediaServicesPrivateEndpointConnection",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.Media/mediaservices', parameters('mediaServicesAccountName'))]",
              "groupIds": [
                "keydelivery"
              ]
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Media/mediaservices', parameters('mediaServicesAccountName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateDnsZones",
      "apiVersion": "2020-06-01",
      "name": "privatelink.media.azure.net",
      "location": "global"
    },
    {
      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
      "apiVersion": "2020-06-01",
      "name": "[format('{0}/mediaServicesDnsZoneLink', 'privatelink.media.azure.net')]",
      "location": "global",
      "properties": {
        "registrationEnabled": false,
        "virtualNetwork": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
      "apiVersion": "2020-08-01",
      "name": "[format('{0}/mediaServicesPrivateDnsZoneGroup', 'mediaServicesPrivateEndpoint')]",
      "properties": {
        "privateDnsZoneConfigs": [
          {
            "name": "config1",
            "properties": {
              "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]"
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]",
        "[resourceId('Microsoft.Network/privateEndpoints', 'mediaServicesPrivateEndpoint')]"
      ]
    },
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2020-08-01",
      "name": "publicIp",
      "location": "[parameters('location')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[toLower(parameters('vmName'))]"
        }
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2020-08-01",
      "name": "vmNetworkInterface",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipConfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'publicIp')]"
              },
              "subnet": {
                "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
              }
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/publicIPAddresses', 'publicIp')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-12-01",
      "name": "myVM",
      "location": "[parameters('location')]",
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[parameters('vmName')]",
          "adminUsername": "[parameters('vmAdminUsername')]",
          "adminPassword": "[parameters('vmAdminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "osDisk",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "managedDisk": {
              "storageAccountType": "Standard_LRS"
            },
            "diskSizeGB": 128
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', 'vmNetworkInterface')]"
            }
          ]
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkInterfaces', 'vmNetworkInterface')]"
      ]
    }
  ],
  "metadata": {
    "_generator": {
      "name": "bicep",
      "version": "0.3.126.58533",
      "templateHash": "2006367938138350540"
    }
  }
}
```