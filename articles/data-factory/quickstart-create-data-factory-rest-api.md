---
title: REST API を使用して Azure データ ファクトリを作成する
description: Azure データ ファクトリ パイプラインを作成して、Azure Blob Storage 内のある場所から別の場所にデータをコピーします。
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/31/2021
ms.author: jingwang
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1fb0686d975c2342bcd562e75f739d83ee0a51f6
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681537"
---
# <a name="quickstart-create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>クイック スタート:REST API を使用して Azure データ ファクトリとパイプラインを作成する

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [現在のバージョン](quickstart-create-data-factory-rest-api.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory は、データドリブン型のワークフローをクラウドに作成することでデータの移動と変換を制御し、自動化することができるクラウドベースのデータ統合サービスです。 Azure Data Factory を使えば、データ主導型のワークフロー (パイプライン) を作成し、スケジューリングできます。具体的には、各種データ ストアからデータを取り込む、そのデータを各種コンピューティング サービス (Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning など) で処理または変換する、データ ストア (Azure Synapse Analytics など) に出力データを公開して、それを利用するビジネス インテリジェンス (BI) アプリケーションに提供するという一連の処理を行えるワークフローです。

このクイックスタートでは、REST API を使用して Azure データ ファクトリを作成する方法について説明します。 このデータ ファクトリのパイプラインは、Azure Blob Storage 内のある場所から別の場所にデータをコピーするものです。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure サブスクリプション**。 サブスクリプションがない場合は、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)アカウントを作成できます。
* **Azure Storage アカウント**。 BLOB ストレージを、**ソース** と **シンク** のデータ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md)」を参照してください。
* Blob Storage に **BLOB コンテナー** を作成し、コンテナーに入力 **フォルダー** を作成して、フォルダーにいくつかのファイルをアップロードします。 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのツールを使用して、Azure Blob Storage への接続、BLOB コンテナーの作成、入力ファイルのアップロード、出力ファイルの検証を行うことができます。
* **Azure PowerShell** をインストールします。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)に関するページに記載されている手順に従います。 このクイックスタートでは、PowerShell を使用して、REST API 呼び出しを実行します。
* [この手順](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)に従って、**Azure Active Directory にアプリケーションを作成します**。 **アプリケーション ID**、**clientSecrets**、**テナント ID** の値をメモしておいてください。後の手順で使用します。 サブスクリプションまたはリソース グループのいずれかのレベルで、アプリケーションを "**共同作成者**" ロールに割り当てます。
>[!NOTE]
>   ソブリン クラウドの場合は、ActiveDirectoryAuthority と ResourceManagerUrl (BaseUri) に適したクラウド固有のエンドポイントを使用する必要があります。 PowerShell を使用して、各クラウド環境のエンドポイントの一覧を返す "Get-AzEnvironment | Format-List" を実行することで、さまざまなクラウドのエンドポイント URL を簡単に取得できます。  
>    
## <a name="set-global-variables"></a>グローバル変数の設定

1. **PowerShell** を起動します。 Azure PowerShell は、このクイックスタートが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

    次のコマンドを実行して、Azure Portal へのサインインに使用するユーザー名とパスワードを入力します。

    ```powershell
    Connect-AzAccount
    ```
    次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```powershell
    Get-AzSubscription
    ```
    次のコマンドを実行して、使用するサブスクリプションを選択します。 **SubscriptionId** は、実際の Azure サブスクリプションの ID に置き換えてください。

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. プレースホルダーを独自の値に置き換えた後、次のコマンドを実行して、後の手順で使用するグローバル変数を設定します。

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $clientSecrets = "<your clientSecrets for the application>"
    $subscriptionId = "<your subscription ID to create the factory>"
    $resourceGroupName = "<your resource group to create the factory>"
    $factoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2018-06-01"
    ```

## <a name="authenticate-with-azure-ad"></a>Azure AD による認証

Azure Active Directory (AAD) で認証するには、次のコマンドを実行します。

```powershell
$credentials = Get-Credential -UserName $appId
Connect-AzAccount -ServicePrincipal  -Credential $credentials -Tenant $tenantID
```
パスワードの入力を求められます。clientSecrets 変数の値を使用します。

アクセス トークンを取得する必要がある場合は、次のようにします 

```powershell

GetToken

