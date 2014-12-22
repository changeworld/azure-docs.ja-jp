<properties urlDisplayName="Get started with Azure" pageTitle="Java を使用した Microsoft Azure Websites の概要" metaKeywords="" description="This tutorial shows you how to deploy a Java website to Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Get started with Azure and Java" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Azure Websites と Java の概要

このチュートリアルでは、Azure のアプリケーション ギャラリーまたは Azure Website 構成 UI を利用して、Java を使用する Web サイトを Microsoft Azure に作成する方法を示します。 

これらの手法のいずれも使用しない場合、たとえば、アプリケーション コンテナーをカスタマイズする場合は、「[Azure へのカスタム Java Web サイトのアップロード](../web-sites-java-custom-upload)」を参照してください。

> [WACOM.NOTE] このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、<a href="/ja-jp/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">MSDN サブスクライバーの特典を有効にする</a>か、<a href="/ja-jp/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">無料評価版にサインアップ</a>してください。

# Azure のアプリケーション ギャラリーを使用して Java Web サイトを作成する

ここでは、Azure のアプリケーション ギャラリーを使用して、Web サイトの Java アプリケーション コンテナーとして Apache Tomcat または Jetty を選択する方法を示します。

アプリケーション ギャラリーの Tomcat を使用してビルドした Web サイトは次のようになります。

![Web site using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

アプリケーション ギャラリーの Jetty を使用してビルドした Web サイトは次のようになります。

![Web site using Jetty](./media/web-sites-java-get-started/jetty.png)

1. Microsoft Azure の管理ポータルにログインします。
2. **[新規]**、**[コンピューティング]**、**[Web サイト]**、**[ギャラリーから]** の順にクリックします。
3. アプリケーションの一覧でいずれかの Java アプリケーション サーバー (**Apache Tomcat** や **Jetty** など) を選択します。
4. **[次へ]** をクリックします。
5. URL 名を指定します。
6. リージョンを選択します。たとえば **[米国西部]** にします。
7. **[完了]** をクリックします。

すぐに Web サイトが作成されます。Web サイトを表示するには、Azure の管理ポータルの **[Web サイト]** ビューで、状態が **[実行中]** になるまで待ってから、Web サイトの URL をクリックします。

これで、Web サイトとアプリケーション コンテナーが作成されました。アプリケーションを Web サイトにアップロードする方法については、「**次のステップ**」を参照してください。

# Azure の構成 UI を使用して Java Web サイトを作成する

ここでは、Azure の構成 UI を使用して、Web サイトの Java アプリケーション コンテナーとして Apache Tomcat または Jetty を選択する方法を示します。

1. Microsoft Azure の管理ポータルにログインします。
2. **[新規]**、**[コンピューティング]**、**[Web サイト]**、**[簡易作成]** の順にクリックします。
3. URL 名を指定します。
4. リージョンを選択します。たとえば **[米国西部]** にします。
5. **[完了]** をクリックします。すぐに Web サイトが作成されます。Web サイトを表示するには、Azure の管理ポータルの **[Web サイト]** ビューで、状態が **[実行中]** になるまで待ってから、Web サイトの URL をクリックします。
6. 同じく Azure 管理ポータルの **[Websites]** ビューで、Web サイトの名前をクリックして、
ダッシュボードを表示します。
7. **[構成]** をクリックします。
8. **[全般]** セクションで、使用できるバージョンをクリックすることで **[Java]** を有効にします。
9. Web コンテナーのオプションとして Tomcat や Jetty などが表示されます。使用する Web コンテナーを選択します。 
10. **[保存]** をクリックします。 

すぐに Web サイトが Java ベースになります。Java ベースになっていることを確認するには、Azure 管理ポータルの **[Websites]** ビューで、状態が **[実行中]** になるまで待ってから、Web サイトの URL をクリックします。開いたページには、新しいサイトが Java ベースの Web サイトであることを示すテキストが表示されます。

これで、Web サイトとアプリケーション コンテナーが作成されました。アプリケーションを Web サイトにアップロードする方法については、「**次のステップ**」を参照してください。

# 次のステップ

この時点で、Java アプリケーション サーバーは Azure で Java Web サイトとして動作しています。独自のアプリケーションや Web ページを追加するには、「[Azure の Java Web サイトへのアプリケーションの追加](../web-sites-java-add-app)」を参照してください。
