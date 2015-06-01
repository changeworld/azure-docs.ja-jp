<properties 
	pageTitle="Flask を使用した Python Web サイト - Azure チュートリアル" 
	description="Azure での Python Web サイトの実行について説明するチュートリアル。" 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Flask を使用した Web サイトの作成

このチュートリアルでは、Azure Websites で Python を実行するための基本的な方法について説明します。Azure Web サイトでは、制限付きの無料のホスティングや迅速な展開を実行できます。また、Python も使用できます。アプリケーションの拡張に合わせて、有料のホスティングに切り替えることができます。また、他のすべての Azure サービスと統合することもできます。

Flask Web フレームワークを使用してアプリケーションを作成 (このチュートリアルの [Django](web-sites-python-create-deploy-django-app.md)  と [Bottle](web-sites-python-create-deploy-bottle-app.md)バージョンを参照)。Azure ギャラリーから Web サイトを作成し、Git デプロイを設定して、リポジトリをローカルで複製します。その後、ローカルでアプリケーションを実行し、変更を行い、それらをコミットして Azure に送信します。チュートリアルでは、Windows または Mac/Linux での手順を紹介します。

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


## 前提条件

- Windows、Mac、または Linux
- Python 2.7 または 3.4
- setuptools、pip、virtualenv (Python 2.7 のみ)
- Git
- [Python Tools 2.1 for Visual Studio][] (オプション)

**注**: 現在、TFS 発行は Python プロジェクトではサポートされていません。

### Windows

Python 2.7 または 3.4 (32 ビット) がインストールされていない場合は、Web Platform Installer を使用して [Azure SDK for Python 2.7][] か [Azure SDK for Python 3.4][] をインストールすることをお勧めします。いずれかをインストールすると、Python の 32 ビット バージョン、setuptools、pip、virtualenv などがインストールされます (32 ビット Python は Azure ホスト コンピューターにインストールするものです)。[python.org][] から Python を取得することもできます。

Git には、[Git for Windows に関するページ][] か [GitHub for Windows][] をお勧めします。Visual Studio を使用する場合は、Git の統合のサポートを使用できます。

さらに、[Python Tools 2.1 for Visual Studio][] のインストールもお勧めします。これはオプションですが、無料版の Visual Studio Community 2013 または Visual Studio Express 2013 for Web を含む [Visual Studio][] を使用すれば、Python IDE を有効に活用できます。

### Mac/Linux

Python と Git は既にインストールされていると思いますが、Python 2.7 または 3.4 であることをご確認ください。


## ポータルでの Web サイトの作成

アプリケーションを作成する最初の手順では、Azure の管理ポータルで Web サイトを作成します。そのためには、ポータルにログインし、画面左下にある **[新規]** ボタンをクリックします。ウィンドウが表示されます。**[コンピューティング]**、**[Web サイト]**、**[ギャラリー]** の順にクリックします。

![](./media/web-sites-python-create-deploy-flask-app/portal-create-site.png)

ウィンドウが開き、ギャラリーに使用可能なアプリが一覧表示されます。左側の **[アプリ フレームワーク]** カテゴリをクリックし、**[Django]** を選択します。

![](./media/web-sites-python-create-deploy-flask-app/portal-gallery-flask.png)

次のページでサイト名とリージョンを入力し、[完了] ボタンを押します。

サイトがすぐに設定されます。下のツールバーにある **[参照]** ボタンをクリックすると、Azure で実行中の新しい Flask アプリケーションが表示されます。

![](./media/web-sites-python-create-deploy-flask-app/portal-website-flask.png)
 
次に、Git を使って発行する際に必要となるサポートを追加するため、**[ソース管理からの展開の設定]** をクリックします。

![](./media/web-sites-python-create-deploy-flask-app/portal-site-created.png)

**[展開の設定]** ダイアログで、下へスクロールして **[ローカル Git リポジトリ]** オプションを選択します。右矢印をクリックして次へ進みます。

![](./media/web-sites-python-create-deploy-flask-app/portal-setup-deployment.png)

Git 発行を設定した後で、リポジトリが作成中であることを示すページがすぐに表示されます。準備が整ったら、接続方法に関する手順を確認します。  

![](./media/web-sites-python-create-deploy-flask-app/portal-repo-created.png)

これらの手順については次のセクションで見ていきます。


## アプリケーションの概要

### Git リポジトリのコンテンツ

