<properties 
	pageTitle="Azure Data Factory から Azure Data Lake Analytics で U-SQL スクリプトを実行する" 
	description="Azure Data Lake Analytics コンピューティング サービスで U-SQL スクリプトを実行してデータを処理する方法について説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Azure Data Factory から Azure Data Lake Analytics で U-SQL スクリプトを実行する 
Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。この記事では、**Azure Data Lake Analytics** コンピューティング リンク サービスで **U-SQL** スクリプトを実行する **Data Lake Analytics U-SQL アクティビティ**について説明します。

> [AZURE.NOTE]Data Lake Analytics U-SQL アクティビティでパイプラインを作成する前に、Azure Data Lake Analytics アカウントを作成する必要があります。Azure Data Lake Analytics の詳細については、「[チュートリアル: Azure ポータルで Azure Data Lake Analytics の使用を開始する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)」をご覧ください。
>  
> Data Factory、リンクされたサービス、データセット、およびパイプラインを作成する詳細な手順については、[最初のパイプラインを作成するチュートリアル](data-factory-build-your-first-pipeline.md)に関するページを確認してください。Data Factory エディター、Visual Studio、または Azure PowerShell と JSON のスニペットを使用して、Data Factory エンティティを作成できます。

## Azure Data Lake Analytics リンク サービス
**Azure Data Lake Analytics** リンク サービスを作成して、Azure Data Lake Analytics コンピューティング サービスを Azure Data Factory にリンクしてから、パイプラインで Data Lake Analytics U-SQL アクティビティを使用します。

次の例は、Azure Data Lake Analytics リンク サービスの JSON 定義です。

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>", 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


次の表は、JSON 定義で使用されるプロパティの説明です。

プロパティ | 説明 | 必須
-------- | ----------- | --------
型 | type プロパティは **AzureDataLakeAnalytics** に設定する必要があります。 | あり
accountName | Azure Data Lake Analytics アカウント名。 | あり
dataLakeAnalyticsUri | Azure Data Lake Analytics URI。 | いいえ 
authorization | Data Factory Editor で **[承認]** ボタンをクリックし、OAuth ログインを完了すると、承認コードが自動的に取得されます。 | あり 
subscriptionId | Azure サブスクリプション ID | いいえ (指定されていない場合は Data Factory のサブスクリプションが使用されます)。 
resourceGroupName | Azure リソース グループ名 | いいえ (指定されていない場合は Data Factory のリソース グループが使用されます)。
sessionId | OAuth 承認セッションのセッション ID です。各セッション ID は一意であり、1 回のみ使用できます。セッション ID は、Data Factory Editor で自動生成されます。 | あり

**[認証]** ボタンを使用して生成した認証コードは、いずれ有効期限が切れます。さまざまな種類のユーザー アカウントの有効期限については、次の表を参照してください。認証**トークンの有効期限が切れる**と、次のエラー メッセージが表示される場合があります: "資格情報の操作エラー: invalid\_grant - AADSTS70002: 資格情報の検証中にエラーが発生しました。AADSTS70008: 指定されたアクセス権の付与は期限が切れているか、失効しています。トレース ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相関 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 タイムスタンプ: 2015-12-15 21:09:31Z"

 
| ユーザー タイプ | 有効期限 |
| :-------- | :----------- | 
| 非 AAD ユーザー (@hotmail.com、@live.com など) | 12 時間 |
| AAD ユーザーと OAuth ベースのソースがユーザーの Data Factory のテナントとは異なる[テナント](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)にあります。 | 12 時間 |
| AAD ユーザーと OAuth ベースのソースがユーザーの Data Factory のテナントと同じテナントにあります。 | <p> ユーザーが自身の OAuth ベースのリンクされたサービスのソースに基づいて、少なくとも 14 日間に 1 回スライスを実行する場合、最大値は 90 日です。</p><p>予定されている 90 日の間に、ユーザーが 14 日間、そのソースに基づいてスライスを実行しない場合、最後のスライスから 14 日後に資格情報の有効期限が切れます。</p> | 

このエラーを回避または解決するには、**トークンの有効期限が切れた**ときに、**[認証]** ボタンを使用して再認証し、リンクされたサービスを再デプロイする必要があります。次のセクションのコードを使用して、**sessionId** と **authorization** プロパティの値をプログラムで生成することもできます。

  
### プログラムを使用して sessionId と authorization の値を生成するには 

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

コードで使用する Data Factory クラスに関する詳細は、「[AzureDataLakeStoreLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)」、「[AzureDataLakeAnalyticsLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)」、「[AuthorizationSessionGetResponse クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)」のトピックを参照してください。WindowsFormsWebAuthenticationDialog クラスの Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll に参照を追加する必要があります。
 
 
## Data Lake Analytics U-SQL アクティビティ 

