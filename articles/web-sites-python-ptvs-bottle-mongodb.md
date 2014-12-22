<properties linkid="web-sites-python-ptvs-bottle-mongodb" title="Bottle and MongoDB on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Python Tools 2.1 for Visual Studio を使用した Azure 上の Bottle と MongoDB" description="Learn how to use the Python Tools for Visual Studio to create a Bottle application that stores data in a MongoDB database instance and can be deployed to a web site." metaKeywords="" services="web-sites" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Python Tools 2.1 for Visual Studio を使用した Azure 上の Bottle と MongoDB

このチュートリアルでは、PTVS サンプル テンプレートのいずれかを使用して、簡単な投票アプリケーションを作成します。

この投票アプリケーションでは、リポジトリの抽象化を定義します。そのため、異なる種類のリポジトリ (メモリ内、Azure テーブル ストレージ、MongoDB) を簡単に切り替えることができます。

ここでは、Azure 上でホステッド MongoDB サービスのいずれかを使用する方法、アプリケーションが MongoDB を使用するよう構成する方法、および Azure Web サイトにアプリケーションを発行する方法を学習します。

Bottle、Flask および Django Web フレームワークと MongoDB、Azure テーブル ストレージ、MySQL および SQL Database サービスを使用した、PTVS による Azure Web サイトの開発について説明するその他の記事については、[Python デベロッパー センター][]を参照してください。この記事では Azure Web サイトに焦点を当てますが、手順は [Azure クラウド サービス][]を開発する場合と似ています。