ここでは、初期の Git リポジトリにあるファイルの概要を説明します。次のセクションではこれらのファイルを複製します。

    \FlaskWebProject__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

アプリケーションのメイン ソース。マスター レイアウトを使用した 3 つのページ (index、about、contact) で構成されます。静的コンテンツとスクリプト (bootstrap、jquery、modernizr、respond など)。

    \runserver.py

ローカル開発サーバーのサポート。これを使用すると、アプリケーションをローカルで実行できます。

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

[Python Tools for Visual Studio](http://pytools.codeplex.com) で使用するプロジェクト ファイル。

    \ptvs_virtualenv_proxy.py

仮想環境と PTVS リモート デバッグのサポートのための IIS プロキシ。

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

Python 仮想環境。互換性のある仮想環境がまだサイト上にない場合に、デプロイ時に作成されます。requirements.txt に示されているパッケージは pip インストールされますが、パッケージを既にインストールしている場合は pip でインストールがスキップされます。

次の 3 つのセクションでは、以下の 3 つの異なる環境における Web サイト開発を使用した処理方法を説明します。

- Windows、Python Tools for Visual Studio を使用
- Windows、コマンド ラインを使用
- Mac/Linux、コマンド ラインを使用


## Web サイトの開発 - Windows - Python Tools for Visual Studio

### リポジトリの複製

最初に、Azure ポータルで提供される URL を使用して、リポジトリを複製します。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-clone.png)

リポジトリのルートにあるソリューション ファイル (.sln) を開きます。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### 仮想環境の作成

次にローカル開発用の仮想環境を作成します。**[Python 環境]** を右クリックし、**[仮想環境の追加]** を選択します。

- 環境名が、 `env` となっていることを確認します。

- ベース インタープリターを選択します。サイトに選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt またはサイト構成ページ)。

- パッケージのダウンロードとインストールのオプションが選択されていることを確認します。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

**[作成]** をクリックします。これにより、仮想環境が作成され、requirements.txt に指定されている依存関係がインストールされます。

### 開発サーバーを使用した実行

F5 キーを押してデバッグを開始します。ローカルに実行されているページが Web ブラウザーで自動的に開きます。

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

ソースにブレークポイントを設定したり、ウォッチ ウィンドウを使用したりできます。各種機能の詳細については、「[PTVS のドキュメント][]」をご覧ください。

### 変更を加える

次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### その他のパッケージのインストール

アプリケーションが、Python と Flask 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。パッケージをインストールするには、仮想環境を右クリックし、**[Install Python Package]** を選択します。

たとえば、Azure ストレージ、サービス バスなどの他の Azure サービスにアクセスできるようになる Azure SDK for Python をインストールするには、 `azure` を入力します。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

仮想環境を右クリックし、**[Generate requirements.txt]** を選択して requirements.txt を更新します。

その後、requirements.txt への変更内容を Git リポジトリにコミットします。

### Azure へのデプロイ

デプロイを開始するには、**[同期]** または **[プッシュ]** をクリックします。同期ではプッシュとプルの両方が実行されます。

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

仮想環境を作成したり、パッケージをインストールしたりするため、最初のデプロイには時間がかかります。

Visual Studio にはデプロイの進捗状況は表示されません。出力を確認する場合は、「[トラブルシューティング - デプロイ](#troubleshooting-deployment)」のセクションをご覧ください。

Azure URL を参照して、変更内容を表示します。


## Web サイトの開発 - Windows - コマンド ライン

### リポジトリの複製

最初に、Azure ポータルで提供される URL を使用してリポジトリを複製し、Azure リポジトリにリモートとして追加します。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### 仮想環境の作成

開発環境用の新しい仮想環境を作成します (リポジトリには追加しません)。Python の仮想環境を再配置することはできません。そのため、アプリケーションで作業する開発者ごとにローカルに仮想環境を作成することになります。

サイトに選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt またはサイト構成ページ)。

Python 2.7 の場合:

    c:\python27\python.exe -m virtualenv env

Python 3.4 の場合:

    c:\python34\python.exe -m venv env

アプリケーションに必要な外部パッケージをインストールします。リポジトリのルートにある requirements.txt ファイルを使用すると、仮想環境にパッケージをインストールできます。

    env\scripts\pip install -r requirements.txt

### 開発サーバーを使用した実行

次のコマンドを使用すると、開発サーバーでアプリケーションを起動できます。

    env\scripts\python runserver.py

