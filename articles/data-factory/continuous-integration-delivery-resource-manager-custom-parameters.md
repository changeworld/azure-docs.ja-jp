---
title: Resource Manager テンプレートのカスタム パラメーター
description: Azure Data Factory 内で継続的インテグレーションおよびデリバリーを使用して、Resource Manager テンプレート内のカスタム パラメーターを使用する方法について説明します。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6584120a0a66fd1d913fdee86a24ce3d91b2555f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519669"
---
# <a name="use-custom-parameters-with-the-resource-manager-template"></a>Resource Manager テンプレートでカスタム パラメーターを使用する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

開発インスタンスに Git リポジトリが関連付けられている場合、テンプレートの公開やエクスポートによって生成された Resource Manager テンプレートの既定の Resource Manager テンプレート パラメーターをオーバーライドすることができます。 既定の Resource Manager パラメーター構成をオーバーライドする必要が生じる例としては、以下のシナリオが考えられます。

* 自動化された CI/CD を使用していて、Resource Manager のデプロイ中にいくつかのプロパティを変更したいが、プロパティが既定でパラメーター化されていない。
* ファクトリが非常に大きく、既定の Resource Manager テンプレートが許容されるパラメーターの上限 (256) よりも多いために無効である。

    カスタム パラメーターの 256 の上限に対処するには、次の 3 つのオプションがあります。    
  
    * カスタム パラメーター ファイルを使用してパラメーター化を必要としない (既定値を保持できる) プロパティを削除して、パラメーターの数を減らします。
    * データフローのロジックをリファクタリングして、パラメーターを減らします。たとえば、パイプライン パラメーターの値がすべて同じである場合は、代わりにグローバル パラメーターを使用できます。
    * 1 つのデータ ファクトリを複数のデータ フローに分割します。

既定の Resource Manager パラメーター構成をオーバーライドするには、 **[管理]** ハブに移動し、[ソース管理] セクションで **[ARM テンプレート]** を選択します。 **[ARM パラメーター構成]** セクションで、[パラメーター構成の編集] の **[編集]** アイコンをクリックして、Resource Manager パラメーター構成コード エディターを開きます。

:::image type="content" source="media/author-management-hub/management-hub-custom-parameters.png" alt-text="カスタム パラメーターの管理":::

> [!NOTE]
> **[ARM パラメーター構成]** は、"Git モード" でのみ有効になります。 現在のところ、"ライブ モード" や "Data Factory" モードでは無効になります。

カスタムの Resource Manager パラメーター テンプレート構成を作成すると、Git ブランチのルート フォルダーに **arm-template-parameters-definition.json** という名前のファイルが作成されます。 正確なファイル名を使用する必要があります。

:::image type="content" source="media/continuous-integration-delivery/custom-parameters.png" alt-text="カスタム パラメーター ファイル":::

コラボレーション ブランチから発行すると、Data Factory では、このファイルを読み取り、その構成を利用してパラメーター化するプロパティが生成されます。 ファイルが見つからない場合は、既定のテンプレートが使用されます。

Resource Manager テンプレートをエクスポートすると、Data Factory により、コラボレーション ブランチでなく、現在作業中のどのブランチからでもこのファイルを読み取られます。 プライベート ブランチからファイルを作成または編集し、UI の **[ARM テンプレートのエクスポート]** を選択して変更内容をテストすることができます。 その後、このファイルをコラボレーション ブランチ内にマージできます。

> [!NOTE]
> カスタムの Resource Manager パラメーター構成では、ARM テンプレート パラメーターの制限である 256 が変更されることはありません。 これにより、パラメーター化されたプロパティの数を選択して減らすことができます。

## <a name="custom-parameter-syntax"></a>カスタムのパラメーター構文

カスタム パラメーター ファイル **arm-template-parameters-definition.json** を作成する際のいくつかのガイドラインを次に示します。 ファイルは、(トリガー、パイプライン、リンクされたサービス、データセット、統合ランタイム、データ フロー) エンティティの種類ごとのセクションで構成されています。

* 関連するエンティティ型の下にプロパティ パスを入力します。
* プロパティ名を `*` に設定すると、その下 (再帰的にではなく、最初のレベルまでのみ) にあるすべてのプロパティをパラメーター化することを指示します。 また、この構成に例外を指定することもできます。
* プロパティの値を文字列として設定すると、プロパティをパラメーター化することを指示します。 「`<action>:<name>:<stype>`」の形式を使用します。
   *  `<action>` には、次のいずれかの文字を指定できます。
      * `=` は、パラメーターの既定値として現在の値を保持することを意味します。
      * `-` は、パラメーターの既定値を保持しないことを意味します。
      * `|` は、接続文字列またはキーに対する Azure Key Vault からのシークレットの特殊なケースです。
   * `<name>` は、パラメーターの名前です。 空白の場合は、プロパティの名前になります。 値が `-` 文字で始まる場合、名前は短縮されます。 たとえば、`AzureStorage1_properties_typeProperties_connectionString` は `AzureStorage1_connectionString` に短縮されます。
   * `<stype>` は、パラメーターの型です。 `<stype>` が空白の場合、既定の型は `string` です。 サポートされる値は `string`、`securestring`、`int`、`bool`、`object`、`secureobject`、および `array` です。
* 定義ファイルに配列を指定すると、テンプレート内の一致するプロパティが配列であることを指示します。 Data Factory は、配列の統合ランタイム オブジェクトに指定された定義を使用することで、配列内のすべてのオブジェクトを反復処理します。 2 番目のオブジェクトである文字列は、各反復処理のパラメーターの名前として使用されるプロパティ名です。
* 定義をリソース インスタンスに固有にすることはできません。 定義はその型のすべてのリソースに適用されます。
* 既定では、Key Vault シークレットなどのセキュリティで保護されたすべての文字列と、接続文字列、キー、トークンなどのセキュリティで保護された文字列がパラメーター化されます。
 
