---
title: Azure クラウド サービス (延長サポート) をデプロイする - テンプレート
description: ARM テンプレートを使用して Azure クラウド サービス (延長サポート) をデプロイする
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8804febe81afc79a4a7eadb56e8350e758ea38ba
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105512"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>ARM テンプレートを使用してクラウド サービス (延長サポート) をデプロイする

このチュートリアルでは、[ARM テンプレート](../azure-resource-manager/templates/overview.md)を使ってクラウド サービス (延長サポート) のデプロイを作成する方法を説明します。 

## <a name="before-you-begin"></a>始める前に

1. クラウド サービス (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認し、関連するリソースを作成します。

2. [Azure portal](../azure-resource-manager/management/manage-resource-groups-portal.md) または [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md) を使用して、新しいリソース グループを作成します。 既存のリソース グループを使用する場合は、この手順は省略可能です。

3. パブリック IP アドレスを作成し、そのパブリック IP アドレスの DNS ラベル プロパティを設定します。 Cloud Services (延長サポート) では、[Basic](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) SKU のパブリック IP アドレスのみをサポートしています。 Standard SKU のパブリック IP は、Cloud Services では機能しません。
静的 IP を使用する場合は、サービス構成ファイル (.cscfg) で予約済み IP として参照する必要があります。 既存の IP アドレスを使用する場合は、この手順をスキップし、ARM テンプレートのロード バランサーの構成設定に IP アドレスの情報を直接追加します。
 
4. [Azure portal](../storage/common/storage-account-create.md?tabs=azure-portal) または [PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell) を使用して、新しいストレージ アカウントを作成します。 既存のストレージ アカウントを使用する場合は、この手順は省略可能です。

5. [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)、[AzCopy](../storage/common/storage-use-azcopy-blobs-upload.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)、または [PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md#upload-blobs-to-the-container) を使用して、サービス定義 (.csdef) ファイルとサービス構成 (.cscfg) ファイルをストレージ アカウントにアップロードします。 両方のファイルについて、SAS URI を取得します。この URI は、このチュートリアルで後ほど ARM テンプレートに追加します。

6. (省略可能) キー コンテナーを作成し、証明書をアップロードします。

    -  クラウド サービスに証明書を関連付けると、サービスとの間にセキュリティで保護された通信を実現できます。 証明書を使用するためには、サービス構成 (.cscfg) ファイル内でその証明書の拇印を指定したうえで、キー コンテナーにアップロードする必要があります。 キー コンテナーは、[Azure portal](../key-vault/general/quick-create-portal.md) または [PowerShell](../key-vault/general/quick-create-powershell.md) を使用して作成できます。
    - 関連付けるキー コンテナーは、クラウド サービスと同じリージョンおよびサブスクリプションに配置する必要があります。
    - また、Cloud Services (延長サポート) リソースが Key Vault から証明書を取得できるようにするために、関連付けるキー コンテナーで適切なアクセス許可を有効にする必要があります。 詳細については、[証明書とキー コンテナー](certificates-and-key-vault.md)に関するページを参照してください。
    - 以下の手順に示した ARM テンプレートの OsProfile セクションで、キー コンテナーを参照する必要があります。

## <a name="deploy-a-cloud-service-extended-support"></a>クラウド サービス (延長サポート) をデプロイする

> [!NOTE]
> ARM テンプレートとパラメーター ファイルは、[Azure portal](https://portal.azure.com) を使用することで簡単かつ迅速に生成できます。 Powershell でクラウド サービスを作成するには、ポータルを通じて、[生成された ARM テンプレートをダウンロード](generate-template-portal.md)します。
 
1. 仮想ネットワークを作成します。 仮想ネットワークの名前は、サービス構成 (.cscfg) ファイル内の参照と一致している必要があります。 既存の仮想ネットワークを使用する場合は、ARM テンプレートからこのセクションを削除してください。

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     新しい仮想ネットワークを作成する場合には、`dependsOn` セクションに以下を追加します。こうすることによって、クラウド サービスの作成前にプラットフォームにより仮想ネットワークが作成されます。

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. パブリック IP アドレスを作成し、(必要に応じて) そのパブリック IP アドレスの DNS ラベル プロパティを設定します。 静的 IP を使用する場合は、サービス構成 (.cscfg) ファイルで予約済み IP として参照する必要があります。 既存の IP アドレスを使用する場合は、この手順をスキップし、ARM テンプレートのロード バランサーの構成設定に IP アドレスの情報を直接追加します。
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     新しい IP アドレスを作成する場合には、`dependsOn` セクションに以下を追加します。こうすることによって、クラウド サービスの作成前にプラットフォームにより IP アドレスが作成されます。
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. ネットワーク プロファイル オブジェクトを作成し、ロード バランサーのフロントエンドにパブリック IP アドレスを関連付けます。 ロード バランサーは、プラットフォームによって自動的に作成されます。

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4. ARM テンプレートの  `OsProfile` セクションに、キー コンテナーの参照を追加します。 キー コンテナーは、クラウド サービス (延長サポート) に関連付けられている証明書を格納するために使用します。 証明書をキー コンテナーに追加してから、サービス構成 (.cscfg) ファイル内でその証明書の拇印を参照します。 また、Cloud Services (延長サポート) リソースがキー コンテナーからシークレットとして格納されている証明書を取得できるようにするために、キー コンテナーで "[Azure Virtual Machines (展開用)]" に対する "アクセス ポリシー" を (ポータル上で) 有効にする必要があります。 このキー コンテナーは、一意の名前を使用したうえで、クラウド サービスと同じリージョンおよびサブスクリプションに配置する必要があります。 詳細については、[Cloud Services (延長サポート) で証明書を使用する方法](certificates-and-key-vault.md)に関するページを参照してください。
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault は、キー コンテナーの ARM リソース ID です。 この情報は、キー コンテナーのプロパティ セクション内のリソース ID のところにあります。
    > - certificateUrl は、キー コンテナー内の証明書に移動し、 **[シークレット識別子]** というラベルの箇所を見ると確認できます。  
   >  - certificateUrl は、 https://{keyvault-endpoin}/secrets/{secretname}/{secret-id} の形式です。

5. ロール プロファイルを作成します。 ロールの数、ロールの名前、各ロールのインスタンス数、およびサイズが、サービス構成 (.cscfg)、サービス定義 (.csdef)、および ARM テンプレートのロール プロファイル セクションのいずれにおいても同じになっていることを確認します。
    
    ```json
    "roleProfile": {
      "roles": {
        "value": [
          {
            "name": "WebRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            }
          },
          {
            "name": "WorkerRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            } 
          } 
        ]
      }
    }   
    ```

6. (省略可能) クラウド サービスに拡張機能を追加する拡張機能プロファイルを作成します。 この例では、リモート デスクトップ拡張機能と Microsoft Azure Diagnostics 拡張機能を追加します。
   > [!Note] 
   > リモート デスクトップのパスワードは、長さが 8 から 123 文字であり、パスワードの複雑さに関する次の要件のうち 3 つ以上を満たしている必要があります。1) 大文字が含まれている 2) 小文字が含まれている 3) 数字が含まれている 4) 特殊文字が含まれている 5) 制御文字は使用できない

    ```json
        "extensionProfile": {
          "extensions": [
            {
              "name": "RDPExtension",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Windows.Azure.Extensions",
                "type": "RDP",
                "typeHandlerVersion": "1.2.1",
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
            {
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Azure.Diagnostics",
                "type": "PaaSDiagnostics",
                "typeHandlerVersion": "1.5",
                "settings": "[parameters('wadPublicConfig_WebRole1')]",
                "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                "rolesAppliedTo": [
                  "WebRole1"
                ]
              }
            }
          ]
        }
    ```

7. テンプレート全体を確認します。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2021-03-01",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
       }
      ]
    }
    ```

8. テンプレートとパラメーター ファイル (テンプレート ファイルでパラメーターを定義) をデプロイして、クラウド サービス (延長サポート) デプロイを作成します。 必要に応じて、こちらの[サンプル テンプレート](https://github.com/Azure-Samples/cloud-services-extended-support)を参照してください。

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>次のステップ 

- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
- [Cloud Services (延長サポート) のサンプル リポジトリ](https://github.com/Azure-Samples/cloud-services-extended-support)を確認します。
