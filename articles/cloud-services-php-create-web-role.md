<properties linkid="develop-php-common-tasks-create-web-and-worker-roles" urlDisplayName="Web ロールとワーカー ロールを作成する" pageTitle="Web ロールとワーカー ロールを作成する" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="PHP" title="PHP Web ロールとワーカー ロールを作成する方法" authors="bswan" solutions="" manager="paulettm" editor="mollybos" />

#PHP Web ロールとワーカー ロールを作成する方法

このガイドでは、Windows 開発環境で PHP Web ロールまたはワーカー ロールを作成する方法を示します。使用可能な "ビルトイン" バージョンから特定バージョンの PHP を選択し、PHP 構成を変更して、拡張機能を有効にする方法と、Azure に展開する方法についても示します。また、指定した PHP ランタイムを (カスタムの構成および拡張機能と共に) 使用できるように Web ロールまたはワーカー ロールを構成する方法についても説明します。

##<a name="TableOfContents"></a>目次
* [PHP Web ロールとワーカー ロールについて](#WhatIs)
* [Azure SDK for PHP をダウンロードする](#DownloadSdk)
* [方法: クラウド サービス プロジェクトを作成する](#CreateProject)
* [方法: PHP Web ロールとワーカー ロールを追加する](#AddRole)
* [方法: ビルトイン PHP バージョンを指定する](#SpecifyPHPVersion)
* [方法: ビルトインの PHP ランタイムをカスタマイズする](#CustomizePHP)
* [方法: 独自の PHP ランタイムを使用する](#OwnPHP)
* [方法: コンピューティング エミュレーターとストレージ エミュレーターでアプリケーションを実行する](#Emulators)
* [方法: アプリケーションを発行する](#Publish)

##<a name="WhatIs"></a>PHP Web ロールとワーカー ロールについて
Azure にはアプリケーションを実行するためのコンピューティング モデルとして、[Azure Web サイト][execution model-web sites]、[Azure 仮想マシン][execution model-vms]、[Azure クラウド サービス][execution model-cloud services]の 3 種類があります。これら 3 つのモデルはすべて、PHP をサポートしています。クラウド サービスには、Web ロールとワーカー ロールが含まれ、*サービスとしてのプラットフォーム (PaaS)* を提供します。クラウド サービス内で、Web ロールは、フロントエンド Web アプリケーションのホスト専用のインターネット インフォメーション サービス (IIS) Web サーバーを提供します。ワーカー ロールは、ユーザーの操作や入力とは関係なく、長期間または恒久的な非同期タスクを実行できます。

詳細については、「[What is a Cloud Service? (クラウド サービスとは)]」を参照してください。

##<a name="DownloadSdk"></a>Azure SDK for PHP をダウンロードする

[Azure SDK for PHP] は、いくつかのコンポーネントで構成されています。この記事では、Azure PowerShell と Azure エミュレーターの 2 つを使用します。これら 2 つのコンポーネントをインストールするには、Microsoft Web プラットフォーム インストーラーを使用します。[ここ][install ps and emulators]をクリックしてインストールしてください。

##<a name="CreateProject"></a>方法: クラウド サービス プロジェクトを作成する

PHP Web ロールまたはワーカー ロールを作成するための最初のステップは、Azure サービス プロジェクトを作成することです。Azure サービス プロジェクトは、Web ロールおよびワーカー ロールの論理コンテナーとして機能し、プロジェクトの[サービス定義ファイル (.csdef)] と[サービス構成ファイル (.cscfg)] を格納します。

新しい Azure サービス プロジェクトを作成するには、次のコマンドを実行します。

	PS C:\>New-AzureServiceProject myProject

このコマンドを実行すると、Web ロールおよびワーカー ロールを追加できる新しいディレクトリ (`myProject`) が作成されます。

##<a name="AddRole"></a>方法: PHP Web ロールまたはワーカー ロールを追加する

PHP Web ロールをプロジェクトに追加するには、プロジェクトのルート ディレクトリで次のコマンドを実行します。

	PS C:\myProject> Add-AzurePHPWebRole roleName

ワーカー ロールについては、次のコマンドを使用します。

	PS C:\myProject> Add-AzurePHPWorkerRole roleName

<div class="dev-callout"> 
<b>注</b> 
<p><code>roleName</code> パラメーターは省略可能です。省略した場合、ロール名は自動的に生成されます。たとえば、最初に作成された Web ロールは <code>WebRole1</code>、2 番目は <code>WebRole2</code> になります。最初に作成されたワーカー ロールは <code>WorkerRole1</code>、2 番目は <code>WorkerRole2</code> になります。</p>
</div>

##<a name="SpecifyPHPVersion"></a>方法: ビルトイン PHP バージョンを指定する

PHP Web ロールまたはワーカー ロールをプロジェクトに追加すると、プロジェクトの構成ファイルが変更され、アプリケーションの展開時に、アプリケーションの各 Web インスタンスまたはワーカー インスタンスに PHP がインストールされるように指定されます。既定でインストールされる PHP のバージョンを確認するには、次のコマンドを実行します。

	PS C:\myProject> Get-AzureServiceProjectRoleRuntime

このコマンドからの出力は、下のような結果になります。この例では、PHP 5.3.17 に対して `IsDefault` フラグが `true` に設定されており、インストールされる既定の PHP バージョンであることを示しています。

	Runtime Version		PackageUri						IsDefault
	------- ------- 	----------  					---------
   	Node 0.6.17      	http://nodertncu.blob.core...   False
   	Node 0.6.20         http://nodertncu.blob.core...   True
   	Node 0.8.4          http://nodertncu.blob.core...   False
	IISNode 0.1.21      http://nodertncu.blob.core...   True
  	Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

PHP ランタイム バージョンは、表示されている任意の PHP バージョンに設定できます。たとえば、(`roleName` という名前のロールに対して) PHP バージョンを 5.4.0 に設定するには、次のコマンドを使用します。

	PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

<div class="dev-callout">
<b>注</b>
<p>今後は PHP バージョンが増え、使用可能なバージョンが変更される可能性があります。</p>
</div>

##<a name="CustomizePHP"></a>方法: ビルトインの PHP ランタイムをカスタマイズする

前に示した手順に従ってインストールした PHP ランタイムの構成は、`php.ini` 設定の変更や拡張機能の有効化など、詳細に制御できます。

ビルトインの PHP ランタイムをカスタマイズするには、次の操作を行います。

1. Web ロールの `bin` ディレクトリに、`php` という名前の新しいフォルダーを追加します。ワーカー ロールについては、このフォルダーをロールのルート ディレクトリに追加します。
2. `php` フォルダー内に、`ext` という名前の別のフォルダーを作成します。有効にする `.dll` 拡張ファイル (`php_mongo.dll` など) はすべて、このフォルダーに配置します。
3. `php.ini` ファイルを `php` フォルダーに追加します。このファイル内では、カスタム拡張を有効にし、PHP ディレクティブを設定します。たとえば、`display_errors` をオンにして `php_mongo.dll` 拡張を有効にする場合、`php.ini` ファイルの内容は次のようになります。

		display_errors=On
		extension=php_mongo.dll

<div class="dev-callout">
<b>注</b>
<p>使用する <code>php.ini</code> ファイル内で明示的に指定されていない設定は、自動的に既定値に設定されます。ただし、すべてを指定した <code>php.ini</code> ファイルを追加できることも覚えておいてください。</p> 
</div>

##<a name="OwnPHP"></a>方法: 独自の PHP ランタイムを使用する
必要に応じて、ビルトインの PHP ランタイムを選択して前の手順のように構成する代わりに、独自の PHP ランタイムを使用することができます。たとえば、開発環境で使用するものと同じ PHP ランタイムを Web ロールまたはワーカー ロールで使用すると、運用環境でもアプリケーションの動作が変わらないことが容易に確認できます。

<h3><a name="OwnPHPWebRole"></a>独自の PHP ランタイムが使用されるように Web ロールを構成する</h3>

指定した PHP ランタイムが使用されるように Web ロールを構成するには、次の操作を行います。

1. 前の「[方法: クラウド サービス プロジェクトを作成する](#CreateProject)」セクションおよび「[方法: PHP Web ロールまたはワーカー ロールを追加する](#AddRole)」セクションの説明に従い、Azure サービス プロジェクトを作成して、PHP Web ロールを追加します。
2. Web ロールのルート ディレクトリにある `bin` フォルダー内に `php` フォルダーを作成し、この `php` フォルダーに PHP ランタイム (すべてのバイナリ、構成ファイル、サブフォルダーなど) を追加します。
3. (省略可能) PHP ランタイムに [Microsoft Drivers for PHP for SQL Server][sqlsrv drivers] が使用されている場合は、Web ロールを構成して、プロビジョニング時に [SQL Server Native Client 2012][sql native client] がインストールされるように指定する必要があります。これには、Web ロールのルート ディレクトリにある `bin` フォルダーに、`sqlncli.msi` インストーラーを追加します。このインストーラーは[ここ]からダウンロードできます。次のステップで説明するスタートアップ スクリプトでは、ロールのプロビジョニング時にインストーラーが自動的に実行されます。PHP ランタイムに Microsoft Drivers for PHP for SQL Server が使用されていない場合は、次のステップで示すスクリプトから、この行を削除できます。

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. 次のステップでは、`.php` ページに対する要求を処理する PHP ランタイムが使用されるように[インターネット インフォメーション サービス (IIS)][iis.net] を構成するスタートアップ タスクを定義します。これには、`setup_web.cmd` ファイル (Web ロールのルート ディレクトリにある `bin` フォルダー内) をテキスト エディターで開き、内容を次のスクリプトに置き換えます。

		@ECHO ON
		cd "%~dp0"
		
		if "%EMULATED%"=="true" exit /b 0
		
		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
		
		SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
		SET NEW_PATH=%PATH%;%RoleRoot%\base\x86
		
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. アプリケーション ファイルを Web ロールのルート ディレクトリに追加します。これは Web サーバーのルート ディレクトリになります。

6. 後の「[方法: アプリケーションを発行する](#Publish)」セクションの説明に従って、アプリケーションを発行します。

<div class="dev-callout">
<b>注</b>
<p>前の手順で説明されていた、独自の PHP ランタイムを使用するための操作を実行した後、<code>download.ps1</code> スクリプト (Web ロールのルート ディレクトリにある <code>bin</code> フォルダー内) は削除することができます。</p>
</div>

<h3><a name="OwnPHPWorkerRole"></a>独自の PHP ランタイムが使用されるようにワーカー ロールを構成する</h3>

指定した PHP ランタイムが使用されるようにワーカー ロールを構成するには、次の操作を行います。

1. 前の「[方法: クラウド サービス プロジェクトを作成する](#CreateProject)」セクションおよび「[方法: PHP Web ロールまたはワーカー ロールを追加する](#AddRole)」セクションの説明に従い、Azure サービス プロジェクトを作成して、PHP ワーカー ロールを追加します。
2. ワーカー ロールのルート ディレクトリに `php` フォルダーを作成し、この `php` フォルダーに PHP ランタイム (すべてのバイナリ、構成ファイル、サブフォルダーなど) を追加します。
3. (省略可能) PHP ランタイムに [Microsoft Drivers for PHP for SQL Server][sqlsrv drivers] が使用されている場合は、ワーカー ロールを構成して、プロビジョニング時に [SQL Server Native Client 2012][sql native client] がインストールされるように指定する必要があります。これには、ワーカー ロールのルート ディレクトリに、`sqlncli.msi` インストーラーを追加します。このインストーラーは[ここ]からダウンロードできます。次のステップで説明するスタートアップ スクリプトでは、ロールのプロビジョニング時にインストーラーが自動的に実行されます。PHP ランタイムに Microsoft Drivers for PHP for SQL Server が使用されていない場合は、次のステップで示すスクリプトから、この行を削除できます。

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. 次のステップでは、ロールのプロビジョニング時にワーカー ロールの PATH 環境変数に `php.exe` 実行可能ファイルを追加するスタートアップ タスクを定義します。これには、`setup_worker.cmd` ファイル (ワーカー ロールのルート ディレクトリ内) をテキスト エディターで開き、内容を次のスクリプトに置き換えます。

		@echo on

		cd "%~dp0"

		echo Granting permissions for Network Service to the web root directory...
		icacls ..\ /grant "Network Service":(OI)(CI)W
		if %ERRORLEVEL% neq 0 goto error
		echo OK

		if "%EMULATED%"=="true" exit /b 0

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

		setx Path "%PATH%;%~dp0php" /M

		if %ERRORLEVEL% neq 0 goto error

		echo SUCCESS
		exit /b 0

		:error

		echo FAILED
		exit /b -1	

5. アプリケーション ファイルをワーカー ロールのルート ディレクトリに追加します。

6. 後の「[方法: アプリケーションを発行する](#Publish)」セクションの説明に従って、アプリケーションを発行します。

##<a name="Emulators"></a>方法: コンピューティング エミュレーターとストレージ エミュレーターでアプリケーションを実行する

Azure コンピューティング エミュレーターとストレージ エミュレーターでは、Azure アプリケーションをクラウドに展開する前にテストできるローカル環境が提供されます。ただし、エミュレーターと Azure 環境では、いくつか相違点があります。詳細については、「[コンピューティング エミュレーターと Azure との違い](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg432960.aspx)」および「[ストレージ エミュレーターと Azure ストレージ サービスとの違い](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433135.aspx)」を参照してください。

コンピューティング エミュレーターを使用するには、ローカルで PHP をインストールしておく必要があります。コンピューティング エミュレーターでは、アプリケーションを実行するために、ローカルの PHP インストールが使用されます。

エミュレーターでプロジェクトを実行するには、プロジェクトのルート ディレクトリで次のコマンドを実行します。

	PS C:\MyProject> Start-AzureEmulator

次のような出力が表示されます。

	Creating local package...
	Starting Emulator...
	Role is running at http://127.0.0.1:81
	Started

Web ブラウザーを開き、出力に示されているローカル アドレス (上の出力例では `http://127.0.0.1:81`) に移動すると、アプリケーションがエミュレーターで実行されていることを確認できます。

エミュレーターを停止するには、次のコマンドを実行します。

	PS C:\MyProject> Stop-AzureEmulator

##<a name="Publish"></a>方法: アプリケーションを発行する

アプリケーションを発行するには、まず **Import-PublishSettingsFile** コマンドレットを使用して発行設定をインポートする必要があります。次に **Publish-AzureServiceProject** コマンドレットを使用して、アプリケーションを発行できます。これらのコマンドレットの使用方法については、それぞれ「[How to: Import publish settings (方法: 発行の設定をインポートする)]」および「[How to: Deploy a cloud service to Azure (方法: Azure にクラウド サービスを展開する)]」を参照してください。

[execution model-web sites]: /ja-jp/develop/net/fundamentals/compute/#WebSites
[execution model-vms]: /ja-jp/develop/net/fundamentals/compute/#VMachine
[execution model-cloud services]: /ja-jp/develop/net/fundamentals/compute/#CloudServices
[PHP 用 Azure SDK]: /ja-jp/develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/?LinkId=253447&clcid=0x409
[クラウド サービスとは]: /ja-jp/manage/services/cloud-services/what-is-a-cloud-service/
[サービス定義ファイル (.csdef)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758711.aspx
[サービス構成ファイル (.cscfg)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/ja-jp/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[sqlncli.msi x64 インストーラー]: http://go.microsoft.com/fwlink/?LinkID=239648
[How to: Import publish settings (方法: 発行の設定をインポートする)]: /ja-jp/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
[How to: Deploy a cloud service to Azure (方法: Azure にクラウド サービスを展開する)]: /ja-jp/develop/php/how-to-guides/powershell-cmdlets/#Deploy

