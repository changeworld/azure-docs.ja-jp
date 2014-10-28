<properties linkid="develop-dotnet-website-from-gallery" urlDisplayName="Website from Gallery" pageTitle="Create an Orchard CMS website from the gallery in Azure" metaKeywords="Azure build website, manage website Azure" description="A tutorial that teaches you how to create a new website in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Azure でギャラリーから Orchard CMS Web サイトを作成する

ギャラリーには、Microsoft、サード パーティ企業、およびオープン ソース ソフトウェア活動によって開発された多種多様な人気の Web アプリケーションが用意されています。ギャラリーから作成した Web アプリケーションは、[Azure の管理ポータル][Azure の管理ポータル]への接続に使用するブラウザー以外に、他のソフトウェアをインストールする必要はありません。ギャラリー内の Web アプリケーションの詳細については、「[Windows Web App Gallery][Windows Web App Gallery]」を参照してください。

このチュートリアルでは、次のことについて説明します。

-   ギャラリーから新しいサイトを作成する方法

-   管理ポータルからサイトを公開して管理する方法

既定のテンプレートを使用する Orchard CMS サイトを構築します。[Orchard][Orchard] はオープン ソースで .NET ベースの無料 CMS アプリケーションで、カスタマイズしたコンテンツ主導の Web サイトを作成できます。Orchard CMS には、[追加のモジュールとテーマをダウンロード][追加のモジュールとテーマをダウンロード]してサイトをカスタマイズできる機能拡張フレームワークが付属しています。次の図に、これから作成する Orchard CMS サイトを示します。

![Orchard ブログ][Orchard ブログ]

[WACOM.INCLUDE [create-account-and-websites-note][create-account-and-websites-note]]

## ギャラリーから Orchard Web サイトを作成する

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインします。

2.  ポータルの左下にある **[新規]** アイコンをクリックします。

    ![Create New][Create New]

3.  **[Web サイト]** アイコンをクリックし、**[ギャラリーから]** をクリックします。

    ![Create From Gallery][Create From Gallery]

4.  一覧から **[Orchard CMS]** アイコンを見つけてクリックし、矢印をクリックして続行します。

    ![一覧から Orchard][一覧から Orchard]

5.  **[アプリケーションの構成]** ページで、すべてのフィールドの値を入力または選択します。

-   好みの URL 名を入力します
-   ユーザーに最も近いリージョンを選択します (これにより最大限のパフォーマンスが保証されます)。

    ![configure your app][configure your app]

1.  ボックスの右下にあるチェック マークをクリックして、新しい Orchard CMS Web サイトのデプロイを開始します。

Azure によって構築操作と展開操作が開始されます。Web サイトの構築とデプロイが実行されている間、操作の状態が Web サイト管理ポータルの下部に表示されます。すべての操作が実行された後、Web サイトが作成されたことを示すメッセージが表示されます。

## Orchard サイトの起動と管理

1.  **[Web サイト]** ページで新しいサイトの名前をクリックし、ポータルの下部にある **[参照]** をクリックして Web サイトの開始ページを開きます。

    ![launch dashboard][launch dashboard]

    ![参照 ボタン][参照 ボタン]

2.  Orchard に必要となる構成情報を入力し、**[セットアップの終了]** をクリックして構成を完了し、Web サイトのホーム ページを開きます。

    ![Orchard へのログイン][Orchard へのログイン]

    新しい Orchard サイトは次のスクリーンショットのようになります。

    ![自分の Orchard サイト][Orchard ブログ]

3.  Orchard の詳細と新しいサイトの構成方法については、[Orchard のドキュメント][Orchard のドキュメント]の記述に従ってください。

## <span class="short-header">次のステップ</span>次のステップ

-   [Microsoft WebMatrix を使用して Web サイトを開発してデプロイする][Microsoft WebMatrix を使用して Web サイトを開発してデプロイする] -- WebMatrix で Azure Web サイトを編集する方法について説明します。
-   [メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC アプリケーションを Azure Web サイトにデプロイする][メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC アプリケーションを Azure Web サイトにデプロイする]-- Visual Studio から新しい Web サイトを作成する方法について説明します。

  [Azure の管理ポータル]: http://manage.windowsazure.com
  [Windows Web App Gallery]: http://www.microsoft.com/web/gallery/categories.aspx
  [Orchard]: http://www.orchardproject.net/
  [追加のモジュールとテーマをダウンロード]: http://gallery.orchardproject.net/
  [Orchard ブログ]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Create New]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
  [Create From Gallery]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
  [一覧から Orchard]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
  [configure your app]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
  [launch dashboard]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
  [参照 ボタン]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
  [Orchard へのログイン]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
  [Orchard のドキュメント]: http://docs.orchardproject.net/
  [Microsoft WebMatrix を使用して Web サイトを開発してデプロイする]: /ja-jp/develop/net/tutorials/website-with-webmatrix/
  [メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC アプリケーションを Azure Web サイトにデプロイする]: /ja-jp/develop/net/tutorials/web-site-with-sql-database/
