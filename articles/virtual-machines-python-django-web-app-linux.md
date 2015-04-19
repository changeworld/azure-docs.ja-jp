<properties 
	pageTitle="Mac で Django を使用した Python Web アプリケーション - Azure チュートリアル" 
	description="Linux 仮想マシンを使用して Azure で Django ベースの Web サイトをホストする方法について説明するチュートリアルです。" 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>





# Django Hello World Web アプリケーション (mac-linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

このチュートリアルでは、Linux 仮想マシンを使用して Windows 
Azure で Django ベースの Web サイトをホストする方法について説明します。このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルを完了すると、クラウドで動作する Django ベースのアプリケーションが完成します。

学習内容:

* Django をホストするように Azure の仮想マシンを設定します。このチュートリアルでは **Linux** で Azure の仮想マシンを設定する方法について説明しますが、Azure でホストされている Windows Server VM の場合も基本的な手順は同じです。 
* 新しい Django アプリケーションを Linux から作成します。

このチュートリアルを実行して、単純な Hello World Web
アプリケーションを作成します。このアプリケーションは Azure の仮想マシンでホストされます。

完成したアプリケーションのスクリーンショットは次のようになります。

![A browser window displaying the hello world page on Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Django をホストする Azure の仮想マシンの作成と構成

1. [ここ ][portal-vm] に記載されている手順に従って *Ubuntu Server 14.04 LTS* ディストリビューションの Azure 仮想マシンを作成します。

  **注:** ここで作成する必要があるのは、仮想マシン*only*です。*How to log on to the virtual machine after you create it* というセクションで終了します。

1. Azure で、ポート **80** トラフィックを Web から仮想マシン上のポート **80** に転送します。
	* Azure ポータルで新しく作成した仮想マシンに移動し、*[ENDPOINTS]* タブをクリックします。
	* 画面の下部にある *[ADD]* をクリックします。
	![add endpoint](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
	* *TCP* プロトコルの *PUBLIC PORT 80* を *PRIVATE PORT 80* として開きます。
	![port80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

## <a id="setup"> </a>開発環境の設定

**注:** Python をインストールする、またはクライアント ライブラリを使用する場合は、「[Python Installation Guide (Python インストール ガイド)](python-how-to-install.md)」をご覧ください.

Ubuntu Linux VM には既に Python 2.7 がプレインストールされていますが、Apache または Django はインストールされていません。VM に接続し、Apache と Django をインストールするには、次の手順に従います。

1.  新しい**ターミナル** ウィンドウを起動します。
    
1.  次のコマンドを入力して、Azure VM に接続します。

		$ ssh yourusername@yourVmUrl

1.  次のコマンドを入力して、Django をインストールします。

		$ sudo apt-get install python-setuptools
		$ sudo easy_install django

1.  次のコマンドを入力して、Apache と mod-wsgi をインストールします。

		$ sudo apt-get install apache2 libapache2-mod-wsgi


## 新しい Django アプリケーションの作成

1.  前のセクションで使用した**[ターミナル]** ウィンドウを開き、SSH キーを使用して VM にログインします。
    
1.  次のコマンドを入力して、新しい Django プロジェクトを作成します。

		$ cd /var/www
		$ sudo django-admin.py startproject helloworld

    **django-admin.py** スクリプトによって、Django ベースの Web サイトの基本構造が生成されます。
    -   **helloworld/manage.py** を使用すると、Django ベースの Web サイトに対するホスティングの開始や停止を実行できます。
    -   **helloworld/helloworld/settings.py** には、アプリケーション用の Django の設定が含まれています。
    -   **helloworld/helloworld/urls.py** には、各 URL とそのビューとの間のマッピング コードが含まれています。

1.  **/var/www/helloworld/helloworld** ディレクトリに **「views.py」** という名前の新しいファイルを作成します。このファイルには、"Hello World" ページをレンダリングするビューが含まれます。エディターを起動し、次のコードを入力します。
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  ここで、**urls.py** ファイルの内容を次のコードに置き換えます。

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)


## Apache の設定

1.  Apache 仮想ホスト構成ファイル **/etc/apache2/sites-available/helloworld.conf** を作成します。下記にコンテンツを設定します。*yourVmUrl* は、使用するマシンの実際の URL に置き換えてください (例: *pyubuntu.cloudapp.net*)。

		<VirtualHost *:80>
		ServerName yourVmUrl
		</VirtualHost>
		WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
		WSGIPythonPath /var/www/helloworld

1.  次のコマンドでサイトを有効にします。

        $ sudo a2ensite helloworld

1.  次のコマンドで Apache を再起動します。

        $ sudo service apache2 reload

1.  最後に、ブラウザーで Web ページを読み込みます。

	![A browser window displaying the hello world page on Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)


## Azure の仮想マシンのシャットダウン

このチュートリアルが終了したら、新しく作成した Azure の仮想マシンをシャットダウンまたは削除して、他のチュートリアル用にリソースを解放し、Azure に対する利用料金の発生を回避します。


[portal-vm]: /manage/linux/tutorials/virtual-machine-from-gallery/

<!--HONumber=47-->
