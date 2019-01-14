---
title: Azure Blob インデクサーから JSON BLOB のインデックスを作成する - Azure Search
description: Azure Search Blob インデクサーを使用してテキスト コンテンツのために Azure JSON BLOB をクロールします。 インデクサーにより、選択したデータ ソース (Azure Blob Storage など) のデータ インジェストが自動化されます。
ms.date: 12/21/2018
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 6df8d9a5c1ca1e587834ea08f73b3dd9498f8537
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753151"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Azure Search の BLOB インデクサーを使用して JSON BLOB のインデックスを作成する
この記事では、Azure Blob Storage 内の JSON BLOB から構造化コンテンツを抽出するために Azure Search BLOB インデクサーを構成する方法を説明します。

[ポータル](#json-indexer-portal)、[REST API](#json-indexer-rest)、または [.NET SDK](#json-indexer-dotnet) を使用して、JSON コンテンツのインデックスを作成できます。 すべての方法に共通するのは、JSON ドキュメントが Azure ストレージ アカウントの BLOB コンテナー内にあるということです。 他の Azure 以外のプラットフォームから JSON ドキュメントをプッシュする方法については、[Azure Search でのデータのインポート](search-what-is-data-import.md)に関する記事をご覧ください。

Azure Blob Storage 内の JSON BLOB は通常、単一の JSON ドキュメントまたは JSON 配列のいずれかです。 Azure Search の BLOB インデクサーでは、要求で **parsingMode** パラメーターを設定する方法に応じて、構成を解析できます。

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>ポータルの使用

JSON ドキュメントのインデックスを作成する最も簡単な方法は、[Azure portal](https://portal.azure.com/) のウィザードを使用することです。 Azure BLOB コンテナー内のメタデータを解析することにより、[**データのインポート**](search-import-data-portal.md) ウィザードでは、既定のインデックスを作成し、ソース フィールドをターゲット インデックス フィールドにマップし、1 回の操作でインデックスを読み込むことができます。 ソース データのサイズと複雑さによっては、数分で運用可能なフルテキスト検索インデックスを作成できます。

### <a name="1---prepare-source-data"></a>1 - ソース データを準備する

Azure ストレージ アカウントと BLOB ストレージおよび JSON ドキュメントのコンテナーが必要です。 これらのタスクのいずれかに慣れていない場合は、[Cognitive Search クイック スタート](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data)に関する記事の前提条件「Azure BLOB サービスを設定し、サンプル データを読み込む」を確認してください。

### <a name="2---start-import-data-wizard"></a>2 - データのインポート ウィザードを開始する

Azure Search サービス ページのコマンド バーから、またはストレージ アカウントの左側のナビゲーション ウィンドウの **[Blob service]** セクションで **[Azure Search の追加]** をクリックして、[ウィザードを開始する](search-import-data-portal.md)ことができます。

### <a name="3---set-the-data-source"></a>3 - データ ソースを設定する

**データソース**のページでは、ソースが次のように指定された **Azure BLOB ストレージ**になっている必要があります。

+ **[抽出されるデータ]** は、*[コンテンツとメタデータ]* になっている必要があります。 このオプションを選択すると、ウィザードでインデックス スキーマを推測して、インポート用のフィールドをマップすることができます。
   
+ **[解析モード]** は、*[JSON]* または *[JSON 配列]* に設定する必要があります。 

  *[JSON]* は、各 BLOB が 1 つの検索ドキュメントであり、検索結果で独立した項目として表示されることを示します。 

  *[JSON 配列]* は、BLOB が複数の要素で構成されており、各要素をスタンドアロンの独立した検索ドキュメントとして統合する必要があることを示します。 BLOB が複雑な場合に、*[JSON 配列]* を選択しないと、BLOB 全体が 1 つのドキュメントとして取り込まれます。
   
+ **[ストレージ コンテナー]** では、使用するストレージ アカウントとコンテナー、またはコンテナーとして解決される接続文字列を指定する必要があります。 接続文字列は、Blob service のポータル ページで取得できます。

   ![BLOB データ ソースの定義](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - ウィザードの [認知検索を追加します] ページをスキップする

JSON ドキュメントをインポートするときは、コグニティブ スキルを追加する必要はありません。 インデックス作成パイプラインに [Cognitive Services APIs と変換を含める](cognitive-search-concept-intro.md)特定のニーズがない限り、このステップをスキップする必要があります。

ステップをスキップするには、次のページ **[対象インデックスをカスタマイズします]** をクリックします。

### <a name="5---set-index-attributes"></a>5 - インデックスの属性を設定する

**[インデックス]** ページには、フィールドとデータ型およびインデックスの属性を設定するための一連のチェック ボックスが一覧表示されます。 ウィザードでは、メタデータに基づき、ソース データをサンプリングすることで、既定のインデックスを生成できます。 

多くの場合、既定値では実際に使用できるソリューションが生成されます。各ドキュメントを一意に識別するためのキーまたはドキュメント ID として使用できる (文字列としてキャストされた) フィールドと、フルテキスト検索および結果セットでの取得可能に適した候補となるフィールドを選択します。 BLOB では、`content` フィールドが検索可能なコンテンツに最適な候補です。

既定値を受け入れることも、または[インデックス属性](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)および[言語アナライザー](https://docs.microsoft.com/rest/api/searchservice/language-support)の説明を確認して初期値をオーバーライドまたは補足することもできます。 

時間をかけて選択内容を確認します。 ウィザードを実行すると、物理データ構造が作成されて、すべてのオブジェクトを削除して再作成しない限り、これらのフィールドを編集することはできません。

   ![BLOB のインデックス定義](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - インデクサーを作成する

完全に指定すると、ウィザードによって検索サービスに 3 つの異なるオブジェクトが作成されます。 データ ソース オブジェクトとインデックス オブジェクトは、Azure Search サービスに名前付きリソースとして保存されます。 最後のステップでは、インデクサー オブジェクトが作成されます。 インデクサーに名前を付けると、インデクサーはスタンドアロン リソースとして存在することができ、同じウィザードのシーケンスで作成されるインデックス オブジェクトやデータ ソース オブジェクトとは独立して、スケジュールを設定したり管理したりできます。

インデクサーに慣れていないユーザーのために説明すると、"*インデクサー*" とは Azure Search のリソースであり、外部データ ソースで検索可能なコンテンツのクロールを行います。 **データのインポート** ウィザードで出力されるインデクサーでは、JSON データ ソースがクロールされ、検索可能なコンテンツが抽出されて、Azure Search のインデックスにインポートされます。

   ![BLOB インデクサーの定義](media/search-howto-index-json/import-wizard-json-indexer.png)

**[OK]** をクリックしてウィザードを実行し、すべてのオブジェクトを作成します。 インデックスの作成はすぐに開始されます。

ポータルのページでデータのインポートを監視することができます。 進行状況の通知では、インデックス作成の状態と、アップロードされたドキュメントの数が示されます。 インデックスの作成が完了したら、[Search エクスプローラー](search-explorer.md)を使用してインデックスのクエリを実行できます。

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API の使用

JSON BLOB のインデックス作成は、Azure Search のすべてのインデクサーに共通する 3 部構成のワークフローでの通常のドキュメント抽出に似ています。つまり、データ ソースを作成し、インデックスを作成して、インデクサーを作成します。

コードに基づく JSON のインデックス作成の場合は、[インデクサー用](https://docs.microsoft.com/rest/api/searchservice/create-indexer)、[データ ソース用](https://docs.microsoft.com/rest/api/searchservice/create-data-source)、[インデックス用](https://docs.microsoft.com/rest/api/searchservice/create-index)の API で REST API を使用できます。 ポータルのウィザードとは異なり、コードによる方法では、**インデクサー作成**要求を送信する時点で、JSON ドキュメントを受け入れられる状態のインデックスが存在している必要があります。

Azure Blob Storage 内の JSON BLOB は通常、単一の JSON ドキュメントまたは JSON 配列のいずれかです。 Azure Search の BLOB インデクサーでは、要求で **parsingMode** パラメーターを設定する方法に応じて、構成を解析できます。

| JSON ドキュメント | parsingMode | 説明 | 可用性 |
|--------------|-------------|--------------|--------------|
| BLOB あたり 1 つ | `json` | JSON BLOB を 1 つのテキスト チャンクとして解析します。 各 JSON BLOB は、1 つの Azure Search ドキュメントになります。 | 一般に、[REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) と [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) 両方の API で利用できます。 |
| BLOB あたり複数 | `jsonArray` | 配列の各要素が別々の Azure Search ドキュメントになる、BLOB 内の JSON 配列を解析します。  | 一般に、[REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) と [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) 両方の API で利用できます。 |


### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する

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

### <a name="step-2-create-a-target-search-index"></a>手順 2:ターゲット検索インデックスを作成する 

インデクサーは、インデックス スキーマとペアになります。 (ポータルではなく) API を使用する場合は、インデクサー操作で指定できるようにインデックスを事前に準備します。

インデックスには、Azure Search の検索可能なコンテンツが格納されます。 インデックスを作成するには、検索エクスペリエンスを構成するドキュメント内のフィールド、属性、およびその他の構造が指定されているスキーマを提供します。 ソースと同じフィールド名とデータ型を持つインデックスを作成すると、インデクサーによってソースのフィールドとインデックスのフィールドが照合されるので、フィールドを明示的にマップする作業をしなくて済みます。

[インデックス作成](https://docs.microsoft.com/rest/api/searchservice/create-index)要求の例を次に示します。 インデックスには、BLOB から抽出されたテキストを格納するための、検索可能な `content` フィールドが含まれます。   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="step-3-configure-and-run-the-indexer"></a>手順 3:インデクサーを構成して実行する

これまで、データ ソースとインデックスの定義は parsingMode に依存しませんでした。 しかし、手順 3. のインデクサー構成では、JSON BLOB コンテンツが Azure Search インデックスで解析および構成される方法に応じてパスが分岐します。 `json` または `jsonArray` を選択できます。

+ **parsingMode** を `json` に設定すると、インデックスの作成では各 BLOB が 1 つのドキュメントとして処理されます。

+ BLOB が JSON の配列で構成されていて、配列の各要素を Azure Search で異なるドキュメントにする必要がある場合は、**parsingMode** を `jsonArray` に設定します。 ドキュメントは、検索結果の 1 つの項目として考えることができます。 検索結果において配列の各要素が独立した項目として表示されるようにしたい場合は、`jsonArray` オプションを使用します。

インデクサーの定義では、必要に応じて、**フィールド マッピング**を使用して、ターゲットの検索インデックスの設定に使用されるソース JSON ドキュメントのプロパティを選択できます。 JSON 配列では、配列が下位レベルのプロパティとして存在する場合、BLOB 内での配列の配置場所を示すドキュメント ルートを設定できます。

> [!IMPORTANT]
> `json` または `jsonArray` 解析モードを使用すると、Azure Search は、データ ソース内のすべての BLOB が JSON を含んでいるとみなします。 JSON BLOB と JSON 以外の BLOB が混在するデータ ソースをサポートする必要がある場合は、[UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search) でお知らせください。


### <a name="how-to-parse-single-json-blobs"></a>1 つの JSON BLOB を解析する方法

[Azure Search BLOB インデクサー](search-howto-indexing-azure-blob-storage.md) は、既定では JSON BLOB を 1 つのテキスト チャンクとして解析します。 多くの場合、JSON ドキュメントの構造はそのままに維持する必要があります。 たとえば、Azure Blob Storage に次の JSON ドキュメントがあると仮定します。

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

BLOB インデクサーでは、JSON ドキュメントが 1 つの Azure Search ドキュメントとして解析されます。 インデクサーでは、ソースの "text"、"datePublished"、"tags" が、同じ名前と型のターゲット インデックス フィールドと照合されて、インデックスが読み込まれます。

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

### <a name="how-to-parse-json-arrays"></a>JSON 配列を解析する方法

JSON 配列機能を選ぶこともできます。 この機能は、BLOB に "*JSON オブジェクトの配列*" が含まれ、各要素を個別の Azure Search ドキュメントにしたい場合に役立ちます。 たとえば、次の JSON BLOB では、それぞれ "id" および "text" フィールドを持つ 3 つの独立したドキュメントで Azure Search インデックスを作成できます。  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

JSON 配列の場合、インデクサーの定義は次の例のようになります。 parsingMode パラメーターで `jsonArray` パーサーが指定されていることに注意してください。 JSON BLOB のインデックス作成において配列に固有の要件は、適切なパーサーを指定することと、適切なデータを入力することの 2 つだけです。

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

この場合も、フィールドのマッピングを省略できることに注意してください。 インデックスに同じ名前のフィールド "id" と "text" があるものとすると、フィールド マッピング リストを明示的に指定しなくても、BLOB インデクサーで正しいマッピングを推測できます。

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

### <a name="using-field-mappings-to-build-search-documents"></a>フィールド マッピングを使用して検索ドキュメントを作成する

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

マッピング内のソース フィールド名は、 [JSON ポインター](https://tools.ietf.org/html/rfc6901) の表記を使用して指定されています。 スラッシュから始めて、JSON ドキュメントのルートを参照し、その後、スラッシュ区切りのパスを使用して、目的のプロパティ (任意の入れ子レベル) まで指定します。

0 から始まるインデックスを使用して個々の配列要素を参照することもできます。 たとえば、上の例から "tags" 配列の最初の要素を選択するには、次のようなフィールド マッピングを使用します。

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> フィールド マッピングのパス内のソース フィールド名が、JSON に存在しないプロパティを参照している場合、そのマッピングはエラーなしでスキップされます。 これは、異なるスキーマを使用したドキュメントをサポートできるようにするためです (一般的なユース ケースです)。 検証機能がないため、フィールド マッピングの仕様を入力するときは、入力ミスをしないように注意する必要があります。
>
>

### <a name="rest-example-indexer-request-with-field-mappings"></a>REST の例:フィールド マッピングを含むインデクサー要求

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

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>.NET SDK の使用

.NET SDK は REST API と完全に同等です。 前の REST API セクションを確認し、概念、ワークフロー、要件を理解することをお勧めします。 その後は、次の .NET API リファレンス ドキュメントを参照して、マネージド コードで JSON インデクサーを実装できます。

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>関連項目

+ [Azure Search のインデクサー](search-indexer-overview.md)
+ [Azure Blob Storage のインデックスを Azure Search で作成する](search-howto-index-json-blobs.md)
+ [Azure Search BLOB インデクサーを使用した CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)
+ [チュートリアル:Azure Blob Storage で半構造化データを検索する](search-semi-structured-data.md)
