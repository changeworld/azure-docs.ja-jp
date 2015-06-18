<properties 
	pageTitle=".NET で Azure Search アプリケーションを初めて作成する" 
	description="Azure Search .NET SDK の .NET クライアント ライブラリを使用してソリューションをビルドするチュートリアルです。" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="03/05/2015" 
	ms.author="heidist"/>

#.NET で Azure Search アプリケーションを初めて作成する#

このチュートリアルでは、検索エクスペリエンスとして Azure Search を使用するカスタム Web 検索アプリケーションを、Visual Studio 2013 以降で作成します。[Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) を利用して、サンプルで使用するオブジェクトと操作のクラスを作成します。

codeplex の [USGS データを使用した Azure Search デモ](https://azsearchdemos.codeplex.com/SourceControl/latest)からサンプルをダウンロードして、このチュートリアルの手順をたどることもできます。このサンプル アプリケーションでは、[United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) からのデータをワシントン州でフィルター処理して使用します。このデータを使用して、病院や学校などの目立つ建物および河川、湖沼、山などの地理的特徴に関するデータに基づく検索アプリケーションを作成します。

このサンプルを実行するには、Azure Search サービスが必要です。このサービスには、[Azure ポータル](https://portal.azure.com)でサインアップできます。

サービスをプロビジョニングしてその可用性を検証する方法については、「[Create a service in the portal (ポータルでのサービスの作成)](../search-create-service-portal/)」を参照してください。この記事では、Azure Search を含むすべてのチュートリアルとソリューションで使用されるサービス名と管理キーを調べる方法についても説明されています。

> [AZURE.TIP]ビルド エラーを避けるため、すべてのプロジェクトをビルドする前に NuGet パッケージを更新することをお勧めします。ソリューションを右クリックし、**[NuGet パッケージの管理]** をクリックします。このソリューションで使用されるパッケージをすべて更新します。

##インデックスの作成##

1. サービス名と管理キーを [Azure ポータル](https://portal.azure.com)からコピーし、**DataIndexer** | **App.config** に貼り付けます。
1. **DataIndexer** プロジェクトを右クリックし、スタートアップ プロジェクトとして設定します。
1. プロジェクトをビルドして実行します。

コンソール ウィンドウに次のようなメッセージが表示されます。

![][1]

ポータルでは、新しい機能インデックスが xx および xx で表示されます。ポータルが追いつくには数分かかることがあるので、数分後に画面を更新して結果を表示するように計画してください。

![][2]

##アプリケーションのビルド##


1. サービス名と管理キーを [Azure ポータル](https://portal.azure.com)からコピーし、**SimpleSearchMVCApp** | **Web.config** に貼り付けます。
1. **SimpleSearchMVCApp** プロジェクトを右クリックし、スタートアップ プロジェクトとして設定します。
1. プロジェクトをビルドして実行します。

既定のブラウザーで Web ページが表示され、Azure Search サービスのインデックスに格納されている USGS データにアクセスするための検索ボックスが提供されます。

![][3]

##USGS データの検索##

USGS データ セットには、ワシントン州に関連するレコードが含まれています。検索ボックスが空の状態で **[Search]** をクリックすると、既定で、上位 50 のエントリが取得されます。

検索語句を入力すると、検索エンジンに処理するものが提供されます。地域の名前を入力してみてください。"Snoqualmie" は、ワシントン州の都市です。また、川、美しい滝、峠、州立公園の名前でもあります。

![][4]

次のような語句も試すことができます。

- Seattle
- Rainier
- Seattle and Rainier
- Seattle +Rainier -Mount (すべてシアトル市内限定で、レーニア アベニューの史跡やレーニア クラブといった結果が得られます)

##コードの調査##

.NET SDK の基礎を学習するには、「[How to use Azure Search from a .NET Application (.NET アプリケーションから Azure Search を使用する方法)](../search-howto-dotnet-sdk/)」で、クライアント ライブラリで最もよく使用されるクラスの説明を参照してください。

このセクションの残りの部分では、各プロジェクトについてのいくつかの点に触れておきます。必要に応じて、さらに高度な機能を使用する代替方法を示します。

**DataIndexer プロジェクト**

簡単にするため、データは [United States Geological Services (USGS) Web サイト](http://geonames.usgs.gov/domestic/download_data.htm)のデータから生成されたテキスト ファイルでソリューションに組み込まれています。

それ以外の方法としては、[DocumentDB 用のインデクサー](documentdb/documentdb-search-indexer.md)や [Azure SQL Database 用のインデクサー](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)などがあります。インデクサーはデータを Azure Search インデックスに取得するもので、作成して管理する必要があるコードが非常に簡素化されます。

オンプレミスの SQL Server データベースからデータを読み込むこともできます。方法については、[このチュートリアル](http://azure.microsoft.com/blog/2014/11/10/how-to-sync-sql-server-data-with-azure-search/)を参照してください。

**DataIndexer** プログラムには、データを検索してフィルター処理するための **SearchDocuments** メソッドが含まれます。このメソッドは検証手順として存在し、コンソール ウィンドウへのステータス メッセージ出力をサポートしており、検索結果とフィルター動作が表示されます。ほとんどの場合、インデックスを作成して読み込むために作成するコードではこのようなメソッドは必要はありません。

**SimpleSearchMVCApp プロジェクト**

MVC プロジェクトは、ビューとコントローラーを使用して、入力と出力をプレゼンテーション層にルーティングします。**Index.cshtml** は、検索結果の表示に使用される HTML を提供します。現時点では、これはデータセットからのデータを編成する単純なテーブルです。プロトタイプおよびテストのためには便利ですが、さらによいプレゼンテーションに簡単に変更できます。結果を一括処理してページに件数を表示する方法のヒントについては、「[How to page search results in Azure Search (Azure Search で検索結果をページ処理する方法)](search-pagination-page-layout.md)」を参照してください。

Azure Search への接続およびクエリ要求の実行は、**FeatureSearch.cs** ファイルで定義されています。

最後の注意点として、.NET SDK の価値と簡単さがまだ納得できない場合は、このサンプルのソース ファイルを、[Azure Search Adventure Works デモ](https://azuresearchadventureworksdemo.codeplex.com/)の REST API に基づくソース ファイルと比較してください。このチュートリアルで説明されている .NET SDK バージョンの方がはるかに簡単で、コード行も少なくて済みます。

##次のステップ##

これは、USGS データセットに基づく最初の Azure Search チュートリアルです。時間をかけてこのチュートリアルを拡張し、カスタム ソリューションで使用できる検索機能を紹介する新しいチュートリアルを作成する予定です。

Azure Search についての知識が既にある場合は、このサンプルを基にして、サジェスター (先行入力またはオートコンプリート クエリ)、フィルター、ファセット ナビゲーションなどを試すことができます。また、件数を追加してドキュメントを一括処理することで検索結果の表示を改善し、ユーザーが結果をページ移動できるようにすることもできます。

Azure Search を初めて使用する場合は、 他のチュートリアルも試して、作成できるものについての理解を深めることをお勧めします。他のリソースについては、[ドキュメントのページ](http://azure.microsoft.com/documentation/services/search/)を参照してください。[ビデオとチュートリアルの一覧](https://msdn.microsoft.com/library/azure/dn798933.aspx)のリンクから、さらに多くの情報にアクセスすることもできます。

<!--Image references-->
[1]: ./media/search-get-started-20150228/consolemessages.png
[2]: ./media/search-get-started-20150228/portalindexstatus.png
[3]: ./media/search-get-started-20150228/usgssearchbox.png
[4]: ./media/search-get-started-20150228/snoqualmie.png
<!--HONumber=54-->