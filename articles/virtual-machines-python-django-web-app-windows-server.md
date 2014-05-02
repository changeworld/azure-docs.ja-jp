<properties linkid="develop-python-web-app-with-django" urlDisplayName="Django を使用した Web (Windows)" pageTitle="Django を使用した Python Web アプリケーション - Azure チュートリアル" metaKeywords="Azure Django Web アプリケーション, Azure Django 仮想マシン" description="Windows Server 2008 R2 仮想マシンを使用して Azure で Django ベースの Web サイトをホストする方法について説明するチュートリアルです。" metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web アプリケーション" authors="" solutions="" manager="" editor="" />




# Django Hello World Web アプリケーション

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/ja-jp/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

このチュートリアルでは、Windows Server 仮想マシンを使用し、Windows 
Azure で Django ベースの Web サイトをホストする方法について説明します。このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルを完了すると、クラウドで動作する Django ベースのアプリケーションが完成します。

学習内容: 

* Django をホストするように Azure の仮想マシンを設定します。このチュートリアルでは **Windows Server** で Azure の仮想マシンを設定する方法について説明しますが、Azure でホストされている Linux VM の場合も基本的な手順は同じです。
* 新しい Django アプリケーションを Windows から作成します。

このチュートリアルを実行して、単純な Hello World Web
アプリケーションを作成します。このアプリケーションは Azure の仮想マシンでホストされます。

完成したアプリケーションのスクリーンショットは次のようになります。

![Azure で Hello World ページを表示するブラウザー ウィンドウ][1]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Django をホストする Azure の仮想マシンの作成と構成

1. [ここ][portal-vm]に記載されている手順に従って、*Windows Server 2012 データセンター* ディストリビューションの Azure の仮想マシンを作成します。

1. Azure で、ポート **80** トラフィックを Web から仮想マシン上のポート **80** に転送します。
 - Azure ポータルで新しく作成した仮想マシンに移動し、*[エンドポイント]* タブをクリックします。
 - 画面の下部にある *[エンドポイントの追加]* ボタンをクリックします。
	![エンドポイントの追加](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

 - *TCP* プロトコルの*パブリック ポート 80* を*プライベート ポート 80* として開きます。
![][port80]
1. Windows *リモート デスクトップ*を使用して、新しく作成した Azure の仮想マシンにリモートでログインします。

**重要:** 次に示しているすべての手順では、仮想マシンに正しくログインしており、ローカル コンピューターではなく仮想マシンでコマンドを発行することを前提としています。

## <a id="setup"> </a>開発環境の設定

Python と Django の環境を設定する場合、詳細については「[Installation Guide (インストール ガイド)][]」を参照してください。

**注 1:** *この*チュートリアルに示されている手順を実施するには、Windows WebPI インストーラーから **Django** 製品をインストールすること*のみ*が必要になります。

**注 2:** WebPI インストーラーをダウンロードするには、IE ESC 設定の構成が必要になる場合があります。そのためには、[スタート]、[管理ツール]、[サーバー マネージャー] の順にクリックしてから、**[IE ESC の構成]** をクリックし、[オフ] に設定します。

## FastCGI での IIS の設定


1. FastCGI のサポートを利用した IIS のインストール

		start /wait %windir%\System32\\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI


1. Python FastCGI ハンドラーの設定

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"



1. このサイトのハンドラーの登録

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Django アプリケーションを実行するようにハンドラーを構成する

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='DjangoApplication.settings']" /commit:apphost

1. Python インタープリターで Django アプリケーションを検索できるように PYTHONPATH を構成する

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\DjangoApplication']" /commit:apphost

	次のように表示されます。

	![IIS 構成 1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis1.png)

1. FastCGI から WSGI へのゲートウェイにどの WSGI ハンドラーを使用するかを伝えます。

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost


1. [CodePlex](http://go.microsoft.com/fwlink/?LinkID=316392&clcid=0x409) から wfastcgi.py をダウンロードし、C:\Python27\Scripts に保存します。このディレクトリは、FastCGI ハンドラーの登録で以前のコマンドが使用していた場所です。または、Web Platform Installer を使用してインストールすることもできます。「WFastCGI」を検索してください。


## 新しい Django アプリケーションの作成


1.  cmd.exe を起動します。
    
1.  C:\inetpub\wwwroot ディレクトリへ移動します。

1. 次のコマンドを入力して、新しい Django プロジェクトを作成します。


	C:\Python27\python.exe -m django.bin.django-admin startproject DjangoApplication
    
	![New-AzureService コマンドの結果](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

 **django-admin.py** スクリプトによって、Django ベースの Web サイトの基本的な構造が生成されます。
    
- **manage.py** を使用すると、Django ベースの Web サイトに対するホスティングの開始や停止を実行できます。
- **DjangoApplication\settings.py には、アプリケーション向けの Django の設定が含まれています。
- **DjangoApplication\urls.py** には、各 URL とそのビューとの間のマッピング コードが含まれています。



1. *C:\inetpub\wwwroot\DjangoApplication* のサブディレクトリ *DjangoApplication* に、**views.py** という名前の新しいファイルを作成します。このファイルは **urls.py** と兄弟関係にあります。このファイルには、"Hello World" ページをレンダリングするビューが含まれます。エディターを起動し、次のコードを入力します。
		
		from django.http import HttpResponse
		def hello(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1. ここで、**urls.py** ファイルの内容を次のコードに置き換えます。

		from django.conf.urls.defaults import patterns, include, url
		from DjangoApplication.views import hello
		urlpatterns = patterns('',
			(r'^$',hello),
		)

1. 最後に、ブラウザーで Web ページを読み込みます。

![Azure で Hello World ページを表示するブラウザー ウィンドウ][1]

## Azure の仮想マシンのシャットダウン

このチュートリアルが終了したら、新しく作成した Azure の仮想マシンをシャットダウンまたは削除して、他のチュートリアル用にリソースを解放し、Azure に対する利用料金の発生を回避します。

[1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png

[portal-vm]: /ja-jp/manage/windows/tutorials/virtual-machine-from-gallery/

[Installation Guide (インストール ガイド)]: ../python-how-to-install/

