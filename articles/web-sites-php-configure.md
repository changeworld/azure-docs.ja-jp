<properties title="How to Configure PHP in Azure Websites" pageTitle="How to Configure PHP in Azure Websites" metaKeywords="Azure, Azure Web Sites, configuration, PHP" description="Learn how to configure the default PHP installation or add a custom PHP installation in Azure Websites." services="Web Sites" documentationCenter="PHP" authors="cephalin" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Azure Websites での PHP の構成方法

このガイドでは、ビルトインの PHP ランタイムを Azure Websites で構成し、カスタムの PHP ランタイムを指定して、Azure Websites で拡張機能を有効にする方法を示します。Azure Websites を使用するには、[無料評価版][無料評価版]にサインアップしてください。このガイドを最大限に活用するには、まず Azure Websites で PHP サイトを作成する必要があります (「[PHP デベロッパー センター - チュートリアル][PHP デベロッパー センター - チュートリアル]」を参照してください)。Azure Websites でのサイトの構成の一般情報については、「[Web サイトの構成方法][Web サイトの構成方法]」を参照してください。

## 目次

-   [Azure Websites とは][Azure Websites とは]
-   [方法: 既定の PHP 構成を変更する][方法: 既定の PHP 構成を変更する]
-   [方法: ビルトインの PHP ランタイムで拡張機能を有効にする][方法: ビルトインの PHP ランタイムで拡張機能を有効にする]
-   [方法: カスタムの PHP ランタイムを使用する][方法: カスタムの PHP ランタイムを使用する]
-   [次のステップ][次のステップ]

## <a name="WhatIs"></a>Azure Websites とは

Azure Websites を使用すると、スケーラビリティに優れた Web サイトを Azure に構築できます。小規模から開始し、トラフィックの拡大に合わせて規模を調整できる、拡張性の高いクラウド環境に、サイトをすばやく簡単にデプロイできます。Azure Websites では、好みの言語とオープン ソース アプリケーションを使用でき、Git、FTP、および TFS によるデプロイがサポートされます。MySQL、SQL データベース、Caching、CDN、ストレージなど、他のサービスを簡単に統合できます。

## <a name="ChangeBuiltInPHP"></a>方法: ビルトインの PHP 構成を変更する

既定では、Azure Website を作成すると PHP 5.4 がインストールされ、直ちに使用できる状態になります。使用可能なリリース リビジョン、既定の構成、および有効な拡張機能を確認するには、[phpinfo()][phpinfo()] 関数を呼び出すスクリプトをデプロイします。

PHP 5.5 も使用できますが、既定では有効になっていません。有効にするには、次の手順に従います。

1.  Azure ポータルで、Web サイトのダッシュボードに移動し、**[構成]** をクリックします。

    ![Web サイト ダッシュボードの 構成 タブ][Web サイト ダッシュボードの 構成 タブ]

2.  PHP 5.5 をクリックします。

    ![PHP バージョンを選択する][PHP バージョンを選択する]

3.  ページの下部にある **[保存]** をクリックします。

    ![構成設定を保存する][構成設定を保存する]

次の手順に従うと、いずれのビルトイン PHP ランタイムについても、システム レベルのみのディレクティブではない任意の構成オプションを変更できます (システム レベルのみのディレクティブについては、[php.ini ディレクティブの一覧][php.ini ディレクティブの一覧]を参照してください)。

1.  [.user.ini][.user.ini] ファイルをルート ディレクトリに追加します。
2.  `.user.ini` ファイルで使用するものと同じ構文を使用して、構成設定を `php.ini` ファイルに追加します。たとえば、`display_errors` 設定をオンにして `upload_max_filesize` を 10 M に設定する場合は、`.user.ini` ファイルに次のテキストを含めます。

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

3.  アプリケーションをデプロイします。
4.  Web サイトを再起動します (再起動する必要があるのは、`.user.ini` ファイルが PHP によって読み取られる頻度が、`user_ini.cache_ttl` 設定によって制御されるためです。この設定はシステム レベルの設定であり、既定では 300 秒 (5 分) です。サイトを再起動すると、PHP により、`.user.ini` ファイル内の新しい設定が読み取られます)。

`.user.ini` ファイルを使用する代わりに、スクリプト内で [ini\_set()][ini\_set()] 関数を使用して、システム レベルのディレクティブではない構成オプションを設定することもできます。

## <a name="EnableExtDefaultPHP"></a>方法: 既定の PHP ランタイムで拡張機能を有効にする

前のセクションにも示されているように、既定の PHP バージョン、既定の構成、および有効な拡張機能を確認するには、[phpinfo()][phpinfo()] を呼び出すスクリプトをデプロイします。追加の拡張機能を有効にするには、次の手順に従います。

1.  `bin` ディレクトリをルート ディレクトリに追加します。
2.  `bin` ディレクトリに、`.dll` 拡張ファイル (`php_mongo.dll` など) を配置します。拡張機能は、PHP の既定バージョン (この文書の作成時点では PHP 5.4) との互換性があり、VC9 および非スレッドセーフ (nts) 互換であることを確認してください。
3.  アプリケーションをデプロイします。
4.  Azure ポータルで、サイトのダッシュボードに移動し、**[構成]** をクリックします。

    ![Web サイト ダッシュボードの 構成 タブ][Web サイト ダッシュボードの 構成 タブ]

