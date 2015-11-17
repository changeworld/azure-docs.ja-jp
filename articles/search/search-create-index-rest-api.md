<properties
	pageTitle="REST API を使用した Azure Search インデックスの作成 | Microsoft Azure | ホスト型クラウド検索サービス"
	description="Azure Search と HTTP REST API を使用して、コードでインデックスを作成します。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# REST API を使用した Azure Search インデックスの作成
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

この記事では、[Azure Search REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) を使用してインデックスを作成する方法について説明します。以下のコンテンツの一部は、[インデックスの作成 (Azure Search REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx) に関するページから抜粋したものです。詳しいコンテキストについては、元の記事を参照してください。

インデックスを作成するうえでの前提条件として、(通常は httpClient を介して) 検索サービスへの接続が既に確立されていることが挙げられます。

REST API を介してインデックスを作成する 3 つのパートから成るアプローチでは、接続をセットアップし、インデックス スキーマを指定してから、インデックスを構築するコマンドを実行します。次のコード スニペットは、[スコアリング プロファイルのサンプル](search-get-started-scoring-profiles.md)から取得したものです。

##インデックス スキーマの定義

REST API は、JSON で要求を受け入れます。スキーマの作成方法の 1 つに、スタンドアロン スキーマ ファイルを JSON ドキュメントとして含める方法があります。次の例でこれを具体的に示します。

このスニペットの派生元のサンプルでは、以下の JSON は schema.json という名前のファイルに保存されています。

	{
	    "name": "musicstoreindex",
	    "fields": [
	        { "name": "key", "type": "Edm.String", "key": true },
	        { "name": "albumTitle", "type": "Edm.String"},
	        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
	        { "name": "genre", "type": "Edm.String" },
	        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
	        { "name": "artistName", "type": "Edm.String"},
	        { "name": "orderableOnline", "type": "Edm.Boolean" },
	        { "name": "rating", "type": "Edm.Int32" },
	        { "name": "tags", "type": "Collection(Edm.String)" },
	        { "name": "price", "type": "Edm.Double", "filterable": false },
	        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
	        { "name": "inventory", "type": "Edm.Int32" },
	        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
	    ],
	    "scoringProfiles": [
	        {
	            "name": "boostGenre",
	            "text": {
	                "weights": {
	                    "albumTitle": 5,
	                    "genre": 50,
	                    "artistName": 5
	                }
	            }
	        },
	        {
	            "name": "newAndHighlyRated",
	            "functions": [
	                {
	                    "type": "freshness",
	                    "fieldName": "lastUpdated",
	                    "boost": 10,
	                    "interpolation": "quadratic",
	                    "freshness": {
	                        "boostingDuration": "P365D"
	                    }
	                },
	                {
	                    "type": "magnitude",
	                    "fieldName": "rating",
	                    "boost": 10,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 1,
	                        "boostingRangeEnd": 5,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        },
	        {
	            "name": "boostMargin",
	            "functions": [
	
	                {
	                    "type": "magnitude",
	                    "fieldName": "margin",
	                    "boost": 50,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 50,
	                        "boostingRangeEnd": 100,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        }
	    ]
	}

##HTTP 要求をセットアップしてインデックスを接続、作成する

REST API は、すべての要求で HTTP 接続を作成します。各要求は、サービスの URL、使用している API のバージョン、サービスでの読み取り/書き込み操作を許可する管理者キー (以下では、プライマリ キーと呼んでいます。ポータルでそのように命名されているためです) を常に規定します。

このコード スニペッドでは、app.config ファイルで指定されているとおりに (ここでは示されていません)、インデックスの名前などの静的な値を入力として使用します。サンプルは、コードの簡潔さを保つためにこのような方法で作成されています。

        private const string ApiVersion = "api-version=2015-02-28";
        private static string serviceUrl = ConfigurationManager.AppSettings["serviceUrl"];
        private static string indexName = ConfigurationManager.AppSettings["indexName"];
        private static string primaryKey = ConfigurationManager.AppSettings["primaryKey"];

次の例では、サービスの URL への HTTP PUT 要求のほか、`api-version` と作成するインデックスの名前が示されています。

        static bool CreateIndex()
        {
            // Create an index using an index name
            Uri requestUri = new Uri(serviceUrl + indexName + "?" + ApiVersion);

            // Load the json containing the schema from an external file
            string json = File.ReadAllText("schema.json");

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PutAsync(requestUri,        // To create index use PUT
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.Created)   // For Posts we want to know response had no content
                {
                    Console.WriteLine("Index created. \r\n");
                    return true;
                }

                Console.WriteLine("Index creation failed: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->