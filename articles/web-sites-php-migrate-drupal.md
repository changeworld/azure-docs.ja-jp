<properties linkid="migrating-drupal-to-azure-websites" urlDisplayName="Azure の Web サイトへの Drupal の移行" pageTitle="Azure の Web サイトへの Drupal の移行" metaKeywords="Drupal, PHP, Web サイト" description="Drupal PHP サイトを Azure の Web サイトへ移行します。" metaCanonical="" services="web-sites" documentationCenter="PHP" title="Azure の Web サイトの Drupal への移行" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />


# Azure の Web サイトへの Drupal の移行

Azure の Web サイトは PHP と MySQL を両方サポートしているので、Drupal サイトは比較的簡単に Azure の Web サイトに移行できます。また、Drupal と PHP はプラットフォームを選ばないため、現在どのプラットフォームを使用しているかにかかわらず、同じ手順で Drupal を Azure の Web サイトに移行できます。同じ理由から、Drupal のインストール環境はサイトによって大きく異なるので、ここに記載されていない独自の移行手順が必要になる場合もあります。Drush ツールは Azure の Web サイトでサポートされないため、使用されません。

> [WACOM.NOTE]
> 大規模で複雑な Drupal アプリケーションを移行する場合は、Azure クラウド サービスを使用する方法もあります。Web サイトとクラウド サービスの違いの詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkId=310123">Azure の Web サイト、クラウド サービス、仮想マシン: いつ、どれを使用するか</a>」を参照してください。クラウド サービスへの Drupal の移行については、「<a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">Migrating a Drupal Site from LAMP to Azure (LAMP から Azure への Drupal サイトの移行)</a>」を参照してください。

## 目次

- [Azure の Web サイトを作成する][]
- [データベースをコピーする][]
- [Settings.php を修正する][]
- [Drupal コードをデプロイする][]
- [関連情報][]
 
##<a name="create-siteanddb"></a><span class="short-header">Azure の Web サイトと MySQL データベースを作成する</span>1. Azure の Web サイトと MySQL データベースを作成する

最初に、「[PHP-MySQL Azure の Web サイトを作成して Git で展開する][]」チュートリアルの手順に従って、MySQL を使用する新しい Web サイトを作成します。Git を使用して Drupal サイトを発行する場合は、Git リポジトリの構成方法を説明しているチュートリアルの手順に従います。必ず「**MySQL のリモート接続情報の取得**」セクションの指示に従ってください。この情報は後で必要になります。Drupal サイトを展開することが目的であれば、このチュートリアルの他の部分は無視してかまいません。ただし、Azure の Web サイト (および Git) を初めて使用する場合は、ひととおりお読みになることをお勧めします。

MySQL データベースを使用する新しい Web サイトを設定すると、MySQL データベース接続情報と (オプションの) Git リポジトリが作成されます。次の手順では、Azure の Web サイトの MySQL にデータベースをコピーします。

##<a name="copy-database"></a><span class="short-header">Azure の Web サイトの MySQL にデータベースをコピーする</span>2.Azure の Web サイトの MySQL にデータベースをコピーする

データベースを Azure に移行するにはさまざまな方法があります。MySQL データベースの場合、[MySqlDump][] ツールを使用する方法が適しています。次のコマンドは、ローカル コンピューターから Azure の Web サイトにコピーする方法の例を示しています。

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

もちろん、実際に使用する Drupal データベースのユーザー名とパスワードを指定してください。同様に、最初の手順で作成した MySQL データベースのホスト名、ユーザー名、パスワード、データベース名を指定します。この情報は、前に収集した接続文字列情報に含まれています。接続文字列の形式は次のとおりです。

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

データベースのサイズによっては、コピー処理に数分かかる場合があります。

これで Drupal データベースを Azure の Web サイトに移行できました。Drupal コードを展開する前に、このコードを修正して、新しいデータベースに接続できるようにする必要があります。

##<a name="modify-settingsphp"></a><span class="short-header">settings.php のデータベース接続情報を修正する</span>3. settings.php のデータベース接続情報を修正する

ここで、新しいデータベースの接続情報が再び必要になります。テキスト エディターで **/drupal/sites/default/setting.php** ファイルを開き、**$databases** 配列内の 'database'、'username'、'password'、'host' の値を新しいデータベースの値に書き換えます。書き換えた配列は次のようになります。

    $databases = array (
       'default' => 
       array (
         'default' => 
         array (
           'database' => 'remote_db_name',
           'username' => 'remote_username',
           'password' => 'remote_password',
           'host' => 'remote_host',
           'port' => '',
           'driver' => 'mysql',
           'prefix' => '',
         ),
       ),
     );

**settings.php** ファイルを保存します。これで展開する準備が整いました。

##<a name="deploy-drupalcode"></a><span class="short-header">Git または FTP を使用して Drupal コードを展開する</span>4. Git または FTP を使用して Drupal コードを展開する

最後に、Git または FTP を使用して、Azure の Web サイトにコードを展開します。

FTP を使用する場合は、Web サイトのダッシュボードから FTP のホスト名とユーザー名を取得します。次に、任意の FTP クライアントを使用して、リモート サイトの **/site/wwwroot** フォルダーに Drupal ファイルをアップロードします。

Git を使用する場合は、前の手順で Git リポジトリを設定している必要があります。ローカル コンピューターに Git をインストールします。リポジトリを作成した後、記載の指示に従ってください。

> [WACOM.NOTE]
> Git の設定によっては、.gitignore ファイル (git commit の実行後にローカル ルート ディレクトリに作成される .git フォルダーと同じ階層にある隠しファイル) の編集が必要になることがあります。これは、Drupal アプリケーションで無視されるファイルを指定します。展開する必要のあるファイルがここに含まれている場合は、それらのファイルを削除して無視されないようにします。

Azure の Web サイトに Drupal を展開した後は、引き続き Git または FTP を使用して更新を展開できます。

##<a name="related-information"></a><span class="short-header">関連情報</span>関連情報

詳細については、次の投稿およびトピックをご覧ください。

- [Azure Websites, a PHP Perspective (Azure の Web サイト - PHP について)][]
- [Azure の Web サイト、クラウド サービス、仮想マシン: いつ、どれを使用するか][]
- [Configuring PHP in Azure Websites with .user.ini Files (.user.ini ファイルを使用して Azure の Web サイトで PHP を構成する)][]
- [Azure Integration Module (Azure 統合モジュール)](https://drupal.org/project/azure_auth)
- [Azure Blob Storage Module (Azure BLOB ストレージ モジュール)](https://drupal.org/project/azure_blob)

  [Azure の Web サイトを作成する]: #create-siteanddb
  [データベースをコピーする]: #copy-database
  [Settings.php を修正する]: #modify-settingsphp
  [Drupal コードをデプロイする]: #deploy-drupalcode
  [関連情報]: #related-information
  [PHP-MySQL Azure の Web サイトを作成して Git で展開する]: http://www.windowsazure.com/ja-jp/develop/php/tutorials/website-w-mysql-and-git/
  
  [Azure Websites, a PHP Perspective (Azure の Web サイト - PHP について)]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Azure の Web サイト、クラウド サービス、仮想マシン: いつ、どれを使用するか]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Configuring PHP in Azure Websites with .user.ini Files (.user.ini ファイルを使用して Azure の Web サイトで PHP を構成する)]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Azure Integration Module (Azure 統合モジュール)]: http://drupal.org/project/azure

