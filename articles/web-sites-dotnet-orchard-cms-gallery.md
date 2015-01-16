<properties urlDisplayName="Website from Gallery" pageTitle="Azure でギャラリーから Orchard CMS Web サイトを作成する" metaKeywords="Azure 構築 Web サイト, Web サイトの管理 Azure" description="Azure で新しい Web サイトを作成する方法について説明するチュートリアルです。また、管理ポータルを使用してサイトを公開し、管理する方法についても説明しています。" metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

# Azure でギャラリーから Orchard CMS Web サイトを作成する

ギャラリーには、Microsoft、サード パーティ企業、およびオープン ソース ソフトウェア活動によって開発された多種多様な人気の Web アプリケーションが用意されています。ギャラリーから作成した Web アプリケーションは、[Azure の管理ポータル](http://manage.windowsazure.com)への接続に使用するブラウザー以外に、他のソフトウェアをインストールする必要はありません。ギャラリー内の Web アプリケーションの詳細については、「[Windows Web App Gallery](http://www.microsoft.com/web/gallery/categories.aspx)」を参照してください。

このチュートリアルでは、次のことについて説明します。

- ギャラリーから新しいサイトを作成する方法

- 管理ポータルからサイトを公開して管理する方法
 
既定のテンプレートを使用する Orchard CMS サイトを構築します。[Orchard](http://www.orchardproject.net/) はオープン ソースで .NET ベースの無料 CMS アプリケーションで、カスタマイズしたコンテンツ主導の Web サイトを作成できます。Orchard CMS には、[追加のモジュールとテーマをダウンロード](http://gallery.orchardproject.net/)してサイトをカスタマイズできる機能拡張フレームワークが付属しています。次の図に、これから作成する Orchard CMS サイトを示します。

![Orchard blog][13]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2>ギャラリーから Orchard Web サイトを作成する</h2>

1. [Azure の管理ポータル](http://manage.windowsazure.com)にログインします。

2. ポータルの左下にある **[新規]** アイコンをクリックします。
	
	![Create New][1]

3. **Web サイト** アイコンをクリックし、**[ギャラリーから]** をクリックします。
	
	![Create From Gallery][2]

4. 一覧から **[Orchard CMS]** アイコンを見つけてクリックし、矢印をクリックして続行します。
	
	![Orchard from list][3]

5. **[アプリケーションの構成]** ページで、すべてのフィールドの値を入力または選択します。
	
- 好みの URL 名を入力します。	
- ユーザーに最も近いリージョンを選択します (これにより最大限のパフォーマンスが保証されます)。

	![configure your app][4]

6. ボックスの右下にあるチェック マークをクリックして、新しい Orchard CMS Web サイトのデプロイを開始します。

Azure によって構築操作とデプロイ操作が開始されます。Web サイトの構築とデプロイが実行されている間、操作の状態が Web サイト管理ポータルの下部に表示されます。すべての操作が実行された後、Web サイトが作成されたことを示すメッセージが表示されます。

<h2>Orchard サイトの起動と管理</h2>

1. **[Web サイト]** ページで新しいサイトの名前をクリックし、ポータルの下部にある **[参照]** をクリックして Web サイトの開始ページを開きます。

	![launch dashboard][5]

	![browse button][12]

2. Orchard に必要となる構成情報を入力し、**[セットアップの終了]** をクリックして構成を完了し、Web サイトのホーム ページを開きます。

	![login to Orchard][7]

新しい Orchard サイトは次のスクリーンショットのようになります。  

	![your Orchard site][13]

3. Orchard の詳細と新しいサイトの構成方法については、[Orchard Documentation](http://docs.orchardproject.net/) の記述に従ってください。

<h2>次のステップ</h2>
* [Microsoft WebMatrix を使用して Web サイトを開発してデプロイする](/ja-jp/develop/net/tutorials/website-with-webmatrix/) -- WebMatrix で Azure Web サイトを編集する方法について説明します。 
* [メンバーシップ、OAuth、SQL Database を使用した安全な ASP.NET MVC アプリケーションを Azure Web サイトにデプロイする](/ja-jp/develop/net/tutorials/web-site-with-sql-database/)-- Visual Studio から新しい Web サイトを作成する方法について説明します。

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png


