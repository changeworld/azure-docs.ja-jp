---
title: プライマリ ノード タイプの SKU を上位の SKU にアップグレード/移行する手順 | Microsoft Docs
description: PowerShell コマンドと CLI コマンドを使用してプライマリ ノード タイプの SKU を上位の SKU にアップグレード/移行する方法について説明します。
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 96956543a44b6d5d967e3bae3fd833b08baf3d6f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642735"
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>プライマリ ノード タイプの SKU を上位の SKU にアップグレード/移行する

この記事では、Azure PowerShell を使用して Service Fabric クラスターのプライマリ ノード タイプの SKU を上位の SKU にアップグレードまたは移行する方法について説明します

## <a name="add-a-new-virtual-machine-scale-set"></a>新しい仮想マシン スケール セットの追加

新しい仮想マシン スケール セットとロード バランサーをデプロイします。 新しい仮想マシン スケール セットの Service Fabric 拡張機能の構成 (特にノードの種類) は、アップグレードしようとしている以前のスケール セットと同じである必要があります。 Service Fabric エクスプローラーで、新しいノードを利用できることを確認します。

#### <a name="azure-powershell"></a>Azure PowerShell

次の例では、Azure PowerShell を使用して、更新されたリソース マネージャー テンプレート *template.json* を、*myResourceGroup* という名前のリソース グループを使用してデプロイします。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile template.json -TemplateParameterFile parameters.json
```

#### <a name="azure-cli"></a>Azure CLI

次のコマンドでは、Azure Service Fabric CLI を使用して、更新されたリソース マネージャー テンプレート *template.json* を、*myResourceGroup* という名前のリソース グループを使用してデプロイします。

```CLI
az group deployment create --resource-group myResourceGroup --template-file template.json --parameters parameters.json
```

次の例を参照して json テンプレートを変更し、既存のクラスターにプライマリ ノード タイプの新しい仮想マシン スケール セット リソースを追加します

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
                                "properties": {
                                    "type": "IaaSDiagnostics",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                        "storageAccountEndPoint": "https://core.windows.net/"
                                    },
                                    "publisher": "Microsoft.Azure.Diagnostics",
                                    "settings": {
                                        "WadCfg": {
                                            "DiagnosticMonitorConfiguration": {
                                                "overallQuotaInMB": "50000",
                                                "EtwProviders": {
                                                    "EtwEventSourceProviderConfiguration": [
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Actors",
                                                            "scheduledTransferKeywordFilter": "1",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableActorEventTable"
                                                            }
                                                        },
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Services",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                            }
                                                        }
                                                    ],
                                                    "EtwManifestProviderConfiguration": [
                                                        {
                                                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                            "scheduledTransferLogLevelFilter": "Information",
                                                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricSystemEventTable"
                                                            }
                                                        }
                                                    ]
                                                }
                                            }
                                        },
                                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                                    },
                                    "typeHandlerVersion": "1.5"
                                }
                            }
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
                                                }
                                            }
                                        }
                                    ],
                                    "primary": true
                                }
                            }
                        ]
                    },
                    "osProfile": {
                        "adminPassword": "[parameters('adminPassword')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[parameters('sourceVaultValue')]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateStore": "[parameters('certificateStoreValue')]",
                                        "certificateUrl": "[parameters('certificateUrlValue')]"
                                    }
                                ]
                            }
                        ]
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[parameters('vmImagePublisher')]",
                            "offer": "[parameters('vmImageOffer')]",
                            "sku": "[parameters('vmImageSku')]",
                            "version": "[parameters('vmImageVersion')]"
                        },
                        "osDisk": {
                            "caching": "ReadOnly",
                            "createOption": "FromImage",
                            "managedDisk": {
                                "storageAccountType": "[parameters('storageAccountType')]"
                            }
                        }
                    }
                }
            },
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```

## <a name="remove-old-virtual-machine-scale-set"></a>以前の仮想マシン スケール セットの削除

