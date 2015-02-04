<properties title="Enterprise class WordPress on Azure Websites" pageTitle="エンタープライズ クラスの WordPress を Azure Websites で運用する" description="エンタープライズ クラスの WordPress サイトを Azure Websites でホストする方法について説明します。" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="tomfitz" manager="wpickett" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.devlang="php" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="web" ms.date="11/11/2014" ms.author="tomfitz" />

#エンタープライズ クラスの WordPress を Azure Websites で運用する

Azure Websites は、基幹業務系の大規模な [WordPress][wordpress] サイト用に、拡張性のある、安全で使いやすい環境を提供します。Microsoft も [Office][officeblog] ブログや [Bing][bingblog] ブログなどのエンタープライズ クラスのサイトを運営しています。このドキュメントでは、Azure Websites を使用して、クラウドベースで大量のアクセスを処理できる能力を備えたエンタープライズ クラスの WordPress サイトを構築し、管理する方法について説明します。

##この記事の内容 

* [アーキテクチャと計画](#planning) - サイトの作成に先立って、アーキテクチャ、要件、パフォーマンスに関する考慮事項について説明します。

* [方法](#howto) - サイトの作成、デプロイ、構成方法を示します。

* [その他のリソース](#resources) - 他のリソースと情報を示します。

##<a id="plan"></a>アーキテクチャと計画

基本的な WordPress インストールの要件は 2 つだけです。

* **MySQL データベース** - [Azure ストアに含まれている ClearDB][cdbnstore] を介して利用できるほか、[Windows][mysqlwindows] または [Linux][mysqllinux] を実行する Azure Virtual Machines で独自の MySQL インストールを管理できます。

    > [WACOM.NOTE] ClearDB では複数の MySQL 構成を利用でき、構成ごとにパフォーマンス特性が異なります。Azure ストアを通じて提供されるサービスの情報については、[Azure ストア][cdbnstore]を参照してください。または、ClearDB から直接提供されるサービスについては、[ClearDB の料金](http://www.cleardb.com/pricing.view) を参照してください。
    
* **PHP 5.2.4 以降** - 現在、Azure Websites では [PHP Version 5.3、5.4、5.5][phpwebsite] が利用できます。

	> [WACOM.NOTE] 最新のセキュリティ更新プログラムが適用されるよう、常に最新バージョンの PHP で実行することをお勧めします。

###基本的なデプロイメント

基本要件だけを使用して、Azure のリージョン内に基本的なソリューションを作成できます。

![an Azure Website and MySQL Database hosted in a single Azure region][basic-diagram]

このデプロイでは、Websites のインスタンスを複数作成することで、アプリケーションをスケール アウトできますが、いずれも特定リージョンのデータ センターにホストされます。このリージョンの外部からアクセスするユーザーがサイトを使用すると、応答時間が長くなることがあります。また、このリージョンのデータ センターがダウンした場合は、アプリケーションもダウンします。


###複数リージョンのデプロイメント

Azure [Traffic Manager][trafficmanager] を使用すると、WordPress サイトを複数のリージョンに展開する一方で、アクセスするユーザーには 1 つの URL のみを提示できます。すべてのユーザーは Traffic Manager を介してサイトにアクセスし、負荷分散構成に基づいて特定のリージョンにルーティングされます。

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][multi-region-diagram]

各リージョン内でも、WordPress サイトは複数の Web サイト インスタンス間に展開されることがありますが、こうしたスケーリングはリージョンに固有です。つまり、トラフィックの多いリージョンと少ないリージョンでは、異なるスケーリングが実施されます。

複数の MySQL データベースへのレプリケーションとルーティングは、ClearDB の [CDBR High Availability Router][cleardbscale] (前掲図を参照) または [MySQL Cluster CGE][cge] を使用して実行できます。 

###メディア ストレージとキャッシュを用いる複数リージョンのデプロイメント

サイトがアップロードを受け入れている場合、またはメディア ファイルをホストする場合は、Azure BLOB ストレージを使用します。キャッシュが必要な場合、[Redis cache][rediscache]、[Memcache Cloud](http://azure.microsoft.com/ja-jp/gallery/store/garantiadata/memcached/)、[MemCachier](http://azure.microsoft.com/ja-jp/gallery/store/memcachier/memcachier/) のほか、[Azure ストア](http://azure.microsoft.com/ja-jp/gallery/store/)で提供されているいずれかのキャッシュ サービスをご検討ください。

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][performance-diagram]

BLOB ストレージは、既定では複数のリージョンにまたがって地理的に分散されるため、すべてのサイトにファイルをレプリケートしなくてもかまいません。また、BLOB ストレージ用に Azure [コンテンツ配信ネットワーク (CDN)][cdn] を有効にして、ユーザーに近いエンド ノードへとファイルを配信することもできます。

###計画

####その他の要件

目的 | 方法
------------------------|-----------
**大きなファイルのアップロードまたは保存** | [BLOB ストレージ用 WordPress プラグイン][storageplugin]
**電子メールの送信** | [SendGrid][storesendgrid] および [SendGrid 用 WordPress プラグイン][sendgridplugin]
**ドメイン名のカスタマイズ** | [Azure Websites でのカスタム ドメイン名][customdomain]
**HTTPS** | [Azure の Web サイトでの HTTPS の有効化][httpscustomdomain]
**運用前検証** | [Azure Websites のステージングされた発行のサポート][staging] <p>サイトをステージングから運用に切り替えると、WordPress の構成も変化することに注意してください。ステージング中のサイトを運用段階に切り替える前に、すべての設定が本番サイトの要件に合わせて更新されていることを確認する必要があります。</p> 
**監視とトラブルシューティング** | [Azure Websites の診断ログ][log]と [Azure Websites の監視][monitor]
**サイトのデプロイ** | [Azure Websites のデプロイ][deploy]

####高可用性と障害復旧

目的 | 方法
------------------------|-----------
**サイトの負荷分散**または**地理的分散** | [Azure の Traffic Manager を使用したトラフィックのルーティング][trafficmanager]
**バックアップと復元** | [Azure Websites のバックアップ][backup]と[復元][restore]

####パフォーマンス

クラウド内のパフォーマンスは、主にキャッシュとスケール アウトによって達成されます。しかし、Web サイトのホスティングに関するメモリや帯域幅などの属性も、考慮に入れる必要があります。

目的 | 方法
------------------------|-----------
**Web サイト インスタンス機能について理解する** |  [料金の詳細 (Web サイトのサイズとモードごとの機能を含む)][websitepricing]
**リソースのキャッシュ** | [Redis Cache][rediscache]、[Memcache Cloud](http://azure.microsoft.com/ja-jp/gallery/store/garantiadata/memcached/)、[MemCachier](http://azure.microsoft.com/ja-jp/gallery/store/memcachier/memcachier/) のほか、[Azure ストア](http://azure.microsoft.com/ja-jp/gallery/store/)で提供されているいずれかのキャッシュ サービスをご検討ください
**アプリケーションの規模の設定** | [Azure Web サイトの規模の設定][websitescale]および [ClearDB High Availability ルーティング][cleardbscale]。独自の MySQL インストールをホストし、管理する場合は、[MySQL Cluster CGE][cge] によるスケール アウトを検討してください。

####移行

既存の WordPress サイトから Azure Websites に移行する方法は、次の 2 種類です。

* **[WordPress のエクスポート][export]** - この方法では、ブログのコンテンツをエクスポートし、次に [WordPress Importer プラグイン][import]を使用して、Azure 上の新しい WordPress サイトにインポートします。

	> [WACOM.NOTE] このプロセスでブログのコンテンツは移行できますが、プラグインやテーマ、その他のカスタマイズ内容は移行できません。これらは、改めて手動でインストールする必要があります。

* **手動での移行** - [バックアップしたサイト][wordpressbackup]と[データベース][wordpressdbbackup]を、手動で Azure Websites のサイトとそれに関連付けられた MySQL データベースとして復元します。これにより、高度にカスタマイズされたサイトを移行できると共に、プラグインやテーマ、さらには他のカスタマイズを手動でインストールする手間が大幅に軽減されます。

##方法

###<a id="create"></a>新規 WordPress サイトを作成する

1. [Azure ストア][cdbnstore]を使用して、「[アーキテクチャと計画](#planning)」セクションで見極めたサイズの MySQL データベースを、サイトのホスト先となるリージョンに作成します。

2. 「[Azure でギャラリーから WordPress Web サイトを作成する][createwordpress]」の手順で、新規 WordPress サイトを作成します。サイトを作成する場合は、**[既存の MySQL データベースを使用します]** を選択し、手順 1. で作成したデータベースを選択します。

既存の WordPress サイトを移行する場合は、新しいサイトを作成した後、「[既存の WordPress サイトを Azure に移行する](#migrate)」を参照してください。

###<a id="migrate"></a>既存の WordPress サイトを Azure に移行する

WordPress Web サイトに移行する場合、「[アーキテクチャと計画](#planning)」セクションでも触れたように、2 とおりの方法があります。

* **エクスポートとインポート** - カスタマイズの量が多くないサイトを移行する場合や、コンテンツだけを移行する場合に実行します。

* **バックアップと復元** - 数多くのカスタマイズが施されたサイトを丸ごと移行する場合に実行します。

以下のセクションのいずれかに従って、サイトを移行します。

####エクスポートとインポートによる移行

1. [WordPress のエクスポート][export]を実行して、既存のサイトをエクスポートします。

2. 「[新規 WordPress サイトを作成する]」セクションの手順で、新しい Web サイトを作成します(#create) セクションを参照してください。

3. Azure Websites で WordPress サイトにログインし、**[プラグイン]**、**[新規追加]** の順にクリックします。**WordPress Importer** プラグインを検索し、インストールします。

4. プラグインのインストールが完了したら、**[ツール]**、**[インポート]** の順にクリックして **[WordPress]** を選択し、WordPress Importer プラグインを使用します。

5. **[WordPress のインポート]** ページで、**[ファイルの選択]** を選択します。既存の WordPress サイトからエクスポートした WXR ファイルを探して、**[ファイルのアップロードとインポート]** を選択します。

6. **[送信]** をクリックします。インポートに成功したことを示すメッセージが表示されます。

8. 以上の手順をすべて完了したら、[Azure 管理ポータル][mgmtportal]のダッシュボードから対象の Web サイトを再起動します。

サイトをインポートした後に、以下の手順を実行して、インポート ファイルに含まれない設定を有効にすることが必要になる場合があります。

使用している要素 | これを行うには、次の手順を実行します。
------------------ | ----------
**固定リンク** | 新しいサイトの WordPress ダッシュボードで、**[設定]**、**[固定リンク]** の順にクリックし、固定リンクの構造を更新します。
**画像/メディアのリンク** | リンク先を新しい場所に更新するには、検索置換ツールの [Velvet Blues Update URLs プラグイン][velvet]を使用するか、データベースを直接修正します。
**テーマ** | **[外観]** から **[テーマ]** に移動し、必要に応じて Web サイトのテーマを更新します。
**メニュー** | 使用しているテーマがメニューをサポートしている場合は、ホーム ページへのリンクに使用していないサブディレクトリが残っていることがあります。**[外観]** から **[メニュー]** に移動して、更新します。

####バックアップと復元による移行

1. 「[WordPress Backups (WordPress のバックアップ)][wordpressbackup]」の情報を参照して、既存の WordPress をバックアップします。

2. 「[Backing Up Your Database (データベースのバックアップ)][wordpressdbbackup]」の情報を参照して、既存のデータベースをバックアップします。

3. 新しいデータベースを作成し、バックアップを復元します。

	1.[Azure ストア][cdbnstore]で新しいデータベースを購入するか、[Windows][mysqlwindows] または [Linux][mysqllinux] VM で MySQL データベースを設定します。

	2.[MySQL Workbench][workbench] などの MySQL クライアントを使用して、新しいデータベースに接続し、WordPress データベースをインポートします。

	3.データベースを更新して、ドメイン エントリを "mywordpress.azurewebsites.net" などのように、新しい Azure Web サイトのドメインに変更します。[WordPress データベースの検索と置換用スクリプト][searchandreplace]を使用すると、すべてのインスタンスを安全に変更できます。

4. 新しい Web サイトを作成し、WordPress バックアップを発行します。

	1. [Azure の管理ポータル][mgmtportal]の **[新規]**、**[Web サイト]**、**[カスタム作成]** を使用して、データベースを含んだ新しい Web サイトを作成します。これにより、空のサイトが作成されます。

	2. WordPress バックアップで **wp-config.php** ファイルを見つけて、エディターで開きます。以下のエントリを新しい MySQL データベースの情報に変更します。

		* **DB_NAME** - データベースのユーザー名

		* **DB_USER** - データベースへのアクセスに使用するユーザー名

		* **DB_PASSWORD** - ユーザーのパスワード

		これらのエントリを変更したら、**wp-config.php** ファイルを保存して閉じます。

	3.「[Azure の Web サイトのデプロイ方法][deploy]」の情報を参照して、利用するデプロイメント方法を有効にし、WordPress バックアップを Azure で作成した Web サイトにデプロイします。

5. WordPress サイトのデプロイが完了したら、その新しいサイトに *.azurewebsite.net という形式の URL でアクセスできるようになります。

###サイトの構成

WordPress サイトを作成、移行した後は、以下の情報を参照して、パフォーマンスの向上と追加機能の有効化を行います。

目的 | 方法
------------- | -----------
**Web サイトのモードとサイズを設定し、規模の設定を有効にする** | [ Web サイトの規模の設定方法][websitescale]
**永続的なデータベース接続の有効化** <p>既定では、WordPress は永続的なデータベース接続を使用しないため、複数接続を実行すると、データベースへの接続が制限される場合があります。</p>  | <ol><li><p><strong>wp-includes/wp-db.php</strong> ファイルを編集します。</p></li><li><p>次の行を見つけます。</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>この行を次の行で置き換えます。</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>次の行を見つけます。</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>この行を次の行で置き換えます。</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p><strong>wp-includes/wp-db.php</strong> ファイルを保存して、サイトを再デプロイします。</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>注:</h5><p>これらの変更は、WordPress の更新時に上書きされます。</p><p>WordPress は既定で自動更新を行いますが、この設定は <strong>wp-config.php</strong>  ファイルを編集し、 <code>('WP_AUTO_UPDATE_CORE', false ) を追加することで、無効にできます。</code></p><p>更新に対処するもう 1 つの手段として、WebJob を使用する方法があります。WebJob は、 <strong>wp-db.php</strong>  ファイルを監視し、ファイルが更新されるたびに前掲の変更を行います。詳細については、「<a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">WebJob の概要</a>」を参照してください。</p></div>
**パフォーマンスの向上** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">ARR クッキーの無効化</a> - 複数の Websites のインスタンスで WordPress を実行している場合に、パフォーマンスを向上させる方法です。</p></li><li><p>キャッシュの有効化。 <a href="http://msdn.microsoft.com/ja-jp/library/azure/dn690470.aspx">Redis Cache</a> (プレビュー) を <a href="https://wordpress.org/plugins/redis-object-cache/">Redis Object Cache WordPress プラグインと</a>組み合わせて使用するか、 <a href="http://azure.microsoft.com/ja-jp/gallery/store/">Azure ストアから提供されている他のいずれかのキャッシュ サービスを使用します。</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">WordPress を Wincache で高速化する方法</a> - Wincache は Websites に対して既定で有効化されています。</p></li><li><p><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-scale/">Azure Web サイトの規模の設定</a> および  <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB High Availability ルーティング</a> または <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**ストレージ用 BLOB の使用** |  <ol><li><p><a href="http://azure.microsoft.com/ja-jp/documentation/articles/storage-create-storage-account/">Azure のストレージ アカウントの作成</a></p></li><li><p>コンテンツ配信ネットワーク (CDN)  <a href="http://azure.microsoft.com/ja-jp/documentation/articles/cdn-how-to-use/">を使用して、</a> BLOB に格納されているデータを地理的に分散させる方法を確認します。</p></li><li><p>Azure Storage for WordPress プラグインを <a href="https://wordpress.org/plugins/windows-azure-storage/">インストールして構成します</a>。</p><p>このプラグインに関する詳細な設定と構成の情報については、 <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">ユーザー ガイドを参照してください</a>。</p> </li></ol>
**電子メールの有効化** |  <ol><li><p><a href="http://azure.microsoft.com/ja-jp/gallery/store/sendgrid/sendgrid-azure/">Azure ストアで SendGrid を有効にします。</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">WordPress 用の SendGrid プラグインをインストールします。</a></p></li></ol>
**カスタム ドメイン名の構成** | [Azure Web サイトでのカスタム ドメイン名の使用][customdomain]
**カスタム ドメイン名に対する HTTPS の有効化** | [Azure Web サイトでの HTTPS の使用][httpscustomdomain]
**サイトの負荷分散または地理的分散** | [Azure の Traffic Manager を使用したトラフィックのルーティング][trafficmanager]。カスタム ドメインを使用する場合は、カスタム ドメイン名と Traffic Manager の使用の詳細について、[Azure Web サイトでのカスタム ドメイン名の使用に関するページ][customdomain]を参照してください。
**Web サイトの自動バックアップの有効化** | [Azure Websites のバックアップ][backup]
**診断ログの有効化** | [Websites の診断ログの有効化][log]

##<a href="resources"></a>その他のリソース

* [WordPress の最適化](http://codex.wordpress.org/WordPress_Optimization)

* [WordPress サイトの Multisite への変換](http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-php-convert-wordpress-multisite/)

* [Azure 用の ClearDB アップグレード ウィザード](http://www.cleardb.com/store/azure/upgrade)

* [Azure Web サイトのサブフォルダーに WordPress をホスティングする方法](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Step-By-Step:  Create a WordPress site using Azure (ステップ バイ ステップ: Azure で WordPress サイトを作成する)](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Host your existing WordPress blog on Azure (Azure で既存の WordPress ブログをホストする)](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Enabling pretty permalinks in WordPress (WordPress で見やすい固定リンクを有効にする)](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [How to migrate and run your WordPress blog on Azure Websites (WordPress ブログを Azure Websites に移行し、運用する方法)](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [How to run WordPress on Azure Websites for free (Azure Websites で、無料で WordPress を運用する方法)](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress on Azure in 2 minutes or less (Azure で 2 分以内に WordPress を稼動)](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Moving a WordPress blog to Azure - Part 1: Creating a WordPress blog on Azure（WordPress ブログを Azure に移転する - パート 1: Azure へのWordPress ブログの作成）](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Moving a WordPress blog to Azure - Part 2: Transferring your content (WordPress ブログを Azure に移転する - パート 2: コンテンツの転送)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Moving a WordPress blog to Azure - Part 3: Setting up your custom domain (WordPress ブログを Azure に移転する - パート 3: カスタム ドメインのセットアップ)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Moving a WordPress blog to Azure - Part 4: Pretty permalinks and URL Rewrite rules（WordPress ブログを Azure に移転する - パート 4: 見やすい固定リンクと URL 書き換えルール）](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Moving a WordPress blog to Azure - Part 5: Moving from a subfolder to the root（ WordPress ブログを Azure に移転する - パート 5: ルートへのサブフォルダーの移動）](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Azure アカウントで WordPress Web サイトを設定する方法](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Propping up WordPress on Azure (Azure で WordPress を設置する)](http://www.johnpapa.net/wordpress-on-azure/)

* [Tips for WordPress on Azure (Azure で WordPress を運用するヒント)](http://www.johnpapa.net/azurecleardbmysql/)

[performance-diagram]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-php-configure/
[customdomain]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-custom-domain-name/
[trafficmanager]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-backup/
[restore]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-restore/
[rediscache]: http://msdn.microsoft.com/ja-jp/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/ja-jp/library/azure/dn386122.aspx
[websitescale]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-scale/
[managedcachescale]: http://msdn.microsoft.com/ja-jp/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-staged-publishing/
[monitor]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-monitor/
[log]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-enable-diagnostic-log/
[httpscustomdomain]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/
[mysqlwindows]: http://azure.microsoft.com/ja-jp/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
[mysqllinux]: http://azure.microsoft.com/ja-jp/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: https://azure.microsoft.com/ja-jp/pricing/details/web-sites/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-php-web-site-gallery/
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://manage.windowsazure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-deploy/
[posh]: http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell/
[xplat-cli]: http://azure.microsoft.com/ja-jp/documentation/articles/xplat-cli/
[storesendgrid]: http://azure.microsoft.com/ja-jp/gallery/store/sendgrid/sendgrid-azure/
[cdn]: http://azure.microsoft.com/ja-jp/documentation/articles/cdn-how-to-use/

<!--HONumber=35.1-->
