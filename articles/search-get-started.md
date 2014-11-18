<properties title="Azure Search の使用" pageTitle="Azure Search の使用" description="Azure Search の使用" metaKeywords="" services="Azure Search" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article" ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# Azure Search の使用

[WACOM.INCLUDE [この記事では Azure プレビュー ポータルを使用します](../includes/preview-portal-note.md)]

Microsoft Azure Search (パブリック プレビュー) は、検索機能をカスタム アプリケーションに埋め込むことができる新しいサービスです。検索エンジンとデータのストレージを提供し、REST API を使用してアクセスおよび管理できます。Azure Search を使用する理由の詳細については、「[Azure Search Scenarios and Capabilities (Azure Search のシナリオと機能)][Azure Search Scenarios and Capabilities (Azure Search のシナリオと機能)]」を参照してください。

管理者は、共有サービスを選択する場合は無料で、または専用のリソースを使用する場合は割引料金で、Search サービスを既存のサブスクリプションに追加できます。この記事で取り上げる内容は次のとおりです。

<!--Table of contents -->

-   [無料サービスで開始する][無料サービスで開始する]
-   [標準検索にアップグレードする][標準検索にアップグレードする]
-   [サービス操作をテストする][サービス操作をテストする]
-   [Search サービス ダッシュボードを調べる][Search サービス ダッシュボードを調べる]
-   [実際に使ってみる][実際に使ってみる]

## 無料サービスで開始する

登録者は自動的に、共有のマルチテナント Search サービスに無料でアクセスできます。このサービスは、学習目的に、または概念実証テストや小規模な開発用検索プロジェクトのために使用できます。無料版を使用するには、次の手順でサインアップします。

1.  既存のサブスクリプションを使用して、[Azure プレビュー ポータル][Azure プレビュー ポータル]にサインインします。この URL でプレビュー ポータルにアクセスできます。プレビュー ポータルを使用することが要件となります。

2.  ページの下部にある **[新規]** をクリックします。

    ![][0]

3.  ページの上部にある **[すべて]** をクリックします。

    ![][1]

4.  ギャラリーで、**[データ、ストレージ、キャッシュ、+ バックアップ]** をクリックします。

    ![][2]

5.  **[すべて表示]** をクリックして、データに関するすべてのサービスの一覧を展開します。

    ![][3]

6.  データ サービスで、**[検索]** をクリックします。

    ![][4]

7.  [検索] ページの下部にある **[作成]** をクリックします。

8.  サービス URL にサービス名を小文字で入力します。文字列にはダッシュやスペースは使用せず、15 文字以内にしてください。

    ![][5]

9.  **[料金レベル]** の矢印をクリックして、料金オプションを選択します。ページの下部で、**[無料]** を選択し、**[選択]** をクリックします。無料版は、チュートリアルを試用して概念実証コードを書くには充分な機能を提供しますが、運用アプリケーションには対応していません。

    ![][6]

10. **[リソース グループ]** の矢印をクリックして、既存のグループを選択するか、新しいグループを作成します。リソース グループは、一般的な目的で使用するサービスとリソースのコンテナーです。たとえば、Azure Search、Azure Websites、Azure BLOB ストレージを使用してカスタム検索アプリケーションを構築する場合は、リソース グループを作成することで、これらのサービスをポータル管理ページにまとめておくことができます。

11. 複数のサブスクリプションを所有していて、この Search サービスには別のサブスクリプションを使用する場合は、**[サブスクリプション]** で矢印をクリックします。

12. **[場所]** で矢印をクリックして、データ センターのリージョンを選択します。このプレビューでは、米国西部、米国東部、北ヨーロッパ、東南アジアから選択できます。後で、他のリージョンがオンラインになった場合は、作成しているサービスのリージョンを選択します。複数のデータ センターへのリソースの分散は、パブリック プレビューの構成ではサポートされません。

13. **[作成]** をクリックしてサービスをプロビジョニングします。**[作成]** は、必要な値をすべて入力した後でのみ有効になります。

数分で、サービスが作成されます。構成の設定に戻って、URL または API キーを取得できます。Search サービスに接続するには、URL に加えて、呼び出しを認証するための API キーが必要になります。ここでは、これらの値を簡単に検索する方法を示します。

1.  **[参照]**、**[すべて]**、**[データ、ストレージ、キャッシュ、+ バックアップ]**、**[すべて表示]**、**[Search サービス]** の順に選択し、Search サービスをクリックして、サービス ダッシュボードを開きます。

    ![][7]