```

## <a name="create-a-data-factory"></a>Data Factory の作成

次のコマンドを実行して、データ ファクトリを作成します。

```powershell
$body = @"
{
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@

$response =   Invoke-AzRestMethod -SubscriptionId ${subscriptionId}  -ResourceGroupName ${resourceGroupName} -ResourceProviderName  Microsoft.DataFactory -ResourceType "factories" -Name  ${factoryName} -ApiVersion ${apiVersion} -Method PUT -Payload ${body}
$response.Content  

```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが発生した場合は、名前を変更してからもう一度実行してください。

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* 現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、 **[分析]** を展開して **[Data Factory]** を探してください。[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/) データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。

応答コンテンツの例を次に示します。

```json

{  
    "name":"<dataFactoryName>",
    "identity":{  
        "type":"SystemAssigned",
        "principalId":"<service principal ID>",
        "tenantId":"<tenant ID>"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{  
        "provisioningState":"Succeeded",
        "createTime":"2019-09-03T02:10:27.056273Z",
        "version":"2018-06-01"
    },
    "eTag":"\"0200c876-0000-0100-0000-5d6dcb930000\"",
    "location":"East US",
    "tags":{  

    }
}
```
## <a name="create-linked-services"></a>リンクされたサービスを作成します

データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このクイックスタートでは、コピー ソースとシンク ストアの両方として、Azure Storage のリンクされたサービスを 1 つ作成するだけで済みます。このサービスは、サンプルでは "AzureStorageLinkedService" という名前です。

次のコマンドを実行して、**AzureStorageLinkedService** という名前のリンクされたサービスを作成します。

コマンドを実行する前に、&lt;accountName&gt; と &lt;accountKey&gt; を Azure ストレージ アカウントの名前とキーに置き換えます。

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"

$body = @"
{  
    "name":"AzureStorageLinkedService",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response.content
```

出力例を次に示します。

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":"AzureStorageLinkedService",
    "type":"Microsoft.DataFactory/factories/linkedservices",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;"
        }
    },
    "etag":"07011a57-0000-0100-0000-5d6e14a20000"
}
```
## <a name="create-datasets"></a>データセットを作成する

ソースからシンクにコピーするデータを表すデータセットを定義します。 この例では、次の 2 つのデータセットを作成します: InputDataset と OutputDataset。 これらは、前のセクションで作成した Azure Storage のリンクされたサービスを参照します。 入力データセットは、入力フォルダーのソース データを表します。 入力データセットの定義では、ソース データを格納している BLOB コンテナー (adftutorial)、フォルダー (input)、およびファイル (emp.txt) を指定します。 出力データセットは、ターゲットにコピーされるデータを表します。 出力データセットの定義では、データのコピー先の BLOB コンテナー (adftutorial)、フォルダー (output)、およびファイルを指定します。

**InputDataset を作成する**

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/InputDataset?api-version=${apiVersion}"

$body = @"
{  
    "name":"InputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response  

```

出力例を次に示します。

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/InputDataset",
    "name":"InputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; fileName=emp.txt; folderPath=input; container=adftutorial}"
        }
    },
    "etag":"07011c57-0000-0100-0000-5d6e14b40000"
}
```
**OutputDataset を作成する**

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/OutputDataset?api-version=${apiVersion}"

$body = @"
{  
    "name":"OutputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content
```

出力例を次に示します。

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/OutputDataset",
    "name":"OutputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; folderPath=output; container=adftutorial}"
        }
    },
    "etag":"07013257-0000-0100-0000-5d6e18920000"
}
```
## <a name="create-a-pipeline"></a>パイプラインを作成する

この例では、このパイプラインに Copy アクティビティが 1 つ含まれています。 Copy アクティビティは、入力と出力として、前の手順で作成された "InputDataset" と "OutputDataset" を参照します。

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"

$body = @"
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "InputDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "OutputDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "annotations": []
    }
}
"@
$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content
```

出力例を次に示します。

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":"Adfv2QuickStartPipeline",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "properties":{  
        "activities":[  
            "@{name=CopyFromBlobToBlob; type=Copy; dependsOn=System.Object[]; policy=; userProperties=System.Object[]; typeProperties=; inputs=System.Object[]; outputs=System.Object[]}"
        ],
        "annotations":[  

        ]
    },
    "etag":"07012057-0000-0100-0000-5d6e14c00000"
}
```

## <a name="create-pipeline-run"></a>パイプラインの実行を作成する

この手順では、パイプラインの実行をトリガーします。 応答本文で返されたパイプライン実行 ID は、後で API を監視する際に使用されます。

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"

$response =  Invoke-AzRestMethod  -Path ${path}  -Method POST 
$response.content 
```

出力例を次に示します。

```json
{  
    "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc"
}
```

runId は、次のコマンドを使用して取得することもできます。 
```powershell

($response.content | ConvertFrom-Json).runId

```

## <a name="parameterize-your-pipeline"></a>パイプラインをパラメーター化する 

パラメーターを使用してパイプラインを作成できます。 次の例では、入力データセットと出力データセットを作成します。これらは、パイプラインに渡されるパラメーターとして入力および出力ファイル名を受け取ることができます。 


