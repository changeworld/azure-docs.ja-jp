<properties
	pageTitle="Fiddler を使用して Azure Search REST API を評価およびテストする方法"
	description="コードを作成せずに、Fiddler を使用して、Azure Search の可用性を検証し、REST API を試してみます。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Fiddler を使用して Azure Search REST API を評価およびテストする方法

この記事では、コードを記述することなく、[Telerik から無料でダウンロードできる](http://www.telerik.com/fiddler) Fiddler を利用し、Azure Search REST API を使用して HTTP 要求を発行し、応答を表示する方法について説明します。Azure Search サービス REST API については、[MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx) を参照してください。

以下の手順では、インデックスを作成し、ドキュメントをアップロードし、インデックスのクエリを実行してから、システムでサービス情報のクエリを実行します。

これらの手順を完了するには、Azure Search サービスおよび `api-key` が必要です。作業を始める方法については、「[Create an Azure Search service in the portal (ポータルで Azure Search サービスを作成する)](search-create-service-portal.md)」を参照してください。

## インデックスを作成する

1. Fiddler を起動します。ファイル メニューの **[トラフィックのキャプチャ]** をオフにして、現在のタスクには関係ない外部の HTTP アクティビティを非表示にします。

3. [Composer] タブで、次のような要求を生成します。

  	![][1]

2. **[PUT]** を選択します。

3. サービス URL、要求属性、および API バージョンを指定する URL を入力します。次の点に留意してください。
   + HTTPS をプレフィックスとして使用する
   + 要求属性は "/indexes/hotels" です。この属性は Search に "hotels" という名前のインデックスを作成することを指定します。
   + API バージョンは、小文字で「?api-version=2015-02-28」と指定します。Azure Search は定期的に更新をデプロイするため、API バージョンは重要です。まれに、サービスの更新により API に対する重要な変更が発生する可能性があります。API バージョンを使用すると、既存のバージョンの使用を継続して、必要に応じて新しいバージョンにアップグレードすることができます。

    URL 全体は、次の例のようになります。

         https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.	ホストと API キーをサービスに有効な値で置き換え、要求ヘッダーを指定します。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.	要求の本文には、インデックス定義を構成するフィールドを貼り付けます。

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false,},
          {"name": "hotelName", "type": "Edm.String"},
          {"name": "category", "type": "Edm.String"},
          {"name": "tags", "type": "Collection(Edm.String)"},
          {"name": "parkingIncluded", "type": "Edm.Boolean"},
          {"name": "smokingAllowed", "type": "Edm.Boolean"},
          {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
          {"name": "rating", "type": "Edm.Int32"},
          {"name": "location", "type": "Edm.GeographyPoint"}
         ]
        }

6.	**[実行]** をクリックします。

数秒で、インデックスが正常に作成されたことを示す HTTP 201 応答がセッション一覧に表示されます。

HTTP 504 が表示された場合は、URL で HTTPS の指定を確認してください。HTTP 400 または 404 を確認するには、要求の本文をチェックして、コピーまたは貼り付けのエラーがないことを確認します。HTTP 403 は、API キーに問題があることを示しています (キーが無効か、または API キーの指定方法に構文エラーがあります)。

## ドキュメントを読み込む

[Composer] タブでは、ドキュメントを送信する要求を次のように記述します。要求の本文には、4 つの hotel の検索データが含まれています。

   ![][2]

1. **[POST]** を選択します。

