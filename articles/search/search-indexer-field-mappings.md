---
title: インデクサーのフィールド マッピング
titleSuffix: Azure Cognitive Search
description: フィールド名とデータ表現の間の違いを調整するようにインデクサーでフィールド マッピングを構成します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 9fdff284d3b183ef9a0589d9dee4c568249b854b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238006"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Azure Cognitive Search インデクサーを使用したフィールドのマッピングと変換

![インデクサーのステージ](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "インデクサーのステージ")

Azure Cognitive Search のインデクサーを使用すると、インデクサーにより、フィールド名と型が対応しているという想定の下に、データ ソース内のフィールドがターゲット インデックスのフィールドに自動的にマップされます。 場合によっては、入力データがターゲット インデックスのスキーマと完全に一致していないことがあります。 1 つの解決策は、"*フィールド マッピング*" を使用して、インデックス作成プロセスの間にデータ パスを明示的に設定することです。

フィールド マッピングを使うと、次のシナリオに対応できます。

+ 一致しないフィールド名。 データ ソースに `_id` という名前のフィールドがあるとします。 Azure Cognitive Search ではアンダースコアで始まるフィールド名が許可されていないとすると、フィールド マッピングを使ってフィールドの名前を効果的に変更できます。

+ 1 つのフィールドを複数のフィールドにする。 データ ソースの同じデータを、インデックスの複数のフィールドに設定できます。 たとえば、各フィールドに異なるアナライザーを適用することができます。

+ 複数のフィールドを 1 つのフィールドにする。 インデックス フィールドに複数のデータ ソースのデータを入力し、データ ソースはそれぞれ異なるフィールド名が使用されています。

