<properties linkid="web-sites-python-ptvs-django-mysql" title="Django and MySQL on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Azure における Django と MySQL (Python Tools 2.1 for Visual Studio の使用方法)" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a MySQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Azure における Django と MySQL (Python Tools 2.1 for Visual Studio の使用方法) 

このチュートリアルでは、PTVS のサンプル テンプレートを使用して単純な投票アプリケーションを作成します。このチュートリアルは、[ビデオ]でもご覧いただけます(https://www.youtube.com/watch?v=oKCApIrS0Lo)。

ここでは、Azure でホストされた MySQL サービスを使用する方法、MySQL を使用するためのアプリケーションの構成方法、アプリケーションを Azure Websites に発行する方法について説明します。

MongoDB、Azure テーブル ストレージ、MySQL、SQL Database の各サービスに、Bottle、Flask、Django の各 Web フレームワークを組み合わせて行う PTVS での Azure Websites 開発について取り上げたその他の記事については、[Python デベロッパー センター][]を参照してください。この記事では Azure Websites を重点的に説明していますが、[Azure Cloud Services][] の開発も同様の手順で行います。

+ [前提条件](#prerequisites)
+ [プロジェクトを作成する](#create-the-project)
+ [MySQL データベースを作成する](#create-a-mysql-database)
+ [プロジェクトを構成する](#configure-the-project)
+ [Azure Websites に発行する](#publish-to-an-azure-website)
+ [次のステップ](#next-steps)

##<a name="prerequisites"></a>前提条件

 - Visual Studio 2012 または 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio サンプル VSIX][]
 - [Azure SDK Tools for VS 2013][] または [Azure SDK Tools for VS 2012][]
 - [Python 2.7 (32 ビット)][]

> [WACOM.NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 <a href="http://azure.microsoft.com/ja-jp/pricing/member-offers/msdn-benefits-details/">MSDN サブスクライバーの特典を有効にするか、</a> または <a href="http://azure.microsoft.com/ja-jp/pricing/free-trial/">無料評価版にサインアップすることができます</a>。
> 
> アカウントにサインアップする前に Azure Websites を試してみたい場合は、 <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a> で、有効期限が短い ASP.NET スターター サイトを Azure Websites に無料で作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

##<a name="create-the-project"></a>プロジェクトを作成する

このセクションでは、サンプル テンプレートを使用して Visual Studio プロジェクトを作成します。まず仮想環境を作成し、必要なパッケージをインストールします。sqlite を使用してローカル データベースを作成します。その後、アプリケーションをローカルで実行します。

1.  Visual Studio で、**[ファイル]**、**[新しいプロジェクト]** の順にクリックします。

1.  PTVS サンプル VSIX のプロジェクト テンプレートは、**[Python]** の **[サンプル]** にあります。**[Polls Django Web Project]** を選択し、[OK] をクリックしてプロジェクトを作成します。

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  外部のパッケージをインストールするよう求めるメッセージが表示されます。**[Install into a virtual environment]** を選択します。

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  **[Python 2.7]** をベース インタープリターとして選択します。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  プロジェクト ノードを右クリックし、**[Python]**、**[Django Sync DB]** の順に選択します。

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Django 管理コンソールが表示されます。プロンプトに従ってユーザーを作成します。

    これで、プロジェクト フォルダーに sqlite データベースが作成されます。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  <kbd>F5</kbd> キーを押してアプリケーションの動作を確認します。

1.  一番上のナビゲーション バーの **[Log in]** をクリックします。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  データベースを同期したときに作成したユーザーの資格情報を入力します。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  **[Create Sample Polls]** をクリックします。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  投票内容をクリックして投票します。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>MySQL データベースを作成する

データベースに関しては、ClearDB MySQL ホステッド データベースを Azure 上に作成します。

Azure 上で動作する独自の仮想マシンを作成し、MySQL をインストールして自分で管理してもかまいません。

次の手順に従い、無料プランでデータベースを作成できます。

1.  [Azure の管理ポータル][]にログインします。

1.  ナビゲーション ウィンドウの下部にある **[+新規]** をクリックします。

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  **[ストア]** をクリックし、**[ClearDB MySQL Database]** をクリックします。

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  [名前] に、データベース サービスに使用する名前を入力します。

1.  データベース サービスの配置先となるリージョンまたはアフィニティ グループを選択します。Azure アプリケーションからデータベースを使用する場合は、アプリケーションのデプロイ先と同じリージョンを選択します。

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  **[購入]** をクリックします。

##<a name="configure-the-project"></a>プロジェクトを構成する

このセクションでは、先ほど作成した MySQL データベースを使用するための構成をアプリケーションに対して行います。まず、Azure ポータルから接続設定を取得する方法を見ていきます。また、Django で MySQL データベースを使用するために必要な Python パッケージも別途インストールします。その後、アプリケーションをローカルで実行します。

1.  [Azure の管理ポータル][]で、**[アドオン]** をクリックし、先ほど作成した ClearDB MySQL Database サービスをクリックします。

1.  **[接続文字列]** をクリックします。コピー ボタンを使用すると、**CONNECTIONSTRING** の値をクリップボードに取得できます。

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  Visual Studio で、*ProjectName* フォルダーの **settings.py** を開きます。接続文字列を一時的にエディターに貼り付けます。この接続文字列の形式を次に示します。

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    MySQL を使用するように既定のデータベースの **ENGINE** を変更し、**CONNECTIONSTRING** に基づく **NAME**、**USER**、**PASSWORD**、**HOST** の値を変更します。

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


1.  ソリューション エクスプローラーの **[Python Environments]** で、対象の仮想環境を右クリックし、**[Install Python Package]** をクリックします。

1. **easy_install** を使用して `mysql-python` パッケージをインストールします。

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  プロジェクト ノードを右クリックし、**[Python]**、**[Django Sync DB]** の順に選択します。  

    前のセクションで作成した MySQL データベースのテーブルが作成されます。プロンプトに従ってユーザーを作成してください。最初のセクションで作成した sqlite データベースのユーザーと一致させる必要はありません。

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  <kbd>F5</kbd> キーでアプリケーションを実行します。**[Create Sample Polls]** で作成された投票内容と投票によって送信されたデータが MySQL データベースにシリアル化されます。

##<a name="publish-to-an-azure-website"></a>Azure Websites に発行する

作成した Web アプリケーションは、PTVS を使用して簡単に Azure Websites にデプロイすることができます。

1.  **ソリューション エクスプローラー**で、プロジェクト ノードを右クリックして **[発行]** をクリックします。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  **[Microsoft Azure Websites]** をクリックします。

1.  **[新規]** をクリックして新しいサイトを作成します。

1.  **[サイト名]** と **[リージョン]** を選択し、**[作成]** をクリックします。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  それ以外はすべて既定値のままにし、**[発行]** をクリックします。

1.  Web ブラウザーが自動的に開いて、発行したサイトが表示されます。これでアプリケーションは、Azure 上にホストされた **MySQL** データベースを使用して正しく動作します。

    ご利用ありがとうございます。

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>次のステップ

Python Tools for Visual Studio、Django、MySQL の詳細については、以下のリンクをクリックしてください。

- [Python Tools for Visual Studio Documentation (Python Tools for Visual Studio のドキュメント)][]
  - [Web プロジェクト][]
  - [クラウド サービス プロジェクト][]
  - [Microsoft Azure でのリモート デバッグ][]
- [Django のドキュメント][]
- [MySQL][]


<!--Link references-->
[Python デベロッパー センター]: /ja-jp/develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure の管理ポータル]: https://manage.windowsazure.com
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio サンプル VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 ビット)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python Tools for Visual Studio Documentation (Python Tools for Visual Studio のドキュメント)]: http://pytools.codeplex.com/documentation
[Microsoft Azure でのリモート デバッグ]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web プロジェクト]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[クラウド サービス プロジェクト]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Django のドキュメント]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/

<!--HONumber=35_1-->
