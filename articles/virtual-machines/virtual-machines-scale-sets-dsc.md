<properties
   pageTitle="Desired State Configuration と仮想マシン スケール セットの使用 | Microsoft Azure"
   description="仮想マシン スケール セットと Azure DSC 拡張機能の使用"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="zachal"/>

# 仮想マシン スケール セットと Azure DSC 拡張機能の使用

[仮想マシンのスケール セット (VMSS)](virtual-machines-windows-vmss-powershell-creating.md) は、[Azure Desired State Configuration (DSC)](virtual-machines-windows-extensions-dsc-overview.md) 拡張機能ハンドラーと共に使用できます。VMSS は、負荷を満たすために仮想マシンのデプロイを構成するときに使用されます。DSC は、オンラインで運用ソフトウェアを実行できるように VM を構成するときに使用されます。

## VM へのデプロイと VMSS へのデプロイの違い

VMSS の基本テンプレートの構造は、1 つの VM とは少し異なります。具体的には、1 つの VM では、"virtualMachines" ノードに拡張機能がデプロイされます。エントリ タイプ "extensions" があり、ここで DSC がテンプレートに追加されます

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

VMSS ノードには、"VirtualMachineProfile" を備えた "properties" セクション、"extensionProfile" 属性があります。DSC は "extensions" の下に追加します

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.9",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## VMSS の動作

VMSS の動作は、1 つの VM の動作と同じです。新しく作成された VM は、DSC 拡張機能で自動的にプロビジョニングされます。拡張機能に新しいバージョンの WMF が必要な場合、VM はオンラインになる前に再起動されます。オンラインになると、DSC 構成 .zip がダウンロードされ、VM 上でプロビジョニングされます。詳細については、[Azure DSC 拡張機能の概要](virtual-machines-windows-extensions-dsc-overview.md)に関するページをご覧ください。

<!---HONumber=AcomDC_0914_2016-->