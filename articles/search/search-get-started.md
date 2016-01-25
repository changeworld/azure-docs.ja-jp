<properties 
	pageTitle="Azure Search の使用 | Microsoft Azure | ホスト型クラウド検索サービス" 
	description="Microsoft Azure のホスト型クラウド検索サービスである Azure Search の概要。" 
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
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/11/2016" 
	ms.author="heidist"/>

# Azure Search の使用

Microsoft Azure Search は、検索機能をカスタム アプリケーションに埋め込むことができるホスト型クラウド検索サービスです。検索エンジンとデータのストレージを提供し、.NET ライブラリまたは REST API を使用してアクセスおよび管理できます。

この記事では、Azure Search REST API の概要を示します。

.NET 開発者向けに、Azure Search.NET SDK を使用する別の方法もあります。詳細については、「[.NET での Azure Search の使用](search-get-started-dotnet.md)」または「[Azure Search .NET SDK の使用方法](search-howto-dotnet-sdk.md)」を参照してください。


> [AZURE.NOTE]このチュートリアルを完了するには、[Azure サブスクリプション](../includes/free-trial-note.md)が必要です。試用版サブスクリプションにサインアップする準備がまだできていない場合は、このチュートリアルをスキップし、代わりに [Azure App Service を試用](https://tryappservice.azure.com/)してください。この代替オプションの場合、Azure Search で ASP.NET Web アプリを無料で使用でき (セッションごとに 1 時間)、サブスクリプションは不要です。
 
<a id="sub-1"></a>
## Azure Search サービスを作成する

管理者は、共有サービスを選択する場合は無料で、または専用のリソースを使用する場合は割引料金で、Search サービスを既存のサブスクリプションに追加できます。

登録者は自動的に、共有のマルチテナント Search サービスに無料でアクセスできます。このサービスは、学習目的に、または概念実証テストや小規模な開発用検索プロジェクトのために使用できます。

既存のサブスクリプションを使用して、[Azure ポータル](https://portal.azure.com)にサインインします。詳しい手順については、「[Azure クラシック ポータルでの Azure Search サービスの作成](search-create-service-portal.md)」をご覧ください。

## サービス URL と API キーの取得

サービスが作成されたら、構成設定に戻って URL と API キーを取得できます。Search サービスに接続するには、URL に加えて、呼び出しを認証するための API キーが必要になります。ここでは、これらの値を簡単に検索する方法を示します。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ジャンプ バーで、**[Search サービス]** をクリックして、サブスクリプション用にプロビジョニングされたすべての Azure Search サービスの一覧を表示します。
3. 使用するサービスを選択します。
4.	サービス ダッシュボードには、**[プロパティ]** と **[キー]** のタイル、およびリソース使用がひとめでわかる使用状況情報が表示されます。 


<a id="sub-3"></a>
## サービス操作をテストする

検証手順として、サービスが動作しており、クライアント アプリケーションからアクセスできるかどうかをテストします。この手順は、[Telerik から無料でダウンロード](http://www.telerik.com/fiddler)できる Fiddler を使用し、HTTP 要求を発行して応答を確認します。Fiddler を使用することで、コードを記述することなく、API をすぐにテストできます。

以下の手順では、インデックスを作成し、ドキュメントをアップロードし、インデックスのクエリを実行してから、システムでサービス情報のクエリを実行します。

### インデックスを作成する

1. Fiddler を起動します。ファイル メニューの **[トラフィックのキャプチャ]** をオフにして、現在のタスクには関係ない外部の HTTP アクティビティを非表示にします。[Composer] タブで、次のような要求を生成します。 

  	![][16]

2. **[PUT]** を選択します。

3. サービス URL ([プロパティ] ページを参照)、要求属性、および API バージョンを指定する URL を入力します。次の点に留意してください。
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
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
          {"name": "hotelName", "type": "Edm.String", "suggestions": true},
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

### ドキュメントを読み込む

[Composer] タブでは、ドキュメントを送信する要求を次のように記述します。要求の本文には、4 つの hotel の検索データが含まれています。

   ![][17]

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

### インデックスのクエリを実行する

インデックスとドキュメントが読み込まれたら、クエリを発行することができます。[Composer] タブで、サービスのクエリを実行する GET コマンドは次のようになります。

   ![][18]

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
 
   ![][19]

次のクエリ例は、MSDN の「[Search Index operation (Azure Search API) (検索インデックス操作 (Azure Search API))](http://msdn.microsoft.com/library/dn798927.aspx)」に記載されているものです。このトピックのクエリ例の多くに、Fiddler では許可されないスペースが含まれています。Fiddler でクエリを実行する場合は、次のように、スペースを + 文字に置き換えてからクエリ文字列に貼り付けてください。

**スペースを置き換える前:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**スペースを + で置き換えた後:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28
### システムのクエリを実行する

システムのクエリを実行して、ドキュメント数とストレージ消費を取得することもできます。[Composer] タブでは、要求は次のように表示され、応答ではドキュメント数と使用されているストレージ領域が返されます。

   ![][20]

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

6.	[インスペクター] タブの [ヘッダー] をクリックし、JSON 形式を選択します。ドキュメント数とストレージ サイズ (KB) が表示されます。

 	![][21]

<a id="sub-4"></a>
## Search サービス ダッシュボードを調べる

構成ページの場所を思い出せない場合は、次の手順に従って、サービス ダッシュボードを表示します。

1.	既存のサブスクリプションを使用して、[Azure ポータル](https://portal.azure.com)にサインインします。 
2.	**[ホーム]** をクリックしてから、検索サービスのタイルをクリックします。

 	![][22]

4.	タイルをクリックすると、サービスのダッシュボードが開きます。**[開始]**、**[停止]**、および **[削除]** コマンドが上部に表示されます。

5.	ページの上部付近にサービス URL が示されていることに注意してください。この URL は、Azure Search サービスに接続するために必要になります。
	
7.	**[キー]** アイコンをクリックして API キーを表示しますサービスに対して認証を行うには、管理者キーが必要です。プライマリまたはセカンダリのどちらかを使用できます。必要に応じて、サービスへの読み取り専用アクセス用のクエリ キーを作成できます。


<!--Next steps and links -->
<a id="next-steps"></a>
## 実際に使ってみる

次のステップに進む準備ができていますか。 次のリンクでは、Azure Search を使用する検索アプリケーションを作成して管理する方法について、その他の資料を参照できます。

- [Azure Search ジオサーチのサンプルの作成](search-create-geospatial.md)

- [Microsoft Azure で検索サービスを管理する](search-manage.md)

- [Azure Search とは](search-what-is-azure-search.md)

- [Azure Search サービス REST API](http://msdn.microsoft.com/library/dn798935.aspx)

- [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)

- [Channel 9 video: Introduction to Azure Search (チャネル 9 ビデオ: Azure Search の概要)](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)

- [Channel 9 video: Azure Search and Geospatial Data (チャネル 9: Azure Search と地理空間データ)](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)

- [Cloud Cover episode 152: Generate an index in Azure Search (クラウド カバー エピソード 152: Azure Search でのインデックスの生成)](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)

<!--Anchors-->
[Start with the free service]: #sub-1
[Upgrade to standard search]: #sub-2
[Test service operations]: #sub-3
[Explore Search service dashboard]: #sub-4
[Try it out]: #next-steps

<!--Image references-->
[6]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
[7]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
[8]: ./media/search-get-started/Azuresearch_Configure1_3a_Gallery.PNG
[9]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
[10]: ./media/search-get-started/AzureSearch_Configure1_5_SearchTile.PNG
[11]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
[12]: ./media/search-get-started/AzureSearch_Configure1_7a_Free.PNG
[13]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[14]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
[15]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
[16]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
[17]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
[18]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
[19]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
[20]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
[21]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
[22]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[23]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG


<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md

<!---HONumber=AcomDC_0114_2016-->