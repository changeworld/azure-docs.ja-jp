<properties
pageTitle="データ ソースと検索インデックスの橋渡し役としての Azure Search インデクサー フィールド マッピング"
description="フィールド名とデータ表現の間の違いを調整するよう Azure Search インデクサー フィールド マッピングを構成する"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="04/30/2016"
ms.author="eugenesh" />

# データ ソースと検索インデックスの橋渡し役としての Azure Search インデクサー フィールド マッピング

Azure Search インデクサーを使用する際、入力データがターゲット インデックスのスキーマと大きく異なっているのに気づく場合があります。このような場合に、**フィールド マッピング**を使用してデータを必要な形式に変換できます。

フィールド マッピングが役立つシナリオは次のとおりです。
 
- データ ソースにフィールド `_id` があるが、Azure Search でフィールド名をアンダースコアで開始できない場合。フィールド マッピングにより、フィールドの "名前を変更" できます。 
- インデックスのいくつかのフィールドに同じデータ ソースのデータを設定する必要がある場合。それらのフィールドに個別のアナライザーを適用する必要がある場合など。フィールド マッピングにより、データ ソース フィールドを "フォーク" できます。
- Base64 エンコードまたはデータのデコードが必要な場合。フィールド マッピングは、いくつかの**マッピング関数**をサポートしています。これには、Base64 エンコードおよびデコードの関数などがあります。   


> [AZURE.IMPORTANT] 現在、フィールド マッピング機能はプレビュー版です。バージョン **2015-02-28-Preview** を使用した REST API でのみ利用できます。プレビュー版の API は、テストと評価を目的としたものです。運用環境での使用は避けてください。

## フィールド マッピングの設定

フィールド マッピングは、[インデクサーの作成](search-api-indexers-2015-02-28-preview.md#create-indexer) API を使用して新しいインデクサーを作成するときに追加できます。フィールド マッピングは、[インデクサーの更新](search-api-indexers-2015-02-28-preview.md#update-indexer) API を使用してインデックス作成インデクサーで管理できます。

フィールド マッピングは、次の 3 つの部分で構成されます。

1. `sourceFieldName`。データ ソース内のフィールドを表します。このプロパティは必須です。 

2. `targetFieldName` (省略可能)。検索インデックス内のフィールドを表します。省略すると、データ ソースと同じ名前が使用されます。

3. `mappingFunction` (省略可能)。定義済みのいずれかの関数を使用してデータを変換できます。関数の完全な一覧については、[以下](#mappingFunctions)をご覧ください。

フィールド マッピングは、インデクサー定義の `fieldMappings` 配列に追加されます。

たとえば、フィールド名間の違いを調整する方法は以下のとおりです。

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
} 
```

インデクサーは、複数のフィールド マッピングを持つことができます。たとえば、フィールドを "フォーク" する方法を以下に示します。

```JSON

"fieldMappings" : [ 
	{ "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
	{ "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }, 
] 
```

> [AZURE.NOTE] Azure Search は、大文字と小文字は区別されない比較を使用してフィールド マッピングのフィールドと関数名を解決します。これは便利ですが (大文字と小文字を区別する必要がないため)、同時に、データ ソースまたはインデックスが、大文字と小文字のみが異なるフィールドを持つことができないことを意味します。

<a name="mappingFunctions"></a>
## フィールド マッピング関数

現在サポートされている関数は次のとおりです。

- [base64Encode](#base64EncodeFunction)
- [base64Decode](#base64DecodeFunction)
- [extractTokenAtPosition](#extractTokenAtPositionFunction)
- [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>
### base64Encode 

入力文字列の *URL の安全な* Base64 エンコードを実行します。入力は UTF-8 でエンコードされていることを前提としています。

#### サンプル ユース ケース 

URL の安全な文字を Azure Search ドキュメント キーにのみ表示できます (お客様が参照 API を使用してドキュメントに対処する必要がある場合など)。データに URL の安全でない文字が含まれ、それを使用して検索インデックスにキー フィールドを設定する場合に、この関数を使用します。

#### 例 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Path", 
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" } 
  }] 
```

<a name="base64DecodeFunction"></a>
### base64Decode

入力文字列の Base64 デコードを実行します。入力値は *URL の安全な* Base64 でエンコードされた文字列と想定されます。

#### サンプル ユース ケース 

BLOB カスタム メタデータ値を、ASCII でエンコードする必要がある場合。Base64 エンコードを使用して、BLOB のカスタム メタデータ内の任意の Unicode 文字列を表すことができます。ただし、意味のある検索を行うために、検索インデックスを設定する際に、エンコードされたデータを "通常の" 文字列に戻すときにこの関数を使用できます。

#### 例 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Base64EncodedMetadata", 
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" } 
  }] 
```

<a name="extractTokenAtPositionFunction"></a>
### extractTokenAtPosition

指定された区切り記号を使用して文字列フィールドを分割し、結果として得られる分割の指定位置でトークンを取得します。

たとえば、入力が `Jane Doe`、`delimiter` が `" "` (空白)、`position` が 0 の場合、結果は `Jane` になり、`position` が 1 の場合、結果は `Doe` になります。位置が、存在しないトークンを参照する場合、エラーが返されます。

#### サンプル ユース ケース 

データ ソースに `PersonName` フィールドが含まれ、それを 2 つの別々の `FirstName` および `LastName` フィールドとしてインデックスする必要がある場合。この関数を使用して、空白文字を区切り記号として使って入力を分割できます。

#### パラメーター

- `delimiter`: 入力文字列を分割するときに区切り記号として使用する文字列。
- `position`: 入力文字列の分割後に取得するトークンの整数の 0 から始まる位置。    

#### 例

```JSON 

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } } 
  }, 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } } 
  }] 
```

<a name="jsonArrayToStringCollectionFunction"></a>
### jsonArrayToStringCollection

文字列の JSON 配列として書式設定された文字列を、インデックス内の `Collection(Edm.String)` フィールドの入力に使用できる文字列配列に変換します。

たとえば、入力文字列が `["red", "white", "blue"]` の場合、`Collection(Edm.String)` 型のターゲット フィールドに `red`、`white`、`blue` の 3 つの値が入力されます。JSON 文字列配列として解析できない入力値では、エラーが返されます。

#### サンプル ユース ケース

Azure SQL データベースには、Azure Search の `Collection(Edm.String)` のフィールドに自然にマップされる組み込みのデータ型がありません。文字列コレクション フィールドに値を入力するには、ソース データを JSON 文字列配列とし書式設定して、この関数を使用します。

#### 例 

```JSON

"fieldMappings" : [ 
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
] 
```

## Azure Search の品質向上にご協力ください

ご希望の機能や品質向上のアイデアがありましたら、[UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search/)にぜひお寄せください。

<!---HONumber=AcomDC_0504_2016-->