<properties urlDisplayName="Websites with Django" pageTitle="Django を使用した Python Web サイト - Azure チュートリアル" metaKeywords="Azure django, django website" description="Azure での Python Web サイトの実行について説明するチュートリアル。" metaCanonical="" services="web-sites" documentationCenter="Python" title="Creating Websites with Django" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="08/01/2014" ms.author="huvalo" />




# Django を使用した Web サイトの作成

このチュートリアルでは、Azure Web サイトで Python を実行するための基本的な方法について説明します。Azure Web サイトでは、制限付きの無料のホスティングや迅速な展開を実行できます。また、Python も使用できるようになりました。アプリケーションの拡張に合わせて、有料のホスティングに切り替えることができます。また、他のすべての Azure サービスと統合することもできます。  

このチュートリアルでは、Django Web フレームワークを使用して構築されたアプリケーションを展開する方法について説明します。ここでは、アプリケーションを展開する手順や Django などの必要なライブラリについて紹介します。また、このアプリケーションやライブラリをすべて Git リポジトリに登録し、Web サイトに更新情報をすばやく簡単にプッシュできるようにします。最後に、Python アプリケーションが実行できるように、Azure で新たに作成したサイトを構成します。  

> [WACOM.NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 <a href="http://azure.microsoft.com/ja-jp/pricing/member-offers/msdn-benefits-details/">MSDN サブスクライバーの特典を有効にするか、</a> または <a href="http://azure.microsoft.com/ja-jp/pricing/free-trial/">無料評価版にサインアップすることができます</a>。
> 
> アカウントにサインアップする前に Azure Websites を試してみたい場合は、 <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a> で、有効期限が短い ASP.NET スターター サイトを Azure Websites に無料で作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

このチュートリアルでは、Python 2.7 と Django 1.4 を使用します。ユーザーはこれらを独自に入手できますが、[http://www.windowsazure.com/ja-jp/develop/python/] にある Windows インストーラーのリンクを使用するとすばやく簡単にインストールできます(http://www.windowsazure.com/ja-jp/develop/python/)。  

**メモ**: Azure Web サイトには Python (2.7.3 または 3.4.0) が用意されており、wfastcgi ハンドラーがプレインストールされています。ただし、Django などの Web フレームワークは用意されていません。必要であれば、別の Python インタープリターを使用することもできます。そのインタープリターを Git リポジトリに登録して、既にインストールされている Python 2.7 インタープリターではなく必要なインタープリターを使用するように Web サイトを構成するだけです。

> [WACOM.NOTE] Azure Websites のポータルで使用する Python のバージョンを選択できるようになりました。Web サイトの [構成] タブを開き、**[Python バージョン]** の設定を変更します。

Azure にサイトをプッシュするために展開オプションをインストールする必要もあります。さまざまな展開ツールがありますが、このチュートリアルでは Git を使用します。[msysgit] をお勧めします(http://code.google.com/p/msysgit/)。 

**メモ**: 現在、TFS 発行は Python プロジェクトではサポートされていません。

Python、Django、Git をインストールすると、このチュートリアルを進めるうえで必要なものがすべて揃ったことになります。

## ポータルでの Web サイトの作成

アプリケーションを作成する最初の手順では、Azure の管理ポータルで Web サイトを作成します。そのためには、ポータルにログインし、画面左下にある [新規] ボタンをクリックします。ウィンドウが表示されます。**[簡易作成]** をクリックし、URL を入力して **[Web サイトの作成]** を選択します。

![](./media/web-sites-python-create-deploy-django-app/django-ws-003.png)

サイトがすぐに設定されます。次に、Git を使って発行する際に必要となるサポートを追加するため、**[ソース管理からの展開の設定]** をクリックします。

![](./media/web-sites-python-create-deploy-django-app/django-ws-004.png)

**[展開の設定]** ダイアログで、下へスクロールして **[ローカル Git]** オプションを選択します。右矢印をクリックして次へ進みます。

![](./media/web-sites-python-create-deploy-django-app/django-ws-005.png)

Git 発行を設定した後で、リポジトリが作成中であることを示すページがすぐに表示されます。リポジトリの準備ができると、展開に関するタブが表示されます。このタブには、接続方法を示す手順が記載されています。  

![](./media/web-sites-python-create-deploy-django-app/django-ws-006.png)


## Web サイトの開発

これで、Azure に Git リポジトリが作成されました。次に、ローカル コンピューターからこのリポジトリに、Web サイトを登録します。最初の手順では、指定の URL を使用して既存の空のサイトを複製します。

![](./media/web-sites-python-create-deploy-django-app/django-ws-007.png)

この時点で、Web サイトを使用した登録を設定する準備ができています。必要な操作をいくつか実行します。

1.  Web サイトの実行で使用する Django ライブラリとその他のライブラリを登録します。
2.  Django アプリケーション コードを登録します。

最初に、Django ライブラリを登録します。そのためには、次のコマンドを使用して、site-packages という新しいディレクトリを作成し、インストールされている Django のバージョンをコピーします。

	mkdir site-packages
	cd site-packages
	xcopy /s C:\Python27\lib\site-packages\* .

これらのコマンドによって、Django が含まれている site-packages にあるすべてのライブラリがコピーされます。Web サイトで使用されないライブラリがある場合は、それらを削除してもかまいません。

![](./media/web-sites-python-create-deploy-django-app/django-ws-008.png)
 
次に、最初の Django アプリケーションを作成します。そのためには、コマンド ラインから任意の Django アプリケーションを作成する方法、または [Python Tools for Visual Studio] (http://pytools.codeplex.com/) を使用してプロジェクトを作成する方法を利用できます。ここでは両方の方法について説明します。

**方法 1:** 
コマンド ラインから新しいプロジェクトを作成するために、次のコマンドを実行します。このコマンドによって、Django アプリケーションが DjangoApplication フォルダーに作成されます。

	 C:\Python27\python.exe -m django.bin.django-admin startproject DjangoApplication

![](./media/web-sites-python-create-deploy-django-app/django-ws-010.png)

**方法 2:**  
Python Tools for Visual Studio を使用して新しいサイトを作成することもできます。インストールされている Python Tools for Visual Studio で Visual Studio を開始し、**[ファイル]**、**[新しいプロジェクト]** の順に選択します。**[他の言語]** の下にある [Python] プロジェクトを表示し、**[Django Application]** を選択します。プロジェクトの名前に「**DjangoApplication**」と入力し、**[ソリューションのディレクトリを作成]** がオフになっていることを確認します。これにより、コマンド ラインから Django アプリケーションを作成する場合と同じディレクトリ構造が作成されます。この方法では、Visual Studio ソリューションとプロジェクト ファイルを使用した設定を行います。プロジェクト ファイルによって、テンプレート デバッグや Intellisense などのローカルな開発に役立つ機能を利用できます。

![](./media/web-sites-python-create-deploy-django-app/django-ws-011.png)

ここで、上でコピーしたファイルや作成したファイルをすべて Git に追加し、サイトを Git にプッシュする必要があります。そのためには、次のコマンドを実行します。

	git add DjangoApplication site-packages
	git commit -m "Initial site"
	git remote add azure https://dinov@pythonwebsite.scm.azurewebsites.net/PythonWebSite.git
	git push azure master

最初のコマンドは追跡対象外のファイルを追跡対象のファイルに追加します。2 番目のコマンドは、追加したファイルをリポジトリへコミットします。3 番目のコマンドは、リポジトリ用に *azure* という名前を指定してリモート リポジトリを追加します。最後のコマンドは、ここで行った変更内容をリモート リポジトリにプッシュします。これにより展開も開始されます。このコマンドを実行すると、次のような結果が表示されます。

![](./media/web-sites-python-create-deploy-django-app/django-ws-013.png)

プッシュの実行後、Azure ポータルが更新され、アクティブな展開が表示されます。

![](./media/web-sites-python-create-deploy-django-app/django-ws-014.png)

## Web サイトの構成

Django プロジェクトを認識し、wfastcgi ハンドラーを使用するように Web サイトを構成する必要があります。そのためには、画面の上部にある [構成] タブをクリックします。このタブで、ページの半分まで下へスクロールし、**アプリケーションの設定**と**ハンドラー マッピング**のセクションを表示します。  

ここで設定されているすべての設定値は、実際の要求が発生したときの環境変数の値になります。つまり、この設定を使用して、DJANGO\_SETTINGS\_MODULE 環境変数、PYTHONPATH、および WSGI\_HANDLER を構成できます。アプリケーションが他の構成値を含んでいる場合は、ここで設定値を割り当て、環境から構成値を選択することができます。必要に応じて、Web サイトにおけるファイルへのパスなどを設定します。たとえば、PYTHONPATH に対して値を設定します。Azure の Web サイトとして実行する場合、Web サイトは **D:\home\site\wwwroot\** 内で動作します。これにより、ディスク上にあるファイルへの完全パスが必要となる場所であれば、どの場所でもその Web サイトを使用することができます。

Django アプリケーションを設定する場合、3 つの環境変数を作成する必要があります。最初の環境変数は DJANGO\_SETTINGS\_MODULE です。これは、構成に使用される Django アプリケーションのモジュール名を示します。2 番目の環境変数は PYTHONPATH です。この変数は、設定モジュールが存在するパッケージを指定します。3 番目の環境変数は WSGI\_HANDLER です。この変数はモジュールおよびパッケージ名で、その後にモジュールが使用される属性 (たとえば、mypackage.mymodule.handler) が指定されます。属性を呼び出す必要がある場合は、かっこを追加します。これらの変数は、次のように設定します。
                
	DJANGO_SETTINGS_MODULE    DjangoApplication.settings
	PYTHONPATH                D:\home\site\wwwroot\DjangoApplication;D:\home\site\wwwroot\site-packages
	WSGI_HANDLER              django.core.handlers.wsgi.WSGIHandler()

![](./media/web-sites-python-create-deploy-django-app/django-ws-015.png)

次に、ハンドラー マッピングを構成する必要があります。そのためには、Python インタープリターへのパスと wfastcgi.py スクリプトへのパスを使用して、すべての拡張のハンドラーを登録します。

	EXTENSION                 *
	SCRIPT PROCESSOR PATH     D:\python27\python.exe
	ADDITIONAL ARGUMENTS      D:\python27\scripts\wfastcgi.py

![](./media/web-sites-python-create-deploy-django-app/django-ws-016.png)

ここで、下部にある **[保存]** ボタンをクリックします。

最後に、ダッシュボードへ戻ります。左下の **[サイトの URL]** を確認します。リンクをクリックすると、次のような新しい Django サイトが開きます。

![](./media/web-sites-python-create-deploy-django-app/django-ws-017.png)

## 次のステップ

この後で、既に利用しているツールを使用して Django アプリケーションを引き続き開発することができます。[Python Tools for Visual Studio] (http://pytools.codeplex.com/) を開発に使用している場合、[VisualGit] (http://code.google.com/p/visualgit/) をインストールすることによって、Visual Studio 内からソース管理を行うことができます。  

アプリケーションで、Python や Django 以外の依存関係が必要になる場合があります。[http://www.windowsazure.com/ja-jp/develop/python/] にあるインストーラーを使用して Python をインストールした場合は、(http://www.windowsazure.com/ja-jp/develop/python/)既に PIP がインストールされています。PIP を使用して、新しい依存関係を簡単に追加できます。たとえば、自然言語ツールキットとその依存関係をすべてインストールするには、次のように入力します。

	pip install nltk

次に、C:\Python27\Lib\site-packages からローカルの site-packages ディレクトリにファイルをコピーして、site-packages ディレクトリを更新する必要があります。

ファイルをコピーした後で、コマンド **git status** を実行して新たに追加されたファイルを表示し、**git add** の後で **git commit** を実行してリポジトリに変更をコミットします。最後に、**git push** を実行して、更新された Web サイトを Azure に展開できます。

ここで、DjangoApplication ディレクトリに移動して、manage.py を一般的な方法に従って使用し、新しいアプリケーションを Django プロジェクトに追加することができます。  

<!--HONumber=35.1-->
