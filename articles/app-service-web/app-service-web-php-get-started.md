<properties
	pageTitle="Azure への PHP Web アプリの作成、構成、デプロイ"
	description="Azure App Service で動作する PHP (Laravel) Web アプリの作成方法をわかりやすく説明しています。ご利用の PHP フレームワークの要件を満たすように Azure App Service を構成する方法について説明します。"
	services="app-service\web"
	documentationCenter="php"
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="mysql"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="06/03/2016" 
	ms.author="cephalin"/>

# Azure への PHP Web アプリの作成、構成、デプロイ

[AZURE.INCLUDE [タブ](../../includes/app-service-web-get-started-nav-tabs.md)]

このチュートリアルでは、Azure 用に PHP Web アプリを作成、構成、デプロイする方法のほか、その PHP Web アプリの要件を満たすように Azure App Service を構成する方法について説明します。このチュートリアルの最後には、実用的な [Laravel](https://www.laravel.com/) Web アプリが完成し、[Azure App Service](../app-service/app-service-value-prop-what-is.md) で実際に動作するようすを確認できます。

PHP 開発者の方は、お気に入りの PHP フレームワークを Azure に持ち込むことができます。このチュートリアルでは、単にアプリの具体例として Laravel を使用しています。学習内容:

- Git を使用してデプロイする
- PHP バージョンを設定する
- ルート アプリケーション ディレクトリに存在しないスタート ファイルを使用する
- 環境固有の変数にアクセスする
- Azure でアプリを更新する

ここで覚えたことは、Azure にデプロイする他の PHP Web アプリにも応用することができます。

## 前提条件

- [PHP 5.6.x](http://php.net/downloads.php) をインストールします (PHP 7 のサポートはベータ段階)。
- [Composer](https://getcomposer.org/download/) をインストールします。
- [Azure CLI](../xplat-cli-install.md) をインストールします。
- [Git](http://www.git-scm.com/downloads) をインストールします。
- Microsoft Azure アカウントを取得します。アカウントを持っていない場合は、[無料試用版にサインアップする](/pricing/free-trial/?WT.mc_id=A261C142F)か、または [Visual Studio サブスクライバー特典を有効にする](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)ことができます。

>[AZURE.NOTE] Web アプリの動作を確認してください。今すぐ [App Service を試用](http://go.microsoft.com/fwlink/?LinkId=523751)して、有効期間が短いスターター アプリを作成してみてください。このサービスの利用にあたり、クレジット カードや契約は必要ありません。

## 開発コンピューター上で PHP (Laravel) アプリを作成する

1. 新しい Windows コマンド プロンプト、PowerShell ウィンドウ、Linux のシェル、または OS X ターミナルを開きます。必要なツールが適切にコンピューターにインストールされていることを次のコマンドを実行して確認します。 

        php --version
        composer --version
        azure --version
        git --version

    ![Test tools installation before creating your PHP (Laravel) app for Azure](./media/app-service-web-php-get-started/test-tools.png)

    ツールをインストールしていない場合は、「[前提条件](#Prerequisites)」のダウンロード リンクを参照してください。
    
2. Laravel をインストールします。

        composer global require "laravel/installer

3. `CD` で作業ディレクトリに移動し、新しい Laravel アプリケーションを作成します。

        cd <working_directory>
        laravel new <app_name>

4. 新しく作成した `<app_name>` ディレクトリに `CD` で移動し、アプリをテストします。

        cd <app_name>
        php artisan serve
        
    ブラウザーで http://localhost:8000 にアクセスすると、Laravel のスプラッシュ スクリーンが表示されます。
    
    ![Test your PHP (Laravel) app locally before deploying it to Azure](./media/app-service-web-php-get-started/laravel-splash-screen.png)
    
ここまでは、Laravel の標準的なワークフローです。<a href="https://laravel.com/docs/5.2" rel="nofollow">Laravel について</a>は本題から逸れるので、これぐらいにして、次に進みましょう。

## Azure Web アプリの作成と Git デプロイの設定

>[AZURE.NOTE] FTP で デプロイしたいという方のために、 [FTP チュートリアル](web-sites-php-mysql-deploy-use-ftp.md)が用意されています。

Azure App Service への Web アプリの作成と、Git デプロイに必要なセットアップは、Azure CLI から 1 行のコマンドで実行できます。以下に示したのは、そのための手順です。

3. 次のようにして、Azure にログインします。

        azure login
    
    ヘルプ メッセージに従って、ログイン プロセスを続行します。
    
    ![Log in to Azure to deploy your PHP (Laravel) app to Azure](./media/app-service-web-php-get-started/log-in-to-azure-cli.png)

4. Git デプロイで Azure Web アプリを作成するためのコマンドを実行します。メッセージが表示されたら、必要なリージョンの数を指定します。

        azure site create --git <app_name>
    
    ![Create the Azure resource for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/create-site-cli.png)
    
    >[AZURE.NOTE] Azure サブスクリプションのデプロイ資格情報がまだ設定されていない場合は、それらを作成するように求められます。これらの資格情報 (Azure アカウント資格情報ではありません) は App Service で Git のデプロイと FTP のログインのみに使用されます。
    
    このコマンドは、(`git init` で) 現在のディレクトリに新しい Git リポジトリを作成し、(`git remote add` で) Git リモートとして Azure 内のリポジトリに接続します。

<a name="configure"/>
## Azure Web アプリの構成

作成した Laravel アプリが Azure で正しく動作するためには、いくつかのことに注意する必要があります。これと同様の作業は、すべての PHP フレームワークで必要となります。

- PHP 5.5.9 以降を構成します。サーバーの全要件については、[最新の Laravel 5.2 サーバーの要件](https://laravel.com/docs/5.2#server-requirements)を参照してください。その他の要件は拡張機能に関するものであり、Azure の PHP 環境であらかじめ使用できる状態になっています。 
- アプリに必要な環境変数を設定します。Laravel では、`.env` ファイルを使用して簡単に環境変数を設定できます。ただし、このファイルをソース管理下に置くことは避けてください ([Laravel の環境構成](https://laravel.com/docs/5.2/configuration#environment-configuration)に関するページを参照)。Azure Web アプリに対するアプリ設定は自分で行うことになります。
- Laravel アプリのエントリ ポイント (`public/index.php`) が最初に読み込まれることを確認します。[Laravel のライフサイクルの概要](https://laravel.com/docs/5.2/lifecycle#lifecycle-overview)に関するページを参照してください。つまり、Web アプリのルート URL が `public` ディレクトリを指すように設定する必要があります。
- composer.json があるので、Azure で Composer 拡張機能を有効にします。これにより、`git push` でのデプロイ時に必要なパッケージの入手に関連した面倒な処理を Composer に委ねることができます。これは利便性の問題です。Composer によるオートメーションを有効にしなかった場合、必要なことは、コードのコミットとデプロイ時に `vendor` ディレクトリの内容がすべて対象となる ("無視されない") ように、`/vendor` を `.gitignore` ファイルから削除するだけです。

では、これらの作業を順に行っていきます。

4. Laravel アプリに必要な PHP バージョンを設定します。

        azure site set --php-version 5.6

    これで PHP のバージョンが設定されました。
    
4. Azure Web アプリ用の新しい `APP_KEY` を生成し、その Azure Web アプリ用のアプリ設定として指定します。

        php artisan key:generate --show
        azure site appsetting add APP_KEY="<output_of_php_artisan_key:generate_--show>"

4. また、`Whoops, looks like something went wrong.` という謎めいたページが表示されないようにするため、Laravel デバッグを有効にします。

        azure site appsetting add APP_DEBUG=true

    環境変数の設定はこれで完了です。
    
    >[AZURE.NOTE] ここでひと息吐いて、Laravel と Azure がここで何をしているかについて説明します。Laravel はルート ディレクトリにある `.env` ファイルを使用して、アプリに環境変数を渡します。`APP_DEBUG=true` や `APP_KEY=...` の行が該当します。`config/app.php` では、`'debug' => env('APP_DEBUG', false),` というコードでこの変数にアクセスしています。[env()](https://laravel.com/docs/5.2/helpers#method-env) は Laravel のヘルパー メソッドで、内部的には PHP の [getenv()](http://php.net/manual/en/function.getenv.php) が使われています。
    >
    >ただし `.env` は、ルート ディレクトリの `.gitignore` ファイルによって除外指定されているため、Git によって無視されます。つまり、ローカル Git リポジトリ内の `.env` だけは、他のファイルとは異なり、Azure にプッシュされません。もちろん、この行を `.gitignore` から削除すればよいのですが、既に述べたように、このファイルをソース管理に追加することは推奨されません。とはいえ Azure でこれらの環境変数を指定する手段は必要です。
    >
    >さいわい、Azure App Service におけるアプリ設定では、PHP の [getenv()](http://php.net/manual/en/function.getenv.php) がサポートされています。FTP などの手段を使用して手動で `.env` ファイルを Azure にアップロードしてもかまいませんが、Azure では、`.env` を使わず単に必要な変数を Azure のアプリ設定として指定することができます。先ほど行った手順がまさにこれに該当します。さらに、`.env` ファイルと Azure のアプリ設定との両方に同じ変数が存在した場合、Azure のアプリ設定が優先されます。

4. 最後の 2 つのタスク (仮想ディレクトリの設定と Composer の有効化) には [Azure ポータル](https://portal.azure.com)が必要となります。ご使用の Azure アカウントで[ポータル](https://portal.azure.com)にログインしましょう。

4. 左側のメニューから、**[App Services]**、**[&lt;app\_name>]**、**[ツール]** の順にクリックします。

    ![Enable Composer for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-composer-tools.png)
    
    >[AZURE.TIP] **[ツール]** の代わりに **[設定]** をクリックした場合は **[アプリケーション設定]** ブレードが表示され、先ほど行った PHP のバージョンやアプリ設定、仮想ディレクトリの設定を行うことができます。
    
4. **[拡張機能]**、**[追加]** の順にクリックして拡張機能を追加します。

4. **[拡張機能の選択]** [ブレード](../azure-portal-overview.md)で **[Composer]** を選択します (*ブレード* = 横並びで表示されるポータル ページ)。

4. **[法律条項に同意する]** ブレードの **[OK]** をクリックします。

5. **[拡張機能の追加]** ブレードの **[OK]** をクリックします。

    Azure で拡張機能の追加が完了すると、端の方に親切なポップアップ メッセージが表示され、**[拡張機能]** ブレードの一覧に **Composer** が表示されます。

    ![Extensions blade after enabling Composer for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-composer-end.png)

    Composer の有効化はこれで完了です。
    
4. Web アプリのブレードに戻り、**[設定]**、**[アプリケーション設定]** の順にクリックします。

    ![Access Settings blade to set virtual directory for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)

    **[アプリケーション設定]** ブレードで、先ほど設定した PHP バージョンを確認します。

    ![PHP version in Settings blade for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a.png)

    先ほど追加したアプリ設定も確認してください。
    
    ![App settings in Settings blade for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)

4. ブレードの一番下までスクロールし、ルート仮想ディレクトリの基準となるパスを **site\\wwwroot** から **site\\wwwroot\\public** に変更します。

    ![Set virtual directory for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)

4. ブレードの上部にある **[保存]** をクリックします。

    仮想ディレクトリの設定はこれで完了です。

## Git を使用した Web アプリのデプロイ (と環境変数の設定)

いよいよコードをデプロイする段階となりました。デプロイ作業は、再びコマンド プロンプトまたは端末から行います。

4. Git リポジトリの場合と同様に、すべての変更をコミットし、Azure Web アプリにコードをデプロイします。

        git add .
        git commit -m "Hurray! My first commit for my Azure app!"
        git push azure master 

    `git push` を実行すると、Git デプロイのパスワードを入力するよう求められます。先ほど `azure site create` でデプロイの資格情報を作成するように指定した場合は、そのときに使用したパスワードを入力します。
    
5. 次のコマンドを実行してブラウザーでの実行状態を確認してみましょう。

        azure site browse

    正常に実行されていれば、ブラウザーに Laravel のスプラッシュ スクリーンが表示されます。
    
    ![Laravel splash screen after deploying web app to Azure](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
    
    この画面が表示されれば成功です。Laravel Web アプリが Azure で実行されています。
             
## 一般的なエラーのトラブルシューティング

ここでは、このチュートリアルで遭遇する可能性のあるエラーをいくつか取り上げます。

- [Azure CLI で "'site' is not an azure command ('サイト' は Azure コマンドではありません)" が表示される](#clierror)
- [Web アプリで HTTP 403 エラーが表示される](#http403)
- [Web アプリで "Whoops, looks like something went wrong. (問題が発生しました)" が表示される](#whoops)
- [Web アプリで "No supported encryptor found. (サポートされている暗号化機能が見つかりません)" が表示される](#encryptor)

<a name="clierror"></a>
### Azure CLI で "'site' is not an azure command ('サイト' は Azure コマンドではありません)" が表示される

コマンド ライン端末から `azure site *` を実行しているときに、`error:   'site' is not an azure command. See 'azure help'.` というエラーが表示されます。

一般に、"ARM" (Azure Resource Manager) モードに切り替えた結果として、このエラーが発生します。これを解決するには、「`azure config mode asm`」を実行して "ASM" (Azure Service Management) モードに戻してください。

<a name="http403"></a>
### Web アプリで HTTP 403 エラーが表示される

Web アプリを正しく Azure にデプロイしたにもかかわらず、ブラウザーで Azure Web アプリにアクセスすると、"`HTTP 403`" や "`You do not have permission to view this directory or page.`" が表示されます。

その原因として、Laravel アプリのエントリ ポイントの検出に Web アプリが失敗していることがまず考えられます。ルート仮想ディレクトリの基準となるパスが `site\wwwroot\public` に変更されていることを確認してください。この場所に Laravel の `index.php` が置かれます (「[Azure Web アプリの構成](#configure)」を参照)。

<a name="whoops"></a>
### Web アプリで "Whoops, looks like something went wrong. (問題が発生しました)" が表示される

Web アプリを正しく Azure にデプロイしたにもかかわらず、Azure Web アプリにブラウザーでアクセスすると、"`Whoops, looks like something went wrong.`" という謎めいたメッセージが表示されます。

エラーの状況をもっと詳しく把握するために、`APP_DEBUG` 環境変数を `true` に設定して Laravel のデバッグを有効にします (「[Azure Web アプリの構成](#configure)」を参照)。

<a name="encryptor"></a>
### Web アプリで "No supported encryptor found. (サポートされている暗号化機能が見つかりません)" が表示される

Web アプリを正しく Azure にデプロイしたにもかかわらず、Azure Web アプリにブラウザーでアクセスすると、次のエラー メッセージが表示されます。

![APP\_KEY missing in your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)
    
わかりにくいエラーではありますが、Laravel のデバッグを有効にしたので、少なくとも最初の状態よりは問題を把握しやすくなりました。Laravel のフォーラムでエラー文字列をざっと検索してみると、`.env` に APP\_KEY が設定されていないことに原因があるようです。今回のケースでは、Azure には `.env` が送信されません。この問題は、`APP_KEY` を Azure アプリ設定として追加することによって解決できます (「[Azure Web アプリの構成](#configure)」を参照)。
    
## 次のステップ

[Azure に MySQL データベースを作成](../store-php-create-mysql-database.md)してアプリにデータを追加する方法について確認します。また、Azure での PHP について解説された有益な情報は他にもあります。以下のリンクからアクセスしてください。

- [PHP デベロッパー センター](/develop/php/)
- [Azure Marketplace から Web アプリを作成する](app-service-web-create-web-app-from-marketplace.md)
- [Azure App Service Web Apps での PHP の構成方法](web-sites-php-configure.md)
- [Azure App Service での WordPress から Multisite への変換](web-sites-php-convert-wordpress-multisite.md)
- [Azure App Service のエンタープライズ クラスの WordPress](web-sites-php-enterprise-wordpress.md)

<!---HONumber=AcomDC_0608_2016-->