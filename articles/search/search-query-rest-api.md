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
	ms.date="11/17/2015"
	ms.author="heidist"/>

# REST 呼び出しを使用して Azure Search のクエリを作成する
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

この記事では、[Azure Search REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) を使用してインデックスに対するクエリを作成する方法について説明します。以下の内容の一部は、[ドキュメントの検索 (Azure Search REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx) に関するページから抜粋したものです。詳しいコンテキストについては、元の記事を参照してください。

インポートの前提条件として、既存のインデックスの準備が完了しており、検索可能なデータを提供するドキュメントと共に読み込まれていることが挙げられます。

REST API を使用してインデックスを検索するには、GET HTTP 要求を発行します。クエリ パラメーターは、HTTP 要求の URL 内で定義します。

**要求と要求ヘッダー**:

URL では、サービス名、インデックス名、適切な API バージョンを提供する必要があります。URL の最後のクエリ文字列では、クエリ パラメーターを指定します。クエリ文字列内のパラメーターの 1 つで、適切な API バージョン (このドキュメントが書かれた時点で最新の API バージョンは "2015-02-28") を指定する必要があります。

要求ヘッダーとして、Content-Type を定義し、サービスのプライマリまたはセカンダリ管理キーを提供する必要があります。

	GET https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Azure Search では、非常に強力なクエリを作成できる多くのオプションが用意されています。クエリ文字列のパラメーターの詳細については、「[こちらのページ](https://msdn.microsoft.com/library/azure/dn798927.aspx)」を参照してください。

**例**:

さまざまなクエリ文字列を含むいくつかの例を次に示します。これらの例では、"hotels" という名前のダミーのインデックスを使用しています。

用語 "quality" のインデックス全体を検索します。

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=quality&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

インデックス全体を検索します。

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

インデックス全体を検索し、特定のフィールド (lastRenovationDate) で並べ替えます。

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

クエリが成功すると状態コード "200 OK" が返され、検索結果は応答本文に JSON 形式で格納されています。詳細については、[こちらのページ](https://msdn.microsoft.com/library/azure/dn798927.aspx)の「Response (応答)」セクションを参照してください。

<!---HONumber=AcomDC_0128_2016-->