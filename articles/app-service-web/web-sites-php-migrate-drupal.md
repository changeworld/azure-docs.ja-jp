<properties 
	pageTitle="Azure App Service への Drupal の移行" 
	description="Drupal PHP サイトを Azure App Service に移行します。" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>


# Azure App Service への Drupal の移行

Azure App Service は PHP と MySQL を両方サポートしているので、Drupal サイトは比較的簡単に Azure App Service に移行できます。また、Drupal と PHP はプラットフォームを選ばないため、現在使用しているプラットフォームにかかわらず、同じ手順で Drupal を Azure App Service に移行できます。同じ理由から、Drupal のインストール環境はサイトによって大きく異なるので、ここに記載されていない独自の移行手順が必要になる場合もあります。Drush ツールは Azure App Service でサポートされないため、使用されません。

大規模で複雑な Drupal アプリケーションを移行する場合は、Azure クラウド サービスを使用する方法もあります。Websites と Cloud Services の違いの詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkId=310123">Azure App Service、Cloud Services、仮想マシン: いつ、どれを使用するか</a>」をご覧ください。Cloud Services への Drupal の移行については、「<a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">LAMP から Azure への Drupal サイトの移行 (ブログ記事)</a>」をご覧ください。
 
## Web アプリと MySQL データベースの作成

最初に、「[Azure App Service で PHP-MySQL Windows Azure の Web サイトを作成して Git でデプロイする][]」チュートリアルの手順に従い、MySQL を使用して新しい Web サイトを作成します。Git を使用して Drupal サイトを発行する場合は、Git リポジトリの構成方法を説明しているチュートリアルの手順に従います。必ず「**MySQL のリモート接続情報の取得**」セクションの指示に従ってください。この情報は後で必要になります。Drupal サイトのデプロイが目的であれば、このチュートリアルの他の部分は無視してかまいません。ただし、Azure App Service (および Git) を初めて使用する場合は、ひととおりお読みになることをお勧めします。

MySQL データベースを使用する新しい Web アプリを設定すると、MySQL データベース接続情報と (オプションの) Git リポジトリが作成されます。次の手順では、Azure App Service の MySQL にデータベースをコピーします。

## Web アプリの MySQL にデータベースをコピーする

データベースを Azure に移行するにはさまざまな方法があります。MySQL データベースの場合、[MySqlDump][] ツールを使用する方法が適しています。次のコマンドは、ローカル コンピューターから Azure にコピーする方法の例を示しています。

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

もちろん、実際に使用する Drupal データベースのユーザー名とパスワードを指定してください。同様に、最初の手順で作成した MySQL データベースのホスト名、ユーザー名、パスワード、データベース名を指定します。この情報は、前に収集した接続文字列情報に含まれています。接続文字列の形式は次のとおりです。

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

データベースのサイズによっては、コピー処理に数分かかる場合があります。

これで Drupal データベースを Azure に移行できました。Drupal コードを展開する前に、このコードを修正して、新しいデータベースに接続できるようにする必要があります。

## settings.php のデータベース接続情報を変更する

ここで、新しいデータベースの接続情報が再び必要になります。テキスト エディターで **/drupal/sites/default/setting.php** ファイルを開き、**$databases** 配列内の "database"、"username"、"password"、"host" の値を新しいデータベースの値に書き換えます。書き換えた配列は次のようになります。

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

## Git または FTP を使用して Drupal コードをデプロイする

最後に、Git または FTP を使用して、Web アプリにコードをデプロイします。

FTP を使用する場合は、[Azure プレビュー ポータル](https://portal.azure.com)の Web アプリのブレードから FTP のホスト名とユーザー名を取得します。次に、任意の FTP クライアントを使用して、リモート サイトの **/site/wwwroot** フォルダーに Drupal ファイルをアップロードします。

Git を使用する場合は、前の手順で Git リポジトリを設定している必要があります。ローカル コンピューターに Git をインストールします。リポジトリを作成した後、記載の指示に従ってください。

> [AZURE.NOTE]Git の設定によっては、.gitignore ファイル (git commit の実行後にローカル ルート ディレクトリに作成される .git フォルダーと同じ階層にある隠しファイル) の編集が必要になることがあります。これは、Drupal アプリケーションで無視されるファイルを指定します。展開する必要のあるファイルがここに含まれている場合は、それらのファイルを削除して無視されないようにします。

Web アプリに Drupal をデプロイした後は、引き続き Git または FTP を使用して更新をデプロイできます。

## 関連情報

詳細については、次の投稿およびトピックをご覧ください。

- [Azure App Service Web Apps - PHP について (ブログ記事)][]
- [Azure App Service、Cloud Services、仮想マシン: いつ、どれを使用するか][]
- [.user.ini ファイルを使用して Azure App Service Web Apps で PHP を構成する (ブログ記事)][]
- [Azure Integration Module (Azure 統合モジュール)](https://drupal.org/project/azure_auth)
- [Azure Blob Storage Module (Azure BLOB ストレージ モジュール)](https://drupal.org/project/azure_blob)

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service および既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* ポータルからプレビュー ポータルへの変更ガイドについては、「[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。

  [Azure App Service で PHP-MySQL Windows Azure の Web サイトを作成して Git でデプロイする]: /develop/php/tutorials/website-w-mysql-and-git/
  
  [Azure App Service Web Apps - PHP について (ブログ記事)]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Azure App Service、Cloud Services、仮想マシン: いつ、どれを使用するか]: http://go.microsoft.com/fwlink/?LinkId=310123
  [.user.ini ファイルを使用して Azure App Service Web Apps で PHP を構成する (ブログ記事)]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Azure Integration Module]: http://drupal.org/project/azure
 

<!---HONumber=62-->