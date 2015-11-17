<properties
	pageTitle="REST API を使用した Azure Search へのデータのインポート | Microsoft Azure | ホステッド クラウド検索サービス"
	description="REST API を使用して Azure Search のインデックスにデータをアップロードする方法"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# REST API を使用した Azure Search へのデータのインポート
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

この記事では、[Azure Search REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) を使用してインデックスにデータを読み込む方法について説明します。内容の一部は、[ドキュメントの追加、更新、または削除] (Azure Search REST API) (https://msdn.microsoft.com/library/azure/dn798930.aspx)から抜粋しています。詳しいコンテキストについては、元の記事を参照してください。

インポートの前提条件として、データを受け取るインデックスが事前に用意されている必要があります。

REST API を使用する場合、データの取り込みは HTTP の PUT 要求または POST 要求に基づいて行われます。次のコード スニペットは、[スコアリング プロファイルのサンプル](search-get-started-scoring-profiles.md)から取得したものです。

        static bool PostDocuments(string fileName)
        {
            // Add some documents to the newly created index
            Uri requestUri = new Uri(serviceUrl + indexName + "/docs/index?" + ApiVersion);

            // Load the json containing the data from an external file
            string json = File.ReadAllText(fileName);

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PostAsync(requestUri,       // To add data use POST
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.OK)
                {
                    Console.WriteLine("Documents posted from file {0}. \r\n", fileName);
                    return true;
                }

                Console.WriteLine("Documents failed to upload: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->