<properties
	pageTitle="Azure App Service での Java Web アプリの作成 | Microsoft Azure"
	description="このチュートリアルでは、Java Web アプリを Azure App Service にデプロイする方法を示します。"
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>
<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="08/31/2015"
	ms.author="robmcm"/>

# Azure App Service での Java Web アプリの作成

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

このチュートリアルでは、Azure プレビュー ポータルを使用して [Azure App Service で Java Web アプリ](http://go.microsoft.com/fwlink/?LinkId=529714)を作成する方法について説明します。Azure Marketplace から Web アプリ テンプレートを選択するか、汎用 Web アプリを作成して Java 用に手動で構成することができます。

これらの手法のいずれも使用しない場合、たとえば、アプリケーション コンテナーをカスタマイズする場合は、「[Azure へのカスタム Java Web アプリのアップロード](web-sites-java-custom-upload.md)」を参照してください。

> [AZURE.NOTE]このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、[MSDN サブスクライバーの特典を有効にする][]か、[無料試用版にサインアップ][]してください。
>
> Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[Azure App Service アプリケーションの作成][]」にアクセスしてください。有効期間が短いスターター Web アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## Azure Marketplace から Web アプリ テンプレートを選択する

このセクションでは、Azure Marketplace を使用して Java Web アプリを作成する方法について説明します。

1. [Azure プレビュー ポータル](https://portal.azure.com/)にサインインします。

2. **[新規]、[Marketplace]** の順にクリックします。

	![](./media/web-sites-java-get-started/newmarketplace.png)

5. **[Web + モバイル]** をクリックします。

	左へスクロールして **[Marketplace]** ブレードを表示すると、**[Web + モバイル]** を選択できます。

6. 検索テキスト ボックスに、**Apache Tomcat** や **Jetty** などの Java アプリケーション サーバーの名前を入力して、Enter キーを押します。

4. 検索結果で、Java アプリケーション サーバーをクリックします。

	![](./media/web-sites-java-get-started/webmobilejetty.png)

5. 最初の **[Apache Tomcat]** ブレードまたは **[Jetty]** ブレードで、**[作成]** をクリックします。

	![](./media/web-sites-java-get-started/jettyblade.png)

4. 次の **[Apache Tomcat]** ブレードまたは **[Jetty]** ブレードで、**[Web アプリ]** ボックスに Web アプリの名前を入力します。

	Web アプリの URL は {name}.azurewebsites.net のようになるため、この名前は azurewebsites.net ドメイン内で一意である必要があります。入力した名前が一意でない場合は、テキスト ボックスに赤色の感嘆符が表示されます。

5. **リソース グループ**を選択するか、新しく作成します。

	リソース グループの詳細については、「[Azure プレビュー ポータルを使用した Azure リソースの管理](../resource-group-portal.md)」を参照してください。

5. **App Service プラン/場所**を選択するか、新しく作成します。

	App Service プランの詳細については、[Azure App Service プランの概要](../azure-web-sites-web-hosting-plans-in-depth-overview.md)に関するページを参照してください。

6. **[作成]** をクリックします。

	![](./media/web-sites-java-get-started/jettyportalcreate2.png)

	短時間 (通常は 1 分未満) で、新しい Web アプリの作成が完了します。

7. **[Web アプリ]、{作成した新しい Web アプリ}** の順にクリックします。

8. **[URL]** をクリックして新しいサイトを参照します。

	![](./media/web-sites-java-get-started/jettyurl.png)

	Tomcat を選択した場合は、次のようなページが表示されます。

	![Web app using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

	Jetty を選択した場合は、次のようなページが表示されます。

	![Web app using Jetty](./media/web-sites-java-get-started/jetty.png)

これで、Web アプリとアプリ コンテナーが作成されました。アプリケーションを Web アプリにアップロードする方法については、「[次のステップ](#next-steps)」を参照してください。

## Web アプリを作成して Java 用に手動で構成する

このセクションでは、Web アプリを作成して Java 用に手動で構成する方法について説明します。

1. [Azure プレビュー ポータル](https://portal.azure.com/)にサインインします。

2. **[新規]、[Web + モバイル]** の順にクリックします。

6. **[Web アプリ]** をクリックします。

4. **[Web アプリ]** ボックスに Web アプリの名前を入力します。

	Web アプリの URL は {name}.azurewebsites.net のようになるため、この名前は azurewebsites.net ドメイン内で一意である必要があります。入力した名前が一意でない場合は、テキスト ボックスに赤色の感嘆符が表示されます。

5. **リソース グループ**を選択するか、新しく作成します。

	リソース グループの詳細については、「[Azure プレビュー ポータルを使用した Azure リソースの管理](../resource-group-portal.md)」を参照してください。

5. **App Service プラン/場所**を選択するか、新しく作成します。

	App Service プランの詳細については、[Azure App Service プランの概要](../azure-web-sites-web-hosting-plans-in-depth-overview.md)に関するページを参照してください。

6. **[作成]** をクリックします。
 
11. Web アプリが作成されたら、**[Web アプリ]、{作成した Web アプリ}** の順にクリックします。
 
13. **[Web アプリ]** ブレードで、**[設定]** をクリックします。

12. [**アプリケーションの設定**] をクリックします。

13. 目的の **[Java バージョン]** を選択します。

14. 目的の **[Web コンテナー]** を選択します。

15. **[保存]** をクリックします。

	すぐに Web アプリが Java ベースになります。

7. **[Web アプリ]、{作成した新しい Web アプリ}** の順にクリックします。

8. **[URL]** をクリックして新しいサイトを参照します。

	表示された Web ページで、Java ベースの Web アプリが作成されたことを確認します。

## 次のステップ

この時点で、Java アプリケーション サーバーは Azure App Service の Web アプリで動作しています。独自のコードを Web アプリにデプロイするには、[Java Web アプリへのアプリケーションまたは Web ページの追加](web-sites-java-add-app.md)に関するページを参照してください。

Azure での Java アプリケーションの開発の詳細については、[Java デベロッパー センター](/develop/java/)を参照してください。

<!-- External Links -->
[MSDN サブスクライバーの特典を有効にする]: http://go.microsoft.com/fwlink/?LinkId=623901
[無料試用版にサインアップ]: http://go.microsoft.com/fwlink/?LinkId=623901

[Azure App Service アプリケーションの作成]: http://go.microsoft.com/fwlink/?LinkId=523751

<!---HONumber=Oct15_HO4-->