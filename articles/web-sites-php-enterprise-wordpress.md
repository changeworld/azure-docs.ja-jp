<properties 
	pageTitle="Azure App Service のエンタープライズ クラスの WordPress" 
	description="Azure App Service にエンタープライズ クラスの WordPress をホストする方法について説明します。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>

# Azure App Service のエンタープライズ クラスの WordPress

Azure App Service は、基幹業務系の大規模な [WordPress][wordpress] サイト用に、拡張性のある、安全で使いやすい環境を提供します。Microsoft も [Office ブログ][officeblog] や [Bing][bingblog] ブログなどのエンタープライズ クラスのサイトを運営しています。このドキュメントでは、Azure App Service Web アプリを使用して、大量の訪問者を処理できるエンタープライズ クラスの、クラウド ベースの WordPress サイトを確立し、維持する方法について説明しています。

## アーキテクチャと計画

基本的な WordPress インストールの要件は 2 つだけです。

* **MySQL データベース** - [Azure ストアに含まれている ClearDB][cdbnstore] を介して利用できるほか、[Windows][mysqlwindows] または [Linux][mysqllinux] を使用して Azure 仮想マシンで独自の MySQL インストールを管理できます。

    > [AZURE.NOTE] ClearDB では複数の MySQL 構成を利用でき、構成ごとにパフォーマンス特性が異なります。Azure ストアで提供されているオファリングの詳細については、[Azure ストア][cdbnstore]を、ClearDB が公表しているオファリングについては、[ClearDB の料金情報](http://www.cleardb.com/pricing.view)をご覧ください。
    
* **PHP 5.2.4 以上** - Azure App Service は現在、[PHP バージョン 5.3、5.4、5.5][phpwebsite] を提供しています。

	> [AZURE.NOTE] 最新のセキュリティ更新プログラムが適用されるよう、常に最新バージョンの PHP で実行することをお勧めします。

### 基本的なデプロイメント

基本要件だけを使用して、Azure のリージョン内に基本的なソリューションを作成できます。

![単一の Azure リージョンでホストされている Azure Web アプリと MySQL データベース][basic-diagram]

このデプロイでは、サイトの複数の Web アプリのインスタンスを作成することで、アプリケーションをスケール アウトできますが、いずれも特定リージョンのデータ センターにホストされます。このリージョンの外部からアクセスするユーザーがサイトを使用すると、応答時間が長くなることがあります。また、このリージョンのデータ センターがダウンした場合は、アプリケーションもダウンします。


### 複数リージョンのデプロイメント

Azure [Traffic Manager][trafficmanager] を使用すると、WordPress サイトを複数のリージョンに展開する一方で、アクセスするユーザーには 1 つの URL のみを提示できます。すべてのユーザーは Traffic Manager を介してサイトにアクセスし、負荷分散構成に基づいて特定のリージョンにルーティングされます。

![Azure の Web アプリは CDBR の高可用性のルーターを使用して、地域間での MySQL にルーティングする複数の地域でホストされています。][multi-region-diagram]

各リージョン内でも、WordPress サイトは複数の Web アプリ インスタンス間に展開されることがありますが、こうしたスケーリングはリージョンに固有です。つまり、トラフィックの多いリージョンと少ないリージョンでは、異なるスケーリングが実施されます。

複数の MySQL データベースへのレプリケーションとルーティングは、ClearDB の [CDBR High Availability Router][cleardbscale] (前掲図を参照) または [MySQL Cluster CGE][cge] を使用して実行できます。 

### メディア ストレージとキャッシュを用いる複数リージョンのデプロイメント

サイトがアップロードを受け入れている場合、またはメディア ファイルをホストする場合は、Azure BLOB ストレージを使用します。キャッシュが必要な場合は、[Redis cache][rediscache]、[Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/)、[MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/)、[Azure ストア](http://azure.microsoft.com/gallery/store/)で取り扱われている別のキャッシュ オファリングの使用を検討してください。

![Azure の Web アプリは、Managed Cache、Blob ストレージ、CDN を使用した CDBR の高可用性のルーターを MySQL に使用し、複数のリージョンでホストされています。][performance-diagram]

BLOB ストレージは、既定では複数のリージョンにまたがって地理的に分散されるため、すべてのサイトにファイルをレプリケートしなくてもかまいません。また、BLOB ストレージ用に Azure [コンテンツ配信ネットワーク (CDN)][cdn] を有効にして、ユーザーに近いエンド ノードへファイルを配信することもできます。

### 計画

#### その他の要件

目的| 方法
------------------------|-----------
**大きいファイルのアップロードまたは保存** | [BLOB ストレージを使うための WordPress プラグイン][storageplugin]
**電子メールの送信** | [SendGrid][storesendgrid]、[SendGrid を使うための WordPress プラグイン][sendgridplugin]
**カスタム ドメイン名** | [Azure App Service でのカスタム ドメイン名を構成する][customdomain]
**HTTPS** | [Azure App Service で Web アプリ用の HTTPS を有効にする][httpscustomdomain]
**実稼働前の検証** | [ステージング環境の Azure App Service で Web アプリを設定][staging] <p>Web アプリをステージングから運用に切り替えると、WordPress の構成も変化することに注意してください。ステージング中のアプリを運用段階に切り替える前に、すべての設定が本番アプリの要件に合わせて更新されていることを確認する必要があります。</p> 
**監視とトラブルシューティング** | [Azure App Service で Web アプリの診断のログ記録を有効にする][ログ] と [Azure App Service で Web アプリを監視][monitor]
**サイトを展開** | [Azure App Service での Web アプリのデプロイ][deploy]

#### 高可用性と障害復旧

目的| 方法
------------------------|-----------
**サイトの負荷分散**または**サイトの地理的な分散** | [Azure Traffic Manager によるトラフィックのルーティング][trafficmanager]
**バックアップと復元** | [Azure App Service での Web アプリのバックアップ][backup]と [Azure App Service での Web アプリの復元][restore]

#### パフォーマンス

クラウド内のパフォーマンスは、主にキャッシュとスケール アウトによって達成されます。しかし、Web アプリのホスティングに関するメモリや帯域幅などの属性も、考慮に入れる必要があります。

目的| 方法
------------------------|-----------
**アプリ サービス インスタンスの機能を理解** |  [アプリケーションのサービス階層の機能を含む、料金の詳細][websitepricing]
**リソースのキャッシュ** | [Redis キャッシュ][rediscache]、[Memcache クラウド](/ギャラリー/ストア/garantiadata/memcached/)、[MemCachier](/ギャラリー/ストア/memcachier/memcachier/)、[Azure ストア](/ギャラリー/ストア/) のその他のキャッシュ オファリングの 1 つ
**アプリケーションのスケールアップ** | [Azure App Service での Web アプリの拡張][websitescale]、[ClearDB の高可用性ルーティング][cleardbscale]。独自の MySQL インストールをホストし、管理する場合は、 [MySQL Cluster CGE][cge] によるスケール アウトを検討してください。

#### 移行

既存の WordPress サイトから Azure App Service に移行する方法は、次の 2 種類です。

* **[WordPress のエクスポート][export]** - この方法では、ブログのコンテンツをエクスポートし、次に [WordPress importer プラグイン][import] を使用して、Azure App Service 上の新しい WordPress サイトにインポートします。

	> [AZURE.NOTE] このプロセスでブログのコンテンツは移行できますが、プラグインやテーマ、その他のカスタマイズ内容は移行できません。これらは、改めて手動でインストールする必要があります。

* **手動での移行** - [バックアップしたサイト][wordpressbackup] と [データベース][wordpressdbbackup] を、手動で Azure App Service のサイトとそれに関連付けられた MySQL データベースとして復元します。これにより、高度にカスタマイズされたサイトを移行できると共に、プラグインやテーマ、さらには他のカスタマイズを手動でインストールする手間が大幅に軽減されます。

## 詳細な手順

### 新規 WordPress サイトを作成する

1. [Azure ストア][cdbnstore] を使用して、[アーキテクチャと計画](#planning)セクションで見極めたサイズの MySQL データベースを、サイトのホスト先となるリージョンに作成します。

2. 「[Azure App Service での WordPress Web アプリの作成][createwordpress]」の手順に従って、新しい WordPress Web アプリを作成します。Web アプリを作成する場合は、**[既存の MySQL データベースを使用します]** を選択し、手順 1 で作成したデータベースを選択します。

既存の WordPress サイトを移行する場合は、新しいサイトを作成した後で、[既存の WordPress サイトを Azure に移行します](#Migrate-an-existing-WordPress-site-to-Azure) 。

### 既存の WordPress サイトを Azure に移行する

[アーキテクチャと計画](#planning) セクションで説明したように、WordPress サイトに移行するには、2 つの方法があります。

* **エクスポートとインポート** - カスタマイズの量が少ないサイトを移行する場合や、コンテンツだけを移行する場合に実行します。

* **バックアップと復元** - 数多くのカスタマイズが施されたサイトを丸ごと移行する場合に実行します。

以下のセクションのいずれかに従って、サイトを移行します。

#### エクスポートとインポートによる移行

1. [WordPress のエクスポート][export] を実行して、既存のサイトをエクスポートします。

2. [新規 WordPress サイトを作成する](#Create-a-new-WordPress-site)セクションの手順で、新しい Web アプリを作成します。

3. Web アプリで WordPress サイトにログインし、**[プラグイン]**、**[新規追加]** の順にクリックします。**WordPress Importer** プラグインを検索し、インストールします。

4. Importer プラグインのインストールが完了したら、**[ツール]**、**[インポート]** の順にクリックして、**\[WordPress]** を選択し、WordPress Importer プラグインを使用します。

5. **[WordPress のインポート]** ページで、**[ファイルの選択]** をクリックします。既存の WordPress サイトからエクスポートした WXR ファイルを探して、**[ファイルのアップロードとインポート]** を選択します。

6. **[Submit]** をクリックします。インポートに成功したことを示すメッセージが表示されます。

8. これらのすべての手順が完了したら、[Azure ポータル][mgmtportal] の Web アプリのブレードからサイトを再起動します。

サイトをインポートした後に、以下の手順を実行して、インポート ファイルに含まれない設定を有効にすることが必要になる場合があります。

使用している要素| これを行うには、次の手順を実行します。
------------------ | ----------
**固定リンク** | 新しいサイトの WordPress ダッシュボードで、**[設定]**、**[固定リンク]** の順にクリックし、固定リンクの構造を更新します。
**イメージまたはメディア リンク** | リンク先を新しい場所に更新するには、検索置換ツールの [Velvet Blues Update URLs プラグイン][velvet] を使用するか、データベースを直接修正します。
**テーマ** | **[外観]** から **[テーマ]** に移動し、必要に応じてサイトのテーマを更新します。
**メニュー** | 使用しているテーマがメニューをサポートしている場合は、ホーム ページへのリンクに使用していないサブディレクトリが残っていることがあります。**[外観]** から **[メニュー]** に移動して、更新します。

#### バックアップと復元による移行

1. 「[WordPress のバックアップ][wordpressbackup]」の情報を参照して、既存の WordPress サイトをバックアップします。

2. 「[Backing Up Your Database (データベースのバックアップ)][wordpressdbbackup]」の情報を参照して、既存のデータベースをバックアップします。

3. 新しいデータベースを作成し、バックアップを復元します。

	1.[Azure ストア][cdbnstore] で新しいデータベースを購入するか、[Windows][mysqlwindows] または [Linux][mysqllinux] の VM で MySQL データベースを設定します。

	2.[MySQL Workbench][workbench] などの MySQL クライアントを使用して、新しいデータベースに接続し、WordPress データベースをインポートします。

	3.データベースを更新して、ドメイン エントリを"mywordpress.azurewebsites.net" などのように、新しい Azure App Service のドメインに変更します。[WordPress データベースの検索と置換用スクリプト][searchandreplace] を使用すると、すべてのインスタンスを安全に変更できます。

4. Azure ポータルで新しい Web アプリを作成し、WordPress のバックアップを公開します。

	1. データベースで **[新規]**、**[Web + モバイル]**、**[Azure Marketplace]**、**[Web アプリ]**、**[Web アプリ + SQL]** (または **[Web アプリ + MySQL]**)、**[作成]** を使用して [Azure ポータル][mgmtportal] に新しい Web アプリを作成します。必要なすべての設定を構成して、空の Web アプリを作成します。

	2. WordPress バックアップで **wp-config.php** ファイルを見つけて、エディターで開きます。以下のエントリを新しい MySQL データベースの情報に変更します。

		* **DB_NAME** - データベースのユーザー名

		* **DB_USER** - データベースへのアクセスに使用するユーザー名

		* **DB_PASSWORD** - ユーザーのパスワード

		これらのエントリを変更したら、**wp-config.php** ファイルを保存して閉じます。

	3. 「[Azure App Service での Web アプリのデプロイ][deploy]」の情報を使用して、使用したい展開方法を有効にし、WordPress バックアップを Azure App Service の Web アプリにデプロイします。

5. WordPress サイトがデプロイされると、サイトの *.azurewebsite.net という URL を使用して新しいサイト (App Service Web アプリとして) アクセスできるようになります。

### サイトの構成

WordPress サイトを作成、移行した後は、以下の情報を参照して、パフォーマンスの向上と追加機能の有効化を行います。

目的| 方法
------------- | -----------
**アプリ サービス プランのモード、サイズの設定とスケーリングを有効にする** | [Azure App Service での Web アプリの拡張][websitescale]
**永続的なデータベース接続を有効にする** <p>既定では、WordPress は永続的なデータベース接続を使用しないため、複数接続を実行すると、データベースへの接続が制限される場合があります。</p>  | <ol><li><p><strong>wp-includes/wp-db.php</strong> ファイルを編集します。</p></li><li><p>次の行を見つけます。</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>この行を次の行で置き換えます。</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>次の行を見つけます。</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>この行を次の行で置き換えます。</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p><strong>wp-includes/wp-db.php</strong> ファイルを保存して、サイトを再デプロイします。</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><p>これらの変更は、WordPress の更新時に上書きされます。</p><p>WordPress は既定で自動更新を行いますが、この設定は <strong>wp-config.php</strong> ファイルを編集し、<code>define ( 'WP_AUTO_UPDATE_CORE', false );</code> を追加することで無効にできます。</p><p>Web ジョブを使用して更新する方法もあります。Web ジョブは <strong>wp-db.php</strong> ファイルを監視して、ファイルが更新されるたびに前述の変更を実行します。詳細については、<a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Web ジョブについての紹介記事</a>をご覧ください。</p></div>
**パフォーマンスの向上** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">ARR クッキーの無効化</a> - 複数の Web アプリのインスタンスで WordPress を実行している場合に、パフォーマンスを向上させる方法です。</p></li><li><p>キャッシュを有効にする。<a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis cache</a> (プレビュー) は、<a href="https://wordpress.org/plugins/redis-object-cache/">WordPress プラグインの Redis Object Cache</a> で使用できます。また、<a href="/gallery/store/">Azure ストア</a>から入手できる他のキャッシュ オファリングも使用できます。</p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">WordPress を WinCache で高速化する方法</a> - WinCache は既定で Web アプリ用に有効になっています。</p></li><li><p><a href="../web-sites-scale/">Azure App Service で Web アプリを拡張</a>して <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB の高可用性ルーティング</a>または <a href="http://www.mysql.com/products/cluster/">MySQL クラスター CGE</a> を使用します</p></li></ul>
**ストレージ用 BLOB の使用** | <ol><li><p><a href="../storage-create-storage-account/">Azure のストレージ アカウントの作成</a></p></li><li><p>「<a href="../cdn-how-to-use/">Azure 用 CDN の使用</a>」を理解し、BLOB を用いて地理的に分散したデータ保存を実施します。</p></li><li><p><a href="https://wordpress.org/plugins/windows-azure-storage/">Azure Storage for WordPress プラグイン</a> をインストールし、構成します。</p><p>このプラグインに関する詳細な設定と構成の情報については、「<a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">ユーザー ガイド</a>」をご覧ください。</p> </li></ol>
**電子メールを有効にする** | <ol><li><p><a href="/gallery/store/sendgrid/sendgrid-azure/">Azure ストアで SendGrid を有効にします</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">WordPress 用の SendGrid プラグインをインストールします</a></p></li></ol>
**カスタム ドメイン名を構成する** | [Azure App Service でのカスタム ドメイン名を構成する][customdomain]
**カスタム ドメイン名に対して HTTPS を有効にする** | [Ｗeb アプリに対する HTTPS を Azure App Service で有効にする][httpscustomdomain]
**サイトの負荷分散または地理的な分散** | [Azure Traffic Manager によるトラフィックのルーティング][trafficmanager].カスタム ドメインを使用する場合は、カスタム ドメイン名と Traffic Manager の使用の詳細について、[Azure App Service でのカスタム ドメイン名の構成][customdomain] に関するページをご覧ください。
**自動バックアップを有効にする** | [Azure App Service での Web アプリをバックアップ][バックアップ]
**診断ログの記録を有効にする** | [Azure App Service で Web アプリの診断のログ記録を有効にする][ログ]

## 次のステップ

* [WordPress optimization (WordPress の最適化)](http://codex.wordpress.org/WordPress_Optimization)

* [Azure App Service での WordPress から Multisite への変換](web-sites-php-convert-wordpress-multisite.md)

* [Azure 用の ClearDB アップグレード ウィザード](http://www.cleardb.com/store/azure/upgrade)

* [Azure App Service で Web アプリのサブフォルダーに WordPress をホストする (ブログの投稿)](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [詳細な手順: Azure で WordPress サイトを作成する (ブログの投稿)](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Azure で既存の WordPress ブログをホストする (ブログの投稿)](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Enabling pretty permalinks in WordPress (WordPress で見やすい固定リンクを有効にする)](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [How to migrate and run your WordPress blog on Azure Websites (WordPress ブログを Azure Websites に移行し、運用する方法)](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [WordPress を無料の Azure App Service で実行する方法](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress on Azure in 2 minutes or less (Azure で 2 分以内に WordPress を稼動)](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Moving a WordPress blog to Azure - Part 1: Creating a WordPress blog on Azure (WordPress ブログ Azure に移行する - 第 1 部: Azure で WordPress ブログを作成する)](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Moving a WordPress blog to Azure - Part 2: Transferring your content (WordPress ブログ Azure に移行する - 第 2 部: コンテンツを転送する)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Moving a WordPress blog to Azure - Part 3: Setting up your custom domain (WordPress ブログ Azure に移行する - 第 3 部: カスタム ドメインを設定する)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Moving a WordPress blog to Azure - Part 4: Pretty permalinks and URL Rewrite rules (WordPress ブログ Azure に移行する - 第 4 部: 見やすい固定リンクと URL 書き換えのルール)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Moving a WordPress blog to Azure - Part 5: Moving from a subfolder to the root: (WordPress ブログ Azure に移行する - 第 5 部: サブフォルダーからルートへの移行)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Azure アカウントに WordPress Web アプリを設定する方法](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Propping up WordPress on Azure (Azure で WordPress を設置する)](http://www.johnpapa.net/wordpress-on-azure/)

* [Tips for WordPress on Azure (Azure で WordPress を運用するヒント)](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[アプリ サービスを試す](http://go.microsoft.com/fwlink/?LinkId=523751)」をご覧ください。そこでは、アプリ サービスで有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトからアプリ サービスへの変更ガイドについては、次のものをご覧ください。[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)
* 古いポータルから新しいポータルへの変更ガイドについては、次のものをご覧ください。[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)

[performance-diagram]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: /blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: web-sites-backup.md
[バックアップ]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[ログ]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: virtual-machines-mysql-windows-server-2008r2.md
[mysqllinux]: virtual-machines-linux-mysql-use-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: install-configure-powershell.md
[xplat-cli]: xplat-cli.md
[storesendgrid]: /gallery/store/sendgrid/sendgrid-azure/
[cdn]: cdn-how-to-use.md

<!--HONumber=52-->