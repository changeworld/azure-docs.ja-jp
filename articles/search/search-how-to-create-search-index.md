---
title: 検索インデックスの作成
titleSuffix: Azure Cognitive Search
description: Azure portal、REST API、または Azure SDK を使用して、検索インデックスを作成します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: 203b4c6c55c4476e27dad484a2edef4609211343
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487999"
---
# <a name="create-a-search-index-in-azure-cognitive-search"></a>Azure Cognitive Search で検索インデックスを作成する

Azure Cognitive Search のクエリは、検索インデックス内の検索可能なテキストをターゲットとします。 この記事では、Azure Cognitive Search でサポートされているモダリティを使用して、検索インデックスを定義し、公開する手順について説明します。 

[インデクサー](search-howto-create-indexers.md)を使用しない限り、インデックスの作成とインデックスの設定は 2 つの別個のタスクになります。 インデクサー以外のシナリオでは、インデックスの作成後に、[データをインポートする](search-what-is-data-import.md)手順が続きます。 

インデックスに関連した概念について詳しくは、[Azure Cognitive Search の検索インデックス](search-what-is-an-index.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

インデックスを作成および読み込むには、要求の[管理 API キー](search-security-api-keys.md)を介して付与される書き込みアクセス許可が必要です。 または、Azure Active Directory [ロールベースのアクセス制御パブリック プレビュー](search-security-rbac.md)に参加している場合は、検索共同作成者ロールのメンバーとして要求を発行できます。

インデックスの作成の大部分は、スキーマ定義の演習になります。 作成を開始するには、次が必要です。

+ インデックス内で検索可能、取得可能、フィルター可能、ファセット可能、並べ替え可能にするフィールドを明確に示します (詳細については、「[スキーマ チェックリスト](#schema-checklist)」を参照してください)。

+ インデックスのドキュメント キー (または ID) として使用できるソース データ内の一意識別子。

+ 安定したインデックスの場所。 既存のインデックスを別の検索サービスに移動することは、簡単にはできません。 アプリケーションの要件を見直し、既存の検索サービスとその容量と場所がニーズに十分に対応していることを確認してください。

最後に、すべてのサービス レベルには、作成できるオブジェクトの数に関する[インデックス制限](search-limits-quotas-capacity.md#index-limits)があります。 たとえば、Free レベルを試している場合は、所定の期間内に 3 つのインデックスしか作成できません。 インデックス自体に、複合フィールドとコレクションの数に制限があります。

## <a name="allowed-updates"></a>許可される更新

[**インデックスの作成**](/rest/api/searchservice/create-index)は、検索サービスに物理データ構造 (ファイルと逆インデックス) を作成する操作になります。 インデックスの作成後、[ **[インデックスの更新]** ](/rest/api/searchservice/update-index) を使用して変更を有効にできるかどうかは、その変更によってこれらの物理構造が無効になるかどうかに応じて決まります。 ほとんどのフィールド属性は、インデックスにフィールドが作成された後は変更できません。

設計プロセスのチャーンを最小限に抑えるために、次の表で、スキーマに固定される要素と、柔軟性のある要素を説明します。 固定要素を変更するにはインデックスを再構築する必要がありますが、柔軟性のある要素は物理実装に影響を与えることなくいつでも変更できます。 

| 要素 | 更新可能かどうか |
|---------|-----------------|
| 名前 | いいえ |
| キー | いいえ |
| フィールド名と型 | いいえ |
| フィールド属性 (検索可能、フィルター可能、ファセット可能、並べ替え可能) | いいえ |
| フィールド属性 (取得可能) | はい |
| [Analyzer](search-analyzers.md) | インデックス内のカスタム アナライザーを追加および変更できます。 文字列フィールドでのアナライザーの割り当てについては、"searchAnalyzer" のみを変更できます。 その他すべての割り当てと変更には、再構築が必要です。 |
| [スコアリング プロファイル](index-add-scoring-profiles.md) | はい |
| [Suggesters](index-add-suggesters.md) | いいえ |
| [クロスオリジン リモート スクリプティング (CORS)](#corsoptions) | はい |
| [暗号化](search-security-manage-encryption-keys.md) | はい |

> [!NOTE]
> [シノニム マップ](search-synonyms.md)はインデックス定義の一部ではありません。 シノニム マップを変更した場合、物理検索インデックスには影響はありません。

## <a name="schema-checklist"></a>スキーマのチェックリスト

このチェックリストは、検索インデックスの設計上の決定を行う際に役立ちます。

1. インデックス名とフィールド名が名前付けルールに準拠するように、[名前付け規則](/rest/api/searchservice/naming-rules)を確認します。

1. [サポートされていないデータ型](/rest/api/searchservice/supported-data-types)を確認します。 データ型は、フィールドの使用方法に影響します。 たとえば、数値コンテンツはフィルター可能ですが、フルテキスト検索はできません。 最も一般的なデータ型は、検索可能な `Edm.String` テキストです。これは、フルテキスト検索エンジンを使用してトークン化され、クエリが実行されます。

1. 一意の値を含むソース データ内の 1 つのフィールドを識別し、インデックスのキー フィールドとして機能させることができます。 たとえば、Blob Storage からインデックスを作成する場合、多くの場合ストレージ パスがドキュメント キーとして使用されます。 

   すべてのインデックスには、*ドキュメント キー* として機能するフィールドが 1 つ 必要です ("ドキュメント ID" とも呼ばれます)。 キーは、検索インデックス内の文字列となりますが、ソース データ内の任意の一意識別子にマッピングできます。 特定の検索ドキュメントを一意に識別する機能は、検索結果内のレコードやエンティティを再構成したり、検索インデックス内の特定のドキュメントを取得したり、ドキュメントごとのレベルで選択的なデータ処理を行ったりする場合に必要です。

1. インデックス内の検索可能なコンテンツに作用するデータ ソース内のフィールドを特定します。 検索可能なコンテンツには、フルテキスト検索エンジンを使用してクエリを実行する短い文字列または長い文字列が含まれます。 コンテンツが詳細である (小さなフレーズまたは大きなチャンク) 場合は、さまざまなアナライザーを試して、テキストがどのようにトークン化されるのか確認します。

   [フィールド属性の割り当て](search-what-is-an-index.md#index-attributes)によって、検索動作と、検索サービスでのインデックスの物理的な表現の両方が決定されます。 フィールドの指定方法の決定は、多くのお客様にとって反復的なプロセスになります。 反復処理の速度を上げるには、簡単に削除して再構築できるサンプル データから始めます。

1. フィルターとして使用できるソース フィールドを特定します。 数値コンテンツと短いテキスト フィールド (特に繰り返し値を持つフィールド) は、優れた選択肢です。 フィルターを使用する場合は、次のことを覚えておいてください。

   + フィルター可能なフィールドは、必要に応じてファセット ナビゲーションで使用できます。

   + フィルター可能なフィールドは任意の順序で返されます。そのため、並べ替え可能にすることも検討してください。

## <a name="formulate-a-request"></a>要求を作成する

インデックスを作成する準備ができたら、先に進むいくつかの方法があります。 早期の開発と概念実証のテストには、Azure portal または REST API をお勧めします。

開発時に、頻繁な再構築を計画します。 物理構造はサービス内で作成されるため、変更のほとんどにおいて[インデックスの削除と再作成](search-howto-reindex.md)が必要になります。 リビルドを高速化するために、データのサブセットを使って作業することを検討してもよいでしょう。

### <a name="azure-portal"></a>[**Azure portal**](#tab/indexer-portal)

ポータルを使用したインデックスの設計では、数値フィールドに対してフルテキスト検索機能を許可しないなど、特定のデータ型に対して要件とスキーマ ルールが適用されます。 ポータルには、検索インデックスを作成するための次の 2 つのオプションがあります。 

+ **[インデックスの追加]** は、インデックス スキーマを指定する埋め込みエディターです
+ [ **[データのインポート]**](search-import-data-portal.md) はウィザードです

ウィザードでは、インデクサー、データソース、およびデータの読み込みも作成することにより、追加の操作が組み込まれます。 この方法では想定よりも操作が多くなる場合は、 **[インデックスの追加]** または別の方法を使用する必要があります。

次のスクリーンショットは、ポータルで **[インデックスの追加]** 機能と **[データのインポート]** が表示される場所を示しています。 インデックスが作成されたら、再度 **[インデックス]** タブで確認できます。

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="[インデックスの追加] コマンド" border="true":::

> [!Tip]
> ポータルでインデックスを作成した後、JSON 表現をコピーしてアプリケーション コードに追加できます。

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

[ **[インデックスの作成 (REST)]**](/rest/api/searchservice/create-index) は、インデックスを作成するために使用されます。 Postman と Visual Studio Code (Azure Cognitive Search 用の拡張機能を備えているもの) はどちらも、検索インデックス クライアントとして機能できます。 どちらのツールを使用しても、検索サービスに接続し、要求を送信できます。

+ [REST と Postman を使用して検索インデックスを作成する](search-get-started-rest.md)
+ [Visual Studio Code と Azure Cognitive Search の使用を開始する](search-get-started-vs-code.md)

REST API は、フィールド属性の既定値を提供します。 たとえば、既定では、すべての Edm.String フィールドは検索可能です。 属性は、説明を目的として以下のように完全に表示されますが、既定値が適用される場合は、属性を省略できます。

インデックス要求を整理する方法については、「[インデックス操作 (REST)](/rest/api/searchservice/index-operations)」を参照してください。

```json
POST https://[servicename].search.windows.net/indexes?api-version=[api-version] 
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "retrievable": true, "searchable": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Address", "type": "Edm.ComplexType", 
      "fields": [
          { "name": "StreetAddress", "type": "Edm.String", "retrievable": true, "filterable": false, "sortable": false, "facetable": false, "searchable": true },
          { "name": "City", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": true, "sortable": true, "facetable": true },
          { "name": "StateProvince", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": true, "sortable": true, "facetable": true }
        ]
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ]
  }
}
```

### <a name="net-sdk"></a>[ **.NET SDK**](#tab/kstore-dotnet)

Azure SDK for .NET には、インデックスを作成および更新するためのメソッドを含む [**SearchIndexClient**](/dotnet/api/azure.search.documents.indexes.searchindexclient) が用意されています。

```csharp
// Create the index
string indexName = "hotels";
SearchIndex index = new SearchIndex(indexName)
{
    Fields =
    {
        new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
        new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
        new SearchableField("description") { AnalyzerName = LexicalAnalyzerName.EnLucene },
        new SearchableField("descriptionFr") { AnalyzerName = LexicalAnalyzerName.FrLucene }
        new ComplexField("address")
        {
            Fields =
            {
                new SearchableField("streetAddress"),
                new SearchableField("city") { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("stateProvince") { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("country") { SynonymMapNames = new[] { synonymMapName }, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("postalCode") { IsFilterable = true, IsSortable = true, IsFacetable = true }
            }
        }
    }
};

await indexClient.CreateIndexAsync(index);
```

詳細については、[azure-search-dotnet-samples/quickstart/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) を参照してください。

### <a name="other-sdks"></a>[**その他の SDK**](#tab/other-sdks)

Cognitive Search の場合、一般公開される機能は Azure SDK によって実装されています。 そのため、いずれかの SDK を使用して検索インデックスを作成できます。 これらのすべてに、インデックスを作成および更新するためのメソッドを含む **SearchIndexClient** が用意されています。

| Azure SDK | Client | 例 |
|-----------|--------|----------|
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [インデックス](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

---

<a name="corsoptions"></a>

## <a name="set-corsoptions-for-cross-origin-queries"></a>クロス オリジン クエリのために `corsOptions` を設定する

インデックス スキーマには、`corsOptions` を設定するためのセクションが含まれます。 ブラウザーではすべてのクロスオリジン要求が禁止されるので、既定ではクライアント側 JavaScript で API を呼び出すことはできません。 インデックスに対するクロスオリジン クエリを許可するには、**corsOptions** 属性を設定することによって、CORS (クロスオリジン リソース共有) を有効にします。 セキュリティ上の理由から、CORS がサポートされているのは[クエリ API](search-query-create.md#choose-query-methods) だけです。

```json
"corsOptions": {
  "allowedOrigins": [
    "*"
  ],
  "maxAgeInSeconds": 300
```

CORS には次のプロパティを設定できます。

+ **allowedOrigins** (必須):インデックスへのアクセスが許可されるオリジンのリストです。 つまり、これらのオリジンから提供されるすべての JavaScript コードは、インデックスのクエリを許可されます (正しい api-key を提供する場合)。 各オリジンの標準的な形式は `protocol://<fully-qualified-domain-name>:<port>` ですが、多くの場合 `<port>` は省略されます。 詳しくは、「[Cross-origin resource sharing (クロスオリジン リソース共有)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)」(Wikipedia) をご覧ください。

  すべてのオリジンにアクセスを許可する場合は、**allowedOrigins** 配列の唯一の要素として `*` を指定します。 "*運用環境の検索サービスに対してはこれは推奨されません*" が、開発およびデバッグでは役に立つことがよくあります。

+ **maxAgeInSeconds** (省略可能):ブラウザーでは、この値を使用して、CORS プレフライト応答をキャッシュする期間 (秒) が決定されます。 負ではない整数を指定する必要があります。 この値が大きいほどパフォーマンスはよくなりますが、CORS ポリシーの変更が有効になるまでの時間は長くなります。 これを設定しないと、既定の期間として 5 分が使用されます。

## <a name="next-steps"></a>次のステップ

次のリンクを使用して、データを使用したインデックスの読み込みについて理解を深めてください。

+ [データ インポートの概要](search-what-is-data-import.md)

+ [ドキュメントの追加、更新、削除 (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 