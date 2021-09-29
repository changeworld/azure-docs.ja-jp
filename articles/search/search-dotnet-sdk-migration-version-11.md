---
title: .NET SDK バージョン 11 にアップグレードする
titleSuffix: Azure Cognitive Search
description: 以前のバージョンから Azure Cognitive Search .NET SDK バージョン 11 にコードを移行します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/16/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 37c03a5be2f81fff0f4a1cac01153601a39ddd10
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557155"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Azure Cognitive Search .NET SDK バージョン 11 へのアップグレード

検索ソリューションが [**AZURE SDK for .net**](/dotnet/azure/) 上に構築されている場合、この記事では、[**Microsoft.Azure.Search**](/dotnet/api/overview/azure/search/client10) の以前のバージョンからバージョン 11 の [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme)クライアントライブラリへコードを移行する方法について説明します。 バージョン 11 は、Azure SDK 開発チームによって完全に再設計され、リリースされたクライアント ライブラリです (以前のバージョンは、Azure Cognitive Search 開発チームによって作成されました)。 

[1 つの例外](#WhatsNew)を除き、バージョン 11 にはバージョン 10 のすべての機能が実装されています。 主な違いは次のとおりです。

+ パッケージは 4つでなく 1 つ (**Azure.Search.Documents**)
+ クライアントは 2 つでなく 3 つ: SearchClient、SearchIndexClient、SearchIndexerClient
+ さまざまな API 全体の名前付けの違いと、一部のタスクを簡略化する細かい構造の違い

クライアント ライブラリの[変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)に、更新内容が箇条書きで列挙されています。

Cognitive Search 製品ドキュメントの C# コード サンプルとスニペットはすべて、新しい **Azure.Search.Documents** クライアント ライブラリを使用するように改訂されています。

## <a name="why-upgrade"></a>アップグレードする理由

アップグレードの利点は、次のようにまとめられています。

+ 新機能は **Azure.Search.Documents** にのみ追加されます。 以前のバージョンである Microsoft Azure Search は、レガシ クライアントという位置付けになりました。 レガシ ライブラリの更新は、優先度の高いバグ修正に限定されます。

+ 他の Azure ライブラリとの一貫性。 **Azure.Search.Documents** は [Azure.Core](/dotnet/api/azure.core) と [System.Text.Json](/dotnet/api/system.text.json) に依存しており、クライアントの接続や承認といった一般的なタスクについては従来のアプローチを踏襲します。

## <a name="package-comparison"></a>パッケージの比較

バージョン11では、管理する対象が減るように、パッケージ管理を統合して簡略化します。

| バージョン 10 以前 | バージョン 11 |
|------------------------|------------|
| [Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search/) </br>[Microsoft.Azure.Search.Service](https://www.nuget.org/packages/Microsoft.Azure.Search.Service/) </br>[Microsoft.Azure.Search.Data](https://www.nuget.org/packages/Microsoft.Azure.Search.Data/) </br>[Microsoft.Azure.Search.Common](https://www.nuget.org/packages/Microsoft.Azure.Search.Common/)  | [Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/) |

## <a name="client-comparison"></a>クライアントの比較

2 つのバージョン間で対応するクライアント ライブラリがある場合、その対応付けを次の表に示します。

|  クライアントによる処理 | Microsoft.Azure.Search&nbsp;(v10) | Azure.Search.Documents&nbsp;(v11) |
|---------------------|------------------------------|------------------------------|
| インデックスのドキュメント コレクション (クエリとデータ インポート) を対象とします。 | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| インデックス関連のオブジェクト (インデックス、アナライザー、シノニム マップ) をターゲットにします。 | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| インデクサー関連のオブジェクト (インデクサー、データ ソース、スキルセット) を対象とします。 | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**新規**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Caution]
> SearchIndexClient は両方のバージョンに存在しますが、異なる操作を対象としていることに注意してください。 バージョン 10 では、SearchIndexClient によりインデックスおよびそのほかのオブジェクトが作成されます。 バージョン 11 では、SearchIndexClient は既存のインデックスと連携し、クエリおよびデータ インジェスト API を使用して ドキュメント コレクションを対象とします。 コードを更新するときの混乱を避けるために、クライアント参照が更新される順序にご注意ください。 「[アップグレードの手順](#UpgradeSteps)」のシーケンスに従うと、文字列の置換の問題を軽減できます。

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>名前付けとその他の API の違い

クライアントの違い (既に説明したため、ここでは省略します) に加え、他の複数の API は名前が変更され、場合によっては設計が変更されています。 クラス名の違いを以下にまとめます。 この一覧は完全なものではありませんが、API の変更をタスクごとにグループ化しているため、特定のコード ブロックを改訂する場合に役立ちます。 API 更新の項目別一覧については、GitHub の `Azure.Search.Documents` の[変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)を参照してください。

### <a name="authentication-and-encryption"></a>認証と暗号化

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| EncryptionKey (API リファレンスには記載されていません。 この API のサポートは、v10 で一般提供に移行しましたが、利用できるのは[プレビュー SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) のみです) | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>インデックス、アナライザー、シノニム マップ

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [フィールド](/dotnet/api/microsoft.azure.search.models.field) | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyzer](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (さらに `AnalyzerName` から `LexicalAnalyzerName` へ) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (さらに `StandardTokenizerV2` から `LuceneStandardTokenizerV2` へ) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (さらに `TokenizerName` から `LexicalTokenizerName` へ) |
| [SynonymMap.Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | [なし] : `Format` への参照を削除します。 |

フィールド定義は整理されています。[SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield)、[SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield)、[ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) は、フィールド定義を作成するための新しい API です。

### <a name="indexers-datasources-skillsets"></a>インデクサー、データソース、スキルセット

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [インデクサー](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Skill](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [スキルセット](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>データのインポート

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-requests-and-responses"></a>クエリの要求と応答

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [DocumentsOperationsExtensions.SearchAsync](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.searchasync) | [SearchClient.SearchAsync](/dotnet/api/azure.search.documents.searchclient.searchasync) |
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | 結果のドキュメントが 1 つか複数かに応じて、[SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) または [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1)。 |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)  |
| [SuggestParameters](/dotnet/api/microsoft.azure.search.models.suggestparameters) |  [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) |
| [SearchParameters.Filter](/dotnet/api/microsoft.azure.search.models.searchparameters.filter) |  [SearchFilter](/dotnet/api/azure.search.documents.searchfilter) (OData フィルター式を構築するための新しいクラス) |

### <a name="json-serialization"></a>JSON シリアル化

既定では、Azure SDK は、JSON のシリアル化に [System.Text.Json](/dotnet/api/system.text.json) を使用します。これらの API の機能を使用して、新しいライブラリに対応するものがないネイティブ [SerializePropertyNamesAsCamelCaseAttribute](/dotnet/api/microsoft.azure.search.models.serializepropertynamesascamelcaseattribute) クラスを介して以前に実装されたテキスト変換を処理します。

プロパティ名を camelCase にシリアル化するには、[JsonPropertyNameAttribute](/dotnet/api/system.text.json.serialization.jsonpropertynameattribute) を使用します ([こちらの例](https://github.com/Azure/azure-sdk-for-net/tree/d263f23aa3a28ff4fc4366b8dee144d4c0c3ab10/sdk/search/Azure.Search.Documents#use-c-types-for-search-results)に類似)。

または、[JsonSerializerOptions](/dotnet/api/system.text.json.jsonserializeroptions) で提供されている [JsonNamingPolicy](/dotnet/api/system.text.json.jsonnamingpolicy) を設定することもできます。 次の System.Text.Json のコード例 ([Microsoft.Azure.Core.Spatial の README](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial/README.md#deserializing-documents) から取得) は、個々のプロパティに割り当てることなく camelCase を使用する方法を示しています。

```csharp
// Get the Azure Cognitive Search endpoint and read-only API key.
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
AzureKeyCredential credential = new AzureKeyCredential(Environment.GetEnvironmentVariable("SEARCH_API_KEY"));

// Create serializer options with our converter to deserialize geographic points.
JsonSerializerOptions serializerOptions = new JsonSerializerOptions
{
    Converters =
    {
        new MicrosoftSpatialGeoJsonConverter()
    },
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
};

SearchClientOptions clientOptions = new SearchClientOptions
{
    Serializer = new JsonObjectSerializer(serializerOptions)
};

SearchClient client = new SearchClient(endpoint, "mountains", credential, clientOptions);
Response<SearchResults<Mountain>> results = client.Search<Mountain>("Rainier");
```

JSON シリアル化に Newtonsoft.Json を使用している場合は、同様の属性を使用するか、[JsonSerializerSettings](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonSerializerSettings.htm) のプロパティを使用して、グローバル名前付けポリシーを渡すことができます。 上記と同等の例については、Newtonsoft.Json の README の [ドキュメントの逆シリアル化の例](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md)を参照してください。

<a name="WhatsNew"></a>

## <a name="inside-v11"></a>Inside v11

Azure Cognitive Search クライアント ライブラリの各バージョンは、REST API の対応するバージョンを対象としています。 この REST API はサービスの基盤と見なされ、個々の SDK によって REST API のバージョンがラップされます。 .NET 開発者として、特定のオブジェクトや操作についてもっと詳しく知りたい場合は、[REST API ドキュメント](/rest/api/searchservice/)の方が情報量が多く参考になるでしょう。 バージョン 11 は、[2020-06-30 の Search サービス](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)を対象としています。 

バージョン 11.0 では、次のオブジェクトと操作が完全にサポートされています。

+ インデックスの作成と管理
+ シノニム マップの作成と管理
+ インデクサーの作成と管理
+ インデクサーのデータ ソースの作成と管理
+ スキルセットの作成と管理
+ すべてのクエリ タイプと構文

バージョン 11.1 の追加 ([変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md#1110-2020-08-11)の詳細):

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (11.1 で追加)
+ カスタムのシリアル化をサポートするための[シリアライザー プロパティ](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (11.1 で追加)

バージョン 11.2 の追加 ([変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md#1120-2021-02-10)の詳細):

+ [EncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchindexer.encryptionkey) プロパティにインデクサー、データ ソース、スキルセットが追加されました
+ [IndexingParameters.IndexingParametersConfiguration ](/dotnet/api/azure.search.documents.indexes.models.indexingparametersconfiguration) プロパティのサポート
+ [地理空間型](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype.geographypoint)は、[FieldBuilder でネイティブにサポートされています](/dotnet/api/azure.search.documents.indexes.fieldbuilder.build)。 [SearchFilter](/dotnet/api/azure.search.documents.searchfilter) では、明示的なアセンブリの依存関係を使用せずに、Microsoft.Spatial からジオメトリック型をエンコードできます。

  [Microsoft.Spatial](https://www.nuget.org/packages/Microsoft.Spatial/) への依存関係を引き続き明示的に宣言することもできます。 この技術の例は、[System.Text.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) および [Newtonsoft.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md) で使用できます。

現在、Azure.Search.Documents の任意のバージョンでサポートされていません。

+ [ナレッジ ストア](knowledge-store-concept-intro.md)

## <a name="before-upgrading"></a>アップグレードする前に

+ [クイックスタート](search-get-started-dotnet.md)、チュートリアル、[C# サンプル](samples-dotnet.md)は、Azure.Search.Documents パッケージを使用するように更新されています。 既存のサンプルとチュートリアルを確認し、新しい API についての知識を深めたうえで、移行の演習に取り組むことをお勧めします。

+ [Azure.Search.Documents を使用する方法](search-howto-dotnet-sdk.md)に関する記事では、最もよく使用される API を紹介しています。 Cognitive Search に詳しい方も、新しいライブラリについて紹介した記事を移行の前段階としてぜひご利用ください。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>アップグレードの手順

次の手順では、コードの移行の基本について説明します。まず必要な最初の一連のタスク (特にクライアント参照) について手順を説明します。

1. Visual Studio でプロジェクト参照を右クリックし、[NuGet パッケージの管理] を選択して、[Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)をインストールします。

1. Microsoft.Azure.Search の using ディレクティブを次のように置き換えます。

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. JSON のシリアル化を必要とするクラスの場合は、`using Newtonsoft.Json` を `using System.Text.Json.Serialization` で置き換えます。

1. クライアント認証コードを変更します。 以前のバージョンでは、クライアント オブジェクトのプロパティを使用して、API キー ([SearchServiceClient.Credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) プロパティなど) を設定します。 現在のバージョンでは、[AzureKeyCredential](/dotnet/api/azure.azurekeycredential) クラスを使用して、キーを資格情報として渡します。これにより、必要な場合は、新しいクライアント オブジェクトを作成せずに API キーを更新することができます。

   クライアントのプロパティは、`Endpoint`、`ServiceName`、および `IndexName` のみに簡素化されています (該当する場合)。 次の例では、システムの [Uri](/dotnet/api/system.uri) クラスを使用して、エンドポイント、およびキー値を読み取るための[環境](/dotnet/api/system.environment)クラスを提供しています。

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. インデクサー関連オブジェクトの新しいクライアント参照を追加します。 インデクサー、データソース、またはスキルセットを使用している場合は、クライアント参照を [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) に変更します。 このクライアントはバージョン 11 で新しく追加されたものであり、元の機能はありません。

1. コレクションとリストを変更します。 新しい SDK では、リストに null 値が含まれている場合にダウンストリームの問題が発生するのを回避するため、すべてのリストが読み取り専用になっています。 コードを変更して、リストに項目を追加するようにします。 たとえば、Select プロパティに文字列を割り当てる代わりに、次のように文字列を追加します。

   ```csharp
   var options = new SearchOptions
    {
       SearchMode = SearchMode.All,
       IncludeTotalCount = true
    };

    // Select fields to return in results.
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Tags");
    options.Select.Add("Rooms");
    options.Select.Add("Rating");
    options.Select.Add("LastRenovationDate");
   ```

   Select、Facets、SearchFields、SourceFields、ScoringParameters、OrderBy はすべて、現在再構築が必要なリストです。

1. クエリとデータ インポートのクライアント参照を更新します。 [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) のインスタンスは [SearchClient](/dotnet/api/azure.search.documents.searchclient) に変更する必要があります。 名前の混乱を避けるために、次の手順に進む前に、すべてのインスタンスをキャッチしてください。

1. インデックス、シノニム マップ、およびアナライザー オブジェクトのクライアント参照を更新します。 [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) のインスタンスは [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) に変更する必要があります。 

1. コードの残りの部分では、新しいライブラリの API を使用するようにクラス、メソッド、およびプロパティを更新します。 [名前付けの違い](#naming-differences)のセクションから始めることができますが、[変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)を確認することもできます。

   同等の API が見つからない場合は、Microsoft でドキュメントを改善し、問題を調査することができるように、[https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) で問題を記録することをお勧めします。

1. ソリューションをリビルドします。 すべてのビルド エラーまたは警告を修正した後、[新しい機能](#WhatsNew)を利用するようにアプリケーションを変更できます。

<a name="ListOfChanges"></a>

## <a name="breaking-changes"></a>重大な変更

ライブラリと API に加えられた全面的な変更を考えると、バージョン 11 へのアップグレードは重大であり、コードがバージョン 10 以前との下位互換性を失うという点で、破壊的変更に相当します。 違いの詳細については、`Azure.Search.Documents` の[変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) を参照してください。

サービス使用条件の更新時に、バージョン 11 のコード変更が、API のリファクタリングに限らず、既存の機能に関連している場合は、次のような動作変更があります。

+ [BM25 ランク付けアルゴリズム](index-ranking-similarity.md)では、前のランク付けアルゴリズムがより新しいテクノロジに置き換えられます。 新しいサービスはこのアルゴリズムを自動的に使用します。 既存のサービスについては、新しいアルゴリズムを使用するようにパラメーターを設定する必要があります。

+ このバージョンでは、[順序付けされた null 値の結果](search-query-odata-orderby.md)が変更されていて、null 値は、並べ替えが `asc` の場合は最初に、並べ替えが `desc` の場合は最後に表示されます。 null 値の並べ替え方法を処理するコードを記述した場合、そのコードを確認し、不要になった場合は削除する必要があります。

## <a name="next-steps"></a>次の手順

+ [C# .NET アプリケーションで Azure.Search.Documents を使用する方法](search-howto-dotnet-sdk.md)
+ [チュートリアル: Web アプリに検索を追加する](tutorial-csharp-overview.md)
+ [Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [GitHub のサンプル](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API リファレンス](/dotnet/api/overview/azure/search.documents-readme)