2.	HTTPS で始まる URL の後に自分のサービス URL、次に「/indexes/<'indexname'>/docs/index?api-version=2015-02-28」と入力します。URL 全体は、次の例のようになります。

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.	要求ヘッダーは前と同じにします。ホストと API キーはサービスで有効な値に置き換えたことを思い出してください。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	要求の本文には、hotels インデックスに追加する 4 つのドキュメントが含まれています。

        {
        "value": [
        {
        	"@search.action": "upload",
        	"hotelId": "1",
        	"baseRate": 199.0,
        	"description": "Best hotel in town",
        	"hotelName": "Fancy Stay",
        	"category": "Luxury",
        	"tags": ["pool", "view", "wifi", "concierge"],
        	"parkingIncluded": false,
        	"smokingAllowed": false,
        	"lastRenovationDate": "2010-06-27T00:00:00Z",
        	"rating": 5,
        	"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "2",
        	"baseRate": 79.99,
        	"description": "Cheapest hotel in town",
        	"hotelName": "Roach Motel",
        	"category": "Budget",
        	"tags": ["motel", "budget"],
        	"parkingIncluded": true,
        	"smokingAllowed": true,
        	"lastRenovationDate": "1982-04-28T00:00:00Z",
        	"rating": 1,
        	"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "3",
        	"baseRate": 279.99,
        	"description": "Surprisingly expensive",
        	"hotelName": "Dew Drop Inn",
        	"category": "Bed and Breakfast",
        	"tags": ["charming", "quaint"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "4",
        	"baseRate": 220.00,
        	"description": "This could be the one",
        	"hotelName": "A Hotel for Everyone",
        	"category": "Basic hotel",
        	"tags": ["pool", "wifi"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
          }
         ]
        }

8.	**[実行]** をクリックします。

数秒で、セッション一覧に HTTP 200 応答が表示されます。この応答は、ドキュメントが正常に作成されたことを示します。207 が表示された場合は、少なくとも 1 つのドキュメントのアップロードが失敗しています。404 が表示された場合は、要求のヘッダーまたは本文に構文エラーがあります。

## インデックスのクエリを実行する

インデックスとドキュメントが読み込まれたら、クエリを発行することができます。[Composer] タブで、サービスのクエリを実行する GET コマンドは次のようになります。

   ![][3]

1.	**[GET]** を選択します。

2.	HTTPS で始まる URL の後に自分のサービス URL、次に「/indexes/<'indexname'>/docs?」、クエリ パラメーターの順に入力します。例として、サンプル ホスト名をサービスで有効な値に置き換えた、次の URL を使用します。

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    このクエリは、用語 "motel" を検索し、評価のファセット カテゴリを取得します。

3.	要求ヘッダーは前と同じにします。ホストと API キーはサービスで有効な値に置き換えたことを思い出してください。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

応答コードは 200 で、応答出力は次の図のようになります。

   ![][4]

次のクエリ例は、MSDN の「[Search Index operation (Azure Search API) (検索インデックス操作 (Azure Search API))](http://msdn.microsoft.com/library/dn798927.aspx)」に記載されているものです。このトピックのクエリ例の多くに、Fiddler では許可されないスペースが含まれています。Fiddler でクエリを実行する場合は、次のように、スペースを + 文字に置き換えてからクエリ文字列に貼り付けてください。

**スペースを置き換える前:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**スペースを + で置き換えた後:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## システムのクエリを実行する

システムのクエリを実行して、ドキュメント数とストレージ消費を取得することもできます。[Composer] タブでは、要求は次のように表示され、応答ではドキュメント数と使用されているストレージ領域が返されます。

 ![][5]

1.	**[GET]** を選択します。

2.	次のように、サービスの URL の後に「/indexes/hotels/stats?api-version=2015-02-28」を続けて URL を入力します。

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.	ホストと API キーをサービスに有効な値で置き換え、要求ヘッダーを指定します。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	要求の本文は空のままにします。

5.	**[実行]** をクリックします。セッション一覧に、状態コード HTTP 200 が表示されます。コマンドに対して送信されたエントリを選択します。

6.	**[インスペクター]** タブの **[ヘッダー]** をクリックし、JSON 形式を選択します。ドキュメント数とストレージ サイズ (KB) が表示されます。

## 次のステップ

コードを作成しないで Azure Search を管理および使用する方法の詳細については、次のリンクを参照してください。

-  [Microsoft Azure で検索サービスを管理する](search-manage.md)
-  [Azure Search で Chrome Postman を使用する方法](search-chrome-postman.md)

<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
 

<!---HONumber=July15_HO2-->