2.  サービス ダッシュボードには、**[プロパティ]** と **[キー]** のタイル、およびリソース使用がひとめでわかる使用状況情報が表示されます。

    ![][8]

**[プロパティ]** にはサービスの URL が含まれます。

**[キー]** には認証に使用する API キーが含まれます。

**[使用]** には、ドキュメント数、利用可能なリソース、およびストレージの上限が表示されます。

これらの値を使用してサービスに接続する方法については、「[サービス操作をテストする][サービス操作をテストする]」に進んでください。

## 標準検索にアップグレードする

標準検索では、Azure データ センターにある自分だけが使用できる専用リソースにアクセスします。検索ワークロードには、ストレージとサービス レプリカの両方が必要です。標準検索にサインアップすると、状況に応じて最も重要なリソースをより多く使用できるように、サービス構成を最適化できます。

専用リソースはスケールの拡大やパフォーマンスの向上に役立ちますが、機能は追加されません。共有検索と標準検索の機能は同じです。

標準検索を使用するには、標準料金レベルを選択して、新しい Search サービスを作成します。無料版をアップグレードして置き換えるのではないことに注意してください。スケールの拡張を可能とする標準版に切り替えるには、新しいサービスが必要です。検索アプリケーションで使用するインデックスとドキュメントをリロードする必要があります。

専用リソースの設定には時間がかかる場合があります (15 分以上)。

**手順 1. - 料金レベルを標準に設定して新しいサービスを作成する**

1.  既存のサブスクリプションを使用して、[Azure プレビュー ポータル][Azure プレビュー ポータル]にサインインします。

2.  ページの下部にある **[新規]** をクリックします。

3.  ページの上部にある **[すべて]** をクリックします。

4.  ギャラリーで、**[データ、ストレージ、キャッシュ、+ バックアップ]** をクリックします。

5.  **[すべて表示]** をクリックして、データに関するすべてのサービスの一覧を展開します。

6.  データ サービスで、**[検索]** をクリックします。

7.  [検索] ページの下部にある **[作成]** をクリックします。

8.  サービス URL にサービス名を小文字で入力します。文字列にはダッシュやスペースは使用せず、15 文字以内にしてください。

9.  **[料金レベル]** の矢印をクリックして、料金オプションを選択します。ページの下部で、**[標準]** を選択して、**[選択]** をクリックします。

![][9]

**手順 2. - スケール要件に基づいて検索単位を変更する**

標準検索は 1 つのレプリカとパーティションから開始されますが、リソースの上位レベルで簡単に変更できます。

1.  サービスを作成してから、サービス ダッシュボードに戻り、**[スケール]** タイルをクリックします。

2.  スライダーを使用して、レプリカ、パーティション、または両方を追加します。

追加のレプリカおよびパーティションは、検索単位で課金されます。リソースの追加に応じて、特定のリソース構成に必要な検索単位の合計数がページに表示されます。「[料金の詳細][料金の詳細]」をチェックして、単位あたりの課金情報を確認できます。

![][10]

## サービス操作をテストする

Search 構成の最後の手順では、サービスがクライアント アプリケーションから操作およびアクセスできるかどうかを確認します。この手順は、[Telerik から無料でダウンロード][Telerik から無料でダウンロード]できる Fiddler を使用し、HTTP 要求を発行して応答を確認します。Fiddler を使用することで、コードを記述することなく、API をすぐにテストできます。

次の手順は、共有検索と標準検索の両方で使用します。以下の手順では、インデックスを作成し、ドキュメントをアップロードし、インデックスのクエリを実行してから、システムでサービス情報のクエリを実行します。

### インデックスを作成する

1.  Fiddler を起動します。ファイル メニューの **[トラフィックのキャプチャ]** をオフにして、現在のタスクには関係ない外部の HTTP アクティビティを非表示にします。[Composer] タブで、次のような要求を生成します。

    ![][11]

2.  **[PUT]** を選択します。

3.  サービス URL ([プロパティ] ページを参照)、要求属性、および API バージョンを指定する URL を入力します。次の点に留意してください。

-   HTTPS をプレフィックスとして使用する
-   要求属性は "/indexes/hotels" です。この属性は Search に "hotels" という名前のインデックスを作成することを指定します。
-   API バージョンは、小文字で "?api-version=2014-07-31-preview" と指定します。Azure Search は定期的に更新をデプロイするため、API バージョンは重要です。まれに、サービスの更新により API に対する重要な変更が発生する可能性があります。API バージョンを使用すると、既存のバージョンの使用を継続して、必要に応じて新しいバージョンにアップグレードすることができます。

    URL 全体は、次の例のようになります。

         https://my-app.search.windows.net/indexes/hotels?api-version=2014-07-31-Preview

