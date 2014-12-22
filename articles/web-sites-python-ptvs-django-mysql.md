<properties linkid="web-sites-python-ptvs-django-mysql" title="Django and MySQL on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Python Tools 2.1 for Visual Studio を使用した Azure 上の Django と MySQL" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a MySQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Python Tools 2.1 for Visual Studio を使用した Azure 上の Django と MySQL 

このチュートリアルでは、PTVS サンプル テンプレートのいずれかを使用して、簡単な投票アプリケーションを作成します。

ここでは、Azure 上でホストされる MySQL サービスを使用する方法、アプリケーションが MySQL を使用するよう構成する方法、および Azure Web サイトにアプリケーションを発行する方法を学習します。

Bottle、Flask および Django Web フレームワークと MongoDB、Azure テーブル ストレージ、MySQL および SQL Database サービスを使用した、PTVS による Azure Web サイトの開発について説明するその他の記事については、[Python デベロッパー センター][]を参照してください。この記事では Azure Web サイトに焦点を当てますが、手順は [Azure クラウド サービス][]を開発する場合と似ています。

+ [前提条件](#prerequisites)
+ [プロジェクトを作成する](#create-the-project)
+ [MySQL データベースを作成する](#create-a-mysql-database)
+ [プロジェクトを構成する](#configure-the-project)
+ [Azure Web サイトへ発行する](#publish-to-an-azure-website)
+ [次のステップ](#next-steps)

##<a name="prerequisites"></a>前提条件

 - Visual Studio 2012 または 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio サンプル VSIX][]
 - [Azure SDK Tools for VS 2013][] または [Azure SDK Tools for VS 2012][]
 - [Python 2.7 (32 ビット)][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>プロジェクトを作成する

このセクションでは、サンプル テンプレートを使用して、Visual Studio プロジェクトを作成します。仮想環境を作成し、必要なパッケージをインストールします。sqlite を使用して、ローカル データベースを作成します。その後、アプリケーションをローカルで実行します。

1.  Visual Studio で、**[ファイル]**、**[新しいプロジェクト]** を順に選択します。

1.  PTVS サンプル VSIX のテンプレートは、**[Python]**、**[サンプル]** の下にあります。**[投票 Django Web プロジェクト]** を選択し、[OK] をクリックしてプロジェクトを作成します。

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  外部のパッケージをインストールするように促されます。**[仮想環境にインストールする]** を選択します。

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  **基になるインタープリターとして **Python 2.7 を選択します。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  プロジェクト ノードを右クリックして、**[Python]****[Django Sync DB]** の順に選択します。

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  これにより、Django 管理コンソールが開きます。表示される手順に従って、ユーザーを作成します。

    これにより、プロジェクト フォルダーに sqlite データベースが作成されます。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  <kbd>F5</kbd> キーを押して、アプリケーションが動作することを確認します。

1.  上部にあるナビゲーション バーで、**[ログイン]** をクリックします。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  データベースを同期したときに作成したユーザーの資格情報を入力します。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  **[サンプル投票の作成]** をクリックします。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  投票をクリックして投票します。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>MySQL データベースを作成する

データベースには、Azure 上でホストされる ClearDB MySQL データベースを作成します。

別の方法として、Azure 上で実行する独自の仮想マシンを作成し、自分で MySQL をインストールして管理できます。

次の手順に従って、無料プランでデータベースを作成できます。

1.  [Azure の管理ポータル][]にログインします。

1.  ナビゲーション ウィンドウの下部にある **[新規]** をクリックします。

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  **[ストア]**、**[ClearDB MySQL データベース]** の順にクリックします。

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  [名前] に、データベース サービスに使用する名前を入力します。

1.  データベース サービスの配置先となる [リージョン] または [アフィニティ グループ] を選択します。Azure アプリケーションからデータベースを使用する場合は、アプリケーションをデプロイするリージョンと同じリージョンを選択します。

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  **[購入]** をクリックします。

##<a name="configure-the-project"></a>プロジェクトを構成する

このセクションでは、先ほど作成した MySQL データベースを使用するようアプリケーションを構成します。Azure ポータルから接続の設定を取得する方法を見ていきます。Django と MySQL データベースを使用するために必要な追加の Python パッケージもインストールします。その後、アプリケーションをローカルで実行します。

1.  [Azure の管理ポータル][]で、**[アドオン]** をクリックし、先ほど作成した ClearDB MySQL データベース サービスをクリックします。

1.  **[接続文字列]** をクリックします。[コピー] ボタンを使用すると、**CONNECTIONSTRING** の値をクリップボードにコピーできます。

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  Visual Studio で、*ProjectName* フォルダーから **settings.py** を開きます。エディターで、接続文字列を一時的に貼り付けます。接続文字列では、次の形式です。

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

既定のデータベース **ENGINE** を変更して MySQL を使用し、**CONNECTIONSTRING** の**NAME**、**USER**、**PASSWORD** および**HOST** の値を設定します。

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1.  ソリューション エクスプローラーで、**[Python 環境]** の下にある仮想環境を右クリックし、**[Python パッケージのインストール]** を選択します。

1. **easy_install** を使用して、パッケージ `mysql-python` をインストールします。

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  プロジェクト ノードを右クリックして、**[Python]**、**[Django Sync DB]** の順に選択します。  

    これにより、前のセクションで作成した MySQL のデータベースのテーブルが作成されます。表示される手順に従って、ユーザーを作成します。このユーザーは、最初のセクションで作成した sqlite データベース内のユーザーと一致する必要はありません。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  <kbd>F5</kbd> キーを使用してアプリケーションを実行します。**[サンプル投票の作成]** で作成された投票と、投票によって送信されたデータは、MySQL データベースでシリアル化されます。

##<a name="publish-to-an-azure-website"></a>Azure Web サイトへ発行する

PTVS は、Azure の Web サイトに Web アプリケーションをデプロイする簡単な方法を提供します。

1.  **ソリューション エクスプローラー**で、プロジェクト ノードを右クリックして **[発行]** を選択します。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  **Microsoft Azure Websites** をクリックします。

1.  **[新規]** をクリックして新しいサイトを作成します。

1.  **[サイト名]** と **[リージョン]** を選択し、**[作成]** をクリックします。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  その他のすべての既定値をそのまま使用し、**[発行]** をクリックします。

1.  Web ブラウザーが、発行されたサイトを自動的に開きます。想定どおりにアプリケーションが Azure でホストされている **MySQL** データベースを使用して動作していることが確認できます。

    ご利用ありがとうございます。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>次のステップ

Python Tools for Visual Studio、Django および MySQL の詳細については、以下のリンクを参照してください。

- [Python Tools for Visual Studio のドキュメント][]
  - [Web プロジェクト][]
  - [クラウド サービス プロジェクト][]
  - [Microsoft Azure でのリモート デバッグ][]
- [Django のドキュメント][]
- [MySQL][]


<!--Link references-->
[Python デベロッパー センター]: /ja-jp/develop/python/
[Azure Cloud Services (Azure Cloud Services)]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure 管理ポータル]: https://manage.windowsazure.com
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio サンプル VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 ビット)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python Tools for Visual Studio のドキュメント]: http://pytools.codeplex.com/documentation
[Microsoft Azure でのリモート デバッグ]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web プロジェクト]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[クラウド サービス プロジェクト]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Django のドキュメント]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
