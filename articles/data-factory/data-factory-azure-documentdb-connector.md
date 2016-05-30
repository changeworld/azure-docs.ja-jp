<properties 
	pageTitle="DocumentDB との間でのデータの移動 | Microsoft Azure" 
	description="Azure Data Factory を使用して Azure DocumentDB コレクションに、または Azure DocumentDB コレクションからデータを移動する方法を説明します。" 
	services="data-factory, documentdb" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="multiple" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/09/2016" 
	ms.author="spelluru"/>

# Azure Data Factory を使用した DocumentDB との間でのデータの移動

この記事では、Azure Data Factory のコピー アクティビティを使用し、別のデータ ストアから Azure DocumentDB へのデータ移動および DocumentDB から別のデータ ストアへのデータ移動の方法について説明します。この記事は、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

次のサンプルは、Azure DocumentDB と Azure BLOB ストレージとの間でデータをコピーする方法を示します。ただし、Azure Data Factory のコピー アクティビティを使用して[ここ](data-factory-data-movement-activities.md#supported-data-stores)から開始したいずれかのシンクに、任意のソースからデータを**直接**コピーすることができます。


## サンプル: Azure DocumentDB から Azure BLOB にデータをコピーする

下のサンプルで確認できる要素:

1. [DocumentDB](#azure-documentdb-linked-service-properties) 型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。 
3. [DocumentDbCollection](#azure-documentdb-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。 
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4. [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) と [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルでは、Azure DocumentDB のデータが Azure BLOB にコピーされます。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure DocumentDB のリンクされたサービス:**

	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Azure BLOB ストレージのリンクされたサービス:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure Document DB 入力データセット:**

このサンプルでは、「**Person**」というコレクションが Azure DocumentDB データベースにあるものと想定しています。
 
「“external”: ”true”」を設定して externalData ポリシーを指定すると、テーブルが Data Factory に対して外部にあり、Data Factory のアクティビティでは生成されていないことが Azure Data Factory のサービスに通知されます。

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}


**Azure BLOB の出力データセット:**

データは、時間の粒度で特定の日時を反映している BLOB のパスの新しい BLOB に 1 時間ごとにコピーされます。

	{
	  "name": "PersonBlobTableOut",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

DocumentDB データベースの「Person」コレクションのサンプル JSON 文書:

	{
	  "PersonId": 2,
	  "Name": {
	    "First": "Jane",
	    "Middle": "",
	    "Last": "Doe"
	  }
	}

DocumentDB では、階層的な JSON 文書に SQL タイプの構文を使用し、文書にクエリを実行できます。

例: SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person

次のパイプラインは DocumentDB データベースの「Person」コレクションから Azure BLOB にデータをコピーします。コピー アクティビティの一部として、入力データセットと出力データセットが指定されています。
	
	{
	  "name": "DocDbToBlobPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "DocumentDbCollectionSource",
	            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
	            "nestingSeparator": "."
	          },
	          "sink": {
	            "type": "BlobSink",
	            "blobWriterAddHeader": true,
	            "writeBatchSize": 1000,
	            "writeBatchTimeout": "00:00:59"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonDocumentDbTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonBlobTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromDocDbToBlob"
	      }
	    ],
	    "start": "2015-04-01T00:00:00Z",
	    "end": "2015-04-02T00:00:00Z"
	  }
	}

## Sample: Azure BLOB から Azure DocumentDB にデータをコピーします。

下のサンプルで確認できる要素:

1. [DocumentDB](#azure-documentdb-linked-service-properties) 型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 型のリンクされたサービス。
3. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [DocumentDbCollection](#azure-documentdb-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。 
4. [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) と [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。


このサンプルでは、Azure BLOB から Azure DocumentDB にデータがコピーされます。これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure BLOB ストレージのリンクされたサービス:**
	
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure DocumentDB のリンクされたサービス:**
	
	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Azure BLOB の入力データセット:**

	{
	  "name": "PersonBlobTableIn",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "FirstName",
	        "type": "String"
	      },
	      {
	        "name": "MiddleName",
	        "type": "String"
	      },
	      {
	        "name": "LastName",
	        "type": "String"
	      }
	    ],
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "fileName": "input.csv",
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Azure DocumentDB 出力データセット:**

このサンプルでは、「Person」という名前のコレクションにデータをコピーします。

	{
	  "name": "PersonDocumentDbTableOut",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "Name.First",
	        "type": "String"
	      },
	      {
	        "name": "Name.Middle",
	        "type": "String"
	      },
	      {
	        "name": "Name.Last",
	        "type": "String"
	      }
	    ],
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

次のパイプラインは Azure BLOB から DocumentDB データベースの「Person」コレクションにデータをコピーします。コピー アクティビティの一部として、入力データセットと出力データセットが指定されています。
	
	{
	  "name": "BlobToDocDbPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "DocumentDbCollectionSink",
	            "nestingSeparator": ".",
	            "writeBatchSize": 2,
	            "writeBatchTimeout": "00:00:00"
	          }
	          "translator": {
	              "type": "TabularTranslator",
	              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonBlobTableIn"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonDocumentDbTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromBlobToDocDb"
	      }
	    ],
	    "start": "2015-04-14T00:00:00Z",
	    "end": "2015-04-15T00:00:00Z"
	  }
	}
 
サンプル BLOB 入力が次の場合

	1,John,,Doe

DocumentDB の JSON は次のようになります。

	{
	  "Id": 1,
	  "Name": {
	    "First": "John",
	    "Middle": null,
	    "Last": "Doe"
	  },
	  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
	}
	
