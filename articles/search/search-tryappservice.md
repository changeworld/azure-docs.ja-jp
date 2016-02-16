<properties 
   pageTitle="Azure App Service での Azure Search の無料試用 | Microsoft Azure"
   description="Azure App Service テンプレートを使用して、Azure Search を最大で 1 時間無料でお試しいただけます。"
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="01/13/2016"
   ms.author="heidist"/>

# Azure App Service での Azure Search の無料試用

[Azure App Service](https://tryappservice.azure.com/) は、サインアップせずに **Azure Search** を含む Azure サービスを 1 時間無料でテスト使用できるブラウザ ベースのセッションです。このサイトには、選択可能ないくつかのテンプレートが用意されています。Azure Search を含む ASP.NET テンプレートを選択すると、選択したサービスのサポートがある、完全に機能する Web サイトにアクセスできます。

[Azure App Service](https://tryappservice.azure.com/) プランの一部である Azure Search サービスは既に作成されており、検索クエリを受け付ける準備ができています。独自のインデックスやデータをアップロードしたり使用したりすることはできませんが、クエリを実行して、コード変更を何回も実行し、ユーザー エクスペリエンスを作り変えることができます。

検索データは[米国地質調査所 (USG)](http://geonames.usgs.gov/domestic/download_data.htm) のもので、約 3 百万行の全米のランドマーク、史跡、建造物、およびその他のランドマーク地物が含まれます。

## 作業開始

1 時間のセッションをまだ開始していない場合、次の手順に従って開始してください。

1. [https://tryappservice.azure.com](https://tryappservice.azure.com/) に移動して、下へスクロールして **[Web アプリ]** を選択します。 
2. **[次へ]** をクリックします。
3. **ASP.NET + Azure Search Site** テンプレートを選択します。

    ![][1]

4. **[作成]** をクリックします。
5. ログイン方法を選択し、ユーザー名とパスワードを指定します。

    ![][2]

6. サイトがプロビジョニングされるのを待ちます。準備が完了すると、次のようなページが表示されます。各ページには、残りの時間を常に把握できる動いている時計が表示されます。

    ![][3]

7. **[Visual Studio Team Services で編集]** を選択してソリューションを表示し、サイトを参照します。
9. Visual Studio Team Services で、ページ上部のセッション オプションの展開し、**[Web サイトの参照]** をクリックします。

    ![][4]

10. Azure Search Web サイトの開始ページが表示されます。**[開始する]** ボタンをクリックし、サイトを開きます。

    ![][5]

11. ブラウザーに検索ボックスがある ASP.NET Web サイトが開きます。検索する *Yellowstone* などのよく使用される用語、または *Mount Rainier* などの有名な山を入力します。なじみ深いランドマークで開始すると、結果の評価が容易になります。

    ![][6]


## 最初の操作

検索インデックスは完全に使用できるため、最初の一歩としていくつかクエリを試してみるのがよいでしょう。Azure Search では、すべての標準的な検索演算子 (+、-、|)、リテラルにマッチさせるための引用符、ワイルドカード (*)、優先順位の演算子をサポートしています。演算子の完全なリストについては、クエリ構文のリファレンスを確認してください。

- まず、アスタリスク (`*`) を追加して、ワイルドカード検索を行います。これによって、インデックス内のドキュメント数、2,262,578 が示されます。
- 次に `Yellowstone +center +building -ND` のように、「Yellowstone」と入力し、「+center」、「+building」および「-ND」と追加し、North Dakota を除いた Yellowstone visitor centers のみに徐々に絞り込みます。  
- 優先順位の演算子と文字列の一致が結合された、`statue+(park+MT)` のような検索語句を試します。次のスクリーン ショットのような結果が表示されます。機能クラスの下に、多くの検索アプリケーションでよく使用されている、自分でフィルタリングを実行できるファセット ナビゲーションが使用された、ファセット カテゴリが表示されることに注意してください。

    ![][7]

先に進む準備はできていますか? コード行をいくつか変更して、それのフルテキスト検索操作への影響を確認してみましょう。

## SearchMode.All の変更

Azure Search には、検索演算子の動作を制御できる構成可能な **searchMode** プロパティがあります。このプロパティの有効な値は、`Any` (既定値) または`All` です。これらのオプションを設定する方法のガイダンスについては、「[単純なクエリ構文](https://msdn.microsoft.com/library/dn798920.aspx)」を参照してください。

- **searchMode.Any** では、一部の検索用語と一致する場合、その項目は検索結果に含められると規定されています。検索語句が `Yellowstone visitor center` である場合、これらの用語のいずれかを含む任意のドキュメントが、検索結果に含まれます。このモードでは、*再呼び出し*が増えます。
- この例で使用されている **searchModel.All** では、指定した語句がすべてドキュメントに存在する必要があります。このモードは **searchMode.Any** よりも厳格ですが、再呼び出しよりも*精度*を優先する場合、アプリケーションにはおそらくより適しているでしょう。 

> [AZURE.NOTE] **searchMode.Any** は、クエリの構造が、演算子は最小限で語句が多い場合に最適です。一般的に経験則では、ユーザーは、電子商取引サイトなどのコンシューマー アプリケーションでは語句のみで検索をします。コンテンツやデータでは、検索語句に演算子を含める可能性が高いです。検索に、演算子 (特に `NOT (-)` 演算子) が含まれる可能性の高いと思う場合、**searchModel.All** で開始してください。これとは逆に、もう一方の選択肢の **searchMode.Any** の `OR`、`NOT` 演算子とその他の検索用語では、検索結果は減るのではなく大幅に増えます。次の例は、違いを理解するのに役立ちます。

このタスクでは、**searchMode** を変更し、モードに基づいて検索結果を比較します。

1. サンプル アプリケーションを含むブラウザー ウィンドウを開き、**[Visual Studio Team Services に接続]** を選択します。

    ![][8]

2. **Search.cshtml** を開き、39 行の `searchMode.All` を探し、`searchMode.Any` に変更します。

    ![][9]

3. 右側のジャンプ バーの **[実行]** をクリックします。

    ![][10]
 
再構築アプリ ウィンドウに、`Yellowstone +center +building -ND` など前に使用した検索用語を入力し、**searchMode** の変更の前と後の結果を比較します。

かなりの違いが見られます。検索結果が 7 つではなく 200万以上になっています。

   ![][11]
 
示された動作は、包含 `NOT` 演算子 (この場合は、「-ND」) のためです (**searchMode** が `Any` に設定されている場合、*AND'd* ではなく *OR'd* になります)。

この構成を指定した場合、検索結果には `NOT North Dakota` ではない、`Yellowstone`、`center`、および `building` の検索用語と一致するすべてのドキュメントが含まれます。`North Dakota` のフレーズを含むドキュメントは 13,081 のみなため、ほぼすべてのデータセットが返されます。

正直なところ、このようなシナリオはあまりないですが、これは検索語句に `NOT` 演算子が含まれる **searchMode** の効果を示しています。これは、この動作を理解し、これを希望しない場合のこれの変更方法を理解する助けとなります。

このチュートリアルを続けるには、**searchMode** を (39 行の設定が `All` の) 元の値に戻し、プログラムを実行し、残りのタスクを行うために再構築アプリを使用します。
 
## ワシントン州用のグローバル フィルターの追加

通常、使用可能なデータのサブセットを検索する場合は、データのインポート時に、データ ソースにフィルターを設定します。読み取り専用のデータを使用し、アプリケーションのフィルターに、Washington State を含むドキュメントのみを返すよう設定し、学習してみましょう。

1. Search.cshtml を開き、(36 行から開始する) **SearchParameters** コード ブロックを探し、コメント行とフィルターを追加します。

        var sp = new SearchParameters
        {
            //Add a filter for _Washington State
            Filter = "STATE_ALPHA eq 'WA'",
            // Specify whether Any or All of the search terms 
            SearchMode = SearchMode.All,
            // Include a count of results in the query result
            IncludeTotalResultCount = true,
            // Return an aggregated count of feature classes based on the specified query
            Facets = new[] { "FEATURE_CLASS" },
            // Limit the results to 20 documents
            Top = 20
        };


フィルターは、OData の構文を使用して指定され、ファセット ナビゲーションと共によく使用されたり、クエリを制限したりするためにクエリ文字列に含まれます。詳細については、「[OData フィルターの構文](https://msdn.microsoft.com/library/azure/dn798921.aspx)」を参照してください。

2. **[実行]** をクリックします。

3. アプリケーションを開きます。

4. カウントを返すワイルドカード (*) を入力します。結果は、ワシントン州のすべての地理的地物のすべてのドキュメントである 42,411 項目に絞り込まれたことに注意してください。

   ![][12]

## 検索結果の強調表示の追加

既に 1 行のコード変更は何回も行ったので、複数箇所でコード変更を行うより難解な変更をしてみましょう。現在のセッションの Search.cshtml ファイルに、次のバージョンの **Search.cshtml** を貼り付けます。

次のコードにより、検索結果の強調表示が追加されます。[SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx) で新しいプロパティが宣言されたことに注意してください。強調表示する必要のあるドキュメントを取得する、結果のコレクション上を反復処理する新しい関数も、結果を表示する HTML もあります。

このコード サンプルを実行すると、指定したフィールドに入力した検索用語と一致するものがある場合、太字で強調表示されます。

   ![][14]

両方のバージョンを比較するために、元の **Search.cshtml** ファイルのコピーを保存するとよいでしょう。

> [AZURE.NOTE] ファイル サイズを小さくするために、コメントはトリミングされています。
 
    @using System.Collections.Specialized
    @using System.Configuration
    @using Microsoft.Azure.Search
    @using Microsoft.Azure.Search.Models
    
    @{
    Layout = "~/_SiteLayout.cshtml";
    }
    
    @{
    // This modified search.cshtml file adds hit-highlighting
    
    string searchText = Request.Unvalidated["q"];
    string filterExpression = Request.Unvalidated["filter"];
    
    DocumentSearchResponse response = null;
    if (!string.IsNullOrEmpty(searchText))
    {
    searchText = searchText.Trim();
    
    // Retrieve search service name and an api key from configuration
    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
    
    var searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    var indexClient = searchClient.Indexes.GetClient("geonames");
    
    // Set the Search parameters used when executing the search request
    var sp = new SearchParameters
    {
    // Specify whether Any or All of the search terms must be matched in order to count the document as a match.
    SearchMode = SearchMode.All,
    // Include a count of results in the query result
    IncludeTotalResultCount = true,
    // Return an aggregated count of feature classes based on the specified query
    Facets = new[] { "FEATURE_CLASS" },
    // Limit the results to 20 documents
    Top = 20,
    // Enable hit-highlighting
    HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
    HighlightPreTag = "<b>",
    HighlightPostTag = "</b>",
    };
    
    if (!string.IsNullOrEmpty(filterExpression))
    {
    // Add a search filter that will limit the search terms based on a specified expression.
    // This is often used with facets so that when a user selects a facet, the query is re-executed,
    // but limited based on the chosen facet value to further refine results
    sp.Filter = filterExpression;
    }
    
    // Execute the search query based on the specified search text and search parameters
    response = indexClient.Documents.Search(searchText, sp);
    }
    }
    
    @functions
    {
    private string GetFacetQueryString(string facetKey, string facetValue)
    {
    NameValueCollection queryString = HttpUtility.ParseQueryString(Request.Url.Query);
    queryString["filter"] = string.Format("{0} eq '{1}'", HttpUtility.UrlEncode(facetKey), HttpUtility.UrlEncode(facetValue));
    return queryString.ToString();
    }
    
    private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)
    {
    if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
    {
    string highlightedResult = string.Join("...", item.Highlights[fieldName]);
    return new HtmlString(highlightedResult);
    }
    return new HtmlString(item.Document[fieldName].ToString());
    }
    }
    
    <!-- Form to allow user to enter search terms -->
    <form class="form-inline" role="search">
    <div class="form-group">
    <input type="search" name="q" id="q" autocomplete="off" size="80" placeholder="Type something to search, i.e. 'park'." class="form-control" value="@searchText" />
    <button type="submit" id="search" class="btn btn-primary">Search</button>
    </div>
    </form>
    @if (response != null)
    {
    if (response.Count == 0)
    {
    <p style="padding-top:20px">No results found for "@searchText"</p>
    <h3>Suggestions:</h3>
    <ul>
    <li>Ensure words are spelled correctly.</li>
    <li>Try rephrasing keywords or using synonyms.</li>
    <li>Try less specific keywords.</li>
    </ul>
    }
    else
    {
    <div class="col-xs-3 col-md-2">
    <h3>Feature Class</h3>
    <ul class="list-unstyled">
    @if (!string.IsNullOrEmpty(filterExpression))
    {
    <li><a href="?q=@HttpUtility.UrlEncode(searchText)">All</a></li>
    }
    <!-- Cycle through the facet results and show the values and counts -->
    @foreach (var facet in response.Facets["FEATURE_CLASS"])
    {
    <li><a href="?@GetFacetQueryString("FEATURE_CLASS", (string)facet.Value)">@facet.Value (@facet.Count)</a></li>
    }
    </ul>
    </div>
    <div class="col-xs-12 col-sm-6 col-md-10">
    <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
    
    <ul class="list-unstyled">
    <!-- Cycle through the search results -->
    @foreach (var item in response.Results)
    {
    <li>
    <h3>@RenderHitHighlightedString(item, "FEATURE_NAME")</h3>
    <p>@RenderHitHighlightedString(item, "DESCRIPTION")</p>
    <p>@RenderHitHighlightedString(item, "FEATURE_CLASS"), elevation: @item.Document["ELEV_IN_M"] meters</p>
    <p>@RenderHitHighlightedString(item, "COUNTY_NAME") County, @RenderHitHighlightedString(item, "STATE_ALPHA")</p>
    <br />
    </li>
    }
    </ul>
    </div>
    }
    }

## Azure Search を選ぶ理由

SQL Database などの他の Azure サービスにもフルテキスト検索はありますが、Azure Search のようなサービスでは、コントロール、改ページおよびカウント、検索結果の強調表示、クエリ候補のオート コンプリート、自然言語のサポート、ファセット ナビゲーション、フィルター処理などのチューニングができます。いくつかの[例](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=search)で、Azure Search と ASP.NET のみを使用して、すべての機能を備えた検索ベースのアプリケーションが開発可能であることを示しています。

## 次のステップ

[Try Azure App Service](https://tryappservice.azure.com/) サイトの読み取り専用のサービスを使用して、クエリ構文とフルテキスト検索の動作を確認し、searchMode とフィルターを学習し、検索アプリケーションに検索結果の強調表示を追加できました。次のステップとして、インデックスの作成と更新に移ることを検討してください。これでは、次の機能を追加します。

- 優先度が高い項目が最初に表示されるよう、検索スコアをチューニングする[スコアリング プロファイルを定義](https://msdn.microsoft.com/library/dn798928.aspx)します。
- ユーザー入力に対し、クエリのオート コンプリートまたは先行入力候補の追加を行う、[Suggesters を定義](https://msdn.microsoft.com/library/mt131377.aspx)します。
- データ ソースが Azure SQL Database または Azure DocumentDB である場合、インデックスを自動更新する[インデクサーを定義](https://msdn.microsoft.com/library/dn946891.aspx)します。

これらのすべてのタスクを実行するのには、サービスにインデックスを作成および入力する Azure サブスクリプションが必要です。無料評価版にサインアップする方法の詳細については、[https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/) を参照してください。

Azure Search に関する詳細については、[http://azure.microsoft.com](https://azure.microsoft.com/) の[ドキュメント ページ](https://azure.microsoft.com/documentation/services/search/)を参照するか、Azure Search の機能を検証する[サンプルおよびビデオ](search-video-demo-tutorial-list.md)をご希望の数参照してください。

## コードおよび Azure Search に関するファクト

Azure Search は完全に管理されたサービスとしてのプラットフォーム ([PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service)) であり、開発者が優れた検索エクスペリエンスを Web アプリケーションやモバイル アプリケーションに簡単に組み込むことができます。検索操作で使用するデータは Azure の検索サービスに格納されるため、データと操作の近接度により、遅延の少なさと一貫性のある動作が保証されています。これについてもう少し詳しく学習しましょう。

- 検索可能なデータが Azure Search によって管理されるインデックスに格納されています。
- インデックスを定義するスキーマはユーザーが定義し、これではフィルター式で便利な検索可能なフィールド、検索不能なフィールド、および結果をチューニングするためのプロファイルのスコアリングのようなコンストラクトを指定します。
- 検索インデックスには検索および取得可能な 1 つ以上のドキュメント (テーブルの行に類似) が含まれています。
- 多くのインデックスは、1 つのデータセットから読み込まれます。これは、検索操作のコンテキストで便利なフィールドのみが含められるようユーザーが事前に準備します。 
- データは、インデクサーで自動的に読み込んだり (Azure SQL Database または Azure DocumentDB でのみサポート)、1 つの Azure Search API で検索インデックスにプッシュしたりできます。API を使用する際には、JSON 形式であれば任意のデータ ソースからデータをプッシュできます。

[Azure App Service](https://tryappservice.azure.com/) オプションの ASP.NET + Azure Search Site テンプレートには、Visual Studio Team Services で変更可能な (1 時間のセッションで使用可能な) Web アプリケーション用のソース コードがあります。コードの表示または変更には、別の開発ツールは必要ありません。

コードは、[Azure Search の .NET クライアント ライブラリ](https://msdn.microsoft.com/library/dn951165.aspx)を使用して C# で記述されており、インデックスに対してクエリを実行したり、ファセット ナビゲーションを提供したり、Web ページにカウントと検索結果を表示できます。

USG 検索インデックスの構築および読み込みには、このテンプレートには含まれていない、その他のコードが使用されています。サービスは読み取り専用であるために、書き込みアクセスが必要なすべての操作は事前に完了している必要があります。この記事の最後に、スキーマの構築に使用した[スキーマのコピー](#schema)があります。

<a name="Schema"></a>
## スキーマについて

次のスクリーン ショットに、このテンプレートで使用されるインデックスの作成に使用されているスキーマを示します。
 
   ![][13]

### Schema.json ファイル

    {
      "@odata.context": "https://tryappservice.search.windows.net/$metadata#indexes/$entity",
      "name": "geonames",
      "fields": [
    {
      "name": "FEATURE_ID",
      "type": "Edm.String",
      "searchable": false,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": true,
      "analyzer": null
    },
    {
      "name": "FEATURE_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "FEATURE_CLASS",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_ALPHA",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_M",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_FT",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "MAP_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DESCRIPTION",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "HISTORY",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_CREATED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_EDITED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    }
      ],
      "scoringProfiles": [
    
      ],
      "defaultScoringProfile": null,
      "corsOptions": {
    "allowedOrigins": [
      "*"
    ],
    "maxAgeInSeconds": 300
      },
      "suggesters": [
    {
      "name": "AUTO_COMPLETE",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
    "FEATURE_NAME",
    "FEATURE_CLASS",
    "COUNTY_NAME",
    "DESCRIPTION"
      ]
    }
      ]
    }

<!--Image references-->
[1]: ./media/search-tryappservice/AzSearch-TryAppService-TemplateTile.png
[2]: ./media/search-tryappservice/AzSearch-TryAppService-LoginAccount.png
[3]: ./media/search-tryappservice/AzSearch-TryAppService-AppCreated.png
[4]: ./media/search-tryappservice/AzSearch-TryAppService-BrowseSite.png
[5]: ./media/search-tryappservice/AzSearch-TryAppService-GetStarted.png
[6]: ./media/search-tryappservice/AzSearch-TryAppService-QueryWA.png
[7]: ./media/search-tryappservice/AzSearch-TryAppService-QueryPrecedence.png
[8]: ./media/search-tryappservice/AzSearch-TryAppService-VSOConnect.png
[9]: ./media/search-tryappservice/AzSearch-TryAppService-cSharpSample.png
[10]: ./media/search-tryappservice/AzSearch-TryAppService-RunBtn.png
[11]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeany.png
[12]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeWAState.png
[13]: ./media/search-tryappservice/AzSearch-TryAppService-Schema.png
[14]: ./media/search-tryappservice/AzSearch-TryAppService-HitHighlight.png

<!---HONumber=AcomDC_0211_2016-->