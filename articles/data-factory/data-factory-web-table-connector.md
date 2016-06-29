<properties 
	pageTitle="Web テーブルからのデータの移動 | Azure Data Factory" 
	description="Azure Data Factory を使用してオンプレミスのテーブルからデータを移動する方法を説明します。" 
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
	ms.date="05/02/2016" 
	ms.author="spelluru"/>

# Azure Data Factory を使用して Web テーブル ソースからデータを移動する
この記事では、Azure Data Factory のコピー アクティビティを使用して、Web ページのテーブルから他のデータ ストアにデータをコピーする方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

現在のところ、データ ファクトリは Web テーブルから他のデータ ストアへのデータの移動のみに対応しており、他のデータ ストアから Web テーブルにデータを移動することはできません。

## サンプル: Web テーブルから Azure BLOB にデータをコピーする

下のサンプルで確認できる要素:

1.	[Web](#web-linked-service-properties) 型のリンクされたサービス。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3.	[WebTable](#WebTable-dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4.	[WebSource](#websource-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプル データでは、1 時間おきに Web テーブルから Azure BLOB にデータがコピーされます。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

次のサンプルは、Web テーブルから Azure BLOB にデータをコピーする方法を示します。ただし、Azure Data Factory のコピー アクティビティを使用して、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」で説明されているシンクのいずれかにデータを直接コピーすることができます。

**Web のリンクされたサービス** この例では、Web サービスにリンクされたサービスと匿名認証が使用されています。使用可能なさまざまな種類の認証については、「[Web のリンクされたサービス](#web-linked-service-properties)」セクションを参照してください。

	{
	    "name": "WebLinkedService",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "https://en.wikipedia.org/wiki/"
	        }
	    }
	}


**Azure Storage のリンクされたサービス**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**WebTable 入力データセット** **external** を **true** に設定して **externalData** ポリシーを指定 (省略可) すると、テーブルが Data Factory に対して外部にあり、データ ファクトリのアクティビティでは生成されていないことが Data Factory のサービスに通知されます。

> [AZURE.NOTE] HTML ページのテーブルのインデックスを取得する方法については、「[HTML ページのテーブルのインデックスを取得する](#get-index-of-a-table-in-an-html-page)」を参照してください。

	
	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 1,
	            "path": "AFI's_100_Years...100_Movies"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}



**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties":
	        {
	            "folderPath": "adfgetstarted/Movies"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}




**コピー アクティビティのあるパイプライン**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。パイプライン JSON 定義で、**source** 型が **WebSource** に設定され、**sink** 型が **BlobSink** に設定されています。

WebSource でサポートされるプロパティの一覧については、「[WebSource type プロパティ](#websource-copy-activity-type-properties)」を参照してください。
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "WebTableToAzureBlob",
	        "description": "Copy from a Web table to an Azure blob",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "WebTableInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "WebSource"
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


## Web のリンクされたサービスのプロパティ

次の表は、Web のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- | 
| type | type プロパティを **Web** に設定する必要があります。 | あり | 
| Url | Web ソースへの URL | あり |
| authenticationType | Anonymous または Basic。 | はい |
| userName | 基本認証のユーザー名。 | はい (基本認証用)
| パスワード | 基本認証のパスワード。 | はい (基本認証用)

### 匿名認証を使用する

	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "https://en.wikipedia.org/wiki/"
	        }
	    }
	}


### 基本認証を使用する
	
	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "basic",
	            "url" : "http://myit.mycompany.com/",
	            "userName": "Administrator",
	            "password": "password"
	        }
	    }
	}


## WebTable データセットのプロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Azure SQL、Azure BLOB、Azure テーブルなど) で同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**WebTable** 型のデータセットの typeProperties セクションには次のプロパティがあります。

プロパティ | 説明 | 必須
:-------- | :----------- | :--------
type | データセットの型。**WebTable** に設定する必要があります。 | あり
path | テーブルを含むリソースの相対 URL。 | いいえ。パスが指定されていないとき、リンクされたサービス定義に指定されている URL のみだけが使用されます。 
Index | リソースのテーブルのインデックス。HTML ページのテーブルのインデックスを取得する方法については、「[HTML ページのテーブルのインデックスを取得する](#get-index-of-a-table-in-an-html-page)」を参照してください。 | あり


**例:**

	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 1,
	            "path": "AFI's_100_Years...100_Movies"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}

## WebSource - コピー アクティビティの type プロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、source と sink の種類によって異なります。

ソースが **WebSource** 型のコピー アクティビティの場合、現時点では追加プロパティはサポートされていません。

## HTML ページのテーブルのインデックスを取得する

1. **Excel 2016** を起動し、**[データ]** タブに切り替えます。  
2. ツール バーの **[新しいクエリ]** をクリックし、**[その他のソースから]** をポイントし、**[Web から]** をクリックします。
	
	![Power Query メニュー](./media/data-factory-web-table-connector/PowerQuery-Menu.png) 
3. **[From Web (Web から)]** ダイアログ ボックスで、リンクされたサービスの JSON で使用する **URL** を入力し (例: https://en.wikipedia.org/wiki/)、データセットに指定するパスを入力し (例: AFI%27s\_100\_Years...100\_Movies)、**[OK]** をクリックします。 

	![Web ダイアログから](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

	この例で使用される URL: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies 
4.  **[Web コンテンツへのアクセス]** ダイアログ ボックスが表示された場合、適切な **URL** と**認証**を選択し、**[接続]** をクリックします。 

	![[Access Web コンテンツ] ダイアログ ボックス](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5.  ツリー ビューの**テーブル** アイテムをクリックしてテーブルのコンテンツを表示し、一番下にある **[編集]** をクリックします。  

	![[ナビゲーター] ダイアログ](./media/data-factory-web-table-connector/Navigator-DialogBox.png)

5. **[クエリ エディター]** ウィンドウで、ツール バーの **[詳細エディター]** をクリックします。

	![[詳細エディター] ボタン](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)

6. [詳細エディター] ダイアログ ボックスで、"Source" の横にある数字はインデックスです。

	![詳細エディター - インデックス](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)


Excel 2013 を使用している場合、[Microsoft Power Query for Excel](https://www.microsoft.com/download/details.aspx?id=39379) を使用してインデックスを取得します。詳細については、「[Web ページに接続する](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8)」を参照してください。[Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/) の使用時と手順は同じです。

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## パフォーマンスとチューニング  
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

<!---HONumber=AcomDC_0615_2016-->