<properties title="Azure の Web サイトでの PHP の構成方法" pageTitle="Azure の Web サイトでの PHP の構成方法" metaKeywords="Azure, Azure の Web サイト, 構成, PHP" description="Azure の Web サイトで、PHP の既定のインストールを構成する方法、または PHP のカスタムのインストールを追加する方法について説明します。" services="Web Sites" documentationCenter="PHP" />

#Azure の Web サイトでの PHP の構成方法

このガイドでは、ビルトインの PHP ランタイムを Azure の Web サイトで構成し、カスタムの PHP ランタイムを指定して、Azure の Web サイトで拡張機能を有効にする方法を示します。Azure の Web サイトを使用するには、[無料評価版]にサインアップしてください。このガイドを最大限に活用するには、まず Azure の Web サイトで PHP サイトを作成する必要があります (「[PHP デベロッパー センター - チュートリアル]」を参照してください)。Azure の Web サイトでのサイトの構成の一般情報については、「[Web サイトの構成方法]」を参照してください。

##目次

* [Azure の Web サイトとは](#WhatIs)
* [方法: 既定の PHP 構成を変更する](#ChangeBuiltInPHP)
* [方法: ビルトインの PHP ランタイムで拡張機能を有効にする](#EnableExtDefaultPHP)
* [方法: カスタムの PHP ランタイムを使用する](#UseCustomPHP)
* [次のステップ](#NextSteps)

<h2><a name="WhatIs"></a>Azure の Web サイトとは</h2>
Azure の Web サイトを使用すると、スケーラビリティに優れた Web サイトを Azure に構築できます。小規模から開始し、トラフィックの拡大に合わせて規模を調整できる、拡張性の高いクラウド環境に、サイトをすばやく簡単にデプロイできます。Azure の Web サイトでは、お好みの言語とオープン ソース アプリケーションを使用でき、Git、FTP、および TFS によるデプロイがサポートされます。MySQL、SQL データベース、Caching、CDN、ストレージなど、他のサービスを簡単に統合できます。

<h2><a name="ChangeBuiltInPHP"></a>方法: ビルトインの PHP 構成を変更する</h2>
既定では、Azure の Web サイトを作成すると PHP 5.4 がインストールされ、直ちに使用できる状態になります。使用可能なリリース リビジョン、既定の構成、および有効な拡張機能を確認するには、[phpinfo()] 関数を呼び出すスクリプトを展開します。

PHP 5.5 も使用できますが、既定では有効になっていません。有効にするには、次の手順に従います。

1. Azure ポータルで、Web サイトのダッシュボードに移動し、**[構成]** をクリックします。

	![Web サイト ダッシュボードの [構成] タブ][configure]

1. PHP 5.5 をクリックします。

	![PHP バージョンを選択する][select-php-version]

1. ページの下部にある **[保存]** をクリックします。

	![構成設定を保存する][save-button]

次の手順に従うと、いずれのビルトイン PHP ランタイムについても、システム レベルのみのディレクティブではない任意の構成オプションを変更できます (システム レベルのみのディレクティブについては、[php.ini ディレクティブの一覧]を参照してください)。

1. [.user.ini] ファイルをルート ディレクトリに追加します。
1. `php.ini` ファイルで使用するものと同じ構文を使用して、構成設定を `.user.ini` ファイルに追加します。たとえば、`display_errors` 設定をオンにして `upload_max_filesize` を 10 MB に設定する場合は、`.user.ini` ファイルに次のテキストを含めます。

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

1. アプリケーションを展開します。
1. Web サイトを再起動します (再起動する必要があるのは、`user.ini` ファイルが PHP によって読み取られる頻度が、`user_ini.cache_ttl` 設定によって制御されるためです。この設定はシステム レベルの設定であり、既定では 300 秒 (5 分) です。サイトを再起動すると、PHP により、`.user.ini` ファイル内の新しい設定が読み取られます)。

`.user.ini` ファイルを使用する代わりに、スクリプト内で [ini_set()] 関数を使用して、システム レベルのディレクティブではない構成オプションを設定することもできます。

<h2><a name="EnableExtDefaultPHP"></a>方法: 既定の PHP ランタイムで拡張機能を有効にする</h2>
前のセクションにも示されているように、既定の PHP バージョン、既定の構成、および有効な拡張機能を確認するには、[phpinfo()] を呼び出すスクリプトを展開します。追加の拡張機能を有効にするには、次の手順に従います。

1. `bin` ディレクトリをルート ディレクトリに追加します。
1. この `bin` ディレクトリに、`.dll` 拡張ファイル (`php_mongo.dll` など) を配置します。拡張機能は、PHP の既定バージョン (この文書の作成時点では PHP 5.4) との互換性があり、VC9 および非スレッドセーフ (nts) 互換であることを確認してください。
1. アプリケーションを展開します。
1. Azure ポータルで、サイトのダッシュボードに移動し、**[構成]** をクリックします。

	![Web サイト ダッシュボードの [構成] タブ][configure]

1. **[アプリ設定]** で、**PHP_EXTENSIONS** というキーと **bin\your-ext-file** という値を作成します。複数の拡張機能を有効にするには、複数の `.dll` ファイルをコンマで区切って指定します。

	![[アプリケーション設定] で拡張機能を有効にする][app-settings]

1. ページの下部にある **[保存]** をクリックします。

	![構成設定を保存する][save-button]

<h2><a name="UseCustomPHP"></a>方法: カスタムの PHP ランタイムを使用する</h2>
Azure の Web サイトでは、既定の PHP ランタイムを使用する代わりに、指定された PHP ランタイムを使用して PHP スクリプトを実行することができます。指定するランタイムは、同様に指定する `php.ini` ファイルによって構成できます。カスタムの PHP ランタイムを Azure の Web サイトで使用するには、次の操作を行います。

1. 非スレッドセーフおよび VC9 互換バージョンの Windows 用 PHP を入手します。Windows 用 PHP の最新のリリースは、[http://windows.php.net/download/] にあります。以前のリリースは、アーカイブとして [http://windows.php.net/downloads/releases/archives/] にあります。
1. 使用するランタイム用に `php.ini` ファイルを変更します。システム レベルのみのディレクティブである構成設定は、Azure の Web サイトでは無視されます (システム レベルのみのディレクティブについては、[php.ini ディレクティブの一覧]を参照してください)。
1. また、PHP ランタイムに拡張機能を追加して、これらを `php.ini` ファイル内で有効にすることもできます。
1. `bin` ディレクトリをルート ディレクトリに追加し、その中に、PHP ランタイムが含まれているディレクトリ (`bin\php` など) を配置します。
1. アプリケーションを展開します。
1. Azure ポータルで、サイトのダッシュボードに移動し、**[構成]** をクリックします。

	![Web サイト ダッシュボードの [構成] タブ][configure]

1. **[ハンドラー マッピング]** で、[拡張] に `*.php` を追加し、`php-cgi.exe` 実行可能ファイルのパスを追加します。アプリケーションのルートにある `bin` ディレクトリに PHP ランタイムを配置した場合、パスは `D:\home\site\wwwroot\bin\php\php-cgi.exe` になります。

	![[ハンドラー マッピング] でハンドラーを指定する][handler-mappings]

1. ページの下部にある **[保存]** をクリックします。

	![構成設定を保存する][save-button]

<h2><a name="NextSteps"></a>次のステップ</h2>
これで、Azure の Web サイトで PHP を構成する方法を学習できました。その他の操作については、次のリンク先を参照してください。

- [Azure での Web サイトの作成、監視、および規模変更]
- [Azure SDK for PHP をダウンロードする]


[無料評価版]: https://www.windowsazure.com/ja-jp/pricing/free-trial/
[PHP デベロッパー センター - チュートリアル]: https://www.windowsazure.com/ja-jp/develop/php/tutorials/
[Web サイトの構成方法]: https://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-configure-websites/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[php.ini ディレクティブの一覧]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[configure]: ./media/web-sites-php-configure/configure.png
[app-settings]: ./media/web-sites-php-configure/app-settings.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[Azure での Web サイトの作成、監視、および規模変更]: http://www.windowsazure.com/ja-jp/manage/services/web-sites/
[Azure SDK for PHP をダウンロードする]: http://www.windowsazure.com/ja-jp/develop/php/common-tasks/download-php-sdk/