コンソールには、URL とサーバーがリッスンするポートが表示されます。

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

次に、Web ブラウザーを開き、その URL に移動します。

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### 変更を加える

次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

    git add <modified-file>
    git commit -m "<commit-comment>"

### その他のパッケージのインストール

アプリケーションが、Python と Flask 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。たとえば、Azure ストレージ、サービス バスなどの他の Azure サービスにアクセスできるようになる Azure SDK for Python をインストールするには、次のように入力します。

    env\scripts\pip install azure

requirements.txt が更新されていることを確認します。

    env\scripts\pip freeze > requirements.txt

変更をコミットします。

    git add requirements.txt
    git commit -m "Added azure package"

### Azure へのデプロイ

デプロイを開始するには、変更を Azure にプッシュします。

    git push azure master

仮想環境の作成、パッケージのインストール、web.config の作成といった、デプロイ スクリプトの出力が表示されます。

Azure URL を参照して、変更内容を表示します。


## Web サイトの開発 - Mac と Linux - コマンド ライン

### リポジトリの複製

最初に、Azure ポータルで提供される URL を使用してリポジトリを複製し、Azure リポジトリにリモートとして追加します。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### 仮想環境の作成

開発環境用の新しい仮想環境を作成します (リポジトリには追加しません)。Python の仮想環境を再配置することはできません。そのため、アプリケーションで作業する開発者ごとにローカルに仮想環境を作成することになります。

サイトに選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt またはサイト構成ページ)。

Python 2.7 の場合:

    python -m virtualenv env

Python 3.4 の場合:

    python -m venv env

アプリケーションに必要な外部パッケージをインストールします。リポジトリのルートにある requirements.txt ファイルを使用すると、仮想環境にパッケージをインストールできます。

    env/bin/pip install -r requirements.txt

### 開発サーバーを使用した実行

次のコマンドを使用すると、開発サーバーでアプリケーションを起動できます。

    env/bin/python runserver.py

コンソールには、URL とサーバーがリッスンするポートが表示されます。

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

次に、Web ブラウザーを開き、その URL に移動します。

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### 変更を加える

次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

    git add <modified-file>
    git commit -m "<commit-comment>"

### その他のパッケージのインストール

アプリケーションが、Python と Flask 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。たとえば、Azure ストレージ、サービス バスなどの他の Azure サービスにアクセスできるようになる Azure SDK for Python をインストールするには、次のように入力します。

    env/bin/pip install azure

requirements.txt が更新されていることを確認します。

    env/bin/pip freeze > requirements.txt

変更をコミットします。

    git add requirements.txt
    git commit -m "Added azure package"

### Azure へのデプロイ

デプロイを開始するには、変更を Azure にプッシュします。

    git push azure master

仮想環境の作成、パッケージのインストール、web.config の作成といった、デプロイ スクリプトの出力が表示されます。

Azure URL を参照して、変更内容を表示します。


## Troubleshooting - Deployment

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


## トラブルシューティング - パッケージのインストール

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


## トラブルシューティング - 仮想環境

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## 次のステップ

Flask と Python Tools for Visual Studio の詳細については、次のリンクをご覧ください。 
 
- [Flask のドキュメント][]
- [Python Tools for Visual Studio のドキュメント][]

Azure テーブル ストレージと MongoDB の使用方法については、次のリンクをご覧ください。

- [Azure における Flask と MongoDB (Python Tools 2.1 for Visual Studio の使用方法)][]
- [Python Tools 2.1 for Visual Studio を使用した Azure 上の Flask と Azure テーブル ストレージ][]


<!--Link references-->
[Azure における Flask と MongoDB (Python Tools 2.1 for Visual Studio の使用方法)]: web-sites-python-ptvs-flask-table-storage.md
[Python Tools 2.1 for Visual Studio を使用した Azure 上の Flask と Azure テーブル ストレージ]: web-sites-python-ptvs-flask-mongodb.md

<!--External Link references-->
[Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git for Windows に関するページ]: http://msysgit.github.io/
[GitHub for Windows]: https://windows.github.com/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[PTVS のドキュメント]: http://pytools.codeplex.com/documentation
[Python Tools for Visual Studio のドキュメント]: http://pytools.codeplex.com/documentation 
[Flask のドキュメント]: http://flask.pocoo.org/ 

<!--HONumber=52-->