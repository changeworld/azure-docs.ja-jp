<properties 
	pageTitle="Python Tools 2.1 for Visual Studio を使用した Azure 上の Bottle と MongoDB" 
	description="Python Tools for Visual Studio を使用して、MongoDB データベース インスタンスにデータを保存する Bottle アプリケーションを作成し、Azure App Service Web Apps にデプロイする方法について説明します。" 
	services="app-service\web" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="huguesv"/>


# Python Tools 2.1 for Visual Studio を使用した Azure 上の Bottle と MongoDB

> **注:** MongoLab のワークフローは、プレビュー ポータルで現在サポートされていません。

このチュートリアルでは、[Python Tools for Visual Studio] のサンプル テンプレートを使用して、単純な投票 Web アプリを作成します。このチュートリアルは、[ビデオ](https://www.youtube.com/watch?v=8hQMyf8p_Jo)でもご覧いただけます。

この投票 Web アプリでは、リポジトリの抽象化を定義します。そのため、異なる種類のリポジトリ (メモリ内、Azure テーブル ストレージ、MongoDB) を簡単に切り替えることができます。

ここでは、Azure でホステッド MongoDB サービスを使用する方法、MongoDB を使用するようにアプリケーションを構成する方法、Web アプリを [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) に発行する方法について説明します。

Bottle、Flask、Django の各 Web フレームワークと、MongoDB、Azure テーブル ストレージ、MySQL、SQL Database の各サービスを使用した、PTVS での Azure App Service Web Apps の開発を取り上げたその他の記事については、[Python デベロッパー センター]をご覧ください。この記事では App Service について重点的に説明していますが、[Azure Cloud Services] の開発も同様の手順で行います。

## 前提条件

 - Visual Studio 2012 または 2013
 - [Python Tools 2.1 for Visual Studio]
 - [Python Tools 2.1 for Visual Studio サンプル VSIX]
 - [Azure SDK Tools for VS 2013] または [Azure SDK Tools for VS 2012]
 - [Python 2.7 (32 ビット)] または [Python 3.4 (32 ビット)]
 - [RoboMongo](optional)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## プロジェクトを作成する

このセクションでは、サンプル テンプレートを使用して Visual Studio プロジェクトを作成します。仮想環境を作成し、必要なパッケージをインストールします。次に、既定のメモリ内リポジトリを使用してアプリケーションをローカルで実行します。

1.  Visual Studio で、**[ファイル]**、**[新しいプロジェクト]** の順にクリックします。 

1.  PTVS サンプル VSIX のプロジェクト テンプレートは、**[Python]** の **[サンプル]** にあります。**[Polls Bottle Web Project]** を選択し、[OK] をクリックしてプロジェクトを作成します。

  	![[新しいプロジェクト] ダイアログ](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  外部のパッケージをインストールするよう求めるメッセージが表示されます。**[仮想環境にインストールする]** を選択します。

  	![External Packages ダイアログ](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  ベース インタープリターとして **[Python 2.7]** または **[Python 3.4]** を選択します。

  	![Add Virtual Environment ダイアログ](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  <kbd>F5</kbd> キーを押して、アプリケーションが動作することを確認します。既定では、アプリケーションは、構成を必要としないメモリ内リポジトリを使用します。Web サーバーが停止すると、すべてのデータが失われます。

1.  **[Create Sample Polls]** をクリックし、投票内容をクリックして投票します。

  	![Web ブラウザー](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

## MongoDB データベースを作成する

データベースには、Azure 上でホストされる MongoLab データベースを作成します。

別の方法として、Azure 上で実行する独自の仮想マシンを作成し、自分で MongoDB をインストールして管理できます。

次の手順に従って、MongoLab を使用した無料試用版を作成できます。

1.  [Azure 管理ポータル]にログインします。

1.  ナビゲーション ウィンドウの下部にある **[+新規]** をクリックします。

  	<!-- ![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png) -->

1.  **[Marketplace]** をクリックし、**[MongoLab]** クリックして、**[次へ]** をクリックします。

  	<!-- ![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png) -->

1.  **[名前]** に、データベース サービスに使用する名前を入力します。

1.  データベース サービスの配置先となる**リージョン**を選択します。Azure アプリケーションからデータベースを使用する場合は、アプリケーションのデプロイ先と同じリージョンを選択します。

  	<!-- ![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png) -->

1.  **[次へ]** をクリックし、**[購入]** をクリックします。

## プロジェクトを構成する

このセクションでは、先ほど作成した MongoDB データベースを使用するための構成をアプリケーションに対して行います。Azure ポータルから接続の設定を取得する方法を見ていきます。その後、アプリケーションをローカルで実行します。

1.  [Microsoft Azure 管理ポータル]で **[Marketplace]** をクリックし、先ほど作成した MongoLab サービスをクリックします。

1.  **[接続文字列]** をクリックします。コピーボタンを使用すると、**MONGOLAB\_URI** の値をクリップボードに取得できます。

  	<!--![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png) -->

1.  ソリューション エクスプローラーで、プロジェクトを右クリックし、**[プロパティ]** をクリックします。**[デバッグ]** タブをクリックします。

  	![Project Debug 設定](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  **[サーバー コマンドのデバッグ]** の **[環境]** で、アプリケーションに必要な環境変数の値を設定します。

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    これにより、**デバッグを開始**したときに環境変数が設定されます。**デバッグなしで開始**したときに変数を設定する場合は、**[サーバー コマンドの実行]** でも同じ値を設定します。

    Windows コントロール パネルを使用して環境変数を定義してもかまいません。ソース コードまたはプロジェクト ファイルに資格情報を格納するのを避ける場合には、これがより優れた方法です。新しい環境変数の値をアプリケーションから利用するためには、Visual Studio を再起動する必要があります。

1.  MongoDB リポジトリを実装するコードは、**models/mongodb.py** にあります。

1.  `F5` キーを押してアプリケーションを実行します。**[Create Sample Polls]** で作成された投票内容と、投票によって送信されたデータが MongoDB にシリアル化されます。

1.  アプリケーションの **[About]** ページを参照して、**MongoDB** リポジトリが使用されていることを確認します。

  	![Web ブラウザー](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

## MongoDB データベースを調査する

MongoDB データベースの照会と編集には、[RoboMongo] などのアプリケーションを使用できます。このセクションでは、RoboMongo を使用して投票アプリケーションのデータベースの内容を表示します。

1.  新しい接続を作成します。前のセクションで取得した **MONGOLAB\_URI** が必要となります。

    URI の形式は、`mongodb://<name>:<password>@<address>:<port>/<name>` です。

    名前は、Azure でサービスを作成したときに入力した名前と一致します。この名前が、データベース名とユーザー名の両方に使用されます。

1.  接続ページで、**[Name]** に接続の任意の名前を指定します。また、**[Address]** フィールドと **[Port]** フィールドに、**MONGOLAB\_URI** の *address* と *port* を設定します。

  	![Connection Settings ダイアログ](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  認証ページで、**[Database]** と **[User name]** に、**MONGOLAB\_URI** の *name* を設定します。また、**[Password]** に **MONGOLAB\_URI** の *password* を設定します。

  	![Connection Settings ダイアログ](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  保存してデータベースに接続します。これで、投票の収集結果を照会できるようになりました。

  	![RoboMongo 照会結果](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

## Web アプリを Azure に発行する

Azure .NET SDK を使用すると、Web アプリケーションを Azure に簡単にデプロイできます。

1.  **ソリューション エクスプローラー**で、プロジェクト ノードを右クリックし、**[発行]** をクリックします。

1.  **[Microsoft Azure Web Apps]** をクリックします。

3. Azure にサインインしていない場合は、**[サインイン]** をクリックし、Azure サブスクリプションの Microsoft アカウントを使用してサインインします。

2.  **[新規]** をクリックして、新しい Web アプリを作成します。

1.  次のフィールドに入力し、**[作成]** をクリックします。
	-	**[Web アプリケーション名]**
	-	**[App Service プラン]**
	-	**[リソース グループ]**
	-	**[リージョン]**
	-	**[データベース サーバー]** は、**[データベースなし]** のままにしておきます。

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png) -->

1.  他のすべての既定値をそのまま使用し、**[発行]** をクリックします。

1.  Web ブラウザーが自動的に開いて、発行した Web アプリが表示されます。[About] ページを参照すると、**MongoDB** リポジトリではなく、**メモリ内**リポジトリが使用されていることがわかります。

    これは、Azure App Service の Web Apps インスタンスで環境変数が設定されていないので、**settings.py** で指定された既定値が使用されているためです。

## Web Apps インスタンスを構成する

このセクションでは、Web Apps インスタンスの環境変数を構成します。

1.  [Microsoft Azure 管理ポータル]で、前のセクションで作成した Web アプリをクリックします。

1.  上部のメニューの **[構成]** をクリックします。

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png) -->

1.  **[アプリ設定]** セクションまで下にスクロールし、前のセクションの説明に従って、**REPOSITORY\_NAME**、**MONGODB\_HOST**、**MONGODB\_DATABASE** の各値を設定します。

  	<!-- ![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png) -->

1.  下部のメニューで、**[保存]**、**[再起動]** の順にクリックし、最後に **[参照]** をクリックします。

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  アプリケーションが **MongoDB** リポジトリを使用して想定どおりに動作していることを確認できます。

    ご利用ありがとうございます。

  	![Web ブラウザー](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

## 次のステップ

Python Tools for Visual Studio、Bottle および MongoDB の詳細については、以下のリンクを参照してください。

- [Python Tools for Visual Studio のドキュメント]
  - [Web プロジェクト]
  - [クラウド サービス プロジェクト]
  - [Microsoft Azure でのリモート デバッグ]
- [Bottle のドキュメント]
- [MongoDB]
- [PyMongo のドキュメント]
- [PyMongo]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Link references-->
[Python デベロッパー センター]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Azure 管理ポータル]: https://manage.windowsazure.com
[Microsoft Azure 管理ポータル]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio サンプル VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 ビット)]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 (32 ビット)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio のドキュメント]: http://pytools.codeplex.com/documentation
[Bottle のドキュメント]: http://bottlepy.org/docs/dev/index.html
[MongoDB]: http://www.mongodb.org/
[PyMongo のドキュメント]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Microsoft Azure でのリモート デバッグ]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web プロジェクト]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[クラウド サービス プロジェクト]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
 

<!---HONumber=August15_HO6-->