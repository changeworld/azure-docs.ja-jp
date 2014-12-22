<properties title="Create an Umbraco website from the gallery in Microsoft Azure" pageTitle="Microsoft Azure でギャラリーから Umbraco Web サイトを作成する" description="required" metaKeywords="Azure, gallery, Umbraco, web site, website" services="web-sites" solutions="web" documentationCenter="" authors="tomfitz" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

#Microsoft Azure でギャラリーから Umbraco Web サイトを作成する

Umbraco CMS は、小規模なものから複雑なものまでさまざまなアプリケーションを作成するために使用できるフル機能装備のオープン ソース コンテンツ管理システムです。Azure Websites アプリケーション ギャラリーには、Microsoft、サード パーティ企業、およびオープン ソース ソフトウェア活動によって開発された多種多様な人気の Web アプリケーションが用意されています。ギャラリーを使用して、スタート キットを適用するか、または自分のデザインを統合することで、数分で Umbraco CMS サイトを作成できます。 

この記事では、リソース管理を大幅に簡素化する新しい Azure プレビュー ポータルを紹介します。新しい Azure ポータルは、Microsoft とそのパートナーが提供するクロスプラットフォーム ツール、テクノロジ、およびサービスを単一のワークスペースに配置することで、ソフトウェア配信プロセスを迅速化できるように設計されています。Azure Web サイト、Visual Studio プロジェクト、データベースなどのスタンドアロン リソースを使用する代わりに、アプリケーション全体を単一のリソース グループとして作成、管理、および分析できます。 

このチュートリアルでは、次のことについて説明します。

- 新しい Azure プレビュー ポータルを使用し、ギャラリーを通じて新しいサイトを作成する方法
- Umbraco CMS を使用してブログ Web サイトをビルドする方法 

##Azure ポータルでギャラリーから Web サイトを作成する

1. [Microsoft Azure の管理ポータル](https://portal.azure.com/)にログインします。

2. **[Azure ギャラリー]** アイコンを選択します。
	
	![Choose Web Gallery][01Startboard]
	
3. **[ギャラリー]** で、**[Web]** タブをクリックし、**[Umbraco CMS]** を選択します。
	
	![Select Umbraco in the Web Gallery][02WebGallery]
	
4. 新しい Umbraco CMS Web サイトを作成するには、**[作成]** をクリックします。
	
	![Click Create][03UmbracoCMS]
	
5. 次の手順では、Umbraco CMS に関連付けるすべてのリソースを構成します。ここでは、リソースは Web サイトと SQL Server データベースです。最初に、**[Web サイト]** を選択し、**[URL]**、**[Web ホスティング プラン]**、**[Web App の設定]**、**[場所]** などの Web サイトの設定を構成します。 
	
	![Configure resources][04AppSettings]
	
6. 次に、データベースを構成します。**[データベース]** を選択し、さらに **[新しいデータベースを作成]** を選択します。この例では、Azure のデータベース用に SQL Server を作成します。
	
	![Create a SQL Server on Azure][05NewServer]
	
7. これで、Web サイトとデータベースが構成されました。前の画像で見た最初の **[Umbraco CMS]** ブレードの下部にある **[作成]** をクリックすることで、アプリケーションのデプロイを開始できます。
	
	![Click Create][06UmbracoCMSGroup]
	
デプロイメントが完了した後、ポータル内のスタート ボードに、Umbraco CMS のリソース グループ、この場合は **UmbracoCMSgroup** が作成されたことが示されます。**[サマリー]** セクションで、Web サイト名 (この場合、**umbracocmsgroup**) をクリックし、Web サイトのプロパティを確認します。また、**[サマリー]** セクションでは、データベース リソースを選択して、関連付けられたデータベースのプロパティも確認できます。
	
![][07UmbracoCMSGroupBlade]

## Umbraco CMS Web サイトの起動と構成

1. Web サイトの詳細ブレードで、**[参照]** をクリックし、サイトを参照します (この場合、umbracocmsgroup.azurewebsites.net)。
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. Web サイトを参照すると、Umbraco CMS がインストーラー ウィザードを開始します。必要な情報を入力して、**[カスタマイズ]** をクリックします。
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Umbraco が使用するデータベースの接続と認証に関する詳細を入力します。データベースの種類として、**[Microsoft SQL Azure]** を選択します。Web サイトの **[サイトの設定]** セクションでデータベースの接続文字列を取得できます。
	
	![Configure your database][10ConfigureYourDatabase] 
	
4. Umbraco CMS の初心者の方は、スタート Web サイト キットを選択できます。そうでない場合は、**[必要ありません、スタート Web サイトはインストールしません]** をクリックします。
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. Umbraco インストーラーによってアプリケーションのセットアップが完了します。アプリケーションが構成されると、Umbraco CMS 管理ダッシュボードにリダイレクトされます。
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. 次に、発行するサンプル テキスト ページを作成します。**[コンテンツ]**、**[オーバーフロー]**、**[テキスト ページ]** の順に選択します。
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. 次に示すように、テキスト ページのタイトルといくつかのコンテンツを入力します。終了したら、**[保存して発行]** をクリックします。
	
	![Enter a title and some content][16EnterAName]
	
8. しばらくすると、ページが発行されます。発行が完了すると、画面右下に小さいアラートが表示されます。これで、Web サイト上の新しいコンテンツを参照できます。 
	
	![Published web site page][17MyPage]
	

これで終了です。わずか数分で Umbraco CMS を使用してブログ Web サイトを正常に作成できました。 

##その他のリソース

[Umbraco Documentation (Umbraco のドキュメント)](http://our.umbraco.org/documentation)

[Umbraco Video Tutorials (Umbraco ビデオ チュートリアル)](https://umbraco.com/help-and-support/video-tutorials.aspx)

[DevOps は最高 (Microsoft Azure プレビュー ポータルの概要)](http://azure.microsoft.com/ja-jp/overview/preview-portal/)

[これまでになかったクラウド ポータル (Microsoft Azure プレビュー ポータルのドキュメント)](http://azure.microsoft.com/ja-jp/documentation/preview-portal/)

[Azure Preview Portal (Channel 9) (Azure プレビュー ポータル (チャネル 9))](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal) 

[Web サイト (Microsoft Azure Web サイトのドキュメント)](http://azure.microsoft.com/ja-jp/documentation/services/web-sites/)


<!-- IMAGES -->
[01Startboard]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
[02WebGallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
[03UmbracoCMS]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
[04AppSettings]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
[05NewServer]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
[06UmbracoCMSGroup]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
[07UmbracoCMSGroupBlade]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
[08UmbracoCMSGroupRunning]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
[09InstallUmbraco7]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
[10ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
[11InstallAStarterWebsite]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
[12ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/12ConfigureYourDatabase.png
[14FriendlyCMS]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
[15CreateItemUnderOverflow]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
[16EnterAName]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
[17MyPage]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