DocumentDB は JSON 文書の NoSQL ストアであり、入れ子構造が許可されます。Azure Data Factory を利用すると、**nestingSeparator** で階層を示すことができます。この例では「.」です。区切り記号により、コピー アクティビティで「Name」オブジェクトが 3 つの子要素 (First、Middle、Last) で生成されます。これはテーブル定義の「Name.First」、「Name.Middle」、「Name.Last」に基づきます。

## Azure DocumentDB のリンクされたサービスのプロパティ

次の表は、Azure DocumentDB のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| **プロパティ** | **説明** | **必須** |
| -------- | ----------- | --------- |
| type | type プロパティを **DocumentDb** に設定する必要があります。 | あり |
| connectionString | Azure DocumentDB データベースに接続するために必要な情報を指定します。 | あり |

## Azure DocumentDB データベースの type プロパティ

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。データセット JSON の構造、可用性、ポリシーなどのセクションはすべてのデータセット型 (Azure SQL、Azure BLOB、Azure テーブルなど) で同じです。
 
typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。**DocumentDbCollection** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| **プロパティ** | **説明** | **必須** |
| -------- | ----------- | -------- |
| collectionName | DocumentDB ドキュメント コレクションの名前です。 | あり |


例:

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

### Data Factory によるスキーマ
DocumentDB などのスキーマのないデータ ストアの場合、Data Factory サービスは次のいずれかの方法でスキーマを推論します。

1.	データセット定義で **structure** プロパティを使用してデータの構造を指定した場合、Data Factory サービスはスキーマとしてこの構造を優先します。この場合、行に列の値が含まれていないと、null 値が指定されます。
2.	データセット定義で **structure** プロパティを使用してデータの構造を指定しなかった場合、Data Factory サービスはデータの最初の行を使用してスキーマを推論します。この場合、最初の行に完全なスキーマが含まれていないと、コピー操作の結果で一部の行が欠落します。

したがって、スキーマのないデータ ソースでは、**structure** プロパティを使用してデータの構造を指定するのがベスト プラクティスです。

## Azure DocumentDB のコピー アクティビティの type プロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティがあらゆる種類のアクティビティで利用できます。
 
**注:** コピー アクティビティは入力を 1 つだけ受け取り、出力を 1 つだけ生成します。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、source と sink の種類によって異なります。

コピー アクティビティで、source の種類が **DocumentDbCollectionSource** である場合は、**typeProperties** セクションで次のプロパティを使用できます。

| **プロパティ** | **説明** | **使用できる値** | **必須** |
| ------------ | --------------- | ------------------ | ------------ |
| query | データを読み取るためのクエリを指定します。 | DocumentDB でサポートされているクエリ文字列。<br/><br/>例: SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > "2009-01-01T00:00:00" | いいえ <br/><br/>指定されていない場合に実行される SQL ステートメント: select <columns defined in structure> from mycollection 
| nestingSeparator | ドキュメントが入れ子であることを示す特殊文字 | 任意の文字。<br/><br/>DocumentDB は JSON 文書の NoSQL ストアであり、入れ子構造が許可されます。Azure Data Factory を利用すると、nestingSeparator で階層が示されます。上記の例では「.」です。区切り記号により、コピー アクティビティで「Name」オブジェクトが 3 つの子要素 (First、Middle、Last) で生成されます。これはテーブル定義の「Name.First」、「Name.Middle」、「Name.Last」に基づきます。 | いいえ

**DocumentDbCollectionSink** では次のプロパティがサポートされます。

| **プロパティ** | **説明** | **使用できる値** | **必須** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | 入れ子になった文書が必要であることを示すソース列名の特殊文字。<br/><br/>上記の例の場合: 出力テーブルの Name.First は DocumentDB 文書で次の JSON 構造を生成します。<br/><br/>"Name": {<br/> "First": "John"<br/>}, | 入れ子レベルの分割に使用される文字。<br/><br/>既定値は . (ドット) です。 | 入れ子レベルの分割に使用される文字。<br/><br/>既定値は . (ドット) です。 | いいえ | 
| writeBatchSize | DocumentDB サービスに文書の作成を要求する並列要求の数。<br/><br/>このプロパティを利用し、DocumentDB との間でコピーするときのパフォーマンスを微調整できます。writeBatchSize を増やすとパフォーマンスが良くなります。DocumentDB に送信される並列要求の数が増えるためです。ただし、スロットルは回避する必要があります。「Request rate is large」というエラー メッセージをスローする可能性があります。<br/><br/>スロットルは、文書のサイズ、文書内の用語の数、ターゲット コレクションの索引作成ポリシーなど、さまざまな要因により決定されます。コピー操作の場合、もっとよいコレクションを利用し (S3 など)、最大のスループットを得ることができます (毎秒 2,500 要求単位)。 | 整数値 | いいえ |
| writeBatchTimeout | タイムアウトする前に操作の完了を待つ時間です。 | (単位 = 時間) 例: “00:30:00” (30 分) | いいえ |
 
## 付録
1. **質問:** コピー アクティビティは、既存のレコードの更新をサポートしていますか?

	**回答:** いいえ。

2. **質問:** DocumentDB へのコピーを再試行すると、既にコピーしたレコードはどのように扱われますか?

	**回答:** レコードに "ID" フィールドがあり、コピー操作で同じ ID のレコードが挿入される場合、そのコピー操作はエラーをスローします。
 
3. **質問:** Data Factory は、[範囲またはハッシュ ベースのデータのパーティション分割](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)をサポートしていますか?

	**回答:** いいえ。 
4. **質問:** 1 つのテーブルに複数の DocumentDB コレクションを指定できますか?
	
	**回答:** いいえ。現時点では、1 つのコレクションだけを指定できます。
     
## パフォーマンスとチューニング  
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

<!---HONumber=AcomDC_0518_2016-->