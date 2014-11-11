<properties linkid="develop-python-web-app-with-django" urlDisplayName="Web with Django (Windows)" pageTitle="Python web app with Django - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to host a Django-based website on Azure using a Windows Server 2008 R2 virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Django Hello World Web アプリケーション

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/ja-jp/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

このチュートリアルでは、Windows Server 仮想マシンを使用して Windows Azure でDjango ベースの Web サイトをホストする方法について説明します。このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルを完了すると、クラウドで動作する Django ベースのアプリケーションが完成します。

学習内容:

-   Django をホストするように Azure の仮想マシンを設定します。このチュートリアルでは **Windows Server** で Azure の仮想マシンを設定する方法について説明しますが、Azure でホストされている Linux VM の場合も基本的な手順は同じです。
-   新しい Django アプリケーションを Windows から作成します。

このチュートリアルを実行して、単純な Hello World Web アプリケーションを作成します。このアプリケーションは Azure の仮想マシンでホストされます。

完成したアプリケーションのスクリーンショットは次のようになります。

![Azure で Hello World ページを表示するブラウザー ウィンドウ][Azure で Hello World ページを表示するブラウザー ウィンドウ]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Django をホストする Azure の仮想マシンの作成と構成

1.  [ここ][ここ]に記載されている手順に従って、*Windows Server 2012 データセンター* ディストリビューションの Azure の仮想マシンを作成します。

2.  Azure で、ポート **80** トラフィックを Web から仮想マシン上のポート **80** に転送します。
 - Azure ポータルで新しく作成した仮想マシンに移動し、*[エンドポイント]* タブをクリックします。
 - 画面の下部にある *[エンドポイントの追加]* ボタンをクリックします。
    ![エンドポイントの追加][エンドポイントの追加]

 - *TCP* プロトコルの *パブリック ポート 80* を *プライベート ポート 80* として開きます。
    ![][0]

1.  Windows リモート デスクトップを使用して、新しく作成した Azure の仮想マシンにリモートでログインします。

**重要:** 以下のすべての手順では、仮想マシンに正しくログインしており、ローカル コンピューターではなく仮想マシンでコマンドを発行することを前提としています。

## <span id="setup"></span> </a>開発環境の設定

Python と Django の環境を設定する場合、詳細については「[Installation Guide (インストール ガイド)][Installation Guide (インストール ガイド)]」を参照してください。

**注 1:** このチュートリアルに示されている手順を実施するには、Windows WebPI インストーラーから **Django** 製品を Azure の仮想マシンにインストールするだけで十分です。

**注 2:** WebPI インストーラーをダウンロードするには、IE ESC 設定の構成が必要になる場合があります。そのためには、[スタート]、[管理ツール]、[サーバー マネージャー] の順にクリックしてから、**[IE ESC の構成]** をクリックし、[オフ] に設定します。

## FastCGI での IIS の設定

1.  FastCGI のサポートを利用した IIS のインストール

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

2.  Python FastCGI ハンドラーの設定

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

3.  このサイトのハンドラーの登録

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

4.  Django アプリケーションを実行するようにハンドラーを構成する

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='DjangoApplication.settings']" /commit:apphost

5.  Python インタープリターで Django アプリケーションを検索できるように PYTHONPATH を構成する

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\DjangoApplication']" /commit:apphost

    次のように表示されます。

    ![IIS 構成 1][IIS 構成 1]

6.  FastCGI から WSGI へのゲートウェイにどの WSGI ハンドラーを使用するかを伝えます。

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

7.  [CodePlex][CodePlex] から wfastcgi.py をダウンロードし、C:\\Python27\\Scripts に保存します。このディレクトリは、FastCGI ハンドラーの登録で以前のコマンドが使用していた場所です。または、Web Platform Installer を使用してインストールすることもできます。「WFastCGI」を検索してください。

## 新しい Django アプリケーションの作成

1.  cmd.exe を起動します。

2.  C:\\inetpub\\wwwroot ディレクトリへ移動します。

3.  次のコマンドを入力して、新しい Django プロジェクトを作成します。

    C:\\Python27\\python.exe -m django.bin.django-admin startproject DjangoApplication

    ![New-AzureService コマンドの結果][New-AzureService コマンドの結果]

     **django-admin.py** スクリプトによって、Django ベースの Web サイトの基本的な構造が生成されます。

 -   **manage.py** を使用すると、Django ベースの Web サイトに対するホスティングの開始や停止を実行できます。
 -   **DjangoApplication\\settings.py** には、アプリケーション向けの Django の設定が含まれています。
 -   **DjangoApplication\\urls.py** には、各 URL とそのビューとの間のマッピング コードが含まれています。
<br/><br/>
1.  *C:\\inetpub\\wwwroot\\DjangoApplication* のサブディレクトリ *DjangoApplication* に、**views.py** という名前の新しいファイルを作成します。このファイルは **urls.py** と兄弟関係にあります。このファイルには、"Hello World" ページをレンダリングするビューが含まれます。エディターを起動し、次のコードを入力します。

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  ここで、**urls.py** ファイルの内容を次のコードに置き換えます。

        from django.conf.urls.defaults import patterns, include, url
        from DjangoApplication.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

3.  最後に、ブラウザーで Web ページを読み込みます。

![Azure で Hello World ページを表示するブラウザー ウィンドウ][Azure で Hello World ページを表示するブラウザー ウィンドウ]

## Azure の仮想マシンのシャットダウン

このチュートリアルが終了したら、新しく作成した Azure の仮想マシンをシャットダウンまたは削除して、他のチュートリアル用にリソースを解放し、Azure に対する利用料金の発生を回避します。

  [Azure で Hello World ページを表示するブラウザー ウィンドウ]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [ここ]: /ja-jp/manage/windows/tutorials/virtual-machine-from-gallery/
  [エンドポイントの追加]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png
  [0]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png
  [IIS 構成 1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis1.png
  [CodePlex]: http://go.microsoft.com/fwlink/?LinkID=316392&clcid=0x409
  [New-AzureService コマンドの結果]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png