5.  **[アプリ設定]** で、**PHP\_EXTENSIONS** というキーと **bin\\your-ext-file** という値を作成します。複数の拡張機能を有効にするには、複数の `.dll` ファイルをコンマで区切って指定します。

    ![アプリケーション設定 で拡張機能を有効にする][アプリケーション設定 で拡張機能を有効にする]

6.  ページの下部にある **[保存]** をクリックします。

    ![構成設定を保存する][構成設定を保存する]

## <a name="UseCustomPHP"></a>方法: カスタムの PHP ランタイムを使用する

Azure Websites では、既定の PHP ランタイムを使用する代わりに、指定された PHP ランタイムを使用して PHP スクリプトを実行することができます。指定するランタイムは、同様に指定する `php.ini` ファイルによって構成できます。カスタムの PHP ランタイムを Azure Websites で使用するには、次の操作を行います。

1.  非スレッドセーフおよび VC9 互換バージョンの Windows 用 PHP を入手します。Windows 用 PHP の最近のリリースは、[][]<http://windows.php.net/download/></a> から入手できます。以前のリリースは、アーカイブ ([][1]<http://windows.php.net/downloads/releases/archives/></a>) から入手できます。
2.  使用するランタイム用に `php.ini` ファイルを変更します。システム レベルのみのディレクティブである構成設定は、Azure Websites では無視されます (システム レベルのみのディレクティブについては、[php.ini ディレクティブの一覧][php.ini ディレクティブの一覧]を参照してください)。
3.  また、PHP ランタイムに拡張機能を追加して、これらを `php.ini` ファイル内で有効にすることもできます。
4.  `bin` ディレクトリをルート ディレクトリに追加し、その中に、PHP ランタイムが含まれているディレクトリ (`bin\php` など) を配置します。
5.  アプリケーションをデプロイします。
6.  Azure ポータルで、サイトのダッシュボードに移動し、**[構成]** をクリックします。

    ![Web サイト ダッシュボードの 構成 タブ][Web サイト ダッシュボードの 構成 タブ]

7.  **[ハンドラー マッピング]** で、[拡張] に `*.php` を追加し、`php-cgi.exe` 実行可能ファイルのパスを追加します。アプリケーションのルートにある `bin` ディレクトリに PHP ランタイムを配置した場合、パスは `D:\home\site\wwwroot\bin\php\php-cgi.exe` になります。

    ![ハンドラー マッピング でハンドラーを指定する][ハンドラー マッピング でハンドラーを指定する]

8.  ページの下部にある **[保存]** をクリックします。

    ![構成設定を保存する][構成設定を保存する]

## <a name="NextSteps"></a>次のステップ

これで、Azure Websites で PHP を構成する方法を学習できました。その他の操作については、次のリンク先を参照してください。

-   [Azure での Web サイトの作成、監視、および規模変更][Azure での Web サイトの作成、監視、および規模変更]
-   [Azure SDK for PHP をダウンロードする][Azure SDK for PHP をダウンロードする]

  [無料評価版]: https://www.windowsazure.com/ja-jp/pricing/free-trial/
  [PHP デベロッパー センター - チュートリアル]: https://www.windowsazure.com/ja-jp/develop/php/tutorials/
  [Web サイトの構成方法]: https://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-configure-websites/
  [Azure Websites とは]: #WhatIs
  [方法: 既定の PHP 構成を変更する]: #ChangeBuiltInPHP
  [方法: ビルトインの PHP ランタイムで拡張機能を有効にする]: #EnableExtDefaultPHP
  [方法: カスタムの PHP ランタイムを使用する]: #UseCustomPHP
  [次のステップ]: #NextSteps
  [phpinfo()]: http://php.net/manual/en/function.phpinfo.php
  [Web サイト ダッシュボードの 構成 タブ]: ./media/web-sites-php-configure/configure.png
  [PHP バージョンを選択する]: ./media/web-sites-php-configure/select-php-version.png
  [構成設定を保存する]: ./media/web-sites-php-configure/save-button.png
  [php.ini ディレクティブの一覧]: http://www.php.net/manual/en/ini.list.php
  [.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
  [ini\_set()]: http://www.php.net/manual/en/function.ini-set.php
  [アプリケーション設定 で拡張機能を有効にする]: ./media/web-sites-php-configure/app-settings.png
  []: http://windows.php.net/download/
  [1]: http://windows.php.net/downloads/releases/archives/
  [ハンドラー マッピング でハンドラーを指定する]: ./media/web-sites-php-configure/handler-mappings.png
  [Azure での Web サイトの作成、監視、および規模変更]: http://www.windowsazure.com/ja-jp/manage/services/web-sites/
  [Azure SDK for PHP をダウンロードする]: http://www.windowsazure.com/ja-jp/develop/php/common-tasks/download-php-sdk/
