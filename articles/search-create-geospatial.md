<properties title="Create a geospatial search solution using Azure Search" pageTitle="Create a geospatial search app using Azure Search" description="Create a geospatial search app using Bing and Azure Search" metaKeywords="" services="" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang ms.workload="search" ms.topic="article" ms.tgt_pltfrm ms.date="09/23/2014" ms.author="heidist"></tags>

# Azure Search を使用した地理空間検索アプリの作成

-   [前提条件](#sub-1)
-   [Bing Maps](#sub-2)
-   [Bing Maps DataFlow API を使用して C# で住所をジオコーディングする](#sub-3)
-   [Azure Search および Bing Maps を使用して MVC4 アプリケーションにマッピングを追加する](#sub-4)
-   [AdventureWorksWebGeo を探索する](#sub-5)
-   [次のステップ](#next-steps)

## 概要

このチュートリアルでは、Azure Search および Bing Maps を使用して地理空間検索を Web アプリケーションに追加する方法を説明します。地理空間検索を使用すると、ある地点から特定の距離内にある検索対象を見つけることができます (現在位置から 5 KM 以内にあるすべてのレストランを検索するなど)。Azure Search の地理空間機能は、一般的に使用されているマッピング技術をサポートしています。たとえば、不動産業のアプリで多角形の範囲内にある売家を多角形を使用して表す必要がある場合は、OData または単純な検索構文を使用して簡単に実現できます。

さらに概要を理解するには、Channel 9 のビデオ「[Azure Search and Geospatial Data (Azure Search および地理空間データ)](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)」を参照してください。

![][7]

アプリケーションを作成するには、Bing のマッピング サービスを活用して、CSV ファイルからロードした住所をジオコーディングし、生成されたデータを Search インデックスに保管します。

このチュートリアルは、[Azure Search – Adventure Works Demo](http://azuresearchadventureworksdemo.codeplex.com) を基に作成されています。このデモをまだ実行していない場合は、まず、このデモでインデックスを作成したり Web アプリから Azure Search API を呼び出したりしてみてください。

## 前提条件

-   Visual Studio 2012 以上および ASP.NET MVC 4 と SQL Server がインストールされていること。Visual Studio 2013 Express は、[Download Center](http://www.microsoft.com/ja-jp/download/details.aspx?id=40747) の Web から入手できます。
-   Azure Search サービス。Search サービス名および管理キーが必要です。詳細については、「[Configure Search on Azure Preview portal (Azure プレビュー ポータルでの Search の構成)](../search-configure/) 」を参照してください。
-   Bing マップ サービスおよびこのサービスを利用するためのキー。次のセクションで手順について説明します。
-   [CodePlex の Azure Search GeoSearch Sample](https://azuresearchgeospatial.codeplex.com/)。[SOURCE CODE] タブで、**[Download]** をクリックしてソリューションの zip ファイルを取得してください。

    ![][12]

このソリューションには次の 2 つのプロジェクトが含まれています。

-   **StoreIndexer** は、Azure Search インデックスを作成してデータをロードします。
-   **AdventureWorksWebGeo** は、Azure Search インデックスに対してクエリを実行して店舗の場所を Bing マップ上に表示する MVC4 ベースのアプリケーションです。

[WACOM.INCLUDE [このチュートリアルを完了するには、Azure アカウントが必要です。](../includes/free-trial-note.md)]

## Bing Maps

ここでは、次の 2 つのことに Bing Maps API を使用します。

-   **住所のジオコーディング:** データには住所 (市、州、郵便コード) が含まれていますが、地理空間検索を可能にするために、住所の経度および緯度の座標も含める必要があります。この座標を取得するために、Bing Maps DataFlow API を使用して、住所を一括送信してジオコーディングします。ここでは Bing の無料体験アカウントを使用するため 1 回につき 50 個の住所に制限されますが、このチュートリアルではこれで十分です。

-   **Bing マップ:** アプリを実行する際には、Bing Maps を使用して Bing マップ上に店舗の場所を表示します。

### Bing Maps のアカウントの作成

1.  [Bing Maps のポータル](https://www.bingmapsportal.com/)にアクセスし、新規アカウントを作成します。詳細な情報を入力してアカウントを作成します。

2.  アカウントが作成されたら、**[Create or view keys]** を選択し、詳細な情報を入力してキーを作成します。このデモでは、**[Trial Key]** を選択してかまいません。

3.  **[Submit]** をクリックすると、Bing マップ アプリケーション用のキーの詳細情報が表示されます。このキーを後で使用するのでメモしておいてください。

## Bing Maps DataFlow API を使用して C# で住所をジオコーディングする

このステップでは、世界各地にあるさまざまな自転車店舗の住所を Bing Maps DataFlow API を使用してジオコーディングします。

このデータは、先ほどダウンロードしたソースの store\_locations.csv という CSV ファイルにあります。このファイルをテキスト エディターまたは Excel で開くと、各店舗の ID 列、店名、および住所があることを確認できます。

コードを見ながら、どのように動作するのか説明していきます。

1.  Visual Studio で AdventureWorksGeo ソリューションを開き、ソリューション エクスプローラーでプロジェクト **StoreIndexer** を展開し、Program.cs を開きます。インデックスの作成は「[Azure Search – Adventure Works Demo](http://azuresearchadventureworksdemo.codeplex.com/)」で既にカバーされているので、Program.cs におけるインデックスの作成に関する説明は省略します。

2.  **Main** 関数で **ApplyStoreData** を呼び出していることに注目してください。この関数に移動してコードを見てみましょう。

3.  **ApplyStoreData** は、"store\_locations.csv" という CSV ファイルのデータを System.Data.DataTable にロードします。

    このファイルには、Azure Search にロードする住所を含め、全店舗の情報が入っています。このファイルの各行に反復処理を実行して、後で (既に **CreateStoresIndex()** 関数で作成された) Azure Search インデックスに挿入する **indexOperations** のセットを作成できます。

    後でインデックスを詳しく確認すると、各店舗の経度および緯度が入る **GeoPt** フィールドが空であることが分かります。では、**Main** 関数の次のステップに進みましょう。

4.  関数 **ExtractAddressInfoToXML()** に移動します。この関数は、store\_locations.csv ファイルから住所情報を抽出し、Bing Maps がジオコーディングできる形式で XML ファイルにロードします。ファイルが作成されると、関数 **GeoCoding.CreateJob** の呼び出しによって Bing Maps DataFlow に送信されて処理されます。

5.  ジオコーディングの処理は時間がかかることがあるため、ジョブが完了したかどうかを調べる **GeoCoding.CheckStatus** を 10 秒おきに呼び出すループがあります。完了すると、**GeoCoding.DownloadResults** の呼び出しによって結果がアドレス クラスにダウンロードされます。

6.  最後のステップでは、ジオコーディングされた住所を取り出し、Azure Search に送信します。**UpdateStoreData** 関数を開いて、これがどのように実行されるのか詳しく見ていきましょう。

  **UpdateStoreData** は、アクション **@search.action:merge** を使用して Edm.GeographyPoint 型のロケーション フィールドを、Bing Maps から先ほどダウンロードしたジオコーディングされた経度および緯度の座標で更新します。これは、"stores" インデックスのドキュメントの一意キーである storeId を検索し、その新しいデータを既存のドキュメントにマージすることで更新しています。

1.  アプリケーションを実行する前に、Azure Search および Bing Maps API の情報を追加します。このためには、App.config を開き、"SearchServiceName"、"SearchServiceApiKey"、"BingMapsAPI" の値を Azure Search サービスおよび Bing Maps API の該当する各値に更新します。Search サービス名については、"mysearch.search.windows.net" というサービスであれば、"mysearch" と入力します。

2.  **StoreIndexer** プロジェクトを右クリックし、**[デバッグ]** \> **[新しいインスタンスを開始]** を選択して実行します。

## Azure Search および Bing Maps を使用して MVC4 アプリケーションにマッピングを追加する

このステップでは、店舗の検索をロードして Bing Map 上にプロットする検索アプリケーションをビルドして Web ブラウザーで実行します。

1.  Visual Studio で、AdventureWorksGeo.sln という名前の Azure Search デモ ソリューションを開きます。

2.  ソリューション エクスプローラーで **AdventureWorksWebGeo** を右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。

3.  このプロジェクトの Web.config を開き、"SearchServiceName"、"SearchServiceApiKey"、"BingMapsAPI" の値を、使用する Azure Search サービスおよび Bing Maps API の該当する値に更新します。Search サービス名については、"mysearch.search.windows.net" というサービスであれば、"mysearch" と入力します。

4.  Web.config を保存します。

5.  **F5** キーを押して、プロジェクトを起動します。ビルド エラーが出た場合は、[トラブルシューティング](#err-mvc)の手順に従ってください。

各店舗がマップ上に点として表示されます。いずれかの店舗をクリックすると、その店舗の詳細をまとめたポップアップが表示されます。この情報はすべて、前のステップで作成した "stores" という Azure Search インデックスから得られた情報です。

## AdventureWorksWebGeo を探索する

プロジェクト **AdventureWorksWebGeo** は、地理空間検索を活用するマッピング アプリケーションを、ASP.NET MVC 4 を使用して Azure Search と対話して作成する方法を学習するためのものです。このセクションでは、このアプリケーションのコードの個々の部分を見ながら何が行われているのかを説明します。

1.  ソリューション エクスプローラーで **[AdventureWorksWebGeo]** \> **[Controller]** を展開し、HomeController.cs. を開いてください。アプリケーションが開始されて Index ページがロードされると、**Index()** 関数が呼び出されます。この関数では、Bing Maps API が Web.config からロードされ、ViewBag.BingAPI として Index ビューに渡されます。

2.  **[Views]** \> **[Home]** から Index.cshtml を開いてください。

3.  このファイルは、Bing Maps を Web アプリケーションに追加する一般的な方法に従っていますが、次のように、注目すべき箇所がいくつかあります。

-   コントローラーから渡された ViewBag を使用し、資格情報 <'@ViewBag.BingAPI>' を使用してマップの資格情報をロードします。

-   マップがロードされると、/home/search を指定して、JQuery $.post が HomeController の **Search** 関数に対して実行されます。。

-   **Search** 関数が受け取った店舗の場所を取り出し、店舗の場所をプッシュピンとして Bing Map に追加します。

1.  **Controllers** 下の HomeController.cs を開き、**Search** 関数を見てみましょう。\_storeSearch.Search(lat, lon, 10000) への呼び出しがどのように行われるのかに注目してください。これにより、クエリが実行されて、指定の緯度 (lat) および経度 (lon) から 10,000 KM 内にあるすべての店舗が検索されます。このクエリの結果が処理され、Index ビューに戻されると、Bing Map 上に表示されるプッシュピンとして処理されます。

これでデモは終了です。Azure Search を使用してマップ ベースの ASP.NET MVC4 アプリケーションを構築する前に理解しておく必要がある主な操作をすべて説明しました。

## 「ファイルまたはアセンブリ 'System.Web.Mvc' が読み込めませんでした」の解決方法

AdventureWorksWeb をビルドするときに、「ファイルまたはアセンブリ 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'、またはその依存関係の 1 つが読み込めませんでした」というエラーが出た場合は、次の手順を試してエラーを解決してください。

1.  パッケージ マネージャー コンソールを開きます (**[ツール]** \> **[NuGet パッケージ マネージャー]** \> **[パッケージ マネージャー コンソール]**)。
2.  PM\> プロンプトに、「Update-package -reinstall Microsoft.AspNet.Mvc」と入力します。
3.  ファイルを再ロードするように求められたら、**[すべてはい]** を選択します。
4.  ソリューションを再ビルドし、**F5** キーをもう一度押します。

## 次のステップ

追加で行う自習として、このマッピング アプリケーションにさらに機能を追加することを検討してください。たとえば、次のようなものを追加できます。

-   特定の店舗を検索できるようにする検索ボックス。

-   国や郡でフィルタリングできるようにするファセット。

-   ユーザーが描画する選択領域。これにより、マップ上に領域を描画して検索領域を指定できるようにする。そして、地理的交差 API を使用して、この領域を Azure Search でフィルタリングし、マップ上にプロットする。


<!--Anchors-->
[Prerequisites]: #sub-1
[Bing Maps]: #sub-2
[Geocode Addresses in C# using Bing Maps DataFlow API]: #sub-3
[Add Mapping to an MVC4 Application using Azure Search and Bing Maps]: #sub-4
[Explore AdventureWorksWebGeo]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-geospatial/AzureSearch-geo1-App.PNG
[12]: ./media/search-create-geospatial/AzureSearch_Create2_CodeplexDownload.PNG
  [トラブルシューティング]: #err-mvc