1.  ホストと API キーをサービスに有効な値で置き換え、要求ヘッダーを指定します。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

2.  要求の本文には、インデックス定義を構成するフィールドを貼り付けます。

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true},
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

3.  **[実行]** をクリックします。

数秒で、インデックスが正常に作成されたことを示す HTTP 204 応答がセッション一覧に表示されます。

HTTP 504 が表示された場合は、URL で HTTPS の指定を確認してください。HTTP 400 または 404 を確認するには、要求の本文をチェックして、コピーまたは貼り付けのエラーがないことを確認します。HTTP 403 は、API キーに問題があることを示しています (キーが無効か、または API キーの指定方法に構文エラーがあります)。

### ドキュメントを読み込む

[Composer] タブでは、ドキュメントを送信する要求を次のように記述します。要求の本文には、4 つの hotel の検索データが含まれています。

![][12]

1.  **[POST]** を選択します。

2.  HTTPS で始まる URL の後に自分のサービス URL、次に「/indexes/\<'indexname'\>/docs/index?api-version=2014-07-31-preview」と入力します。URL 全体は、次の例のようになります。

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2014-07-31-Preview

3.  要求ヘッダーは前と同じにします。ホストと API キーはサービスで有効な値に置き換えたことを思い出してください。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  要求の本文には、hotels インデックスに追加する 4 つのドキュメントが含まれています。

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

5.  **[実行]** をクリックします。

数秒で、セッション一覧に HTTP 200 応答が表示されます。この応答は、ドキュメントが正常に作成されたことを示します。207 が表示された場合は、少なくとも 1 つのドキュメントのアップロードが失敗しています。404 が表示された場合は、要求のヘッダーまたは本文に構文エラーがあります。

### インデックスのクエリを実行する

インデックスとドキュメントが読み込まれたら、クエリを発行することができます。[Composer] タブで、サービスのクエリを実行する GET コマンドは次のようになります。

![][13]

1.  **[GET]** を選択します。

2.  HTTPS で始まる URL の後に自分のサービス URL、次に「/indexes/\<'indexname'\>/docs?」、クエリ パラメーターの順に入力します。例として、サンプル ホスト名をサービスで有効な値に置き換えた、次の URL を使用します。

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2014-07-31-Preview

    このクエリは、用語 "motel" を検索し、評価のファセット カテゴリを取得します。

3.  要求ヘッダーは前と同じにします。ホストと API キーはサービスで有効な値に置き換えたことを思い出してください。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

応答コードは 200 で、応答出力は次の図のようになります。

![][14]

次のクエリ例は、MSDN の「[Search Index operation (Azure Search API) (検索インデックス操作 (Azure Search API))][Search Index operation (Azure Search API) (検索インデックス操作 (Azure Search API))]」に記載されているものです。このトピックのクエリ例の多くに、Fiddler では許可されないスペースが含まれています。Fiddler でクエリを実行する場合は、次のように、スペースを + 文字に置き換えてからクエリ文字列に貼り付けてください。

**スペースを置き換える前:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-07-31-Preview

**スペースを + で置き換えた後:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2014-07-31-Preview

### システムのクエリを実行する

システムのクエリを実行して、ドキュメント数とストレージ消費を取得することもできます。[Composer] タブでは、要求は次のように表示され、応答ではドキュメント数と使用されているストレージ領域が返されます。

![][15]

1.  **[GET]** を選択します。

2.  次のように、サービスの URL の後に「/indexes/hotels/stats?api-version=2014-07-31-Preview」を続けて URL を入力します。

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2014-07-31-Preview 

3.  ホストと API キーをサービスに有効な値で置き換え、要求ヘッダーを指定します。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  要求の本文は空のままにします。

5.  **[実行]** をクリックします。セッション一覧に、状態コード HTTP 200 が表示されます。コマンドに対して送信されたエントリを選択します。

6.  [インスペクター] タブの [ヘッダー] をクリックし、JSON 形式を選択します。ドキュメント数とストレージ サイズ (KB) が表示されます。

    ![][16]

## Search サービス ダッシュボードを調べる

構成ページの場所を思い出せない場合は、次の手順に従って、サービス ダッシュボードを表示します。