## <a name="create-parameterized-input-dataset"></a>パラメーター化された入力データセットを作成する 

strInputFileName という名前のパラメーターを定義し、これをデータセットのファイル名として使用します。 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/ParamInputDataset?api-version=${apiVersion}"

$body = @"
{
    "name": "ParamInputDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strInputFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strInputFileName",
                    "type": "Expression"
                },
                "folderPath": "input",
                "container": "adftutorial"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content

```

出力例を次に示します。 

```json
{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/datasets/ParamInputDataset",
    "name": "ParamInputDataset",
    "type": "Microsoft.DataFactory/factories/datasets",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strInputFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strInputFileName",
                    "type": "Expression"
                },
                "folderPath": "input",
                "container": "adftutorial"
            }
        }
    },
    "etag": "00000000-0000-0000-0000-000000000000"
}

```


## <a name="create-parameterized-output-dataset"></a>パラメーター化された出力データセットを作成する 


strOutputFileName という名前のパラメーターを定義し、これをデータセットのファイル名として使用します。 

```powershell


$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/ParamOutputDataset?api-version=${apiVersion}"
$body = @"
{
    "name": "ParamOutputDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strOutPutFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strOutPutFileName",
                    "type": "Expression"
                },
                "folderPath": "output",
                "container": "adftutorial"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}

"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content

```

出力例を次に示します。 

```json
{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/datasets/ParamOutputDataset",
    "name": "ParamOutputDataset",
    "type": "Microsoft.DataFactory/factories/datasets",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strOutPutFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strOutPutFileName",
                    "type": "Expression"
                },
                "folderPath": "output",
                "container": "adftutorial"
            }
        }
    },
    "etag": "00000000-0000-0000-0000-000000000000"
}
```

## <a name="create-parameterized-pipeline"></a>パラメーター化されたパイプラインを作成する 

2 つのパイプライン レベル パラメーター strParamInputFileName および strParamOutputFileName を使用して、パイプラインを定義します。 次に、これら 2 つのパラメーターをデータセットの strInputFileName および strOutputFileName パラメーターにリンクします。 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartParamPipeline?api-version=${apiVersion}"

$body = @"
{
    "name": "Adfv2QuickStartParamPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "ParamInputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strInputFileName": {
                                "value": "@pipeline().parameters.strParamInputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "ParamOutputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strOutPutFileName": {
                                "value": "@pipeline().parameters.strParamOutputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],   

        "parameters": {
            "strParamInputFileName": {
              "type": "String"
            },
            "strParamOutputFileName": {
              "type": "String"
            }
          }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content


```

出力例を次に示します。 

```json

{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelines/Adfv2QuickStartParamPipeline",
    "name": "Adfv2QuickStartParamPipeline",
    "type": "Microsoft.DataFactory/factories/pipelines",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "ParamInputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strInputFileName": {
                                "value": "@pipeline().parameters.strParamInputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "ParamOutputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strOutPutFileName": {
                                "value": "@pipeline().parameters.strParamOutputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "parameters": {
            "strParamInputFileName": {
                "type": "String"
            },
            "strParamOutputFileName": {
                "type": "String"
            }
        }
    },
    "etag": "5e01918d-0000-0100-0000-60d569a90000"
}

```

## <a name="create-pipeline-run-with-parameters"></a>パラメーターを使用してパイプライン実行を作成する 

これで、パイプライン実行の作成時にパラメーターの値を指定できます。 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartParamPipeline/createRun?api-version=${apiVersion}"

