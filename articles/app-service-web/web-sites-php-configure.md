<properties
	pageTitle="Azure App Service Web アプリでの PHP の構成方法"
	description="Azure App Service の Web Apps 用に既定の PHP インストールを構成する方法、またはカスタム PHP インストールを追加する方法を説明します。"
	services="app-service\web"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="06/24/2015"
	ms.author="tomfitz"/>

#Azure App Service Web アプリでの PHP の構成方法

## はじめに

このガイドでは、ビルトインの PHP ランタイムを [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) の Web Apps で構成する方法、カスタムの PHP ランタイムを提供する方法、および拡張機能を有効にする方法を示します。App Service を使用するには、[無料評価版]にサインアップしてください。このガイドを最大限に活用するには、まず App Service で PHP Web アプリを作成する必要があります。

## 方法: ビルトインの PHP バージョンを変更する
既定では、App Service Web アプリを作成すると PHP 5.4 がインストールされ、直ちに使用できる状態になります。使用可能なリリース リビジョン、既定の構成、および有効な拡張機能を確認するには、[phpinfo()] 関数を呼び出すスクリプトをデプロイします。

PHP 5.5 および PHP 5.6 も使用できますが、既定では有効になっていません。PHP バージョンを更新するには、次のいずれかの方法に従います。

### Azure ポータル

1. [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)で Web アプリに移動し、**[設定]** ボタンをクリックします。

	![Web アプリ設定][settings-button]

2. **[設定]** ブレードで、**[アプリケーションの設定]** を選択し、新しい PHP バージョンを指定します。

    ![アプリケーションの設定][application-settings]

3. **[Web app settings]** ブレードの上部にある **[保存]** ボタンをクリックします。

	![構成設定を保存する][save-button]

### Azure PowerShell (Windows)。

1. Windows PowerShell を開きます。
2. `Set-AzureWebsite -PhpVersion [5.4 | 5.5 | 5.6] -Name <site-name>` を入力してから、Enter キーを押します。
3. PHP バージョンが設定されました。

	![Azure PowerShell での PHP バージョンを設定します。][SETPHPVERPS]
4. 「`Get-AzureWebiste -Name <site-name>`」と入力してから Enter キーを押して、これらの設定を確認することができます。

	![Azure PowerShell での PHP バージョンを検証します。][GETPHPVERPS]

### Azure コマンド ライン インターフェイス (Mac、Linux、Windows)

Azure コマンド ライン インターフェイスを使用するには、**Node.js** をコンピューターにインストールする必要があります。

1. ターミナルを開きます。
2. `azure site set --php-version [5.4 | 5.5] [site-name]` を入力してから、Enter キーを押します。
3. PHP バージョンが設定されました。

	![Azure コマンド ライン インターフェイスを使用して PHP のバージョンを設定します。][SETPHPVERCLI]
4. 「`azure site show [site-name]`」と入力してから Enter キーを押して、これらの設定を確認することができます。

	![Azure コマンド ライン インターフェイスを使用して PHP のバージョンを検証します。][GETPHPVERCLI]

## 方法: ビルトインの PHP 構成を変更する

次の手順に従うと、いずれのビルトイン PHP ランタイムについても、任意の構成オプションを変更できます(php.ini ディレクティブについては、[php.ini ディレクティブの一覧]を参照してください)。

### PHP\_INI\_USER、PHP\_INI\_PERDIR、PHP\_INI\_ALL 構成設定の変更

1. [.user.ini] ファイルをルート ディレクトリに追加します。
2. `php.ini` ファイルで使用するものと同じ構文を使用して、構成設定を `.user.ini` ファイルに追加します。たとえば、`display_errors` 設定をオンにして `upload_max_filesize` を 10 M に設定する場合は、`.user.ini` ファイルに次のテキストを含めます。

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. Web アプリをデプロイします。
4. Web アプリを再起動します。(再起動する必要があるのは、`.user.ini` ファイルが PHP によって読み取られる頻度が、`user_ini.cache_ttl` 設定によって制御されるためです。この設定はシステム レベルの設定であり、既定では 300 秒 (5 分) です。Web アプリを再起動すると、PHP により、`.user.ini` ファイル内の新しい設定が強制的に読み取られます。)

`.user.ini` ファイルを使用する代わりに、スクリプト内で [ini\_set()] 関数を使用して、システム レベルのディレクティブではない構成オプションを設定することもできます。

### PHP\_INI\_SYSTEM 構成設定の変更

1. アプリの設定 (キー `PHP_INI_SCAN_DIR`、値 `d:\home\site\ini`) を Web アプリに追加する
2. Kudu コンソール (http://&lt;site-name&gt;.scm.azurewebsite.net) を使用して `d:\home\site\ini` ディレクトリに `settings.ini` ファイルを作成します。
3. 'php.ini' ファイルで使用するものと同じ構文を使用して、構成設定を `settings.ini` ファイルに追加します。たとえば、`curl.cainfo` 設定を `*.crt` ファイルにポイントし、'wincache.maxfilesize' 設定を 512 K に設定すると、`settings.ini` ファイルには次のテキストが含まれます。

		; Example Settings
		curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
		wincache.maxfilesize=512
4. 変更内容を読み込むには、Web アプリを再起動します。

## 方法: 既定の PHP ランタイムで拡張機能を有効にする
前のセクションにも示されているように、既定の PHP バージョン、既定の構成、および有効な拡張機能を確認するには、[phpinfo()] を呼び出すスクリプトをデプロイします。追加の拡張機能を有効にするには、次の手順に従います。

### Ini 設定を使用して構成します。

1. `d:\home\site` ディレクトリを `ext` ディレクトリに追加します。
2. `ext` ディレクトリに、`.dll` 拡張ファイル (`php_mongo.dll`、`php_xdebug.dll` など) を配置します。拡張機能は、PHP の既定バージョン (この文書の作成時点では PHP 5.4) との互換性があり、VC9 および非スレッドセーフ (nts) 互換であることを確認してください。
3. アプリの設定 (キー `PHP_INI_SCAN_DIR`、値 `d:\home\site\ini`) を Web アプリに追加する
4. `ini` ファイルを `extensions.ini` と呼ばれる `d:\home\site\ini` に作成します。
5. 'php.ini' ファイルで使用するものと同じ構文を使用して、構成設定を `extensions.ini` ファイルに追加します。たとえば、MongoDB や XDebug 拡張機能を有効にする場合、 `extensions.ini` ファイルには次のテキストが含まれます。

		; Enable Extensions
		extension=d:\home\site\ext\php_mongo.dll
		zend_extension=d:\home\site\ext\php_xdebug.dll
6. 変更内容を読み込むには、Web アプリを再起動します。

### アプリ設定の構成

1. ディレクトリを `bin` ディレクトリに追加します。
2. `bin` ディレクトリに、`.dll` 拡張ファイル (`php_mongo.dll` など) を配置します。拡張機能は、PHP の既定バージョン (この文書の作成時点では PHP 5.4) との互換性があり、VC9 および非スレッドセーフ (nts) 互換であることを確認してください。
3. Web アプリをデプロイします。
4. Azure ポータルで Web アプリに移動し、**[設定]** ボタンをクリックします。

	![Web アプリ設定][settings-button]

5. **[設定]** ブレードで、**[アプリケーションの設定]** を選択し、**[アプリの設定]** セクションまでスクロールします。
6. **[アプリの設定]** セクションで、**PHP\_EXTENSIONS** キーを作成します。このキーの値は、Web サイト ルート (**bin\\your-ext-file**) への相対パスになります。

	![[アプリケーション設定] で拡張機能を有効にする][php-extensions]

7. **[Web app settings]** ブレードの上部にある **[保存]** ボタンをクリックします。

	![構成設定を保存する][save-button]

Zend 拡張機能も、**PHP\_ZENDEXTENSIONS** キーを使用することによってサポートされます。複数の拡張機能を有効にするには、複数の `.dll` ファイルをコンマで区切って指定します。


## 方法: カスタムの PHP ランタイムを使用する
App Service Web Apps では、既定の PHP ランタイムを使用する代わりに、指定された PHP ランタイムを使用して PHP スクリプトを実行することができます。指定するランタイムは、同様に指定する `php.ini` ファイルによって構成できます。カスタムの PHP ランタイムを Web Apps で使用するには、次の操作を行います。

1. 非スレッドセーフおよび VC9 または VC11 互換バージョンの Windows 用 PHP を入手します。Windows 用 PHP の最近のリリースは、[http://windows.php.net/download/] にあります。以前のリリースは、アーカイブ [http://windows.php.net/downloads/releases/archives/] にあります。
2. 使用するランタイム用に `php.ini` ファイルを変更します。システム レベルのみのディレクティブである構成設定は、Web Apps では無視されます(システム レベルのみのディレクティブについては、[php.ini ディレクティブの一覧]を参照してください)。
3. また、PHP ランタイムに拡張機能を追加して、これらを `php.ini` ファイル内で有効にすることもできます。
4. `bin` ディレクトリをルート ディレクトリに追加し、その中に、PHP ランタイムが含まれているディレクトリ (`bin\php` など) を配置します。
5. Web アプリをデプロイします。
4. Azure ポータルで Web アプリに移動し、**[設定]** ボタンをクリックします。

	![Web アプリ設定][settings-button]

7. **[設定]** ブレードで、**[アプリケーションの設定]** を選択し、**[ハンドラー マッピング]** セクションまでスクロールします。[拡張] フィールドに `*.php` を追加し、`php-cgi.exe` 実行可能ファイルのパスを追加します。アプリケーションのルートにある `bin` ディレクトリに PHP ランタイムを配置した場合、パスは `D:\home\site\wwwroot\bin\php\php-cgi.exe` になります。

	![[ハンドラー マッピング] でハンドラーを指定する][handler-mappings]

8. **[Web app settings]** ブレードの上部にある **[保存]** ボタンをクリックします。

	![構成設定を保存する][save-button]

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、「[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。

[無料評価版]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[php.ini ディレクティブの一覧]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini\_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
 

<!---HONumber=August15_HO6-->