1.  既存のサブスクリプションを使用して、[Azure プレビュー ポータル][Azure プレビュー ポータル]にサインインします。
2.  **[参照]**、**[すべて]** の順にクリックします。

    ![][17]

3.  一覧から **[サービスの検索]** を選択します。サブスクリプションで作成した Search サービスがすべて一覧表示されます。

4.  ダッシュボードを開くサービスをクリックします。**[開始]**、**[停止]**、および **[削除]** コマンドが上部に表示されます。サービス ダッシュボードには、プロパティとキーを表示するためのタイル、および情報と指示にリンクするクイック スタートが含まれています。下にスクロールして使用状況を表示します。

5.  **[プロパティ]** をクリックします。[プロパティ] ページが右側に開きます。ページの上部にサービス URL が表示されています。サービスへの認証に使用する API キーを取得するには、**[キー]** をクリックします。

    ![][8]

<!--Next steps and links -->

## 実際に使ってみる

次のステップに進む準備ができていますか。次のリンクでは、Azure Search を使用する検索アプリケーションを作成して管理する方法について、その他の資料を参照できます。

-   [最初の Azure Search ソリューションを作成する][最初の Azure Search ソリューションを作成する]

-   [Azure Search ジオサーチのサンプルの作成][Azure Search ジオサーチのサンプルの作成]

-   [Microsoft Azure で検索ソリューションを管理する][Microsoft Azure で検索ソリューションを管理する]

-   [Azure Search の技術概要][Azure Search の技術概要]

-   [Azure Search REST API][Azure Search REST API]

-   [Channel 9 のビデオ: Introduction to Azure Search (Azure Search の概要)][Channel 9 のビデオ: Introduction to Azure Search (Azure Search の概要)]

-   [Channel 9 のビデオ: Azure Search and Geospatial Data (Azure Search および地理空間データ)][Channel 9 のビデオ: Azure Search and Geospatial Data (Azure Search および地理空間データ)]

-   [Cloud Cover のエピソード 152: Generate an index in Azure Search (Azure Search でのインデックス生成)][Cloud Cover のエピソード 152: Generate an index in Azure Search (Azure Search でのインデックス生成)]

<!--Anchors--> <!--Image references--> <!--Link references-->

  [Azure Search Scenarios and Capabilities (Azure Search のシナリオと機能)]: http://azure.microsoft.com/blog/2014/08/28/azure-search-scenarios-and-capabilities/
  [無料サービスで開始する]: #sub-1
  [標準検索にアップグレードする]: #sub-2
  [サービス操作をテストする]: #sub-3
  [Search サービス ダッシュボードを調べる]: #sub-4
  [実際に使ってみる]: #next-steps
  [Azure プレビュー ポータル]: https://portal.azure.com
  [0]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
  [1]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
  [2]: ./media/search-get-started/Azuresearch_Configure1_3_Gallery.PNG
  [3]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
  [4]: ./media/search-get-started/AzureSearch_Configure1_5_DataServicesList.PNG
  [5]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
  [6]: ./media/search-get-started/AzureSearch_Configure1_7_Free.PNG
  [7]: ./media/search-get-started/AzureSearch_Configure1_8_SearchServiceList.PNG
  [8]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG
  [9]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
  [料金の詳細]: http://go.microsoft.com/fwlink/p/?LinkID=509792
  [10]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
  [Telerik から無料でダウンロード]: http://www.telerik.com/fiddler
  [11]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
  [12]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
  [13]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
  [14]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
  [Search Index operation (Azure Search API) (検索インデックス操作 (Azure Search API))]: http://msdn.microsoft.com/ja-jp/library/dn798927.aspx
  [15]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
  [16]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
  [17]: ./media/search-get-started/AzureSearch_Configure1_17_BrowseEverything.PNG
  [最初の Azure Search ソリューションを作成する]: ../search-create-first-solution/
  [Azure Search ジオサーチのサンプルの作成]: ../search-create-geospatial/
  [Microsoft Azure で検索ソリューションを管理する]: ../search-manage/
  [Azure Search の技術概要]: http://msdn.microsoft.com/ja-jp/library/dn798933.aspx
  [Azure Search REST API]: http://msdn.microsoft.com/ja-jp/library/dn798935.aspx
  [Channel 9 のビデオ: Introduction to Azure Search (Azure Search の概要)]: http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search
  [Channel 9 のビデオ: Azure Search and Geospatial Data (Azure Search および地理空間データ)]: http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data
  [Cloud Cover のエピソード 152: Generate an index in Azure Search (Azure Search でのインデックス生成)]: http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh
