<properties
	pageTitle="Azure App Service での Java Web アプリの作成"
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
	ms.date="06/03/2015"
	ms.author="robmcm"/>

# Azure App Service での Java Web アプリの作成

このチュートリアルでは、Azure Marketplace または [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 構成 UI を利用して、Java を使用する Web アプリを Microsoft Azure に作成する方法を示します。

これらの手法のいずれも使用しない場合、たとえば、アプリケーション コンテナーをカスタマイズする場合は、「[Azure へのカスタム Java Web アプリのアップロードする](web-sites-java-custom-upload.md)」を参照してください。

> [AZURE.NOTE]このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、<a href="/ja-jp/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">MSDN サブスクライバーの特典を有効にする</a>か、<a href="/ja-jp/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">無料評価版にサインアップ</a>してください。

Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、「[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## Azure Marketplace を使用して Java Web アプリを作成する

ここでは、Azure Marketplace を使用して、Web アプリの Java アプリケーション コンテナーとして Apache Tomcat または Jetty を選択する方法を示します。

Azure Marketplace から Tomcat を使用して構築した Web アプリは次のようになります。

<!--todo:![Web app using Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)-->

Azure Marketplace から Jetty を使用して構築した Web アプリは次のようになります。

<!--todo:![Web app using Jetty](./media/web-sites-java-get-started/jetty.png)-->

1. [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)にログインします。
2. ページの左下にある [**新規**] をクリックします。
3. [**Web + モバイル**] ブレードをクリックします。
4. [**Web + モバイル**] ブレードの下にある [**Azure Marketplace**] をクリックします。
5. [**Web**] をクリックします。
6. [**Web**] ページの先頭には検索テキスト ボックスがあります。このテキスト ボックスに、**Apache Tomcat** や **Jetty** など、希望する Java アプリケーション サーバーを入力します。
4. 目的の Java アプリケーション サーバーをクリックします。
5. **[作成]** をクリックします。
6. URL 名を指定します。
6. リージョンを選択します。たとえば **[米国西部]** にします。
7. **[作成]** をクリックします。

すぐに Web アプリが作成されます。Web アプリを表示するには、Azure 管理ポータルの [**Web Apps**] ブレードで、新しく作成した Web アプリをクリックし、Web アプリの URL をクリックします。

これで、Web アプリとアプリケーション コンテナーが作成されました。アプリケーションを Web アプリにアップロードする方法については、「**次のステップ**」を参照してください。

## Azure の構成 UI を使用して Java Web アプリを作成する

ここでは、Azure の構成 UI を使用して、Web アプリの Java アプリケーション コンテナーとして Apache Tomcat または Jetty を選択する方法を示します。

1. Microsoft Azure の管理ポータルにログインします。
2. ページの左下にある [**新規**] をクリックします。
3. [**Web + モバイル**] ブレードをクリックします。
4. [**Web + モバイル**] ブレードの下にある [**Azure Marketplace**] をクリックします。
5. [**Web**] をクリックします。
6. [**Web App**] をクリックします。
7. **[作成]** をクリックします。
8. URL 名を指定します。
9. リージョンを選択します。たとえば **[米国西部]** にします。
10. **[作成]** をクリックします。
11. Web アプリが作成されたら、[**すべての設定**] をクリックします。
12. [**アプリケーションの設定**] をクリックします。
13. 目的の Java バージョンをクリックします。
14. Web コンテナーのオプションとして Tomcat や Jetty などが表示されます。希望する **Web コンテナー**を選択します。
15. **[保存]** をクリックします。

すぐに Web アプリが Java ベースになります。Java ベースであることを確認するには、Web アプリの URL をクリックします。開いたページには、新しい Web アプリが Java ベースの Web アプリであることを示すテキストが表示されます。

これで、Web アプリとアプリ コンテナーが作成されました。アプリケーションを Web アプリにアップロードする方法については、「**次のステップ**」を参照してください。

## 次のステップ

この時点で、Java アプリケーション サーバーは Azure で Java Web アプリとして動作しています。独自のアプリケーションや Web ページを追加するには、「[Java Web アプリへのアプリケーションまたは Web ページの追加](web-sites-java-add-app.md)」を参照してください。

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、「[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。
 

<!---HONumber=July15_HO4-->