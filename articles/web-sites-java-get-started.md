<properties linkid="develop-java-tutorials-web-site-get-started" urlDisplayName="Get started with Azure" pageTitle="Get started with Microsoft Azure Websites using Java" metaKeywords="" description="This tutorial shows you how to deploy a Java website to Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Get started with Azure and Java" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Azure Websites と Java の概要

このチュートリアルでは、Azure のアプリケーション ギャラリーまたは Azure Website 構成 UI を利用して、Java を使用する Web サイトを Microsoft Azure に作成する方法を示します。

これらの手法のいずれも使用しない場合、たとえば、アプリケーション コンテナーをカスタマイズする場合は、「[Upload a custom Java web site to Azure (カスタム Java Web サイトを Azure にアップロードする)][Upload a custom Java web site to Azure (カスタム Java Web サイトを Azure にアップロードする)]」を参照してください。

> [WACOM.NOTE] このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、[MSDN サブスクライバーの特典を有効にする][MSDN サブスクライバーの特典を有効にする]か、[無料評価版にサインアップ][無料評価版にサインアップ]してください。

# Azure のアプリケーション ギャラリーを使用して Java Web サイトを作成する

ここでは、Azure のアプリケーション ギャラリーを使用して、Web サイトの Java アプリケーション コンテナーとして Apache Tomcat または Jetty を選択する方法を示します。

アプリケーション ギャラリーの Tomcat を使用してビルドした Web サイトは次のようになります。

![Apache Tomcat を使用する Web サイト][Apache Tomcat を使用する Web サイト]

アプリケーション ギャラリーの Jetty を使用してビルドした Web サイトは次のようになります。

![Jetty を使用する Web サイト][Jetty を使用する Web サイト]

1.  Microsoft Azure の管理ポータルにログインします。
2.  **[新規]**、**[コンピューティング]**、**[Web サイト]**、**[ギャラリーから]** をクリックします。
3.  アプリケーションの一覧でいずれかの Java アプリケーション サーバー (**Apache Tomcat** や **Jetty** など) を選択します。
4.  **[次へ]** をクリックします。
5.  URL 名を指定します。
6.  リージョンを選択します。たとえば **[米国西部]** にします。
7.  **[完了]** をクリックします。

すぐに Web サイトが作成されます。Web サイトを表示するには、Azure 管理ポータルの **[Web サイト]** ビューで、状態が **[実行中]** になるまで待ってから、Web サイトの URL をクリックします。

これで、Web サイトとアプリケーション コンテナーが作成されました。アプリケーションを Web サイトにアップロードする方法については、「**次のステップ**」を参照してください。

# Azure の構成 UI を使用して Java Web サイトを作成する

ここでは、Azure の構成 UI を使用して、Web サイトの Java アプリケーション コンテナーとして Apache Tomcat または Jetty を選択する方法を示します。

1.  Microsoft Azure の管理ポータルにログインします。
2.  **[新規]**、**[コンピューティング]**、**[Web サイト]**、**[簡易作成]** をクリックします。
3.  URL 名を指定します。
4.  リージョンを選択します。たとえば **[米国西部]** にします。
5.  **[完了]** をクリックします。すぐに Web サイトが作成されます。Web サイトを表示するには、Azure 管理ポータルの **[Web サイト]** ビューで、状態が **[実行中]** になるまで待ってから、Web サイトの URL をクリックします。
6.  同じく Azure 管理ポータルの **[Websites]** ビューで、Web サイトの名前をクリックして、ダッシュボードを開きます。
7.  **[構成]** をクリックします。
8.  **[全般]** セクションで、使用できるバージョンをクリックすることで **[Java]** を有効にします。
9.  Web コンテナーのオプションとして Tomcat や Jetty などが表示されます。使用する Web コンテナーを選択します。
10. **[保存]** をクリックします。

すぐに Web サイトが Java ベースになります。Java ベースになっていることを確認するには、Azure 管理ポータルの **[Websites]** ビューで、状態が **[実行中]** になるまで待ってから、Web サイトの URL をクリックします。開いたページには、新しいサイトが Java ベースの Web サイトであることを示すテキストが表示されます。

これで、Web サイトとアプリケーション コンテナーが作成されました。アプリケーションを Web サイトにアップロードする方法については、「**次のステップ**」を参照してください。

# 次のステップ

この時点で、Java アプリケーション サーバーは Azure で Java Web サイトとして動作しています。独自のアプリケーションや Web ページを追加するには、「[Add an application or web page to your Java web site (Java Web サイトにアプリケーションや Web ページを追加する)][Add an application or web page to your Java web site (Java Web サイトにアプリケーションや Web ページを追加する)]」を参照してください。

  [Upload a custom Java web site to Azure (カスタム Java Web サイトを Azure にアップロードする)]: ../web-sites-java-custom-upload
  [MSDN サブスクライバーの特典を有効にする]: /ja-jp/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [無料評価版にサインアップ]: /ja-jp/pricing/free-trial/?WT.mc_id=A261C142F
  [Apache Tomcat を使用する Web サイト]: ./media/web-sites-java-get-started/tomcat.png
  [Jetty を使用する Web サイト]: ./media/web-sites-java-get-started/jetty.png
  [Add an application or web page to your Java web site (Java Web サイトにアプリケーションや Web ページを追加する)]: ../web-sites-java-add-app
