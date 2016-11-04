---
title: Azure Data Lake Store の間でのデータの移動 | Microsoft Docs
description: Azure Data Factory を使用して Azure Data Lake Store に、または Azure Data Lake Store からデータを移動する方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: jingwang

---
# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Data Lake Store との間でのデータの移動
この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Data Lake Store と別のデータ ストアの間でデータを移動する方法の概要を説明します。 この記事は、コピー アクティビティによるデータ移動の概要とサポートされるデータ ストアの組み合わせについて説明する、 [データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事に基づいています。

> [!NOTE]
> Azure Data Lake Store との間でデータを移動するには、コピー アクティビティを含むパイプラインを作成する前に Azure Data Lake Store アカウントを作成します。 Azure Data Lake Store の詳細については、 [Azure Data Lake Store の概要](../data-lake-store/data-lake-store-get-started-portal.md)に関する記事をご覧ください。
> 
> Data Factory、リンクされたサービス、データセット、およびパイプラインを作成する詳細な手順については、 [最初のパイプラインを作成するチュートリアル](data-factory-build-your-first-pipeline.md) に関するページを確認してください。 Data Factory エディター、Visual Studio、または Azure PowerShell と JSON のスニペットを使用して、Data Factory エンティティを作成できます。
> 
> 

## <a name="copy-data-wizard"></a>データのコピー ウィザード
Azure Data Lake Store との間でデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。 

以下の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 ここでは、Azure Data Lake Store と Azure BLOB Storage との間でデータをコピーする方法を示します。 ただし、Azure Data Factory のコピー アクティビティを使用して、 **ここ** に示したいずれかのシンクに、任意のソースからデータを [直接](data-factory-data-movement-activities.md#supported-data-stores) コピーすることができます。  

## <a name="sample:-copy-data-from-azure-blob-to-azure-data-lake-store"></a>サンプル: Azure BLOB から Azure Data Lake Store にデータをコピーする
次のサンプルは以下を示しています。

1. [AzureStorage](#azure-storage-linked-service-properties)型のリンクされたサービス。
2. [AzureDataLakeStore](#azure-data-lake-linked-service-properties)型のリンクされたサービス。
3. [AzureBlob](#azure-blob-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureDataLakeStore](#azure-data-lake-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [BlobSource](#azure-blob-copy-activity-type-properties) と [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure BLOB ストレージから Azure Data Lake Store に時系列データを 1 時間おきに コピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure Storage のリンクされたサービス:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure Data Lake のリンクされたサービス:**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

### <a name="to-create-azure-data-lake-linked-service-using-data-factory-editor"></a>Data Factory エディターを使用して Azure Data Lake のリンクされたサービスを作成するには
次の手順では、Data Factory エディターを使用して Azure Data Lake Store のリンクされたサービスを作成する手順について説明します。

1. コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure Data Lake Store]** を選択します。
2. JSON エディターで、 **dataLakeStoreUri** プロパティに Data Lake の URI を入力します。
3. コマンド バーの **[承認する]** をクリックします。 ポップアップ ウィンドウが表示されます。
   
    ![Authorize button](./media/data-factory-azure-data-lake-connector/authorize-button.png)
4. 資格情報を使用してサインインし、JSON の **authorization** プロパティに今すぐ値を割り当てる必要があります。
5. (省略可能) JSON の **accountName**、**subscriptionID**、**resourceGroupName** などの省略可能なパラメーターの値を指定するか、これらのプロパティを JSON から削除します。
6. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

> [!IMPORTANT]
> **[認証]** ボタンを使用して生成した認証コードは、いずれ有効期限が切れます。 **トークンの有効期限が切れたら**、**[承認する]** ボタンを使用して**再承認**し、リンクされたサービスを再デプロイします。 詳細については、「 [Azure Data Lake Store のリンクされたサービス](#azure-data-lake-store-linked-service-properties) 」のセクションを参照してください。 
> 
> 

**Azure BLOB の入力データセット:**

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスでは開始時間の年、月、日の部分を使用し、ファイル名では開始時間の時刻部分を使用します。 "external": "true" の設定により、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ]
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }


**Azure Data Lake 出力データセット:**

このサンプルは Azure Data Lake Store にデータをコピーします。 新しいデータが 1 時間おきに Data Lake Store にコピーされます。

    {
        "name": "AzureDataLakeStoreOutput",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**コピー アクティビティのあるパイプライン:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **AzureDataLakeStoreSink** に設定されています。

    {  
        "name":"SamplePipeline",
        "properties":
        {  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":
            [  
                {
                    "name": "AzureBlobtoDataLake",
                    "description": "Copy Activity",
                    "type": "Copy",
                    "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                    ],
                    "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "treatEmptyAsNull": true,
                            "blobColumnSeparators": ","
                        },
                        "sink": {
                            "type": "AzureDataLakeStoreSink"
                        }
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
            ]
        }
    }

## <a name="sample:-copy-data-from-azure-data-lake-store-to-azure-blob"></a>サンプル: Azure Data Lake Store から Azure BLOB にデータをコピーする
次のサンプルは以下を示しています。

1. [AzureDataLakeStore](#azure-data-lake-linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](#azure-storage-linked-service-properties)型のリンクされたサービス。
3. [AzureDataLakeStore](#azure-data-lake-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) と [BlobSink](#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure Data Lake Store から Azure BLOB に時系列データを 1 時間おきにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure Data Lake Store のリンクされたサービス:**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

> [!NOTE]
> 承認 URL を取得するには、前の例の手順を参照してください。  
> 
> 

**Azure Storage のリンクされたサービス:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure Data Lake の入力データセット:**

**"external": true** の設定により、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

    {
        "name": "AzureDataLakeStoreInput",
        "properties":
        {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**コピー アクティビティのあるパイプライン:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** の型が **AzureDataLakeStoreSource** に設定され、**sink** の型が **BlobSink** に設定されています。

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureDakeLaketoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                      {
                        "name": "AzureDataLakeStoreInput"
                      }
                    ],
                    "outputs": [
                      {
                        "name": "AzureBlobOutput"
                      }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureDataLakeStoreSource",
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
             ]
        }
    }


## <a name="azure-data-lake-store-linked-service-properties"></a>Azure Data Lake Store のリンクされたサービスのプロパティ
Azure Storage のリンクされたサービスを利用し、Azure Storage アカウントを Azure Data Factory にリンクできます。 次の表は、Azure Storage のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティを **AzureDataLakeStore** |はい |
| dataLakeStoreUri |Azure Data Lake Store アカウントの情報を指定します。 https://<Azure Data Lake account name>.azuredatalakestore.net/webhdfs/v1 という形式で指定します。 |はい |
| authorization |**Data Factory エディター**で **[承認する]** をクリックし、資格情報を入力すると、自動生成された承認 URL がこのプロパティに割り当てられます。 |はい |
| sessionId |OAuth 承認セッションの OAuth セッション ID。 各セッション ID は一意であり、1 回のみ使用できます。 Data Factory エディターを使用すると、この設定が自動的に生成されます。 |はい |
| accountName |Data Lake アカウント名 |なし |
| subscriptionId |Azure サブスクリプション ID。 |いいえ (指定されていない場合、Data Factory のサブスクリプションが使用されます)。 |
| resourceGroupName |Azure リソース グループ名 |いいえ (指定されていない場合は Data Factory のリソース グループが使用されます)。 |

## <a name="token-expiration"></a>トークンの有効期限
**[承認する]** ボタンを使用して生成した承認コードは、いずれ有効期限が切れます。 さまざまな種類のユーザー アカウントの有効期限については、次の表を参照してください。 認証**トークンの有効期限が切れる**と、次のエラー メッセージが表示される場合があります: "資格情報の操作エラー: invalid_grant - AADSTS70002: 資格情報の検証中にエラーが発生しました。 AADSTS70008: 指定されたアクセス権の付与は期限が切れているか、失効しています。 トレース ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相関 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 タイムスタンプ: 2015-12-15 21-09-31Z"

| ユーザー タイプ | 有効期限 |
|:--- |:--- |
| Azure Active Directory で管理されていないユーザー アカウント (@hotmail.com, @live.com, など)。 |12 時間 |
| Azure Active Directory (AAD) で管理されているユーザー アカウント |スライスの最後の実行から 14 日後。 <br/><br/>OAuth ベースのリンクされたサービスに基づいて、少なくとも 14 日間に 1 回スライスが実行する場合、90 日です。 |

このトークンの有効期限の前にパスワードを変更すると、トークンが即座に期限切れとなり、前述のエラーが表示されます。 

このエラーを回避または解決するには、**トークンの有効期限が切れた**ときに、**[承認する]** ボタンを使用して再承認し、リンクされたサービスを再デプロイします。 次のセクションのコードを使用して、**sessionId** と **authorization** プロパティの値をプログラムで生成することもできます。 

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>プログラムを使用して sessionId と authorization の値を生成するには
    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

コードで使用する Data Factory クラスの詳細については、「[AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)」、「[AzureDataLakeAnalyticsLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)」、および「[AuthorizationSessionGetResponse クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)」をご覧ください。 このコードで使用されている WindowsFormsWebAuthenticationDialog クラスの **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** のバージョン **2.9.10826.1824** に参照を追加します。 

## <a name="azure-data-lake-dataset-type-properties"></a>Azure Data Lake データセットの type プロパティ
データセットの定義に利用できる JSON のセクションとプロパティの完全一覧については、[データセットの作成](data-factory-create-datasets.md)に関する記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションは、データセットの型ごとに異なり、データ ストアのデータの場所や書式などに関する情報を提供します。 **AzureDataLakeStore** 型のデータセットの typeProperties セクションには、次のプロパティがあります。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| folderPath |Azure Data Lake Store のコンテナーとフォルダーのパス。 |はい |
| fileName |Azure Data Lake Store 内のファイルの名前。 fileName は省略可能で、大文字と小文字を区別します。 <br/><br/>fileName を指定すると、アクティビティ (コピーを含む) は特定のファイルで動作します。<br/><br/>fileName が指定されていない場合、コピーには入力データセットの folderPath のすべてのファイルが含まれます。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイル名は次の形式になります: Data.<Guid>.txt (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |いいえ |
| partitionedBy |partitionedBy は任意のプロパティです。 これを使用し、時系列データに動的な folderPath と fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath をパラメーター化できます。 詳細と例については、「 [partitionedBy プロパティの使用](#using-partitionedby-property) 」をご覧ください。 |なし |
| BlobSink の format |次のファイル形式がサポートされます: **TextFormat**、**AvroFormat**、**JsonFormat**、**OrcFormat**、**ParquetFormat**。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、「[TextFormat の指定](#specifying-textformat)」、「[AvroFormat の指定](#specifying-avroformat)」、「[JsonFormat の指定](#specifying-jsonformat)」、「[OrcFormat の指定](#specifying-orcformat)」、および「[ParquetFormat の指定](#specifying-parquetformat)」を参照してください。 ファイル ベースのストア間でファイルをそのままコピーする場合は (バイナリ コピー)、入力と出力の両方のデータセット定義で format セクションをスキップできます。 |なし |
| compression |データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2** です。サポートされるレベルは、**Optimal** と **Fastest** です。 現時点では、**AvroFormat** と **OrcFormat** のデータの圧縮設定はサポートされていません。 詳細については、「[圧縮のサポート](#compression-support)」セクションを参照してください。 |いいえ |

### <a name="using-partitionedby-property"></a>partitionedBy プロパティの使用
**partitionedBy** セクション、Data Factory マクロ、特定のデータ スライスの開始時刻と終了時刻を示すシステム変数の SliceStart と SliceEnd を使用して、時系列データの動的な folderPath と fileName を指定できます。

時系列データセット、スケジュール設定、スライスの詳細については、[データセットの作成](data-factory-create-datasets.md)に関する記事および[スケジュール設定と実行](data-factory-scheduling-and-execution.md)に関する記事をご覧ください。

#### <a name="sample-1"></a>サンプル 1
    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

この例では、{Slice} は、指定された形式 (YYYYMMDDHH) で、Data Factory システム変数の SliceStart の値に置き換えられます。 SliceStart はスライスの開始時刻です。 folderPath はスライスごとに異なります。 例: wikidatagateway/wikisampledataout/2014100103 または wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>サンプル 2
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

この例では、SliceStart の年、月、日、時刻が folderPath プロパティと fileName プロパティで使用される個別の変数に抽出されます。

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

### <a name="compression-support"></a>圧縮のサポート
大量のデータセットを処理すると、I/O およびネットワークにボトルネックが生じる可能性があります。 そのため、データを圧縮して保存すると、ネットワークでのデータ転送速度が上昇してディスク領域を節約できるだけでなく、ビッグ データの処理性能を大幅に高めることができます。 現時点では、圧縮は Azure BLOB やオンプレミスのファイル システムなど、ファイルベースのデータ ストアでサポートされています。  

データセットの圧縮を指定するには、次の例のように、データセットの JSON で **compression** プロパティを使用します。   

    {  
        "name": "AzureDatalakeStoreDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureDatalakeStore",  
            "linkedServiceName": "DataLakeStoreLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  

**compression** セクションには次の 2 つのプロパティがあります。  

* **type:** 圧縮コーデックです。**GZIP**、**Deflate**、または **BZIP2** を指定できます。  
* **level:** 圧縮率です。**Optimal** または **Fastest** を指定できます。 
  
  * **Fastest:** 圧縮操作は可能な限り短時間で完了しますが、生成ファイルが最適に圧縮されない場合があります。 
  * **Optimal**: 圧縮操作では最適に圧縮されますが、操作完了までの時間が増加する場合があります。 
    
    詳細については、 [圧縮レベル](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) に関するトピックをご覧ください。 

サンプル データセットをコピー アクティビティの出力として使用するとします。 コピー アクティビティで出力データを GZIP コーデックによって最適な圧縮率で圧縮してから、Azure Data Lake Store の pagecounts.csv.gz という名前のファイルに圧縮データを書き込みます。   

入力データセット JSON で圧縮プロパティを指定すると、パイプラインはソースから圧縮データを読み取ります。 出力データセット JSON で圧縮プロパティを指定すると、コピー アクティビティによって圧縮データをターゲットに書き込むことができます。 いくつかのサンプル シナリオを次に示します。 

* Azure Data Lake Store から GZIP 圧縮データを読み取り、展開して、生成されたデータを Azure SQL Database に書き込みます。 この場合、Azure Data Lake Store 入力データセットを、compression JSON プロパティを使用して定義します。 
* オンプレミスのファイル システムのプレーンテキスト ファイルからデータを読み取り、GZip 形式で圧縮して、圧縮データを Azure Data Lake Store に書き込みます。 この場合、Azure Data Lake 出力データセットを、compression JSON プロパティを使用して定義します。  
* Azure Data Lake Store から GZIP 圧縮データを読み取って展開し、BZIP2 で圧縮して、生成されたデータを Azure Data Lake Store に書き込みます。 入力データセットと出力データセットの圧縮の種類を、それぞれ GZIP、BZIP2 として設定します。   

## <a name="azure-data-lake-copy-activity-type-properties"></a>Azure Data Lake のコピー アクティビティの type プロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります

**AzureDataLakeStoreSource** の **typeProperties** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True (既定値)、False |いいえ |

**AzureDataLakeStoreSink** の **typeProperties** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| copyBehavior |コピー動作を指定します。 |**PreserveHierarchy:** ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><br/>**FlattenHierarchy**: ソース フォルダーのすべてのファイルがターゲット フォルダーの最初のレベルに作成されます。 ターゲット ファイルは、自動生成された名前で作成されます。<br/><br/>**MergeFiles:** ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル/Blob の名前を指定した場合、マージされたファイル名は指定した名前になります。それ以外は自動生成されたファイル名になります。 |いいえ |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

<!--HONumber=Oct16_HO2-->