$body = @"
{  
        "strParamInputFileName": "emp2.txt",
        "strParamOutputFileName": "aloha.txt"
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method POST -Payload $body
$response.content
$runId  = ($response.content | ConvertFrom-Json).runId

```
出力例を次に示します。 

```json
{"runId":"ffc9c2a8-d86a-46d5-9208-28b3551007d8"}
```


## <a name="monitor-pipeline"></a>パイプラインを監視する

1. 次のスクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。

    ```powershell
        $path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
        
        
        while ($True) {
        
            $response =  Invoke-AzRestMethod  -Path ${path}  -Method GET 
            $response = $response.content | ConvertFrom-Json
        
            Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"
        
            if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") -or ($response.Status -eq "In Progress") ) {
                Start-Sleep -Seconds 10
            }
            else {
                $response | ConvertTo-Json
                break
            }
        }
    ```

    出力例を次に示します。

    ```json
        {
          "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelineruns/ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "runId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "debugRunId": null,
          "runGroupId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "pipelineName": "Adfv2QuickStartParamPipeline",
          "parameters": {
            "strParamInputFileName": "emp2.txt",
            "strParamOutputFileName": "aloha.txt"
          },
          "invokedBy": {
            "id": "9c0275ed99994c18932317a325276544",
            "name": "Manual",
            "invokedByType": "Manual"
          },
          "runStart": "2021-06-25T05:34:06.8424413Z",
          "runEnd": "2021-06-25T05:34:13.2936585Z",
          "durationInMs": 6451,
          "status": "Succeeded",
          "message": "",
          "lastUpdated": "2021-06-25T05:34:13.2936585Z",
          "annotations": [],
          "runDimension": {},
          "isLatest": true
        }
    ```

2. 次のスクリプトを実行し、コピー アクティビティの実行の詳細 (たとえば、読み書きされたデータのサイズ) を取得します。

    ```powershell
         $path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}/queryActivityruns?api-version=${apiVersion}"
        
        
        while ($True) {
        
            $response =  Invoke-AzRestMethod  -Path ${path}  -Method POST 
            $responseContent = $response.content | ConvertFrom-Json
            $responseContentValue = $responseContent.value
        
            Write-Host  "Activity run status: " $responseContentValue.Status -foregroundcolor "Yellow"
        
            if ( ($responseContentValue.Status -eq "InProgress") -or ($responseContentValue.Status -eq "Queued") -or ($responseContentValue.Status -eq "In Progress") ) {
                Start-Sleep -Seconds 10
            }
            else {
                $responseContentValue | ConvertTo-Json
                break
            }
        }
    ```
    出力例を次に示します。

    ```json
        {
          "activityRunEnd": "2021-06-25T05:34:11.9536764Z",
          "activityName": "CopyFromBlobToBlob",
          "activityRunStart": "2021-06-25T05:34:07.5161151Z",
          "activityType": "Copy",
          "durationInMs": 4437,
          "retryAttempt": null,
          "error": {
            "errorCode": "",
            "message": "",
            "failureType": "",
            "target": "CopyFromBlobToBlob",
            "details": ""
          },
          "activityRunId": "40bab243-9bbf-4538-9336-b797a2f98e2b",
          "iterationHash": "",
          "input": {
            "source": {
              "type": "BinarySource",
              "storeSettings": "@{type=AzureBlobStorageReadSettings; recursive=True}"
            },
            "sink": {
              "type": "BinarySink",
              "storeSettings": "@{type=AzureBlobStorageWriteSettings}"
            },
            "enableStaging": false
          },
          "linkedServiceName": "",
          "output": {
            "dataRead": 134,
            "dataWritten": 134,
            "filesRead": 1,
            "filesWritten": 1,
            "sourcePeakConnections": 1,
            "sinkPeakConnections": 1,
            "copyDuration": 3,
            "throughput": 0.044,
            "errors": [],
            "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
            "usedDataIntegrationUnits": 4,
            "billingReference": {
              "activityType": "DataMovement",
              "billableDuration": ""
            },
            "usedParallelCopies": 1,
            "executionDetails": [
              "@{source=; sink=; status=Succeeded; start=06/25/2021 05:34:07; duration=3; usedDataIntegrationUnits=4; usedParallelCopies=1; profile=; detailedDurations=}"
            ],
            "dataConsistencyVerification": {
              "VerificationResult": "NotVerified"
            },
            "durationInQueue": {
              "integrationRuntimeQueue": 0
            }
          },
          "userProperties": {},
          "pipelineName": "Adfv2QuickStartParamPipeline",
          "pipelineRunId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "status": "Succeeded",
          "recoveryStatus": "None",
          "integrationRuntimeNames": [
            "defaultintegrationruntime"
          ],
          "executionDetails": {
            "integrationRuntime": [
              "@{name=DefaultIntegrationRuntime; type=Managed; location=East US; nodes=}"
            ]
          },
          "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelineruns/ffc9c2a8-d86a-46d5-9208-28b3551007d8/activityruns/40bab243-9bbf-4538-9336-b797a2f98e2b"
        }
    ```
## <a name="verify-the-output"></a>出力を検証する

Azure Storage Explorer を使用して、パイプラインの実行を作成したときに指定したようにファイルが "inputPath" から "outputPath" にコピーされていることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
このクイックスタートで作成したリソースは、2 とおりの方法でクリーンアップすることができます。 [Azure リソース グループ](../azure-resource-manager/management/overview.md)を削除した場合、そのリソース グループに含まれたすべてのリソースが対象となります。 他のリソースをそのまま維持する場合は、このチュートリアルで作成したデータ ファクトリだけを削除してください。

次のコマンドを実行して、リソース グループ全体を削除します。
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

次のコマンドを実行して、データ ファクトリだけを削除します。

```powershell
Remove-AzDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Azure Blob Storage 内のある場所から別の場所にデータをコピーするものです。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-dot-net.md)を参照してください。
