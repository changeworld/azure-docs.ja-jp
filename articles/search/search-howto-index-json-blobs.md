<properties
pageTitle="Azure Search BLOB インデクサーを使用した JSON BLOB のインデックス作成"
description="Azure Search を使用して JSON BLOB のインデックスを作成する方法について説明します"
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
ms.date="04/20/2016"
ms.author="eugenesh" />

# Azure Search BLOB インデクサーを使用した JSON BLOB のインデックス作成 

[Azure Search BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)は、既定では JSON BLOB を 1 つのテキスト チャンクとして解析します。多くの場合、JSON ドキュメントの構造はそのままに維持する必要があります。たとえば、次のような JSON ドキュメントについて考えてみましょう。

	{ 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

この場合は、JSON ドキュメントを解析して検索インデックスの "text"、"datePublished"、"tags" の各フィールドにする必要があります。

この記事では、Azure Search BLOB インデクサーを JSON 解析用に構成する方法を示します。インデックス作成にご活用ください。

> [AZURE.IMPORTANT] 現在この機能はプレビュー版です。バージョン **2015-02-28-Preview** を使用した REST API でのみ利用できます。プレビュー版の API は、テストと評価を目的としたものです。運用環境での使用は避けてください。

## JSON インデックス作成の設定

JSON BLOB のインデックスを作成するには、BLOB インデクサーを "JSON 解析" モードで使用します。インデクサー定義の `parameters` プロパティで `useJsonParser` 構成設定を有効にします。

	{
	  "name" : "my-json-indexer",
	  ... other indexer properties
	  "parameters" : { "configuration" : { "useJsonParser" : true } }
	}

必要に応じて**フィールド マッピング**を使用し、ターゲットの検索インデックスへの設定に使用するソース JSON ドキュメントのプロパティを選択します。これについては、以下で詳しく説明します。

> [AZURE.IMPORTANT] JSON 解析モードを使用すると、Azure Search ではデータ ソース内のすべての BLOB が JSON になると見なされます。JSON BLOB と JSON 以外の BLOB が混在するデータ ソースをサポートする必要がある場合は、[UserVoice のサイト](https://feedback.azure.com/forums/263029-azure-search)でお知らせください。

## フィールド マッピングを使用して検索ドキュメントを作成する 

現在、Azure Search ではプリミティブ データ型、文字列配列、GeoJSON ポイントのみをサポートしているため、任意の JSON ドキュメントに対して直接インデックス作成を行うことはできません。ただし、**フィールド マッピング**を使用すると、JSON ドキュメントの一部を選択して、それを検索ドキュメントの最上位レベルのフィールドに "引き上げる" ことができます。フィールド マッピングの基本については、「[データ ソースと検索インデックスの橋渡し役としての Azure Search インデクサー フィールド マッピング](search-indexer-field-mappings.md)」を参照してください。

JSON ドキュメントの例に戻りましょう。

    { 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

Edm.String 型の `text` フィールド、Edm.DateTimeOffset 型の `date` フィールド、コレクション (Edm.String) 型の `tags` フィールドを持つ検索インデックスがあるとします。JSON を必要な形式にマッピングするには、次のフィールド マッピングを使用します。

	"fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	]

マッピング内のソース フィールド名は、[JSON ポインター](http://tools.ietf.org/html/rfc6901)の表記を使用して指定されています。スラッシュから開始して JSON ドキュメントのルートを参照し、その後はスラッシュ区切りのパスで目的のプロパティ (任意の入れ子レベル) まで指定します。

0 から始まるインデックスを使用して個々の配列要素を参照することもできます。たとえば、上の例から "tags" 配列の最初の要素を選択するには、次のようなフィールド マッピングを使用します。

	{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] フィールド マッピングのパス内のソース フィールド名が、JSON に存在しないプロパティを参照している場合、そのマッピングはエラーなしでスキップされます。これは、異なるスキーマを使用したドキュメントをサポートできるようにするためです (一般的なユース ケースです)。検証機能がないため、フィールド マッピングの仕様を入力するときは、入力ミスをしないように注意する必要があります。

JSON ドキュメントに単純な最上位レベルのプロパティのみが含まれる場合、フィールド マッピングは必要ありません。たとえば、次のような JSON の場合、最上位レベルのプロパティ "text"、"datePublished"、"tags" は検索インデックス内の対応する各フィールドに直接マッピングされます。
 
	{ 
	   "text" : "A hopefully useful article explaining how to parse JSON blobs",
	   "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
 	}

> [AZURE.NOTE] 現在の Azure Search では、1 つの JSON BLOB から 1 つの検索ドキュメントへの解析のみをサポートしています。BLOB に JSON 配列が含まれ、それを複数の検索ドキュメントに解析する必要がある場合は、機能改善が優先的に行われるように、[この UserVoice の提案](https://feedback.azure.com/forums/263029-azure-search/suggestions/13431384-parse-blob-containing-a-json-array-into-multiple-d)に投票してください。

## 要求例

すべてをまとめた完全なペイロード例を次に示します。

データソース:

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "my-blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "optional, my-folder" }
	}   

インデクサー:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-json-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	  ]
	}

## Azure Search の品質向上にご協力ください

ご希望の機能や品質向上のアイデアがありましたら、[UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search/)にぜひお寄せください。

<!---HONumber=AcomDC_0518_2016-->