+ Base64 エンコードまたはデータのデコード。 フィールド マッピングでは、Base64 エンコードやデコードの関数など、複数の [**マッピング関数**](#mappingFunctions)がサポートされています。

+ 文字列コレクションへの文字列の分割または JSON 配列の再キャスト。 この機能は、フィールド マッピング関数で提供されます。

インデクサーのフィールド マッピングは、データ フィールドをインデックス フィールドにマップする簡単な方法であり、軽量のデータ変換の機能も備えています。 より複雑なデータでは、インデックス作成に適した形式に変換するための前処理が必要になる場合があります。 1 つのオプションとして [Azure Data Factory](../data-factory/index.yml) を検討することができます。

> [!NOTE]
> フィールド マッピングは、検索インデックスにのみ適用されます。 [ナレッジ ストア](knowledge-store-concept-intro.md)も作成するインデクサーの場合は、データ シェイプとプロジェクションによってフィールドの関連付けが決まり、インデクサーでのフィールド マッピングと出力フィールド マッピングは無視されます。

## <a name="set-up-field-mappings"></a>フィールド マッピングの設定

フィールド マッピングは、3 つの部分で構成されます。

+ "sourceFieldName"。データ ソース内のフィールドを表します。 このプロパティは必須です。
+ "targetFieldName" (省略可能)。検索インデックス内のフィールドを表します。 省略した場合、"sourceFieldName" の値がターゲットに使用されます。
+ "mappingFunction" (省略可能)。複数の[定義済み関数](#mappingFunctions)のいずれかを使用して、データを変換できます。 これは入力と出力の両方のフィールド マッピングに適用できます。

フィールド マッピングは、インデクサー定義の "fieldMappings" 配列に追加されます。

> [!NOTE]
> フィールド マッピングがない場合、インデクサーでは、データ ソースのフィールドを同じ名前のインデックス フィールドにマップする必要があると想定されます。 フィールド マッピングを追加すると、ソースとターゲットのフィールドに対するこれらの既定のフィールド マッピングはオーバーライドされます。 [Blob Storage インデクサー](search-howto-indexing-azure-blob-storage.md)などの一部のインデクサーを使用すると、インデックス キー フィールドに対して既定のフィールド マッピングが追加されます。

## <a name="map-fields-using-rest"></a>REST を使用してフィールドをマップする

フィールド マッピングは、[インデクサーの作成](/rest/api/searchservice/create-Indexer) API 要求を使用して新しいインデクサーを作成するときに追加できます。 [インデクサーの更新](/rest/api/searchservice/update-indexer) API 要求を使用して、既存のインデクサーのフィールド マッピングを管理できます。

たとえば、ソース フィールドを別の名前のターゲット フィールドにマップする方法は次のとおりです。

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

ソース フィールドは複数のフィールド マッピングで参照できます。 次の例は、同じソース フィールドを 2 つの異なるインデックス フィールドにコピーしてフィールドを "分岐させる" 方法を示しています。

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Cognitive Search では、大文字と小文字が区別されない比較を使用して、フィールド マッピングのフィールドと関数名を解決します。 これは便利ですが (大文字と小文字を区別する必要がないため)、同時に、データ ソースまたはインデックスが、大文字と小文字のみが異なるフィールドを持つことができないことを意味します。  
>

## <a name="map-fields-using-net"></a>.NET を使用してフィールドをマップする

プロパティ "SourceFieldName" と "TargetFieldName" を持つ [FieldMapping](/dotnet/api/azure.search.documents.indexes.models.fieldmapping) クラスと、省略可能な "MappingFunction" 参照を使用して、.NET SDK でのフィールド マッピングを定義できます。

インデクサーを作成するとき、または後で [SearchIndexer.FieldMappings](/dotnet/api/azure.search.documents.indexes.models.searchindexer.fieldmappings) を直接設定することによって、フィールド マッピングを指定できます。

次の C# の例では、インデクサーを作成するときにフィールド マッピングを設定します。

```csharp
var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "SQL data indexer",
    Schedule = schedule,
    Parameters = parameters,
    FieldMappings =
    {
        new FieldMapping("_id") {TargetFieldName = "HotelId", FieldMappingFunction.Base64Encode()},
        new FieldMapping("Amenities") {TargetFieldName = "Tags"}
    }
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>フィールド マッピング関数

フィールド マッピング関数によって、インデックスに格納される前にフィールドのコンテンツが変換されます。 現在、以下のマッピング関数がサポートされています。

+ [base64Encode](#base64EncodeFunction)
+ [base64Decode](#base64DecodeFunction)
+ [extractTokenAtPosition](#extractTokenAtPositionFunction)
+ [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
+ [urlEncode](#urlEncodeFunction)
+ [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode 関数

入力文字列の *URL の安全な* Base64 エンコードを実行します。 入力は UTF-8 でエンコードされていることを前提としています。

#### <a name="example---document-key-lookup"></a>例 - ドキュメント キーの検索

Azure Cognitive Search ドキュメント キーには、URL で使用できる文字のみを使用できます ([Lookup API](/rest/api/searchservice/lookup-document) を使用してドキュメントのアドレスを指定できるように)。 URL の安全ではない文字がキーのソース フィールドに含まれている場合は、インデックス作成時に `base64Encode` 関数を使用して変換できます。 ただし、ドキュメント キー (変換前と変換後の両方) を 1024 文字より長くすることはできません。

検索時にエンコードされたキーを取得するときは、`base64Decode` 関数を使用して元のキー値を取得し、それを使用してソース ドキュメントを取得します。

```JSON
"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

#### <a name="example---preserve-original-values"></a>例 - 元の値を保持する

フィールド マッピングが指定されていない場合、[Blob Storage インデクサー](search-howto-indexing-azure-blob-storage.md)によって自動的に `metadata_storage_path`(BLOB の URI) からインデックス キー フィールドにフィールド マッピングが追加されます。 この値は Base64 によってエンコードされているため、Azure Cognitive Search ドキュメント キーとして安全に使用できます。 次の例では、*URL が安全な* Base64 によってエンコードされたバージョンの `metadata_storage_path` を `index_key` フィールドにマップして、同時に、`metadata_storage_path` フィールドで元の値を保持する方法を示します。

```JSON
"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

マッピング関数に parameters プロパティを含めない場合、既定で値 `{"useHttpServerUtilityUrlTokenEncode" : true}` になります。

Azure Cognitive Search では、2 つの異なる Base64 エンコードがサポートされています。 同じフィールドをエンコードおよびデコードするときは、同じパラメーターを使用する必要があります。 詳細については、[base64 エンコード オプション](#base64details)に関するページを参照して使用するパラメーターを決定します。

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode 関数

入力文字列の Base64 デコードを実行します。 入力値は *URL 対応* の Base64 でエンコードされた文字列と想定されます。

#### <a name="example---decode-blob-metadata-or-urls"></a>例 - BLOB メタデータまたは URL をデコードする

ソース データには、BLOB メタデータ文字列や Web URL など、プレーン テキストとして検索できるようにする Base64 エンコード文字列が含まれている場合があります。 検索インデックスを設定するときに、`base64Decode` 関数を使用して、エンコードされたデータを通常の文字列に戻すことができます。

```JSON
"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

parameters プロパティを含めない場合、既定で値 `{"useHttpServerUtilityUrlTokenEncode" : true}` になります。

Azure Cognitive Search では、2 つの異なる Base64 エンコードがサポートされています。 同じフィールドをエンコードおよびデコードするときは、同じパラメーターを使用する必要があります。 詳細については、「[base64 エンコード オプション](#base64details)」を参照して使用するパラメーターを決定します。

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 エンコード オプション

Azure Cognitive Search では、URL セーフな base64 エンコードと通常の base64 エンコードがサポートされています。 インデックス作成中に base64 でエンコードされた文字列は、後で同じエンコード オプションでデコードする必要があります。そうしないと、結果が元の文字列と一致しなくなります。

それぞれがエンコードとデコードに対応する `useHttpServerUtilityUrlTokenEncode` パラメーターまたは `useHttpServerUtilityUrlTokenDecode` パラメーターが `true` に設定されると、`base64Encode` は [HttpServerUtility.UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) のように、`base64Decode` は [HttpServerUtility.UrlTokenDecode](/dotnet/api/system.web.httpserverutility.urltokendecode) のように動作します。

> [!WARNING]
> キー値を生成するために `base64Encode` を使用する場合は、`useHttpServerUtilityUrlTokenEncode` を true に設定する必要があります。 キー値に使用できるのは、URL セーフな base64 エンコードのみです。 キー値の文字に関するすべての制限事項については、[名前付け規則](/rest/api/searchservice/naming-rules)に関する記事をご覧ください。

Azure Cognitive Search の .NET ライブラリでは、組み込みのエンコードを提供する完全な .NET Framework が前提になっています。 `useHttpServerUtilityUrlTokenEncode` と `useHttpServerUtilityUrlTokenDecode` オプションは、この組み込み機能を利用します。 .NET Core または別のフレームワークを使用している場合は、これらのオプションを `false` に設定し、フレームワークのエンコードおよびデコード関数を直接呼び出すことをお勧めします。

次の表では、文字列 `00>00?00` の異なる base64 エンコードを比較しています。 base64 関数に必要な追加処理 (ある場合) を判断するには、ライブラリ エンコード関数を文字列 `00>00?00` に適用し、出力を想定出力 `MDA-MDA_MDA` と比較します。

| エンコード | base64 エンコード出力 | ライブラリ エンコード後の追加処理 | ライブラリ デコード前の追加処理 |
| --- | --- | --- | --- |
| パディング付きの base64 | `MDA+MDA/MDA=` | URL で使用できる文字を使用し、パディングを削除する | 標準 base64 文字を使用し、パディングを追加する |
| パディングなしの base64 | `MDA+MDA/MDA` | URL で使用できる文字を使用する | 標準 base64 文字を使用する |
| パディング付きの URL 対応 base64 | `MDA-MDA_MDA=` | パディングを削除する | パディングを追加する |
| パディングなしの URL 対応 base64 | `MDA-MDA_MDA` | なし | なし |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition 関数

指定された区切り記号を使用して文字列フィールドを分割し、結果として得られる分割の指定位置でトークンを取得します。

この関数は以下のパラメーターを使用します。

+ `delimiter`: 入力文字列を分割するときに区切り記号として使用する文字列。
+ `position`: 入力文字列の分割後に取得するトークンの整数の 0 から始まる位置。

たとえば、入力が `Jane Doe`、`delimiter` が `" "` (空白)、`position` が 0 の場合、結果は `Jane` になり、`position` が 1 の場合、結果は `Doe` になります。 位置が、存在しないトークンを参照する場合、エラーが返されます。

#### <a name="example---extract-a-name"></a>例 - 名前を抽出する

データ ソースに `PersonName` フィールドが含まれ、それを 2 つの別々の `FirstName` および `LastName` フィールドとしてインデックスする必要がある場合、 この関数を使用して、空白文字を区切り記号として使って入力を分割できます。

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

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection 関数

文字列の JSON 配列として書式設定された文字列を、インデックス内の `Collection(Edm.String)` フィールドの入力に使用できる文字列配列に変換します。

たとえば、入力文字列が `["red", "white", "blue"]` の場合、`Collection(Edm.String)` 型のターゲット フィールドに `red`、`white`、`blue` の 3 つの値が設定されます。 JSON 文字列配列として解析できない入力値では、エラーが返されます。

#### <a name="example---populate-collection-from-relational-data"></a>例 - リレーショナル データからコレクションを設定する

Azure SQL Database には、Azure Cognitive Search の `Collection(Edm.String)` のフィールドに自然にマップされる組み込みのデータ型がありません。 文字列収集フィールドを設定するには、ソース データを JSON 文字列配列として前処理してから、`jsonArrayToStringCollection` マッピング関数を使用することができます。

```JSON
"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode 関数

この関数を使用すると、"URL セーフ" になるように文字列をエンコードできます。 URL 内で許可されていない文字を含む文字列と共に使用する場合、この関数では、これらの "安全でない" 文字が等価の文字エンティティに変換されます。 この関数では、UTF-8 エンコード形式が使用されます。

#### <a name="example---document-key-lookup"></a>例 - ドキュメント キーの検索

`urlEncode` 関数は、他の文字をそのままにして、URL の安全でない文字だけを変換する場合に、`base64Encode` 関数の代わりに使用できます。

たとえば、入力文字列が `<hello>` の場合、`(Edm.String)` 型のターゲット フィールドに値 `%3chello%3e`が設定されます

検索時にエンコードされたキーを取得すると、`urlDecode` 関数を使用して元のキー値を取得し、それを使用してソース ドキュメントを取得できます。

```JSON
"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

### <a name="urldecode-function"></a>urlDecode 関数

 この関数では、UTF-8 エンコード形式を使用して、URL エンコードされた文字列がデコードされた文字列に変換されます。

### <a name="example---decode-blob-metadata"></a>例 - BLOB メタデータをデコードする

 一部の Azure ストレージ クライアントでは、ASCII 以外の文字が含まれている場合に、BLOB メタデータが自動的に URL エンコードされます。 ただし、このようなメタデータを (プレーンテキストとして) 検索可能にする場合は、`urlDecode` 関数を使用して、検索インデックスを設定する際に、エンコードされたデータを通常の文字列に戻すことがきます。

 ```JSON
"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
### <a name="fixedlengthencode-function"></a>fixedLengthEncode 関数
 
 この関数は、任意の長さの文字列を固定長文字列に変換します。
 
### <a name="example---map-document-keys-that-are-too-long"></a>例 - 長すぎるドキュメント キーをマップする
 
ドキュメント キーの長さが 1024 文字を超えていることを通知するエラーが発生した場合、この関数を適用して、ドキュメント キーの長さを短くすることができます。

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```