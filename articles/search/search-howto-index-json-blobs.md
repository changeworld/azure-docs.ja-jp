---
title: Azure Search BLOB インデクサーを使用した JSON BLOB のインデックス作成
description: Azure Search BLOB インデクサーを使用した JSON BLOB のインデックス作成
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: 5b4cd1c592c4cd965a0b5d9e4fb8eef84a6bea91
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003283"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Azure Search BLOB インデクサーを使用した JSON BLOB のインデックス作成
この記事では、Azure Blob Storage 内の JSON BLOB から構造化コンテンツを抽出するために Azure Search BLOB インデクサーを構成する方法を説明します。

Azure Blob Storage 内の JSON BLOB は通常、単一の JSON ドキュメントまたは JSON 配列のいずれかです。 Azure Search の BLOB インデクサーでは、要求で **parsingMode** パラメーターを設定する方法に応じて、構成を解析できます。

| JSON ドキュメント | parsingMode | 説明 | 可用性 |
|--------------|-------------|--------------|--------------|
| BLOB あたり 1 つ | `json` | JSON BLOB を 1 つのテキスト チャンクとして解析します。 各 JSON BLOB は、1 つの Azure Search ドキュメントになります。 | 一般に、[REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) と [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) 両方の API で利用できます。 |
| BLOB あたり複数 | `jsonArray` | 配列の各要素が別々の Azure Search ドキュメントになる、BLOB 内の JSON 配列を解析します。  | プレビュー版 ([REST api-version=`2017-11-11-Preview`](search-api-2017-11-11-preview.md) と [.NET SDK Preview](https://aka.ms/search-sdk-preview))。 |

> [!Note]
> プレビュー版の API は、テストと評価を目的としたものです。運用環境での使用は避けてください。
>

## <a name="setting-up-json-indexing"></a>JSON インデックス作成の設定
JSON BLOB のインデックス作成は、Azure Search のすべてのインデクサーに共通の 3 部構成ワークフローの通常のドキュメント抽出に似ています。

### <a name="step-1-create-a-data-source"></a>手順 1: データ ソースを作成する

最初の手順では、インデクサーで使用されるデータ ソース接続情報を指定します。 ここで `azureblob` として指定されるデータ ソースの種類によって、インデクサーによって呼び出されるデータ抽出動作が決まります。 JSON BLOB のインデックス作成では、データ ソース定義は JSON ドキュメントと配列の両方で同じです。 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>手順 2: ターゲット検索インデックスを作成する 

インデクサーは、インデックス スキーマとペアになります。 (ポータルではなく) API を使用する場合は、インデクサー操作で指定できるようにインデックスを事前に準備します。 

> [!Note]
> 一般公開された限られた数のインデクサーについて、インデクサーはポータルで **[インポート]** アクションを通じて公開されます。 多くの場合、インポート ワークフローでは、ソース内のメタデータに基づいて予備的なインデックスを作成できます。 詳しくは、「[ポータルで Azure Search にデータをインポート](search-import-data-portal.md)」をご覧ください。

### <a name="step-3-configure-and-run-the-indexer"></a>手順 3: インデクサーを構成して実行する

これまで、データ ソースとインデックスの定義は parsingMode に依存しませんでした。 しかし、手順 3. のインデクサー構成では、JSON BLOB コンテンツが Azure Search インデックスで解析および構成される方法に応じてパスが分岐します。

インデクサーを呼び出すときに、次の操作を行います。

+ **parsingMode** パラメーターを `json` に設定するか (各 BLOB を 1 つのドキュメントとしてインデックスを付ける場合)、または `jsonArray` に設定します (BLOB に JSON 配列が含まれ、配列の各要素を個別のドキュメントとして扱う必要がある場合)。

+ 必要に応じて **フィールド マッピング** を使用し、ターゲットの検索インデックスへの設定に使用するソース JSON ドキュメントのプロパティを選びます。 JSON 配列では、配列が下位レベルのプロパティとして存在する場合、BLOB 内の配列の配置場所を示すドキュメント ルートを設定できます。

> [!IMPORTANT]
> `json` または `jsonArray` 解析モードを使用すると、Azure Search は、データ ソース内のすべての BLOB が JSON を含んでいるとみなします。 JSON BLOB と JSON 以外の BLOB が混在するデータ ソースをサポートする必要がある場合は、[UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search) でお知らせください。


## <a name="how-to-parse-single-json-blobs"></a>1 つの JSON BLOB を解析する方法

[Azure Search BLOB インデクサー](search-howto-indexing-azure-blob-storage.md) は、既定では JSON BLOB を 1 つのテキスト チャンクとして解析します。 多くの場合、JSON ドキュメントの構造はそのままに維持する必要があります。 たとえば、Azure Blob Storage に次の JSON ドキュメントがあると仮定します。

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>1 つの JSON BLOB のインデクサー定義

Azure Search BLOB インデクサーを使用すると、前の例のような JSON ドキュメントは 1 つの Azure Search ドキュメントに解析されます。 インデクサーは、ソースの "text"、"datePublished"、"tags" を同じ名前と型のターゲット フィールドと照合してインデックスを読み込みます。

構成はインデクサー操作の本文で提供されます。 前に定義したデータ ソース オブジェクトは、データ ソースの種類と接続情報を指定することを思い出してください。 さらに、ターゲット インデックスがサービスに空のコンテナーとして存在する必要もあります。 schedule と parameters は省略可能ですが、これらを省略した場合、インデクサーは `json` を解析モードとして使用してすぐに実行されます。

完全に指定された要求は次のようになります。

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

前述のように、フィールド マッピングは必要ありません。 "text"、"datePublished、"tags" のフィールドでインデックスを指定すると、BLOB インデクサーは、要求にフィールド マッピングがない場合でも正しいマッピングを推測することができます。

## <a name="how-to-parse-json-arrays-preview"></a>JSON 配列を解析する方法 (プレビュー)

JSON 配列のプレビュー機能を選ぶこともできます。 この機能は、BLOB に "*JSON オブジェクトの配列*" が含まれ、各要素を個別の Azure Search ドキュメントにしたい場合に役立ちます。 たとえば、次の JSON BLOB では、それぞれ "id" および "text" フィールドを持つ 3 つの独立したドキュメントで Azure Search インデックスを作成できます。  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>JSON 配列のインデクサー定義

JSON 配列の場合、インデクサー要求ではプレビュー API と `jsonArray` パーサーを使用します。 JSON BLOB のインデックス作成では配列固有の要件が 2 つだけあります。

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

この場合も、フィールド マッピングは必要ありません。 "id" と "text" のフィールドでインデックスを指定すると、BLOB インデクサーは、フィールド マッピング リストがない場合でも正しいマッピングを推測することができます。

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>入れ子になった JSON 配列
JSON オブジェクトの配列にインデックスを作成するときに、そのドキュメント内のどこかで配列が入れ子になっていたらどうすればよいでしょうか。 `documentRoot` 構成プロパティを使用して、入れ子になった配列が格納されているプロパティを選択できます。 たとえば、次のような BLOB があるとします。

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

この構成を使用して、`level2` プロパティに格納されている配列にインデックスを付けます。

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>フィールド マッピングを使用して検索ドキュメントを作成する

ソースとターゲットのフィールドが完全には一致していない場合、要求本文のフィールド マッピング セクションでフィールド間の明示的なアソシエーションを定義できます。

現在、Azure Search ではプリミティブ データ型、文字列配列、GeoJSON ポイントのみをサポートしているため、任意の JSON ドキュメントに対して直接インデックス作成を行うことはできません。 ただし、 **フィールド マッピング** を使用すると、JSON ドキュメントの一部を選択して、それを検索ドキュメントの最上位レベルのフィールドに "引き上げる" ことができます。 フィールド マッピングの基礎については、「[Azure Search インデクサーのフィールド マッピング](search-indexer-field-mappings.md)」をご覧ください。

JSON ドキュメントの例に戻りましょう。

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

`Edm.String` 型の `text` フィールド、`Edm.DateTimeOffset` 型の `date` フィールド、`Collection(Edm.String)` 型の `tags` フィールドを持つ検索インデックスがあるとします。 ソースの "datePublished" とインデックスの `date` フィールド間の違いに注目してください。 JSON を必要な形式にマッピングするには、次のフィールド マッピングを使用します。

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

マッピング内のソース フィールド名は、 [JSON ポインター](http://tools.ietf.org/html/rfc6901) の表記を使用して指定されています。 スラッシュから始めて、JSON ドキュメントのルートを参照し、その後、スラッシュ区切りのパスを使用して、目的のプロパティ (任意の入れ子レベル) まで指定します。

0 から始まるインデックスを使用して個々の配列要素を参照することもできます。 たとえば、上の例から "tags" 配列の最初の要素を選択するには、次のようなフィールド マッピングを使用します。

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> フィールド マッピングのパス内のソース フィールド名が、JSON に存在しないプロパティを参照している場合、そのマッピングはエラーなしでスキップされます。 これは、異なるスキーマを使用したドキュメントをサポートできるようにするためです (一般的なユース ケースです)。 検証機能がないため、フィールド マッピングの仕様を入力するときは、入力ミスをしないように注意する必要があります。
>
>

## <a name="example-indexer-request-with-field-mappings"></a>例: フィールド マッピングを含むインデクサー要求

次の例は、フィールド マッピングを含む、完全に指定されたインデクサー ペイロードです。

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


## <a name="help-us-make-azure-search-better"></a>Azure Search の品質向上にご協力ください
ご希望の機能や品質向上のアイデアがありましたら、[UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search/)にぜひお寄せください。

## <a name="see-also"></a>関連項目

+ [Azure Search のインデクサー](search-indexer-overview.md)
+ [Azure Blob Storage のインデックスを Azure Search で作成する](search-howto-index-json-blobs.md)
+ [Azure Search BLOB インデクサーを使用した CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)
+ [チュートリアル: Azure Blob Storage で半構造化データを検索する](search-semi-structured-data.md)
