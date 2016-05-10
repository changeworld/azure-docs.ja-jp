<properties
	pageTitle="仮想マシン スケール セットの VM を管理する | Microsoft Azure"
	description="Azure PowerShell を利用し、仮想マシン スケール セットで仮想マシンを管理する"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# 仮想マシン スケール セットで仮想マシンを管理する

Azure PowerShell を使用すると、Microsoft Azure でリソースを管理する際に高い処理能力と柔軟性が得られます。この記事では、仮想マシン スケール セットで仮想マシン リソースを管理する方法を紹介します。

- [仮想マシン スケール セットに関する情報を表示する](#displayvm)
- [スケール セットで仮想マシンを起動する](#start)
- [スケール セットで仮想マシンを停止する](#stop)
- [スケール セットで仮想マシンを再起動する](#restart)
- [スケール セットから仮想マシンを削除する](#delete)

スケール セットで仮想マシンを管理するためのあらゆる作業で、管理するマシンのインスタンス ID が必要になります。[Azure リソース エクスプローラー](https://resources.azure.com)を利用し、スケール セットの仮想マシンのインスタンス ID を検索できます。リソース エクスプローラーを利用し、完了した作業の状態を確認することもできます。

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>仮想マシン スケール セットに関する情報を表示する

スケール セットに関する全般情報を取得できます。これは、インスタンス ビューとも呼ばれています。あるいは、セットのリソースに関する情報など、より具体的な情報を取得できます。

このコマンドで、*resource group name* を、仮想マシン スケール セットが含まれているリソース グループの名前で置き換え、*スケール セット名*を仮想マシン スケール セットの名前で置き換えます。それから、このコマンドを実行します。

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

次のような結果が返されます。

    Sku                      :  {
                                  "name": "Standard_A0",
                                  "tier": "Standard",
                                  "capacity": 4
                                }
    UpgradePolicy            :  {
                                  "mode": "Manual"
                                }
    VirtualMachineProfile    :  {
                                  "osProfile": {
                                    "computerNamePrefix": "myvmss1",
                                    "adminUserName": "user1",
                                    "adminPassword": null,
                                    "customData": null,
                                    "windowsConfiguration": {
                                      "provisionVMAgent": true,
                                      "enableAutomaticUpdates": true,
                                      "timeZone": null,
                                      "additionalUnattendContent": null,
                                      "winRM": null
                                    }
                                    "linuxConfiguration": null,
                                    "secrets": []
                                  },
                                  "storageProfile": {
                                    "imageReference": {
                                      "publisher": "MicrosoftWindowsServer",
                                      "offer": "WindowsServer",
                                      "sku": "2012-R2-Datacenter",
                                      "version": "latest"
                                    },
                                    "osDisk": {
                                      "name": "vmssosdisk",
                                      "caching": "ReadOnly",
                                      "createOption": "FromImage",
                                      "osType": null,
                                      "image": null,
                                      "vhdContainers": [
                                        "https://amyst1.blob.core.windows.net/vmss",
                                        "https://gmyst1.blob.core.windows.net/vmss",
                                        "https://mmyst1.blob.core.windows.net/vmss",
                                        "https://smyst1.blob.core.windows.net/vmss",
                                        "https://ymyst1.blob.core.windows.net/vmss"
                                      ]
                                    }
                                  },
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myresnc2",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "ip1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/myresvn1/subnets/myressn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/backendAddressPools/bepool1"
                                              }
                                            ],
                                            "properties.loadBalancerInboundNatPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/inboundNatPools/natpool1"
                                              }
                                            ],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": [
                                      {
                                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                                        "properties.publisher": "Microsoft.Azure.Diagnostics",
                                        "properties.type": "IaaSDiagnostics",
                                        "properties.typeHandlerVersion": "1.5",
                                        "properties.autoUpgradeMinorVersion": true,
                                        "properties.settings": {
                                          "xmlCfg": "{encoded configuration}",
                                          "storageAccount": "amyst1"
                                        },
                                        "properties.protectedSettings": null,
                                        "properties.provisioningState": null,
                                        "id": null
                                      }
                                    ]
                                  }
                                }
    ProvisioningState           : Succeeded
    Id                          : /subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                        : myvmss1
	Type                        : Microsoft.Compute/virtualMachineScaleSets
	Location                    : westus
	Tags.Count                  : 0
	Tags                        :

全般情報を取得するには、*resource group name* を、仮想マシン スケール セットが含まれているリソース グループの名前で置き換え、*スケール セット名*を仮想マシン スケール セットの名前で置き換えます。それから、このコマンドを実行します。

	Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

次のような結果が返されます。

    VirtualMachine   :  {
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
    Extensions.Count :  1
    Extensions       :  {
                          "name": "Microsoft.Insights.VMDiagnosticsSettings",
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
	Statuses.Count   :  1
	Statuses         :  {
                          "code": "ProvisioningState/succeeded",
                          "level": "Info",
                          "displayStatus": "Provisioning succeeded",
                          "message": null,
                          "time": "2016-03-14T20:29:37.170809Z"
                        }

## <a id="start"></a>スケール セットで仮想マシンを起動する

このコマンドで、仮想マシン スケール セットが含まれているリソース グループの名前で *resource group name* を置き換え、スケール セットの名前で *VM スケール セット名*を置き換え、再起動する仮想マシンの ID で*インスタンス ID* を置き換えます。それから、このコマンドを実行します。

    Start-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

リソース エクスプローラーで、インスタンスの状態が**実行中**であることがわかります。

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

## <a id="stop"></a>スケール セットで仮想マシンを停止する

このコマンドで、仮想マシン スケール セットが含まれているリソース グループの名前で *resource group name* を置き換え、スケール セットの名前で*スケール セット名*を置き換え、停止する仮想マシンの ID で*インスタンス ID* を置き換えます。それから、このコマンドを実行します。

	Stop-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

リソース エクスプローラーで、インスタンスの状態が**割り当て解除**であることがわかります。

	"statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

## <a id="restart"></a>スケール セットで仮想マシンを再起動する

このコマンドで、仮想マシン スケール セットが含まれているリソース グループの名前で *resource group name* を置き換え、スケール セットの名前で*スケール セット名*を置き換え、再起動する仮想マシンの ID で*インスタンス ID* を置き換えます。それから、このコマンドを実行します。

	Restart-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

## <a id="delete"></a>スケール セットから仮想マシンを削除する

このコマンドで、仮想マシン スケール セットが含まれているリソース グループの名前で *resource group name* を置き換え、スケール セットの名前で*スケール セット名*を置き換え、スケール セットから削除する仮想マシンの ID で*インスタンス ID* を置き換えます。それから、このコマンドを実行します。

	Remove-AzureRmVmssVM -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId "instance id"

<!---HONumber=AcomDC_0427_2016-->