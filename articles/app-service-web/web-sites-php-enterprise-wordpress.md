<properties
	pageTitle="Azure App Service のエンタープライズ クラスの WordPress | Microsoft Azure"
	description="Azure App Service にエンタープライズ クラスの WordPress サイトをホストする方法について説明します。"
	services="app-service\web"
	documentationCenter=""
	authors="sunbuild"
	manager="yochayk"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.devlang="php"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="web"
	ms.date="07/06/2016"
	ms.author="sumuth"/>

# Azure App Service のエンタープライズ クラスの WordPress

Azure App Service は、基幹業務系の大規模な [WordPress][wordpress] サイト用に、拡張性のある、安全で使いやすい環境を提供します。Microsoft も [Office][officeblog] ブログや [Bing][bingblog] ブログなどのエンタープライズ クラスのサイトを運営しています。このドキュメントでは、Azure App Service Web Apps を使用して、大量の訪問者を処理できるエンタープライズ クラスの、クラウド ベースの WordPress サイトを確立し、維持する方法について説明しています。

## アーキテクチャと計画

基本的な WordPress インストールの要件は 2 つだけです。

* **MySQL データベース** - [Azure Marketplace に含まれている ClearDB][cdbnstore] を介して利用できるほか、[Windows][mysqlwindows] または [Linux][mysqllinux] を実行する Azure Virtual Machines で独自の MySQL インストールを管理できます。

    > [AZURE.NOTE] ClearDB では複数の MySQL 構成を利用でき、構成ごとにパフォーマンス特性が異なります。Azure ストアで提供されているオファリングの詳細については [Azure ストア][cdbnstore]を、ClearDB が公表しているオファリングについては [ClearDB の料金情報](http://www.cleardb.com/pricing.view)を参照してください。

* **PHP 5.2.4 以上** - Azure App Service は現在、[PHP バージョン 5.4、5.5、5.6][phpwebsite] を提供しています。

	> [AZURE.NOTE] 最新のセキュリティ更新プログラムが適用されるよう、常に最新バージョンの PHP で実行することをお勧めします。

### 基本的なデプロイ

基本要件だけを使用して、Azure リージョン内に基本的なソリューションを作成できます。

![単一の Azure リージョンでホストされている Azure Web アプリと MySQL データベース][basic-diagram]

このデプロイでは、サイトの複数の Web Apps インスタンスを作成することで、アプリケーションをスケールアウトできますが、いずれも特定リージョンのデータ センターにホストされます。このリージョンの外部からアクセスするユーザーがサイトを使用すると、応答時間が長くなることがあります。また、このリージョンのデータ センターがダウンした場合は、アプリケーションもダウンします。


### 複数リージョンのデプロイ

Azure [Traffic Manager][trafficmanager] を使用すると、WordPress サイトを複数のリージョンに展開する一方で、アクセスするユーザーには 1 つの URL のみを提示できます。すべてのユーザーは Traffic Manager を介してサイトにアクセスし、負荷分散構成に基づいて特定のリージョンにルーティングされます。

![Azure の Web アプリは CDBR の高可用性のルーターを使用して、地域間での MySQL にルーティングする複数の地域でホストされています。][multi-region-diagram]

各リージョン内でも、WordPress サイトは複数の Web Apps インスタンス間に展開されることがありますが、こうしたスケーリングはリージョンに固有です。つまり、トラフィックの多いリージョンと少ないリージョンでは、異なるスケーリングが実施されます。

複数の MySQL データベースへのレプリケーションとルーティングは、ClearDB の [CDBR High Availability Router][cleardbscale] \(前掲図を参照) または [MySQL Cluster CGE][cge] を使用して実行できます。

### メディア ストレージとキャッシュを用いる複数リージョンのデプロイ

サイトがアップロードを受け入れている場合、またはメディア ファイルをホストする場合は、Azure BLOB ストレージを使用します。キャッシュが必要な場合は、[Redis Cache][rediscache]、[Memcached Cloud](https://azure.microsoft.com/marketplace/partners/garantiadata/memcached/)、[MemCachier](https://azure.microsoft.com/marketplace/partners/memcachier/memcachier/)、または [Azure ストア](https://azure.microsoft.com/gallery/store/)で取り扱われている別のキャッシュ オファリングの使用を検討してください。

![Azure の Web アプリは、Managed Cache、Blob ストレージ、CDN を使用した CDBR の高可用性のルーターを MySQL に使用し、複数のリージョンでホストされています。][performance-diagram]

BLOB ストレージは、既定では複数のリージョンにまたがって地理的に分散されるため、すべてのサイトにファイルをレプリケートしなくてもかまいません。また、BLOB ストレージ用に Azure [コンテンツ配信ネットワーク (CDN)][cdn] を有効にして、ユーザーに近いエンド ノードへとファイルを配信することもできます。

### 計画

#### その他の要件

目的 | 方法
------------------------|-----------
**大きいファイルのアップロードまたは保存** | [WordPress plugin for using Blob storage (BLOB ストレージを使うための WordPress プラグイン)][storageplugin]
**電子メールの送信** | [SendGrid][storesendgrid]、[SendGrid を使うための WordPress プラグイン][sendgridplugin]についての説明
**カスタム ドメイン名** | [Azure App Service のカスタム ドメイン名の構成][customdomain]
**HTTPS** | [Web アプリに対する HTTPS を Azure App Service で有効にする][httpscustomdomain]
**運用前検証** | [Azure App Service の Web アプリのステージング環境を設定する][staging] <p>Web アプリをステージングから運用に切り替えると、WordPress の構成も変化することに注意してください。ステージング中のアプリを運用段階に切り替える前に、すべての設定が本番アプリの要件に合わせて更新されていることを確認する必要があります。</p>
**監視とトラブルシューティング** | [Azure App Service で Web アプリの診断のログ記録を有効にし][log]、[Azure App Service で Web Apps を監視する][monitor]
**サイトのデプロイ** | [Azure App Service での Web アプリのデプロイ][deploy]

#### 高可用性と障害復旧

目的 | 方法
------------------------|-----------
**サイトの負荷分散**、**サイトの地理的な分散** | [Azure Traffic Manager によるトラフィックのルーティング][trafficmanager]
**バックアップと復元** | [Azure App Service での Web アプリのバックアップ][backup]と [Azure App Service での Web アプリの復元][restore]

#### パフォーマンス

クラウド内のパフォーマンスは、主にキャッシュとスケールアウトによって達成されます。しかし、Web Apps のホスティングに関するメモリや帯域幅などの属性も、考慮に入れる必要があります。

目的 | 方法
------------------------|-----------
**App Service インスタンス機能について学ぶ** | [App Service 階層 の機能と料金の詳細][websitepricing]
**キャッシュ リソース** | [Redis Cache][rediscache]、[Memcached Cloud](https://azure.microsoft.com/marketplace/partners/garantiadata/memcached/)、[MemCachier](https://azure.microsoft.com/marketplace/partners/memcachier/memcachier/)、または [Azure ストア](/gallery/store/)で取り扱われている別のキャッシュ オファリング
**アプリケーションのスケール** | [Azure App Service で Web アプリ][websitescale]と [ClearDB の高可用性ルーティング][cleardbscale]をスケールします。独自の MySQL インストールをホストし、管理する場合は、[MySQL Cluster CGE][cge] によるスケールアウトを検討してください。

#### 移行

既存の WordPress サイトから Azure App Service に移行する方法は、次の 2 種類です。

* **[WordPress][export]** のエクスポート - この方法では、ブログのコンテンツをエクスポートし、次に [WordPress importer プラグイン][import]を使用して、Azure App Service 上の新しい WordPress サイトにインポートします。

	> [AZURE.NOTE] このプロセスでブログのコンテンツは移行できますが、プラグインやテーマ、その他のカスタマイズ内容は移行できません。これらは、改めて手動でインストールする必要があります。

* **手動での移行** - [バックアップしたサイト][wordpressbackup]と[データベース][wordpressdbbackup]を、手動で Azure App Service のWeb アプリとそれに関連付けられた MySQL データベースとして復元します。これにより、高度にカスタマイズされたサイトを移行できると共に、プラグインやテーマ、さらには他のカスタマイズを手動でインストールする手間が大幅に軽減されます。

## 詳細な手順

### 新規 WordPress サイトを作成する

1. [Azure Marketplace][cdbnstore] を使用して、サイトをホストするリージョンに「[アーキテクチャと計画](#planning)」セクションで特定したサイズの MySQL データベースを作成します。

2. 「[Azure App Service での WordPress Web アプリの作成createwordpress][createwordpress]」の手順に従って、新しい WordPress Web アプリを作成します。Web アプリを作成する場合は、**[既存の MySQL データベースを使用します]** を選択し、手順 1 で作成したデータベースを選択します。

既存の WordPress サイトを移行する場合は、新しい Web アプリを作成した後、「[既存の WordPress サイトを Azure に移行する](#Migrate-an-existing-WordPress-site-to-Azure)」を参照してください。

### 既存の WordPress サイトを Azure に移行する

[アーキテクチャと計画](#planning)セクションで説明したように、WordPress サイトは 2 つの方法で移行することができます。

* **エクスポートとインポート** - カスタマイズの量が多くないサイトを移行する場合や、コンテンツだけを移行する場合に実行します。

* **バックアップと復元** - 数多くのカスタマイズが施されたサイトを丸ごと移行する場合に実行します。

以下のセクションのいずれかに従って、サイトを移行します。

#### エクスポートとインポートによる移行

1. [WordPress のエクスポート][export]を実行して、既存のサイトをエクスポートします。

2. [新規 WordPress サイトを作成する](#Create-a-new-WordPress-site)セクションの手順で、新しい Web アプリを作成します

3. Web Apps で WordPress サイトにログインし、**[プラグイン]**、**[新規追加]** の順にクリックします。**WordPress Importer** プラグインを検索し、インストールします。

4. プラグインのインストールが完了したら、**[ツール]**、**[インポート]** の順にクリックして **[WordPress]** を選択し、WordPress Importer プラグインを使用します。

5. **[WordPress のインポート]** ページで、**[ファイルの選択]** を選択します。既存の WordPress サイトからエクスポートした WXR ファイルを探して、**[ファイルのアップロードとインポート]** を選択します。

6. **[Submit]** をクリックします。インポートに成功したことを示すメッセージが表示されます。

8. これらのすべての手順が完了したら、[Azure ポータル][mgmtportal]の Web アプリのブレードからサイトを再起動します。

サイトをインポートした後に、以下の手順を実行して、インポート ファイルに含まれない設定を有効にすることが必要になる場合があります。

使用している要素 | これを行うには、次の手順を実行します。
------------------ | ----------
**固定リンク** | 新しいサイトの WordPress ダッシュボードで、**[設定]**、**[固定リンク]** の順にクリックし、固定リンクの構造を更新します。
**イメージまたはメディア リンク** | リンク先を新しい場所に更新するには、検索置換ツールの [Velvet Blues Update URLs プラグイン][velvet]を使用するか、データベースを直接修正します。
**テーマ** | **[外観]** から **[テーマ]** に移動し、必要に応じてサイトのテーマを更新します。
**メニュー** | 使用しているテーマがメニューをサポートしている場合は、ホーム ページへのリンクに使用していないサブディレクトリが残っていることがあります。**[外観]** から **[メニュー]** に移動して、更新します。

#### バックアップと復元による移行

1. 「[WordPress Backups (WordPress のバックアップ)][wordpressbackup]」の情報を参照して、既存の WordPress サイトをバックアップします。

2. 「[Backing Up Your Database (データベースのバックアップ)][wordpressdbbackup]」の情報を参照して、既存のデータベースをバックアップします。

3. 新しいデータベースを作成し、バックアップを復元します。

	1. [Azure Marketplace][cdbnstore]で新しいデータベースを購入するか、[Windows][mysqlwindows] または [Linux][mysqllinux] VM で MySQL データベースを設定します。

	2. [MySQL Workbench][workbench] などの MySQL クライアントを使用して、新しいデータベースに接続し、WordPress データベースをインポートします。

	3. データベースを更新して、ドメイン エントリを"mywordpress.azurewebsites.net" などのように、新しい Azure App Service のドメインに変更します。[WordPress データベースの検索と置換用スクリプト][searchandreplace]を使用すると、すべてのインスタンスを安全に変更できます。

4. Azure ポータルで新しい Web アプリを作成し、WordPress のバックアップを発行します。

	1. データベースで **[新規]**、**[Web + モバイル]**、**[Azure Marketplace]**、**[Web Apps]**、**[Web アプリ + SQL]** (または **[Web アプリ + MySQL]**)、**[作成]** を使用して [Azure ポータル][mgmtportal]に新しい Web アプリを作成します。必要なすべての設定を構成して、空の Web アプリを作成します。

	2. WordPress バックアップで **wp-config.php** ファイルを見つけて、エディターで開きます。以下のエントリを新しい MySQL データベースの情報に変更します。

		* **DB\_NAME** - データベースのユーザー名

		* **DB\_USER** - データベースへのアクセスに使用するユーザー名

		* **DB\_PASSWORD** - ユーザーのパスワード

		これらのエントリを変更したら、**wp-config.php** ファイルを保存して閉じます。

	3. 「[Azure App Service での Web アプリのデプロイ][deploy]」の情報を使用して、使用したいデプロイ方法を有効にし、WordPress バックアップを Azure App Service の Web アプリにデプロイします。

5. WordPress サイトがデプロイされると、サイトの *.azurewebsite.net という URL を使用して新しいサイト (App Service Web アプリとして) アクセスできるようになります。

### サイトの構成

WordPress サイトを作成、移行した後は、以下の情報を参照して、パフォーマンスの向上と追加機能の有効化を行います。

目的 | 方法
------------- | -----------
**App Service プランのモード、サイズを設定し、スケーリングを有効化する** | [Azure App Service での Web アプリのスケール][websitescale]
**永続的なデータベース接続の有効化** <p>既定では、WordPress は永続的なデータベース接続を使用しないため、複数接続を実行すると、データベースへの接続が制限される場合があります。</p> | <ol><li><p><strong>wp-includes/wp-db.php</strong> ファイルを編集します。</p></li><li><p>。次の行を見つけます。</p><code>$this->dbh = mysql\_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new\_link, $client\_flags );</code></li><li><p>前の行を次の行に置き換えます。</p><code>$this->dbh = mysql\_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword, $client\_flags ); <br/>if ( false !== $error\_reporting ) { /br/>&nbsp;&nbsp;error\_reporting( $error\_reporting ); <br/>} </code></li><li><p>次の行を見つけます。</p><code>$this->dbh = @mysql\_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new\_link, $client\_flags ); </code></li><li><p>上記の行を次の行に置き換えます。</p><code>$this->dbh = @mysql\_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword, $client\_flags ); </code></li><li><p><strong>wp-includes/wp-db.php</strong> ファイルを保存し、サイトを再デプロイします。</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><p>これらの変更は、WordPress が更新されると上書きされる可能性があります。</p><p>。WordPress の既定値である自動更新は、<strong>wp-config.php</strong> ファイルを編集し、 <code>define ( 'WP\_AUTO\_UPDATE\_CORE', false );</code></p><p> を追加することで無効化できます。更新プログラムをアドレス指定する別の方法として、<strong>wp db.php</strong> ファイルを監視する Web ジョブ を使用し、ファイルが更新されるたびに、上記の変更を実行する方法があります。詳細については、[WebJobs についての紹介記事](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)をご覧ください。</p></div>
**パフォーマンスの向上** | <ul><li><p>[ARR クッキーの無効化](http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx) - 複数の Web Apps インスタンス </p></li><li><p>キャッシュの有効化で WordPress を実行している場合に、パフォーマンスを向上させる方法です。[Redis Cache](http://msdn.microsoft.com/library/azure/dn690470.aspx) は、[WordPress プラグインの Redis Object Cache](https://wordpress.org/plugins/redis-object-cache/) で使用できます。また、[Azure ストア](/gallery/store/)から入手できる他のキャッシュ オファリングも使用できます。</p></li><li><p>[WordPress を WinCache で高速化する方法](http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/) - WinCache は既定で Web Apps 用に有効になっています。</p></li><li><p>[Azure App Service の Web の規模の設定](../app-service-web/web-sites-scale.md)を実施し、[ClearDB High Availability ルーティング](http://www.cleardb.com/developers/cdbr/introduction)か [MySQL Cluster CGE](http://www.mysql.com/products/cluster/)</p></li></ul> を使用します。
**ストレージ用 BLOB の使用** | <ol><li><p>[Azure のストレージ アカウントの作成](../storage/storage-create-storage-account.md)</p></li><li><p>[Content Delivery Network (CDN) を使用する][cdn] 方法を理解し、BLOB を用いて地理的に分散したデータ保存を実施します。</p></li><li><p>[Azure Storage for WordPress プラグイン](https://wordpress.org/plugins/windows-azure-storage/)をインストールし、構成します。</p><p>このプラグインに関する詳細な設定と構成の情報については、[ユーザー ガイド](http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx)をご覧ください。</p> </li></ol>
**電子メールの有効化** | <ol><li><p>[Azure ストアで SendGrid を有効にします。](/gallery/store/sendgrid/sendgrid-azure/)</p></li><li><p>[WordPress 用の SendGrid プラグインをインストールします。](http://wordpress.org/plugins/sendgrid-email-delivery-simplified/)</p></li></ol>
**カスタム ドメイン名の構成** | [Azure App Service のカスタム ドメイン名の構成][customdomain]
**カスタム ドメイン名に対する HTTPS の有効化** | [Web アプリに対する HTTPS を Azure App Service で有効にする][httpscustomdomain]
**サイトの負荷分散、または地理的な分散** | [Azure Traffic Manager によるトラフィックのルーティング][trafficmanager]。カスタム ドメインを使用する場合は、カスタム ドメイン名と Traffic Manager の使用の詳細について、[Azure App Service でのカスタム ドメイン名の構成][customdomain]に関するページをご覧ください。
**自動化されたバックアップを有効にする** | [Azure App Service での Web アプリのバックアップ][backup]
**診断ログの有効化** | [Azure App Service の Web アプリの診断ログの有効化][log]

## 次のステップ

* [WordPress の最適化](http://codex.wordpress.org/WordPress_Optimization)

* [Azure App Service での WordPress から Multisite への変換](web-sites-php-convert-wordpress-multisite.md)

* [Azure 用の ClearDB アップグレード ウィザード](http://www.cleardb.com/store/azure/upgrade)

* [Azure App Service で Web アプリのサブフォルダーに WordPress をホストする (ブログの投稿)](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Step-By-Step: Create a WordPress site using Azure (ステップ バイ ステップ: Azure で WordPress サイトを作成する)](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Host your existing WordPress blog on Azure (Azure で既存の WordPress ブログをホストする)](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Enabling pretty permalinks in WordPress (WordPress で見やすい固定リンクを有効にする)](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [How to migrate and run your WordPress blog on Azure Websites (Azure App Service の WordPress ブログを Azure Websites に移行し、運用する方法)](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [WordPress を無料の Azure App Service で実行する方法](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress on Azure in 2 minutes or less (Azure で 2 分以内に WordPress を稼動)](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Moving a WordPress blog to Azure - Part 1: Creating a WordPress blog on Azure (WordPress ブログを Azure に移転する - パート 1: Azure で WordPress ブログを作成)](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Moving a WordPress blog to Azure - Part 2: Transferring your content (WordPress ブログを Azure に移転する - パート 2: コンテンツの転送)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Moving a WordPress blog to Azure - Part 3: Setting up your custom domain (WordPress ブログを Azure に移転する - パート 3: カスタム ドメインの設定)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Moving a WordPress blog to Azure - Part 4: Pretty permalinks and URL Rewrite rules (WordPress ブログを Azure に移転する - パート 4: 見やすい固定リンクと URL 書き換えのルール)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Moving a WordPress blog to Azure - Part 5: Moving from a subfolder to the root (WordPress ブログを Azure に移転する - パート 5: サブフォルダーからルートへの移動)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Azure アカウントに WordPress Web アプリを設定する方法](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Propping up WordPress on Azure (Azure で WordPress を設置する)](http://www.johnpapa.net/wordpress-on-azure/)

* [Tips for WordPress on Azure (Azure で WordPress を運用するヒント)](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md
[mysqllinux]: ../virtual-machines/virtual-machines-linux-classic-mysql-on-opensuse.md
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
[posh]: ../powershell-install-configure.md
[Azure CLI]: ../xplat-cli-install.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md

<!---HONumber=AcomDC_0713_2016-->