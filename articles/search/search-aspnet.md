<properties
	pageTitle="Azure Search と ASP.NET MVC Web アプリの接続 | Microsoft Azure | ホステッド クラウド検索サービス"
	description="ASP.NET MVC Web アプリと、ホステッド クラウド検索サービスである Azure Search を接続します。.NET ライブラリまたは REST API を使用して接続、クエリ、結果の表示を行う方法について説明します。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

#Azure Search を ASP.NET MVC Web アプリと統合する方法

ASP.NET MVC は、Azure Search と統合できるカスタム ソリューションの主要 Web アプリケーション フレームワークです。この記事では、ASP.NET Web アプリを Azure Search と接続し、一般的な操作に合わせて設計パターンを調整する方法について説明します。また、開発作業をスムーズにするために役立つコーディング方法もいくつか紹介します。

##ASP.NET と Azure Search を使用したサンプルとデモ

Search と ASP.NET の統合方法がわかるコード サンプルは既にいくつかあります。コードとデモ アプリについては、次のリンク先を参照してください。

- [ニューヨーク市 (NYC) の仕事デモ サイト](http://aka.ms/azjobsdemo)
- [Azure Search が使用された Try Azure App Service の試用](search-tryappservice.md)
- [ビデオ、チュートリアル、デモ、およびコード サンプルの完全な一覧](earch-video-demo-tutorial-list.md)

##サービスに接続する

Web アプリケーションからサービスに接続し、要求を発行するために必要なものは、次の 3 つのみです。

- プロビジョニングした Azure Search サービスの URL (書式は https://<service-name>.search.windows.net)
- Azure Search への接続を認証する API キー (文字列)
- 接続要求を構成する HTTPClient または SearchServiceClient

####URL と API キー

[ポータル](search-create-service-portal.md)で URL と API キーを確認するか、[管理 REST API](https://msdn.microsoft.com/library/dn832684.aspx) を使用してプログラムで取得します。

通常、URL とキーは、両方ともユーザー操作プログラムの web.config ファイルに含めます。

      <appSettings>
    	<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
    	<add key="SearchServiceApiKey" value="[API KEY]" />
    	. . .
      </appSettings>

接続でドメイン (search.windows.net) を付加している限り、プロビジョニング中には Search サービス名として短い名前を指定できます。また、web.config で、HTTPS プレフィックスを付けずに完全修飾名 (<service-name>.search.windows.net) を指定することもできます。

API キーは、サービスのプロビジョニング中に生成される認証トークンです (管理者キーのみ)。また、ポータルでクエリ キーを作成する場合に手動で生成されます。キーの種類によって、アプリケーションに使用できる検索操作が決まります。

- 管理者キー (読み取り/書き込みアクセス許可、1 サービスにつき 2 個)
- クエリ キー (読み取り専用、1 サービスにつき 50 個まで)

API キーは、長さ 32 文字の文字列です。管理者キーとクエリ キーに見た目の違いはありません。コードに指定したキーがどちらの種類であるか分からなくなった場合は、ポータルを確認するか、Management REST API を使用してキーの種類を返します。キーの詳細については、「[Azure Search Service REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)」を参照してください。

> [AZURE.TIP]クエリ キーによって、クライアントは読み取り専用機能を利用できます。読み取り専用サービスで利用できる Azure Search 操作の試用については、「[Azure Search が使用された Try Azure App Service の試用](search-tryappservice.md)」を参照してください。TryAppService の Web アプリ コードはすべて修正できます。ASP.NET プロジェクトのすべての C# コードを編集して、Web ページのレイアウト、検索クエリの構造、または検索結果を修正できます。これは、サービス接続にクエリ API キーを追加して、読み取り専用の Azure Search サービス インデックスおよびドキュメントの読み込み操作を行うだけのコードです。

####クライアント接続

次の 2 つのコード スニペットでは、URL と API キーを使用して Search サービスへの接続を設定します。前述のように、サービス名と API キーは web.config ファイルに指定されています。REST の呼び出しのためには、要求ヘッダーで管理者キーを渡す必要がありますが、クエリ キーはヘッダーで渡すか、URL で直接渡すことができます。

**[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx) と REST API 呼び出し**

    public class CatalogSearch
    {
        private static readonly Uri _serviceUri;
        private static HttpClient _httpClient;
        public static string errorMessage;

        static CatalogSearch()
        {
            try
            {
                _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
                _httpClient = new HttpClient();
                _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);
            }
            catch (Exception e)
            {
                errorMessage = e.Message;
            }
        }

**[SearchServiceClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchserviceclient.aspx) と .NET**

        static UsgsSearch()
        {
            try
            {
                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

                // Create an HTTP reference to the catalog index. Alternatively, include the index name in the query
                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
        }

##設計パターン

Web アプリと Azure Search を統合する場合、クエリを構成し、結果を表示する必要があります。このセクションでは、ユーザー操作コードを含むプログラムで実行する作業のコードを構成する方法について説明します。スキーマの定義、インデックスの生成、およびデータの取り込みは意図的に除外されています。これらの操作をコーディングする方法については、「[Azure Search: チュートリアル、ビデオ デモ、サンプル](search-video-demo-tutorial-list.md)」のチュートリアルとサンプルを参照してください。

###クエリの構成

インデックスの全文検索は、インデックスが定義されているスキーマで **isSearchable** とマークされているフィールドに対して実行されます。検索用語が入力されると (次の例では "q" という文字列で表されています)、検索エンジンでは検索可能なすべてのフィールドから一致が検索され、**isRetrievable** とマークされているフィールドから結果が返されます。

> [AZURE.NOTE]多くの場合、ほとんどのフィールドを検索可能にしますが、インデックスには、フィルター式にのみ使用されるフィールドが含まれていることがあります。このような場合、検索不可能とマークして全文検索から除外し、取得不可能とマークして検索結果から除外します。

検索クエリでは、ユーザーが指定した入力用語を、ターゲット インデックスを指定する Search 要求にラップします。また、要求のフィルターや調整にパラメーターを使用します。検索文字列に組み込んだ演算子 (+、-、| など) は自動的に処理されます。つまり、検索用語を解析するコーディングは必要ありません。すべての解析は、内部処理として検索エンジンで実行されます。渡した文字列はエンジンによって解析および分析されるものと仮定することができます。

検索クエリには、**検索**と**提案**という 2 つの種類があります。クエリの種類ごとに別のメソッドを定義します。**検索**は、インデックス内のフィールドの全文検索です。**提案**は、Azure Search の先行入力 (またはオートコンプリート) のクエリ機能です。ユーザーが入力した最初の 3 文字に基づいて、可能性のある検索用語の一覧を構築する機能です。ほとんどの場合、**提案**は、区別されないデータを含むフィールドではなく、比較的固有の値または独特の値 (製品名や出版名) を含むフィールドにのみ制限します。

次のコード スニペットでは、REST API を使用するプログラムで検索用語の入力をキャプチャします。入力用語は文字列 q で表されます。その他のパラメーターは、同じ検索ページのファセット ナビゲーション構造からフィルター値で渡すために使用されます。入力用語とフィルター パラメーターはいずれも Search メソッドで使用されます。

        public ActionResult Search(string q = "", string color = null, string category = null, double? priceFrom = null, double? priceTo = null, string sort = null)
        {
            dynamic result = null;

            // If blank search, assume they want to search everything
            if (string.IsNullOrWhiteSpace(q))
                q = "*";

            result = _catalogSearch.Search(q, sort, color, category, priceFrom, priceTo);
            ViewBag.searchString = q;
            ViewBag.color = color;
            ViewBag.category = category;
            ViewBag.priceFrom = priceFrom;
            ViewBag.priceTo = priceTo;
            ViewBag.sort = sort;

            return View("Index", result);
        }
このクエリを受け取る **Search** メソッドは次のように定義されます。このメソッドでは、クエリ文字列のパラメーターを定義しているだけでなく、ファセット ナビゲーション構造 (検索結果の絞り込みに多くの処理を実行するフィルターでサポートされます) と並び順も定義しています。

        public dynamic Search(string searchText, string sort, string color, string category, double? priceFrom, double? priceTo)
        {
            string search = "&search=" + Uri.EscapeDataString(searchText);
            string facets = "&facet=color&facet=categoryName&facet=listPrice,values:10|25|100|500|1000|2500";
            string paging = "&$top=10";
            string filter = BuildFilter(color, category, priceFrom, priceTo);
            string orderby = BuildSort(sort);

            Uri uri = new Uri(_serviceUri, "/indexes/catalog/docs?$count=true" + search + facets + paging + filter + orderby);
            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
            AzureSearchHelper.EnsureSuccessfulSearchResponse(response);

            return AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
        }

検索文字列を構築する .NET メソッドを MVC ビューまたはコントローラーに含めることができます。この関数は、ホーム コントローラーに文字列を渡します。また、結果のデータ構造も定義します。

    function Search() {

        var q = $("#q").val();
        
        $.post('/home/search',
        {
            q: q
        },
        function (data) {
            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
            searchResultsHTML += "<td>DATE EDITED</td></tr>";
            for (var i = 0; i < data.length; i++) {
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
            }

            $("#searchResults").html(searchResultsHTML);

        });

**Search** を呼び出す .NET メソッドは次のようになります。このメソッドは、接続と検索操作を提供するメイン C# プログラムに含めます。

        public DocumentSearchResponse Search(string searchText)
        {
            // Execute search based on query string
            try
            {
                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
                return _indexClient.Documents.Search(searchText, sp);
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
            return null;
        }


###検索結果の処理

検索結果は、インデックス スキーマで isRetrievable とマークされているフィールドで構成された行セットの形式で返されます。結果セットを表示する簡単な方法の 1 つは、MVC で ViewBag システム オブジェクトを使用することです。次のコード スニペットは、[CodePlex の AdventureWorksDemo プロジェクト](https://azuresearchadventureworksdemo.codeplex.com/)の Index.cshtml からの引用です。

	@model dynamic
	
	@{
	    ViewBag.Title = "Search";
	}
	
	<h2>Product search</h2>
	
	@if (@ViewBag.errorMessage != null) {
	    @ViewBag.errorMessage
	} else {
	    <div class="container">
	        <form action="/Home/Search" method="get">
	            <input type="search" name="q" id="q" value="@ViewBag.searchString" autocomplete="off" size="100" /> <button type="submit">Search</button>
	            <input type="hidden" name="color" id="color" value="@ViewBag.color" />
	            <input type="hidden" name="category" id="category" value="@ViewBag.category" />
	            <input type="hidden" name="priceFrom" id="priceFrom" value="@ViewBag.priceFrom" />
	            <input type="hidden" name="priceTo" id="priceTo" value="@ViewBag.priceTo" />
	            <input type="hidden" name="sort" id="sort" value="@ViewBag.sort" />
	        </form>
	    </div>

###ファセット ナビゲーション

この Index.cshmtl ファイルには、自動フィルター処理の分類を提供し、色、価格、またはカテゴリ別に検索結果を徐々に絞り込む、ファセット ナビゲーション構造の構築に使用される HTML も含まれています。

	    if (@Model != null)
	    {
	        <div class="container">
	            <div class="row">
	                <div class="col-md-4">
	                    Colors:
	                    <ul>
	                        @foreach (var colorFacet in Model["@search.facets"].color)
	                        {
	                            <li><a href="#" onclick="document.getElementById('color').value='@colorFacet.value'; 
	document.forms[0].submit(); 
	return false;">@colorFacet.value</a> (@colorFacet.count)</li>
	                        }
	                    </ul>
	                    Categories:
	                    <ul>
	                        @foreach (var categoryFacet in Model["@search.facets"].categoryName)
	                        {
	                            <li><a href="#" onclick="document.getElementById('category').value='@categoryFacet.value'; document.forms[0].submit(); return false;">@categoryFacet.value</a> (@categoryFacet.count)</li>
	                        }
	                    </ul>
	                    Prices:
	                    <ul>
	                        @foreach (var priceFacet in Model["@search.facets"].listPrice)
	                        {
	                            if (priceFacet.count > 0)
	                            {
	                       <li><a href="#" onclick="document.getElementById('priceFrom').value=@(priceFacet.from ?? 0); document.getElementById('priceTo').value=@(priceFacet.to ?? 0); 
	document.forms[0].submit(); return false;
	">@(priceFacet.from ?? 0) - @(priceFacet.to ?? "more")</a> (@priceFacet.count)</li>
	                            }
	                        }
	                    </ul>
	                </div>
	                <div class="col-md-8">
	                    <p>
	                        Sort -
	                        <a href="#" onclick="document.getElementById('sort').value=null; document.forms[0].submit(); return false;">by relevance</a>
	                        <a href="#" onclick="document.getElementById('sort').value='listPrice'; document.forms[0].submit(); return false;">by list price</a>
	                        <a href="#" onclick="document.getElementById('sort').value='color'; document.forms[0].submit(); return false;">by color</a>
	                    </p>
	                    <p>Found @Model["@odata.count"] products in the catalog</p>
	
	                    <ul>
	                        @foreach (var product in Model.value)
	                        {
	                            <li>
	                                <h3><b>@product.name</b></h3>
	                                price: @product.listPrice, color: @product.color, weight: @product.weight, size: @product.size
	                            </li>
	                        }
	                    </ul>
	                </div>
	            </div>
	        </div>
	    }
	}


###検索結果の強調表示

検索結果の検索用語のインスタンスにスタイルを適用する処理は、ヒット ハイライトと呼ばれます。Azure Search のヒット ハイライトは、クエリのハイライト検索パラメーターで指定します。このパラメーターに、一致する用語をスキャンするフィールドのコンマ区切りの一覧を指定します。実際には、任意のスタイルを適用できます。次の 3 つのコード スニペットは、[TryAppService + Azure Search チュートリアル](search-tryappservice.md)からの引用です。

まず、検索パラメーターとしてヒット ハイライトを使用し、一致する用語を確認するフィールドの一覧を指定します。ヒット ハイライトで使用する HTML スタイルを指定します。

	// Set the Search parameters used when executing the search request
	     var sp = new SearchParameters
	{
	// Include a count of results in the query result
	     IncludeTotalResultCount = true,
	// Limit the results to 20 documents
	     Top = 20,
	// Enable hit-highlighting
	     HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
	     HighlightPreTag = "<b>",
	     HighlightPostTag = "</b>",
	};

次に、検索結果内を反復処理して、強調表示する必要がある文字列を検索します。private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)

	  {
	     if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
	      {
	      string highlightedResult = string.Join("...", item.Highlights[fieldName]);
	      return new HtmlString(highlightedResult);
	      }
	      return new HtmlString(item.Document[fieldName].ToString());
	   }

最後に、前のスニペットで評価された検索セットを指定して、検索結果のレイアウトを提供します。

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


##一般的なコーディング方法

MVC、.NET プログラミング、または REST API の初心者の場合、 以下のセクションを参照すると、コーディング方法の学習時間を短縮できます。

###MVC テンプレート

次の表は、Azure Search を含むアプリケーションで MVC テンプレート コンポーネントを使用する方法をまとめたものです。MVC 4 または MVC 5 を使用している場合、通常、これらのモジュールに Azure Search が追加されます。

ファイル|説明
----|-----------
web.config|サービス URL と API キーを指定します。値を読み取るメイン プログラム モジュールの System.Configuration への参照を追加します。
Program.cs|メイン プログラムでは、サービスとの接続を確立する HttpClient または SearchServiceClient を設定します。このプログラムに Search メソッドを追加します。
DataModel|使用されません。インデックス作成操作とデータの読み込み操作が別のプログラムと仮定すると、Web アプリケーションの Azure Search にデータ モデルは必要ありません。
ビュー|ビューには、検索ボックスの入力から、検索結果を処理する動的 HTML まで、アプリケーション Web ページの HTML が含まれます。
コントローラー|クエリの構造とエラー処理は、一般的に HomeContoller.cs に含まれます。コントローラーには、少なくとも Azure Search から結果を取得し、結果セットをビューに転送する検索メソッドを含める必要があります。 

また、オートコンプリート クエリに提案を使用する場合、ユーザーが指定した検索用語の入力と一致する値がインデックスに含まれるかどうかに応じて、提案されたクエリを返すメソッドも含めます。

###どちらを使用するか: .NET クライアント ライブラリとREST API

ASP.NET アプリケーションでは、.NET クライアント ライブラリが推奨されています。その理由は、HTTP 接続を設定し、JSON シリアル化と逆シリアル化を処理する機能があり、コードを簡略化できるためです。

2 つのアプローチの機能の類似性によって、どちらの API を選択するかが決まる場合もあります。一般的に、[.NET クライアント ライブラリ](https://msdn.microsoft.com/library/azure/dn951165.aspx)と [Service REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) は、必要な操作が両方に実装されている限り、相互に置き換えることができます。ただし、新しい機能がプレビュー リリースの一部として REST API に先に導入され、.NET ライブラリには数か月も後に追加されることがあります。たとえば、インデクサーがあります。インデクサーは、特定のデータ ソースの種類からのデータ読み込み操作を自動化するために使用されます。インデクサーはプレビューの REST API に導入されてから、数か月後にクライアント ライブラリに導入されました。機能の実装に関する制限は、機能のドキュメントに記載されています。

###REST API に JSON のシリアル化と逆シリアル化の AzureSearchHelper.cs を含める

.NET ライブラリではこの手順は自動的に行われますが、Service REST API の場合、サービスとの要求と応答の交換で JSON ドキュメントのシリアル化と逆シリアル化を行う必要があります。JSON は、インデックスのドキュメントの読み込み時または更新時に使用されるデータ転送のペイロード形式です。

JSON シリアル化のコードについては、**AzureSearchHelper.cs** というファイルに含まれるいくつかのサンプルを参照してください。

	using System;
	using System.Net.Http;
	using System.Text;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Converters;
	using Newtonsoft.Json.Serialization;
	
	namespace CatalogCommon
	{
	    public class AzureSearchHelper
	    {
	        public const string ApiVersionString = "api-version=2014-07-31-Preview";
	
	        private static readonly JsonSerializerSettings _jsonSettings;
	
	        static AzureSearchHelper()
	        {
	            _jsonSettings = new JsonSerializerSettings
	            {
	                Formatting = Formatting.Indented, // for readability, change to None for compactness
	                ContractResolver = new CamelCasePropertyNamesContractResolver(),
	                DateTimeZoneHandling = DateTimeZoneHandling.Utc
	            };
	
	            _jsonSettings.Converters.Add(new StringEnumConverter());
	        }
	
	        public static string SerializeJson(object value)
	        {
	            return JsonConvert.SerializeObject(value, _jsonSettings);
	        }
	
	        public static T DeserializeJson<T>(string json)
	        {
	            return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
	        }
	
	        public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
	        {
	            UriBuilder builder = new UriBuilder(uri);
	            string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
	            builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;
	
	            var request = new HttpRequestMessage(method, builder.Uri);
	
	            if (json != null)
	            {
	                request.Content = new StringContent(json, Encoding.UTF8, "application/json");
	            }
	
	            return client.SendAsync(request).Result;
	        }
	
	        public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
	        {
	            if (!response.IsSuccessStatusCode)
	            {
	                string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
	                throw new Exception("Search request failed: " + error);
	            }
	        }
	    }
	}

###コードの構成

ワークロードを同じ Visual Studio ソリューション内で複数のスタンドアロン プロジェクトに分割すると、各プログラムを柔軟に設計、保守、実行できるようになります。次の 3 つをお勧めします。

- インデックス作成コード
- データ取り込みコード
- ユーザー操作コード

Azure Search のインデックス作成操作とドキュメント操作 (ドキュメントの追加や更新、クエリの実行など) は、相互に完全に独立しています。つまり、検索要求を構成し、結果を表示する ASP.NET ユーザー操作コードから、インデックス管理を切り離すことができます。

ここで紹介するほとんどのコード サンプルでは、1 つのプロジェクトでインデックスの作成と読み込みが行われます (さまざまなサンプルで DataIndexer、CatalogIndexer、または DataCatalog と呼ばれます)。一方、検索の要求と応答を処理するコードは、ASP.NET MVC アプリケーション プロジェクトに含めます。コード サンプルでは、インデックス作成とドキュメントのアップロードを 1 つのプロジェクトにまとめることは実用的ですが、運用コードの場合、これらの操作を分離する可能性があります。インデックスを一度作成すると、変更されることはほとんどありません (変更に応じて再構築が必要になる場合があります) が、ドキュメントは何度も更新される可能性があります。

ワークロードを分けると、Azure Search のアクセス許可レベルの分け方 (完全な管理者権限とクエリのみの権限)、異なるプログラミング言語の使用、プログラムごとのより詳細な依存関係などの利点があります。さらに、プログラムを別々に修正したり、中央のインデックス作成アプリケーションで構築および保守しているインデックスを操作するフロントエンド アプリケーションを複数作成したりすることもできるようになります。

##次のステップ

Azure Search と ASP.NET の統合についてさらに理解を深めるには、次のリンク先を参照してください。

- [.NET アプリケーションから Azure Search を使用する方法](search-howto-dotnet-sdk.md) 
- [Azure Search 開発者向けのケース スタディ](search-dev-case-study-whattopedia.md)
- [Azure Search 開発の一般的ワークフロー](search-workflow.md) 

<!---HONumber=Nov15_HO3-->