+ [前提条件](#prerequisites)
+ [プロジェクトを作成する](#create-the-project)
+ [MongoDB データベースを作成する](#create-a-mongodb-database)
+ [プロジェクトを構成する](#configure-the-project)
+ [MongoDB データベースを調査する](#explore-the-mongodb-database)
+ [Azure Web サイトへ発行する](#publish-to-an-azure-website)
+ [Azure Web サイトを構成する](#configure-the-azure-website)
+ [次のステップ](#next-steps)

##<a name="prerequisites"></a>前提条件

 - Visual Studio 2012 または 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio サンプル VSIX][]
 - [Azure SDK Tools for VS 2013][] または [Azure SDK Tools for VS 2012][]
 - [Python 2.7 (32 ビット)][] または [Python 3.4 (32 ビット)][]
 - [RoboMongo][] (オプション)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>プロジェクトを作成する

このセクションでは、サンプル テンプレートを使用して、Visual Studio プロジェクトを作成します。仮想環境を作成し、必要なパッケージをインストールします。その後、既定のメモリ内リポジトリを使用してローカルでアプリケーションを実行します。

1.  Visual Studio で、**[ファイル]**、**[新しいプロジェクト]** を順に選択します。 

1.  PTVS サンプル VSIX のテンプレートは、**[Python]**、**[サンプル]** の下にあります。**[投票 Bottle Web プロジェクト]** を選択し、[OK] をクリックしてプロジェクトを作成します。

  	![New Project Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  外部のパッケージをインストールするように促されます。**[仮想環境にインストールする]** を選択します。

  	![External Packages Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  基になる Python インタープリターとして **[Python 2.7]** または **[Python 3.4]** を選択します。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  <kbd>F5</kbd> キーを押して、アプリケーションが動作することを確認します。既定では、アプリケーションは、構成を必要としないメモリ内リポジトリを使用します。Web サーバーが停止すると、すべてのデータが失われます。

1.  **[サンプル投票の作成]** をクリックし、投票をクリックして投票します。

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>MongoDB データベースを作成する

データベースには、Azure 上でホストされる MongoLab データベースを作成します。

別の方法として、Azure 上で実行する独自の仮想マシンを作成し、自分で MongoDB をインストールして管理できます。

次の手順に従って、MongoLab を使用した無料評価版を作成できます。

1.  [Azure の管理ポータル][]にログインします。

1.  ナビゲーション ウィンドウの下部にある **[新規]** をクリックします。

  	![New Button](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png)

1.  **[ストア]** をクリックし、**[MongoLab]** をクリックします。

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png)

1.  [名前] に、データベース サービスに使用する名前を入力します。

1.  データベース サービスの配置先となる [リージョン] または [アフィニティ グループ] を選択します。Azure アプリケーションからデータベースを使用する場合は、アプリケーションをデプロイするリージョンと同じリージョンを選択します。

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png)

1.  **[購入]** をクリックします。

##<a name="configure-the-project"></a>プロジェクトを構成する

このセクションでは、先ほど作成した MongoDB データベースを使用するようアプリケーションを構成します。Azure ポータルから接続の設定を取得する方法を見ていきます。その後、アプリケーションをローカルで実行します。

1.  [Azure の管理ポータル][]で、**[アドオン]** をクリックし、先ほど作成した MongoLab サービスをクリックします。

1.  **[接続文字列]** をクリックします。[コピー] ボタンを使用すると、**MONGOLAB\_URI** の値をクリップボードにコピーできます。

  	![Connection Info Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  Visual Studio のソリューション エクスプローラーで、使用するプロジェクト ノードを右クリックし、**[プロパティ]** を選択します。[デバッグ]**** タブをクリックします。

  	![Project Debug Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  **[サーバー コマンドのデバッグ]**、**[環境]**で、アプリケーションに必要な環境変数の値を設定します。

        REPOSITORY_NAME=mongodb
        MONGODB_HOST = <MONGOLAB_URI の値>
        MONGODB_DATABASE = <データベース名>

    これにより、**デバッグを開始する**ときに、環境変数が設定されます。**デバッグしないで開始する**ときに変数を設定する場合は、**[サーバー コマンドの実行]** にも同じ値を設定します。

    または、Windows のコントロール パネルを使用して、環境変数を定義できます。ソース コードまたはプロジェクト ファイルに資格情報を格納するのを避ける場合には、これがより優れた方法です。アプリケーションで新しい環境値を使用可能にするには、Visual Studio を再起動する必要があります。

1.  MongoDB リポジトリを実装するコードは、**models/mongodb.py** にあります。

1.  <kbd>F5</kbd> キーを使用してアプリケーションを実行します。**[サンプル投票の作成]** で作成された投票と、投票によって送信されたデータは、MongoDB でシリアル化されます。

1.  アプリケーションが、**MongoDB** のリポジトリを使用していることを確認するには、**[情報]** ページを参照します。

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>MongoDB データベースを調査する

[RoboMongo][] などのアプリケーションを使用して、MongoDB データベースを照会し、編集できます。このセクションでは、投票アプリケーション データベースの内容を表示するのに RoboMongo を使用します。

1.  新しい接続を作成します。前のセクションで取得した **MONGOLAB\_URI** が必要です。

    URI の形式は次のとおりです。`mongodb://<名前>:<パスワード>@<アドレス>:<ポート>/<名前>`

名前は、Azure でサービスを作成したときに入力した名前と一致します。これは、データベース名とユーザー名の両方に使用されます。

1.  [接続] ページで、接続に使用する任意の名前を **[名前]** に設定します。また、**[アドレス] ** および **[ポート] **フィールドに、**MONGOLAB\_URI** の *アドレス* と *ポート* を設定します。

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  [認証] ページで、**[データベース]** と **[ユーザー名]** に **MONGOLAB\_URI** の *名前* を設定します。また、**[パスワード]** に **MONGOLAB\_URI** の *パスワード* を設定します。

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  保存し、データベースに接続します。これで、投票のコレクションを照会できます。

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>Azure Web サイトへ発行する

PTVS は、Azure の Web サイトに Web アプリケーションをデプロイする簡単な方法を提供します。

1.  **ソリューション エクスプローラー**で、プロジェクト ノードを右クリックして **[発行]** を選択します。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  **Microsoft Azure Websites** をクリックします。

1.  **[新規]** をクリックして新しいサイトを作成します。

1.  **[サイト名]** と **[リージョン]** を選択し、**[作成]** をクリックします。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png)

1.  その他のすべての既定値をそのまま使用し、**[発行]** をクリックします。

1.  Web ブラウザーが、発行されたサイトを自動的に開きます。[情報] ページを参照すると、**MongoDB** リポジトリではなく、**メモリ内リポジトリ**を使用していることが確認できます。

    これは、Azure の Web サイトでは環境変数が設定されていないので、**settings.py** で指定されている既定値を使うためです。

##<a name="configure-the-azure-website"></a>Azure Web サイトを構成する

このセクションでは、サイト用の環境変数を構成します。

1.  [Azure の管理ポータル][]で、前のセクションで作成したサイトをクリックします。

1.  上部のメニューで、**[構成]** をクリックします。

  	![Top Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png)

1.  下方向へ **[アプリケーション設定]** セクションまでスクロールし、前のセクションで説明したように **REPOSITORY\_NAME**、**MONGODB\_HOST** および **MONGODB\_DATABASE** の値を設定します。

  	![App Settings](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  下部のメニューで **[保存]**、**[再起動]**、最後に **[参照]** の順にクリックします。

  	![Bottom Menu](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  想定どおりにアプリケーションが **MongoDB** のリポジトリを使用して動作していることが確認できます。

    ご利用ありがとうございます。

  	![Web Browser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

##<a name="next-steps"></a>次のステップ

Python Tools for Visual Studio、Bottle および MongoDB の詳細については、以下のリンクを参照してください。

- [Python Tools for Visual Studio のドキュメント][]
  - [Web プロジェクト][]
  - [クラウド サービス プロジェクト][]
  - [Microsoft Azure でのリモート デバッグ][]
- [Bottle のドキュメント][]
- [MongoDB][]
- [PyMongo のドキュメント][]
- [PyMongo][]


<!--Link references-->
[Python デベロッパー センター]: /ja-jp/develop/python/
[Azure Cloud Services (Azure Cloud Services)]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure 管理ポータル]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
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