次の JSON のスニペットでは、Data Lake Analytics U-SQL アクティビティを使用してパイプラインを定義します。このアクティビティ定義には、先ほど作成した Azure Data Lake Analytics リンク サービスへの参照が含まれています。
  

	{
    	"name": "ComputeEventsByRegionPipeline",
    	"properties": {
        	"description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        	"activities": 
			[
            	{
            	    "type": "DataLakeAnalyticsU-SQL",
                	"typeProperties": {
                    	"scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
	                    "scriptLinkedService": "StorageLinkedService",
    	                "degreeOfParallelism": 3,
    	                "priority": 100,
    	                "parameters": {
    	                    "in": "/datalake/input/SearchLog.tsv",
    	                    "out": "/datalake/output/Result.tsv"
    	                }
    	            },
    	            "inputs": [
	    				{
	                        "name": "DataLakeTable"
	                    }
	                ],
	                "outputs": 
					[
	                    {
                    	    "name": "EventsByRegionTable"
                    	}
                	],
                	"policy": {
                    	"timeout": "06:00:00",
	                    "concurrency": 1,
    	                "executionPriorityOrder": "NewestFirst",
    	                "retry": 1
    	            },
    	            "scheduler": {
    	                "frequency": "Day",
    	                "interval": 1
    	            },
    	            "name": "EventsByRegion",
    	            "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
    	        }
    	    ],
    	    "start": "2015-08-08T00:00:00Z",
    	    "end": "2015-08-08T01:00:00Z",
    	    "isPaused": false
    	}
	}


次の表は、このアクティビティに固有のプロパティの名前と説明です。

プロパティ | 説明 | 必須
:-------- | :----------- | :--------
type | type プロパティは、**DataLakeAnalyticsU-SQL** に設定する必要があります。 | あり
scriptPath | U-SQL スクリプトを含むフォルダーのパス。 | いいえ (スクリプトを使用する場合)
scriptLinkedService | Data Factory に対するスクリプトを含むストレージをリンクするリンク サービス | いいえ (スクリプトを使用する場合)
script (スクリプト) | scriptPath と scriptLinkedService を指定する代わりに、インライン スクリプトを指定します。例: "script" : "CREATE DATABASE test". | いいえ (scriptPath と scriptLinkedService を使用する場合)
degreeOfParallelism | ジョブを実行するために同時に使用される最大ノード数。 | いいえ
priority | キューされているすべてのジョブのうち、先に実行するジョブを決定します。数値が小さいほど、優先度は高くなります。 | いいえ 
parameters | U-SQL スクリプトのパラメーター | いいえ 

スクリプト定義については、[SearchLogProcessing.txt のスクリプト定義](#script-definition)をご覧ください。

### 入力データセットと出力データセットの例

#### 入力データセット
この例では、入力データは Azure Data Lake Store 内 (datalake/input フォルダーの SearchLog.tsv ファイル) にあります。

	{
    	"name": "DataLakeTable",
	    "properties": {
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
    	    "availability": {
    	        "frequency": "Day",
    	        "interval": 1
    	    }
    	}
	}	

#### 出力データセット
この例では、U-SQL スクリプトで生成された出力データは、Azure Data Lake Store (datalake/output フォルダー) に格納されます。

	{
	    "name": "EventsByRegionTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "linkedServiceName": "AzureDataLakeStoreLinkedService",
	        "typeProperties": {
	            "folderPath": "datalake/output/"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

#### Azure Data Lake Store リンク サービスの例
上記の入力/出力データセットで使用される Azure Data Lake Store リンク サービスの定義例を次に示します。

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

上記の Azure Data Lake Store リンク サービスとデータ セット JSON スニペットの JSON プロパティについては、[Azure Data Lake Store のデータの入出力](data-factory-azure-datalake-connector.md)に関するページを参照してください。

### スクリプト定義

	@searchlog =
	    EXTRACT UserId          int,
	            Start           DateTime,
	            Region          string,
	            Query           string,
	            Duration        int?,
	            Urls            string,
	            ClickedUrls     string
	    FROM @in
	    USING Extractors.Tsv(nullEscape:"#NULL#");
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @searchlog
	WHERE Region == "en-gb";
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @rs1
	    WHERE Start <= DateTime.Parse("2012/02/19");
	
	OUTPUT @rs1   
	    TO @out
	      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

上記の U-SQL スクリプトの **@in** パラメーターと **@out** パラメーターの値は、"parameters" セクションを使用して ADF によって動的に渡されます。前述のパイプライン定義の "parameters" セクションを参照してください。

Azure Data Lake Analytics サービスで実行されるジョブのパイプライン定義で、他のプロパティ (degreeOfParallelism や優先度など) も指定できます。

<!---HONumber=AcomDC_0121_2016-->