## <a name="sample-parameterization-template"></a>サンプルのパラメーター化テンプレート

次に示すのは、Resource Manager パラメーター構成の例です。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/credentials" : {
        "properties": {
            "typeProperties": {
                "resourceId": "="
            }
        }
    }
}
```
ここでは、前のテンプレートがどのように構築されるかについて、リソースの種類別に説明します。

### <a name="pipelines"></a>パイプライン
    
* パス `activities/typeProperties/waitTimeInSeconds` 内のすべてのプロパティがパラメーター化されます。 `waitTimeInSeconds` という名前のコードレベルのプロパティを持つパイプライン内のアクティビティ (たとえば、`Wait` アクティビティ) はすべて、既定の名前を使用して数値としてパラメーター化されます。 ただし、Resource Manager テンプレートにその既定値はありません。 これは Resource Manager デプロイ時に必須の入力になります。
* 同様に、`headers` というプロパティ (たとえば、`Web` アクティビティ内の) は、型 `object` (JObject) でパラメーター化されます。 これは、ソース ファクトリの値と同じ値である既定値を保持します。

### <a name="integrationruntimes"></a>IntegrationRuntimes

* パス `typeProperties` の下にあるすべてのプロパティは、それぞれの既定値を使用してパラメーター化されます。 たとえば、`IntegrationRuntimes` 型のプロパティの下には `computeProperties` と `ssisProperties` という 2 つのプロパティがあります。 両方のプロパティの型は、それぞれの既定の値と型 (Object) で作成されます。

### <a name="triggers"></a>トリガー

* `typeProperties` の下では、2 つのプロパティがパラメーター化されます。 1 つ目は `maxConcurrency` で、既定値を持つように指定されていて、型は `string` です。 既定のパラメーター名 `<entityName>_properties_typeProperties_maxConcurrency` を持ちます。
* `recurrence` プロパティもパラメーター化されます。 その下にあるそのレベルのすべてのプロパティは、既定の値とパラメーター名を持つ文字列としてパラメーター化されるよう指定されます。 例外は、型 `int` としてパラメーター化される `interval` プロパティです。 パラメーター名の末尾には `<entityName>_properties_typeProperties_recurrence_triggerSuffix` が付きます。 同様に、`freq` プロパティは文字列であり、文字列としてパラメーター化されます。 ただし、`freq` プロパティは既定値なしでパラメーター化されます。 名前は短縮され、サフィックスが付けられます。 たとえば、「 `<entityName>_freq` 」のように入力します。

### <a name="linkedservices"></a>LinkedServices

* リンクされたサービスは一意です。 リンクされたサービスとデータセットにはさまざまな型があるため、型固有のカスタマイズを行うことができます。 この例では、型 `AzureDataLakeStore` のすべてのリンクされたサービスに対して、特定のテンプレートが適用されます。 他のすべての場合 (`*` を使用)、別のテンプレートが適用されます。
* `connectionString` プロパティは、`securestring` 値としてパラメーター化されます。 既定値はありません。 これには、末尾に `connectionString` が付く、短縮されたパラメーター名が付けられます。
* プロパティ `secretAccessKey` は (たとえば、Amazon S3 のリンクされたサービスでは) `AzureKeyVaultSecret` になることがあります。 Azure Key Vault シークレットとして自動的にパラメーター化され、構成済みのキー コンテナーからフェッチされます。 キー コンテナー自体もパラメーター化できます。

### <a name="datasets"></a>データセット

* データセットに型固有のカスタマイズを使用できますが、\* レベルの構成を明示的に指定しなくても構成を指定できます。 前の例では、`typeProperties` の下にあるすべてのデータセット プロパティがパラメーター化されます。

> [!NOTE]
> **Azure のアラートとマトリックス** がパイプライン用に構成されている場合、それらは ARM デプロイのパラメーターとして現在サポートされていません。 新しい環境にアラートとマトリックスを再適用するには、 [Data Factory の監視、アラート、およびマトリックス](./monitor-metrics-alerts.md)に関するページに従ってください。
> 

## <a name="default-parameterization-template"></a>既定のパラメーター化テンプレート

次に、現在の既定のパラメーター化テンプレートを示します。 いくつかのパラメーターのみを追加する必要がある場合は、このテンプレートを直接編集することをお勧めします。既存のパラメーター化構造が失われないためです。

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

## <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>例: 既存の Azure Databricks 対話型クラスター ID のパラメーター化

次の例は、既定のパラメーター化テンプレートに 1 つの値を追加する方法を示しています。 Databricks のリンクされたサービスの既存の Azure Databricks インタラクティブ クラスター ID をパラメーター ファイルに追加するだけです。 このファイルは、`Microsoft.DataFactory/factories/linkedServices` のプロパティ フィールドの下に `existingClusterId` が追加されていることを除き、前のファイルと同じであることに注意してください。

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="next-steps"></a>次の手順

- [継続的インテグレーションとデリバリーの概要](continuous-integration-delivery.md)
- [Azure Pipelines リリースを使用して継続的インテグレーションを自動化する](continuous-integration-delivery-automate-azure-pipelines.md)
- [Resource Manager テンプレートを各環境に手動でレベル上げする](continuous-integration-delivery-manual-promotion.md)
- [リンクされた Resource Manager テンプレート](continuous-integration-delivery-linked-templates.md)
- [修正プログラム運用環境の適用](continuous-integration-delivery-hotfix-environment.md)
- [サンプルのデプロイ前とデプロイ後のスクリプト](continuous-integration-delivery-sample-script.md)