<properties 
	pageTitle="Django を使用した Python Web サイト - Azure チュートリアル" 
	description="Azure での Python Web サイトの実行について説明するチュートリアル。" 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="12/17/2014" 
	ms.author="huvalo"/>




# Django を使用した Web サイトの作成

このチュートリアルでは、Azure Web サイトで Python を実行するための基本的な方法について説明します。Azure Web サイトでは、制限付きの無料のホスティングや迅速な展開を実行できます。また、Python も使用できます。アプリケーションの拡張に合わせて、有料のホスティングに切り替えることができます。また、他のすべての Azure サービスと統合することもできます。

Django Web フレームワークを使用して (このチュートリアルの [Flask](../web-sites-python-create-deploy-flask-app) と [Bottle] バージョンを参照(../web-sites-python-create-deploy-bottle-app)) 、アプリケーションを作成できます。Azure ギャラリーから Web サイトを作成し、Git デプロイを設定して、リポジトリをローカルで複製します。その後、ローカルでアプリケーションを実行し、変更を行い、それらをコミットして Azure に送信します。チュートリアルでは、Windows または Mac/Linux での手順を紹介します。

> [AZURE.NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。<a href="http://azure.microsoft.com/ja-jp/pricing/member-offers/msdn-benefits-details/">MSDN サブスクライバーの特典を有効にする</a>か、<a href="http://azure.microsoft.com/ja-jp/pricing/free-trial/">無料評価版にサインアップ</a>してください。
> 
> アカウントにサインアップする前に Azure Websites を実際に使ってみるには、<a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a> にアクセスしてください。Azure Websites で、有効期限付きの ASP.NET スターター サイトを無償で簡単に作成できます。クレジット カードは必要ありません。また、支払いも発生しません。


+ [前提条件](#prerequisites)
+ [ポータルでの Web サイトの作成](#website-creation-on-portal)
+ [アプリケーションの概要](#application-overview)
+ Web サイトの開発
  + [Windows - Python Tools for Visual Studio](#website-development-windows-ptvs)
  + [Windows - コマンド ライン](#website-development-windows-command-line)
  + [Mac と Linux - コマンド ライン](#website-development-mac-linux-command-line)
+ [トラブルシューティング - デプロイ](#troubleshooting-deployment)
+ [トラブルシューティング - パッケージのインストール](#troubleshooting-package-installation)
+ [トラブルシューティング - 仮想環境](#troubleshooting-virtual-environment)
+ [トラブルシューティング - 静的ファイル](#troubleshooting-static-files)
+ [トラブルシューティング - 設定](#troubleshooting-settings)
+ [データベースの使用](#using-a-database)
+ [Django 管理インターフェイス](#django-admin-interface)
+ [次のステップ](#next-steps)


<h2><a name="prerequisites"></a>前提条件</h2>

- Windows、Mac、Linux
- Python 2.7 または 3.4
- setuptools、pip、virtualenv (Python 2.7 のみ)
- Git
- Python Tools for Visual Studio (オプション)

**注**:現在、TFS 発行は Python プロジェクトではサポートされていません。

### Windows

Python 2.7 または 3.4 (32 ビット) がインストールされていない場合は、Web Platform Installer を使用して [Azure SDK for Python 2.7](http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409) か [Azure SDK for Python 3.4](http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409) をインストールすることをお勧めします。いずれかをインストールすると、Python の 32 ビット バージョン、setuptools、pip、virtualenv などがインストールされます (32 ビット Python は Azure ホスト マシンにインストールするものです)。[python.org](http://www.python.org/) から Python を取得することもできます。

Git には、[Git for Windows](http://msysgit.github.io/) をお勧めします。このチュートリアルでは、Git for Windows の Git Shell を使用します。Visual Studio を使用する場合は、Git の統合のサポートを使用できます。

さらに、[Python Tools for Visual Studio](http://pytools.codeplex.com) のインストールもお勧めします。これはオプションですが、無料版の Visual Studio Express 2013 for Web を含む [Visual Studio](http://www.visualstudio.com/) を使用すれば、Python IDE を有効に活用できます。

### Mac と Linux

Python と Git は既にインストールされていると思いますが、Python 2.7 または 3.4 であることをご確認ください。


<h2><a name="website-creation-on-portal"></a>ポータルでの Web サイトの作成</h2>

アプリケーションを作成する最初の手順では、Azure の管理ポータルで Web サイトを作成します。そのためには、ポータルにログインし、画面左下にある **[新規]** ボタンをクリックします。ウィンドウが表示されます。**[コンピューティング]**、**[Web サイト]**、**[簡易作成]** の順にクリックします。

![](./media/web-sites-python-create-deploy-django-app/portal-create-site.png)

ウィンドウが開き、ギャラリーに使用可能なアプリケーションが一覧表示されます。左側の **[APP FRAMEWORKS (アプリケーション フレームワーク)]** カテゴリをクリックし、**[Django]** を選択します。

![](./media/web-sites-python-create-deploy-django-app/portal-gallery-django.png)

次のページでサイト名とリージョンを入力し、[complete (完了)] ボタンを押します。

サイトがすぐに設定されます。下のツールバーにある **[参照]** ボタンをクリックすると、Azure で実行中の新しい Django アプリケーションが表示されます。

![](./media/web-sites-python-create-deploy-django-app/portal-website-django.png)
 
次に、Git を使って発行する際に必要となるサポートを追加するため、**[ソース管理からの展開の設定]** をクリックします。

![](./media/web-sites-python-create-deploy-django-app/portal-site-created.png)

**[展開の設定]** ダイアログで、下へスクロールして **[ローカル Git リポジトリ]** オプションを選択します。右矢印をクリックして次へ進みます。

![](./media/web-sites-python-create-deploy-django-app/portal-setup-deployment.png)

Git 発行を設定した後で、リポジトリが作成中であることを示すページがすぐに表示されます。準備が整ったら、接続方法に関する手順を確認します。  

![](./media/web-sites-python-create-deploy-django-app/portal-repo-created.png)

これらの手順については次のセクションで見ていきます。


<h2><a name="application-overview"></a>アプリケーションの概要</h2>

### Git リポジトリのコンテンツ

ここでは、初期の Git リポジトリにあるファイルの概要を説明します。次のセクションではこれらのファイルを複製します。

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

アプリケーションのメイン ソース。マスター レイアウトを使用した 3 つのページ (index、about、contact) で構成されます。静的コンテンツとスクリプト (bootstrap、jquery、modernizr、respond など)。

    \manage.py

ローカル管理、開発サーバーのサポート。このファイルを使用して、アプリケーションをローカルで実行したり、データベースを同期したりします。

    \db.sqlite3

既定のデータベース。アプリケーションの実行に必要なテーブルが含まれますが、ユーザーは含まれません (ユーザーを作成するにはデータベースを同期します)。

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

[Python Tools for Visual Studio](http://pytools.codeplex.com) で使用するプロジェクト ファイル。

    \ptvs_virtualenv_proxy.py

仮想環境とPTVS リモート デバッグのサポートのための IIS プロキシ。

    \requirements.txt

このアプリケーションで必要な外部パッケージ。デプロイ スクリプトにより、このファイルに記載してあるパッケージが pip インストールされます。
 
    \web.2.7.config
    \web.3.4.config

IIS 構成ファイル。デプロイ スクリプトは適切な web.x.y.config を使用して web.config としてコピーします。

### オプションのファイル - デプロイのカスタマイズ

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### オプションのファイル - Python ランタイム

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### サーバー上の追加のファイル

サーバー上にはいくつかのファイルが存在しますが、Git リポジトリには追加されません。これらのファイルはデプロイ スクリプトによって作成されるものです。

    \web.config

IIS 構成ファイル。デプロイごとに web.x.y.config から作成されます。

    \env\

Python 仮想環境。互換性のある仮想環境がまだサイト上にない場合に、デプロイ時に作成されます。requirements.txt に指定されたパッケージが pip インストールされますが、パッケージが既にインストールされている場合はインストールがスキップされます。

次の 3 つのセクションでは、以下の 3 つの異なる環境における Web サイト開発を使用した処理方法を説明します。

- Windows、Python Tools for Visual Studio を使用
- Windows、コマンド ラインを使用
- Mac と Linux、コマンド ラインを使用


<h2><a name="website-development-windows-ptvs"></a>Web サイトの開発 - Windows - Python Tools for Visual Studio</h2>

### リポジトリの複製

最初に、Azure ポータルで提供される URL を使用して、リポジトリを複製します。

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-clone.png)

リポジトリのルートにあるソリューション ファイル (.sln) を開きます。

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### 仮想環境の作成

次にローカル開発用の仮想環境を作成します。**[Python 環境]** を右クリックし、**[仮想環境の追加]** を選択します。

- 環境名が、 `env` となっていることを確認します。

- ベース インタープリターを選択します。サイトに選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt またはサイト構成ページ)。

- パッケージのダウンロードとインストールのオプションが選択されていることを確認します。

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

**[作成]** をクリックします。これにより、仮想環境が作成され、requirements.txt に指定されている依存関係がインストールされます。

### スーパーユーザーを作成します。

アプリケーションに含まれるデータベースには、スーパーユーザーは定義されていません。アプリケーションのサインイン機能または Django 管理インターフェイス (有効にしてある場合) を使用するには、スーパーユーザーを作成する必要があります。

プロジェクト フォルダーのコマンドラインから実行します。

    env\scripts\python manage.py createsuperuser

画面表示に従って、ユーザー名、パスワードなどを設定します。

### 開発サーバーを使用した実行

F5 キーを押してデバッグを開始します。ローカルに実行されているページが Web ブラウザーで自動的に開きます。

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

ソースにブレークポイントを設定したり、ウォッチ ウィンドウを使用したりできます。各種機能の詳細については、[PTVS のドキュメント](http://pytools.codeplex.com/documentation)をご覧ください。

### 変更を行う

次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### その他のパッケージのインストール

アプリケーションが、Python と Django 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。パッケージをインストールするには、仮想環境を右クリックし、**[Install Python Package]** を選択します。

たとえば、Azure ストレージ、サービス、バスにアクセスできるようになる Azure SDK for Python をインストールするには、 `azure` を入力します。

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

仮想環境を右クリックして、**[Generate requirements.txt (requirements.txt の生成)]** を選択して requirements.txt を更新します。

その後、requirements.txt への変更内容を Git リポジトリにコミットします。

### Azure への展開

デプロイを開始するには、**[Sync (同期)]** または **[Push (プッシュ)]** をクリックします。同期ではプッシュとプルの両方が実行されます。

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

仮想環境を作成したり、パッケージをインストールしたりするため、最初のデプロイには時間がかかります。

Visual Studio にはデプロイの進捗状況は表示されません。出力を確認する場合は、「[トラブルシューティング - デプロイ]」のセクションをご覧ください(#troubleshooting-deployment)。

Azure URL を参照して、変更内容を表示します。


<h2><a name="website-development-windows-command-line"></a>Web サイトの開発 - Windows - コマンド ライン</h2>

### リポジトリの複製

最初に、Azure ポータルで提供される URL を使用してリポジトリを複製し、Azure リポジトリにリモートとして追加します。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### 仮想環境の作成

開発環境用の新しい仮想環境を作成します (リポジトリには追加しません)。Python の仮想環境を再配置することはできません。そのため、アプリケーションで作業する開発者ごとにローカルに仮想環境を作成することになります。

サイトに選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt またはサイト構成ページ)。

Python 2.7。

    c:\python27\python.exe -m virtualenv env

Python 3.4。

    c:\python34\python.exe -m venv env

アプリケーションに必要な外部パッケージをインストールします。リポジトリのルートにある requirements.txt ファイルを使用すると、仮想環境にパッケージをインストールできます。

    env\scripts\pip install -r requirements.txt

### スーパーユーザーを作成します。

アプリケーションに含まれるデータベースには、スーパーユーザーは定義されていません。アプリケーションのサインイン機能または Django 管理インターフェイス (有効にしてある場合) を使用するには、スーパーユーザーを作成する必要があります。

プロジェクト フォルダーのコマンドラインから実行します。

    env\scripts\python manage.py createsuperuser

画面表示に従って、ユーザー名、パスワードなどを設定します。

### 開発サーバーを使用した実行

次のコマンドを使用すると、開発サーバーでアプリケーションを起動できます。

    env\scripts\python manage.py runserver

コンソールには、URL とサーバーがリッスンするポートが表示されます。

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

次に、Web ブラウザーを開き、その URL に移動します。

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### 変更を加える。

次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

    git add <modified-file>
    git commit -m "<commit-comment>"

### その他のパッケージのインストール

アプリケーションが、Python と Django 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。たとえば、Azure ストレージ、サービス、バスにアクセスできるようになる Azure SDK for Python をインストールするには、次のように入力します。

    env\scripts\pip install azure

requirements.txt が更新されていることを確認します。

    env\scripts\pip freeze > requirements.txt

変更をコミットします。

    git add requirements.txt
    git commit -m "Added azure package"

### Azure への展開

デプロイを開始するには、変更を Azure にプッシュします。

    git push azure master

仮想環境の作成、パッケージのインストール、web.config の作成といった、デプロイ スクリプトの出力が表示されます。

Azure URL を参照して、変更内容を表示します。


<h2><a name="website-development-mac-linux-command-line"></a>Web サイトの開発 - Mac と Linux - コマンド ライン</h2>

### リポジトリの複製

最初に、Azure ポータルで提供される URL を使用してリポジトリを複製し、Azure リポジトリにリモートとして追加します。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### 仮想環境の作成

開発環境用の新しい仮想環境を作成します (リポジトリには追加しません)。Python の仮想環境を再配置することはできません。そのため、アプリケーションで作業する開発者ごとにローカルに仮想環境を作成することになります。

サイトに選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt またはサイト構成ページ)。

Python 2.7。

    python -m virtualenv env

Python 3.4。

    python -m venv env

アプリケーションに必要な外部パッケージをインストールします。リポジトリのルートにある requirements.txt ファイルを使用すると、仮想環境にパッケージをインストールできます。

    env/bin/pip install -r requirements.txt

### スーパーユーザーを作成します。

アプリケーションに含まれるデータベースには、スーパーユーザーは定義されていません。アプリケーションのサインイン機能または Django 管理インターフェイス (有効にしてある場合) を使用するには、スーパーユーザーを作成する必要があります。

プロジェクト フォルダーのコマンドラインから実行します。

    env/bin/python manage.py createsuperuser

画面表示に従って、ユーザー名、パスワードなどを設定します。

### 開発サーバーを使用した実行

次のコマンドを使用すると、開発サーバーでアプリケーションを起動できます。

    env/bin/python manage.py runserver

コンソールには、URL とサーバーがリッスンするポートが表示されます。

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

次に、Web ブラウザーを開き、その URL に移動します。

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### 変更を行う

次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

    git add <modified-file>
    git commit -m "<commit-comment>"

### その他のパッケージのインストール

アプリケーションが、Python と Django 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。たとえば、Azure ストレージ、サービス、バスにアクセスできるようになる Azure SDK for Python をインストールするには、次のように入力します。

    env/bin/pip install azure

requirements.txt が更新されていることを確認します。

    env/bin/pip freeze > requirements.txt

変更をコミットします。

    git add requirements.txt
    git commit -m "Added azure package"

### Azure への展開

デプロイを開始するには、変更を Azure にプッシュします。

    git push azure master

仮想環境の作成、パッケージのインストール、web.config の作成といった、デプロイ スクリプトの出力が表示されます。

Azure URL を参照して、変更内容を表示します。


<h2><a name="troubleshooting-deployment"></a>トラブルシューティング - デプロイ</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


<h2><a name="troubleshooting-package-installation"></a>トラブルシューティング - パッケージのインストール</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


<h2><a name="troubleshooting-virtual-environment"></a>トラブルシューティング - 仮想環境</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


<h2><a name="troubleshooting-static-files"></a>トラブルシューティング - 静的ファイル</h2>

Django には静的ファイルを収集に関する概念が設定されています。それにより、すべての静的ファイルを元の場所から取得し、単一のフォルダーにそのファイルをコピーします。このアプリケーションの場合、 `/static` にコピーします。

これは、静的ファイルがさまざまな Django 'apps' から取得されるために実行されます。たとえば、Django 管理インターフェイスの静的ファイルは、仮想環境の Django ライブラリ サブフォルダーにあります。このアプリケーションで定義する静的ファイルは  `/app/static` にあります。複数の Django 'apps' を使用する場合は、静的ファイルを複数の場所に配置することになります。

アプリケーションをデバッグ モードで実行すると、アプリケーションは元の場所にある静的ファイルを使用します。

リリース モードで実行すると、アプリケーションは静的ファイルを**使用しません**。ファイルを使用するのは、Web サーバーの役割です。このアプリケーションの場合は、IIS が  `/static` の静的ファイルを使用します。

静的ファイルの収集は、デプロイ スクリプトの一部として自動的に行われます。前に収集されたファイルは消去されます。つまり、収集はデプロイごとに実行されます。デプロイが少し遅くなりますが、古くなったファイルを使用できないようにすることで、セキュリティの問題を回避できます。 

Django アプリケーションでの静的ファイルの収集をスキップする場合は、次の操作を行います。

    \.skipDjango 

その後、ローカル マシンで手動で収集する必要があります。

    env\scripts\python manage.py collectstatic

 `\static` フォルダーを  `.gitignore` から削除し、Git リポジトリに追加します。


<h2><a name="troubleshooting-settings"></a>トラブルシューティング - 設定</h2>

 `DjangoWebProject/settings.py` ではアプリケーションのさまざまな設定を変更できます。

開発者が便利なように、デバッグ モードが有効になっています。ローカルで実行すると、静的ファイルを収集しなくても、イメージやその他の静的コンテンツを表示できるというメリットもあります。

デバッグ モードを無効にするには、次の操作を実行します。

    DEBUG = False

デバッグを無効にすると、Azure のホスト名を含めるために、 `ALLOWED_HOSTS` の値を更新する必要があります。次に例を示します。

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

有効にする場合:

    ALLOWED_HOSTS = (
        '*',
    )
 
実際には、より複雑な設定により、デバッグ モードとリリース モードを切り替え、ホスト名を取得できます。

環境変数は、Azure ポータルの **[構成]** ページの **[アプリケーション設定]** セクションで設定できます。ソースに表示しない値 (接続文字列やパスワードなど) や、Azure とローカル マシンで設定を変える値を設定する場合に便利です。 `settings.py` では、 `os.getenv` を使用して環境変数を照会できます。


<h2><a name="using-a-database"></a>データベースの使用</h2>

アプリケーションに含まれるデータベースは、sqlite データベースです。設定がほとんど必要ないため、開発の際に使用すると便利な既定のデータベースです。データベースはプロジェクト フォルダーの db.sqlite3 ファイルに格納されます。

Azure には、Django アプリケーションから簡単に使用できるデータベース サービスが用意されています。Django アプリケーションからの [SQL データベース](../web-sites-python-ptvs-django-sql) と [MySQL](../web-sites-python-ptvs-django-mysql) の使用に関するチュートリアルでは、データベース サービスの作成と  `DjangoWebProject/settings.py` のデータベース設定の変更に必要な手順、インストールに必要なライブラリを説明しています。

もちろん、独自のデータベース サーバーを管理する場合は、Azure 上で実行する Windows または Linux 仮想マシンを使用して設定できます。


<h2><a name="django-admin-interface"></a>Django 管理インターフェイス</h2>

モデルの構築を開始したら、一部のデータをデータベースに入力できます。Django 管理インターフェイスを使用すると、コンテンツをインタラクティブに追加、編集できます。

管理インターフェイスのコードはアプリケーションのソースではコメント アウトされていますが、わかりやすい記号が付けられていますので、簡単に有効にできます ( 'admin' を検索)。

有効にしたらデータベースを同期し、アプリケーションを実行して  `/admin` に移動します。


<h2><a name="next-steps"></a>次のステップ</h2>

Django と Python Tools for Visual Studio の詳細については、次のリンクをご覧ください。 
 
- [Django のドキュメント][]
- [Python Tools for Visual Studio のドキュメント][]

SQL データベースと MySQL の詳細については、次のリンクをご覧ください。

- [Azure における Django と SQL Database (Python Tools 2.1 for Visual Studio の使用方法)][]
- [Azure における Django と MySQL (Python Tools 2.1 for Visual Studio の使用方法)][]


<!--Link references-->
[Azure における Django と MySQL (Python Tools 2.1 for Visual Studio の使用方法)]: ../web-sites-python-ptvs-django-mysql
[Azure における Django と SQL Database (Python Tools 2.1 for Visual Studio の使用方法)]: ../web-sites-python-ptvs-django-sql

<!--External Link references-->
[Python Tools for Visual Studio のドキュメント]: http://pytools.codeplex.com/documentation 
[Django のドキュメント]: https://www.djangoproject.com/



<!--HONumber=42-->
