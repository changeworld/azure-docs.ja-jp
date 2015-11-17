<properties
	pageTitle="REST 呼び出しを使用して Azure Search のクエリを作成する | Microsoft Azure | ホスト型クラウド検索サービス"
	description="Azure Search の検索クエリを作成し、検索パラメーターを使用して検索結果のフィルター処理、並べ替え、ファセットを行います (.NET ライブラリまたは SDK を使用)。"
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
	ms.date="11/10/2015"
	ms.author="heidist"/>

#REST 呼び出しを使用して Azure Search のクエリを作成する 
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

この記事では、[Azure Search REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) を使用してインデックスに対するクエリを作成する方法について説明します。以下の内容の一部は、[ドキュメントの検索 (Azure Search REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx) に関するページから抜粋したものです。詳しいコンテキストについては、元の記事を参照してください。

インポートの前提条件として、既存のインデックスの準備が完了しており、検索可能なデータを提供するドキュメントと共に読み込まれていることが挙げられます。

REST API を使用する場合、クエリは GET HTTP 要求に基づきます。次のコード スニペットは、[スコアリング プロファイルのサンプル](search-get-started-scoring-profiles.md)から取得したものです。

        static JObject ExecuteRequest(string action, string query = "")
        {
            // original:  string url = serviceUrl + indexName + "/" + action + "?" + ApiVersion; 
            string url = serviceUrl + indexName + "/docs?" + action ;
            if (!String.IsNullOrEmpty(query))
            {
                url += query + "&" + ApiVersion;
            }

            string response = ExecuteGetRequest(url);
            return JObject.Parse(response);

        }

        static string ExecuteGetRequest(string requestUri)
        {
            //This will execute a get request and return the response
            using (HttpClient client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.GetAsync(requestUri).Result;        // Searches are done over port 80 using Get
                return response.Content.ReadAsStringAsync().Result;
            }

        }

<!---HONumber=Nov15_HO3-->