<properties title="How to Configure PHP in Azure Websites" pageTitle="Azure Websites での PHP の構成方法" metaKeywords="Azure, Azure Web Sites, configuration, PHP" description="既定の PHP インストールを構成し、カスタム PHP インストールを Azure Websites で追加する方法について説明します。" services="Web Sites" documentationCenter="PHP" authors="tomfitz" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/18/2014" ms.author="tomfitz" />

#Azure Websites での PHP の構成方法

このガイドでは、ビルトインの PHP ランタイムを Azure Websites で構成し、カスタムの PHP ランタイムを指定して、Azure Websites で拡張機能を有効にする方法を示します。Azure Websites を使用するには、[無料評価版]にサインアップしてください。このガイドを最大限に活用するには、まず Azure Websites で PHP サイトを作成する必要があります (「[PHP デベロッパー センター - チュートリアル]」を参照してください)。Azure Websites でのサイトの構成の一般情報については、「[Web サイトの構成方法]」を参照してください。

##目次

* [Azure Websites とは](#WhatIs)
* [方法: 既定の PHP 構成を変更する](#ChangeBuiltInPHP)
* [方法: ビルトインの PHP ランタイムで拡張機能を有効にする](#EnableExtDefaultPHP)
* [方法: カスタムの PHP ランタイムを使用する](#UseCustomPHP)
* [次のステップ](#NextSteps)

<h2><a name="WhatIs"></a>Azure Websites とは</h2>
Azure Websites を使用すると、スケーラビリティに優れた Web サイトを Azure に構築できます。小規模から開始し、トラフィックの拡大に合わせて規模を調整できる、拡張性の高いクラウド環境に、サイトをすばやく簡単にデプロイできます。Azure Websites では、好みの言語とオープン ソース アプリケーションを使用でき、Git、FTP、および TFS によるデプロイがサポートされます。MySQL、SQL データベース、Caching、CDN、ストレージなど、他のサービスを簡単に統合できます。

<h2><a name="ChangeBuiltInPHP"></a>方法:ビルトインの PHP 構成を変更する</h2>
既定では、Azure Website を作成すると PHP 5.4 がインストールされ、直ちに使用できる状態になります。使用可能なリリース リビジョン、既定の構成、および有効な拡張機能を確認するには、[phpinfo()] 関数を呼び出すスクリプトをデプロイします。

PHP 5.5 も使用できますが、既定では有効になっていません。有効にするには、次の手順に従います。

1. Azure ポータルで、Web サイトのダッシュボードに移動し、**[構成]** をクリックします。

	![Configure tab on Web Sites dashboard][configure]

1. PHP 5.5 をクリックします。

	![Select PHP version][select-php-version]

1. ページの下部にある **[保存]** をクリックします。

	![Save configuration settings][save-button]

次の手順に従うと、いずれのビルトイン PHP ランタイムについても、システム レベルのみのディレクティブではない任意の構成オプションを変更できます(システム レベルのみのディレクティブについては、[php.ini ディレクティブの一覧]を参照してください)。

1. [.user.ini] ファイルをルート ディレクトリに追加します。
1. `php.ini` ファイルで使用するものと同じ構文を使用して、構成設定を `.user.ini` ファイルに追加します。たとえば、`display_errors` 設定をオンにして `upload_max_filesize` を 10 MB に設定する場合は、`.user.ini` ファイルに次のテキストを含めます。

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

1. アプリケーションをデプロイします。
1. Web サイトを再起動します(再起動する必要があるのは、`.user.ini` ファイルが PHP によって読み取られる頻度が、`user_ini.cache_ttl` 設定によって制御されるためです。この設定はシステム レベルの設定であり、既定では 300 秒 (5 分) です。サイトを再起動すると、PHP により、`.user.ini` ファイル内の新しい設定が読み取られます)。

`.user.ini` ファイルを使用する代わりに、スクリプト内で [ini_set()] 関数を使用して、システム レベルのディレクティブではない構成オプションを設定することもできます。

<h2><a name="EnableExtDefaultPHP"></a>方法:既定の PHP ランタイムで拡張機能を有効にする</h2>
前のセクションにも示されているように、既定の PHP バージョン、既定の構成、および有効な拡張機能を確認するには、[phpinfo()] を呼び出すスクリプトをデプロイします。追加の拡張機能を有効にするには、次の手順に従います。

1. `bin` ディレクトリをルート ディレクトリに追加します。
1. `bin` ディレクトリに、`.dll` 拡張ファイル (`php_mongo.dll` など) を配置します。拡張機能は、PHP の既定バージョン (この文書の作成時点では PHP 5.4) との互換性があり、VC9 および非スレッドセーフ (nts) 互換であることを確認してください。
1. アプリケーションをデプロイします。
1. Azure ポータルで、サイトのダッシュボードに移動し、**[構成]** をクリックします。

	![Configure tab on Web Sites dashboard][configure]

1. **[アプリ設定]** で、**PHP_EXTENSIONS** というキーと **bin\your-ext-file** という値を作成します。複数の拡張機能を有効にするには、複数の `.dll` ファイルをコンマで区切って指定します。

	![Enable extension in app settings][app-settings]

1. ページの下部にある **[保存]** をクリックします。

	![Save configuration settings][save-button]

<h2><a name="UseCustomPHP"></a>方法:カスタムの PHP ランタイムを使用する</h2>
Azure Websites では、既定の PHP ランタイムを使用する代わりに、指定された PHP ランタイムを使用して PHP スクリプトを実行することができます。指定するランタイムは、同様に指定する 'php.ini' ファイルによって構成できます。カスタムの PHP ランタイムを Azure Websites で使用するには、次の操作を行います。

1. 非スレッドセーフおよび VC9 互換バージョンの Windows 用 PHP を入手します。Windows 用 PHP の最近のリリースは、[http://windows.php.net/download/] から入手できます。以前のリリースは、アーカイブ ([http://windows.php.net/downloads/releases/archives/]) から入手できます。
1. 使用するランタイム用に `php.ini` ファイルを変更します。システム レベルのみのディレクティブである構成設定は、Azure Websites では無視されます(システム レベルのみのディレクティブについては、[php.ini ディレクティブの一覧]を参照してください)。
1. また、PHP ランタイムに拡張機能を追加して、これらを `php.ini` ファイル内で有効にすることもできます。
1. `bin` ディレクトリをルート ディレクトリに追加し、その中に、PHP ランタイムが含まれているディレクトリ (`bin\php` など) を配置します。
1. アプリケーションをデプロイします。
1. Azure ポータルで、サイトのダッシュボードに移動し、**[構成]** をクリックします。

	![Configure tab on Web Sites dashboard][configure]

1. **[ハンドラー マッピング]** で、[拡張] に '*.php' を追加し、'php-cgi.exe' 実行可能ファイルのパスを追加します。アプリケーションのルートにある `bin` ディレクトリに PHP ランタイムを配置した場合、パスは `D:\home\site\wwwroot\bin\php\php-cgi.exe` になります。

	![Specify handler in hander mappings][handler-mappings]

1. ページの下部にある **[保存]** をクリックします。

	![Save configuration settings][save-button]

<h2><a name="NextSteps"></a>次のステップ</h2>
これで、Azure Websites で PHP を構成する方法を学習できました。その他の操作については、次のリンク先を参照してください。

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

<!--HONumber=35.1-->