1. ノードを削除しようとしている以前の仮想マシン スケール セットの VM インスタンスを無効にします。 この操作が完了するまでに時間がかかる場合があります。 一度にすべてを無効にすることができ、これらはキューに置かれます。 すべてのノードが無効になるまで待ちます。 
#### <a name="azure-powershell"></a>Azure PowerShell
次の例では、Azure Service Fabric PowerShell を使用して、*NTvm1* という名前の以前の仮想マシン スケール セットから *NTvm1_0* という名前のノード インスタンスを無効にします。
```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode
```
#### <a name="azure-cli"></a>Azure CLI
次のコマンドでは、Azure Service Fabric CLI を使用して、*NTvm1* という名前の以前の仮想マシン スケール セットから *NTvm1_0* という名前のノード インスタンスを無効にします。
```CLI
sfctl node disable --node-name "_NTvm1_0" --deactivation-intent RemoveNode
```
2. スケール セット全体を削除します。 スケール セットのプロビジョニングの状態が正常になるまで待ちます
#### <a name="azure-powershell"></a>Azure PowerShell
次の例では、Azure PowerShell を使用して、*myResourceGroup* という名前のリソース グループから *NTvm1* という名前のスケール セット全体を削除します。
```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```
#### <a name="azure-cli"></a>Azure CLI
次のコマンドでは、Azure Service Fabric CLI を使用して、*myResourceGroup* という名前のリソース グループから *NTvm1* という名前のスケール セット全体を削除します。

```CLI
az vmss delete --name NTvm1 --resource-group myResourceGroup
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>以前のスケール セットに関連するロード バランサーの削除

以前のスケール セットに関連するロード バランサーを削除します。 この手順により、クラスターのダウンタイムが短期間発生します

#### <a name="azure-powershell"></a>Azure PowerShell

次の例では、Azure PowerShell を使用して、*myResourceGroup* という名前のリソース グループの以前のスケール セットに関連する *LB-myCluster-NTvm1* という名前のロード バランサーを削除します。

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

次のコマンドでは、Azure Service Fabric CLI を使用して、*myResourceGroup* という名前のリソース グループの以前のスケール セットに関連する *LB-myCluster-NTvm1* という名前のロード バランサーを削除します。

```CLI
az network lb delete --name LB-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>以前のスケール セットに関連するパブリック IP の削除

以前のスケール セットに関連するパブリック IP アドレスの DNS 設定を保存し、パブリック IP アドレスを削除します

#### <a name="azure-powershell"></a>Azure PowerShell

次の例では、Azure PowerShell を使用して、*LBIP myCluster-NTvm1* という名前のパブリック IP アドレスの DNS 設定を変数に保存し、IP アドレスを削除します。

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

次のコマンドでは、Azure Service Fabric CLI を使用して、*LBIP myCluster-NTvm1* という名前のパブリック IP アドレスの DNS 設定を取得し、IP アドレスを削除します。

```CLI
az network public-ip show --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
az network public-ip delete --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>新しいスケール セットに関連するパブリック IP アドレスの更新

新しいスケール セットに関連するパブリック IP アドレスの DNS 設定を以前のスケール セットに関連するパブリック IP アドレスの DNS 設定で更新します

#### <a name="azure-powershell"></a>Azure PowerShell
次の例では、Azure PowerShell を使用して、*LBIP-myCluster-NTvm3* という名前のパブリック IP アドレスを前の手順で変数に保存した DNS 設定で更新します。

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm3  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP
```

#### <a name="azure-cli"></a>Azure CLI

次のコマンドでは、Azure Service Fabric CLI を使用して、*LBIP-myCluster-NTvm3* という名前のパブリック IP アドレスを前の手順で収集した古いパブリック IP の DNS 設定で更新します。

```CLI
az network public-ip update --name LBIP-myCluster-NTvm3 --resource-group myResourceGroup --dns-name myCluster
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>FM からの Service Fabric ノードの知識の除去

ダウンしているノードがクラスターから削除されたことを Service Fabric に通知します。 (このコマンドは以前の仮想マシン スケール セットのすべての VM インスタンスに対して実行します) (以前の仮想マシン スケール セットの耐久性レベルが Silver または Gold の場合、この手順は必要ありません。 この手順はシステムによって自動的に行われます。)

#### <a name="azure-powershell"></a>Azure PowerShell
次の例では、Azure Service Fabric PowerShell を使用して、*NTvm1_0* という名前のノードが削除されたことを Service Fabric に通知します。

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```

#### <a name="azure-cli"></a>Azure CLI

次のコマンドでは、Azure Service Fabric CLI を使用して、*NTvm1_0* という名前のノードが削除されたことを Service Fabric に通知します。

```CLI
sfctl node remove-state --node-name _NTvm1_0
```
