<properties
pageTitle="Azure Table Storage のインデックスを Azure Search で作成する"
description="Azure Search で Azure テーブルに格納されたデータのインデックスを作成する方法について説明します"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="08/16/2016"
ms.author="eugenesh" />

# Azure Table Storage のインデックスを Azure Search で作成する

この記事では、Azure Search を使用して、Azure Table Storage に格納されているデータのインデックスを使用する方法を示します。Azure Search の新しいテーブル インデクサーを使用すると、迅速かつシームレスに作成できます。

> [AZURE.IMPORTANT] 現在この機能はプレビュー版です。バージョン **2015-02-28-Preview** とバージョン 2.0-preview の .NET SDK を使用する REST API でのみ利用できます。プレビュー版の API は、テストと評価を目的としたものです。運用環境での使用は避けてください。

## Azure テーブルのインデックス作成の設定

Azure テーブルのインデクサーの設定と構成は、「[インデクサーの操作](https://msdn.microsoft.com/library/azure/dn946891.aspx)」の説明に従い、Azure Search REST API を使用して**インデクサー**と**データ ソース**を作成、管理することによって行います。[バージョン 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) の .NET SDK を使用することもできます。今後、Azure ポータルにテーブル インデックス作成機能が追加される予定です。

データ ソースでは、インデックスを作成するデータ、データにアクセスするために必要な資格情報、および Azure Search がデータの変更 (新しい行、変更された行、削除された行) を効率よく識別できるようにするポリシーを指定します。

インデクサーはデータ ソースからデータを読み取って、対象となる検索インデックスに読み込みます。

テーブル インデックスを設定するには、次の手順に従います。

1. データ ソースを作成する
	- `type` パラメーターを `azuretable` に設定します。
	- ストレージ アカウントの接続文字列を `credentials.connectionString` パラメーターとして指定します。
	- `container.name` パラメーターを使用して、テーブル名を指定します。
	- 必要に応じて `container.query` パラメーターを使用して、クエリを指定します。可能な場合は、PartitionKey でフィルターを使用して、パフォーマンスを最大限に高めます。他のクエリを実行すると、フル テーブル スキャンが発生し、大きなテーブルの場合はパフォーマンスが低下する可能性があります。
2. インデックスを作成するテーブルの列に対応するスキーマを使用して検索インデックスを作成します。
3. データ ソースを検索インデックスに接続することによって、インデクサーを作成します。

### データ ソースの作成

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "table-datasource",
	    "type" : "azuretable",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
	}   

データ ソース作成 API の詳細については、「[データ ソースの作成](search-api-indexers-2015-02-28-preview.md#create-data-source)」をご覧ください。

### インデックスの作成 

	POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/json
	api-key: [admin key]

	{
  		"name" : "my-target-index",
  		"fields": [
    		{ "name": "key", "type": "Edm.String", "key": true, "searchable": false },
    		{ "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
  		]
	}

インデックス作成 API の詳細については、「[Create Index (インデックスの作成)](https://msdn.microsoft.com/library/dn798941.aspx)」をご覧ください。

### インデクサーの作成 

最後に、データ ソースとターゲット インデックスとを参照するインデクサーを作成します。次に例を示します。

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "table-indexer",
	  "dataSourceName" : "table-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}

インデクサー作成 API の詳細については、「[インデクサーの作成](search-api-indexers-2015-02-28-preview.md#create-indexer)」をご覧ください。

これですべて完了です。是非お試しください。

## さまざまなフィールド名の操作

既存のインデックス内のフィールド名が、テーブルのプロパティ名と異なることは少なくありません。テーブルのプロパティ名は、**フィールド マッピング**を使用して、検索インデックス内のフィールド名に対応付けることができます。フィールド マッピングの詳細については、「[データ ソースと検索インデックスの橋渡し役としての Azure Search インデクサー フィールド マッピング](search-indexer-field-mappings.md)」を参照してください。

## ドキュメント キーの処理

Azure Search では、ドキュメントがそのキーによって一意に識別されます。それぞれの検索インデックスには、`Edm.String` 型のキー フィールドが 1 つだけ必要です。キー フィールドは、インデックスに追加するドキュメントごとに必要です (実際のところ、これは唯一の必須フィールドです)。

テーブル行には複合キーがあるため、Azure Search では、パーティション キーと行キーの値が連結された `Key` と呼ばれる合成フィールドが生成されます。たとえば、行の PartitionKey が `PK1` で、RowKey が `RK1` の場合、`Key` フィールドの値は `PK1RK1` になります。

> [AZURE.NOTE] `Key` 値には、ドキュメント キーでは無効な文字、たとえばダッシュを含めることができます。無効な文字を扱うには、`base64Encode` [フィールド マッピング関数](search-indexer-field-mappings.md#base64EncodeFunction)を使用します。これを行う場合は、Lookup などの API 呼び出しでドキュメント キーを渡す際に、必ず URL の安全な Base64 エンコードを使用する点にも注意してください。

## インデックスの増分作成と削除の検出
 
スケジュールに従って実行するようにテーブルのインデクサーを設定すると、行の `Timestamp` 値に従って、新しい行または更新された行のみでインデックスが再作成されます。変更検出ポリシーを独自に指定する必要はありません。インデックスの増分作成は自動的に有効になります。

特定のドキュメントをインデックスから削除するよう指定する場合は、論理削除方式を使用できます。つまり、行を削除するのではなく、その行が削除されたことを示すためのプロパティを追加し、データソースで論理削除の検出ポリシーを設定します。たとえば、以下に示すポリシーでは、プロパティ `IsDeleted` の値が `"true"` であるとき、行が削除されたと見なされます。

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]
	
	{
	    "name" : "my-table-datasource",
	    "type" : "azuretable",
	    "credentials" : { "connectionString" : "<your storage connection string>" },
	    "container" : { "name" : "table name", "query" : "query" },
	    "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
	}   


## Azure Search の品質向上にご協力ください

ご希望の機能や品質向上のアイデアがありましたら、[UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search/)にぜひお寄せください。

<!---HONumber=AcomDC_0817_2016-->