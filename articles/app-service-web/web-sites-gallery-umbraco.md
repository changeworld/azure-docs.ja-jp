<properties 
	pageTitle="Microsoft Azure で Marketplace から Umbraco Web アプリを作成する" 
	description="Umbraco のコンテンツ管理システムを作成し、Azure App Service Web Apps にデプロイする" 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2015" 
	ms.author="tomfitz"/>

#Microsoft Azure で Marketplace から Umbraco Web アプリを作成する#

Umbraco CMS は、小規模なものから複雑なものまでさまざまなアプリケーションを作成するために使用できるフル機能装備のオープン ソース コンテンツ管理システムです。Azure Marketplace には、Microsoft、サード パーティ企業、およびオープン ソース ソフトウェア活動によって開発された多種多様な人気の Web アプリケーションが用意されています。ギャラリーを使用して、スタート キットを適用するか、または自分のデザインを統合することで、数分で Umbraco CMS アプリを作成できます。

この記事では、リソース管理を大幅に簡素化する Azure プレビュー ポータルを紹介します。Azure プレビュー ポータルは、Microsoft とそのパートナーが提供するクロスプラットフォーム ツール、テクノロジ、およびサービスを単一のワークスペースに配置することで、ソフトウェア配信プロセスを迅速化できるように設計されています。[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps、Visual Studio プロジェクト、データベースなどのスタンドアロン リソースを使用する代わりに、アプリケーション全体を単一のリソース グループとして作成、管理、および分析できます。

このチュートリアルでは、次のことについて説明します。

- Azure プレビュー ポータルを使用して、Marketplace を通じて新しい Web アプリを作成する方法
- Umbraco CMS を使用してブログ Web サイトをビルドする方法 

##Azure プレビュー ポータルで Marketplace から Web アプリを作成する

1. [Azure プレビュー ポータル](https://portal.azure.com/)にログインします。

2. **[Marketplace]** アイコンを選択します。
	
3. **[Marketplace]** で、**[Web + モバイル]** タブを選択し、**[Umbraco CMS]** を選択します。
	
4. 新しい Umbraco CMS Web アプリを作成するには、**[作成]** をクリックします。
	
5. 次の手順では、Umbraco CMS に関連付けるすべてのリソースを構成します。ここでは、リソースは Web アプリと SQL Server データベースです。最初に、**[Web アプリ]** を選択して、**URL**、**App Service プラン**、**Web App の設定**、**場所**など、Web アプリの設定を構成します。
	
	![Configure resources][04AppSettings]
	
6. 次に、データベースを構成します。**[データベース]**、**[サーバー]** の順に選択します。この例では、Azure のデータベース用に SQL Server を作成します。
	
	![Create a SQL Server on Azure][05NewServer]
	
7. これで、Web アプリとデータベースが構成されました。前の画像で見た最初の **[Umbraco CMS]** ブレードの下部にある **[作成]** をクリックすることで、アプリケーションのデプロイを開始できます。
	
	![Click Create][06UmbracoCMSGroup]
	
デプロイメントが完了したら、Umbraco CMS Web アプリのリソース グループのブレードがポータルに表示されます。**[概要]** セクションで、Web アプリ名をクリックして、Web アプリのプロパティを表示します。また、**[サマリー]** セクションでは、データベース リソースを選択して、関連付けられたデータベースのプロパティも確認できます。

## Umbraco CMS Web アプリを起動して構成する ##

1. Web アプリの詳細ブレードで **[参照]** をクリックして、Web アプリを参照します。
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. Web アプリを参照すると、Umbraco CMS によってインストーラー ウィザードが開始されます。必要な情報を入力して、**[カスタマイズ]** をクリックします。
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Umbraco が使用するデータベースの接続と認証に関する詳細を入力します。データベースの種類として、**[Microsoft SQL Azure]** を選択します。Web アプリの **[サイトの設定]** セクションでデータベースの接続文字列を取得できます。
	
	![Configure your database][10ConfigureYourDatabase]
	
4. Umbraco CMS の初心者である場合は、スタート Web サイト キットを選択できます。それ以外の場合は、**[必要ありません、スタート Web サイトはインストールしません]** を選択します。
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. Umbraco インストーラーによってアプリケーションのセットアップが完了します。アプリケーションが構成されると、Umbraco CMS 管理ダッシュボードにリダイレクトされます。
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. 次に、発行するサンプル テキスト ページを作成します。**[コンテンツ]**、**[オーバーフロー]**、**[テキスト ページ]** の順に選択します。
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. 次に示すように、テキスト ページのタイトルといくつかのコンテンツを入力します。終了したら、**[保存して発行]** をクリックします。
	
	![Enter a title and some content][16EnterAName]
	
8. しばらくすると、ページが発行されます。発行が完了すると、画面右下に小さいアラートが表示されます。これで、Web アプリ上の新しいコンテンツを参照できます。
	
	![Published web site page][17MyPage]
	

これで終了です。 わずか数分で Umbraco CMS を使用してブログ Web アプリを正常に作成できました。

##その他のリソース

[Umbraco Documentation (Umbraco のドキュメント)](http://our.umbraco.org/documentation)

[Umbraco Video Tutorials (Umbraco ビデオ チュートリアル)](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Azure ポータルのドキュメント](../preview-portal.md)

[Azure Portal (Channel 9) (Azure ポータル (チャネル 9))](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)

[Azure App Service Web Apps のドキュメント](/documentation/services/websites/)

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* ポータルからプレビュー ポータルへの変更ガイドについては、「[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。


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
 

<!---HONumber=Oct15_HO3-->