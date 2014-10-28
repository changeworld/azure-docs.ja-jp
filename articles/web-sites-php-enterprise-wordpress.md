<properties title="Enterprise class WordPress on Azure Websites" pageTitle="Enterprise class WordPress on Azure Websites" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

## エンタープライズ クラスの WordPress を Azure Websites で運用する

Azure Websites は、基幹業務系の大規模な [WordPress][WordPress] サイト用に、拡張性のある、安全で使いやすい環境を提供します。Microsoft も [Office][Office] ブログや [Bing][Bing] ブログなどのエンタープライズ クラスのサイトを運営しています。このドキュメントでは、Azure Websites を使用して、クラウドベースで大量のアクセスを処理できる能力を備えたエンタープライズ クラスの WordPress サイトを構築し、管理する方法について説明します。

## この記事の内容

-   [アーキテクチャと計画][アーキテクチャと計画] - サイトの作成に先立って、アーキテクチャ、要件、パフォーマンスに関する考慮事項について説明します。

-   [方法][方法] - サイトの作成、デプロイ、構成方法を示します。

-   [その他のリソース][その他のリソース] - 他のリソースと情報を示します。

## <span id="plan"></span></a>アーキテクチャと計画

基本的な WordPress インストールの要件は 2 つだけです。

-   **MySQL データベース** - [Azure ストアに含まれている ClearDB][Azure ストアに含まれている ClearDB] を介して利用できるほか、[Windows][Windows] または [Linux][Linux] を実行する Azure Virtual Machines で独自の MySQL インストールを管理できます。

    > [WACOM.NOTE] ClearDB では複数の MySQL 構成を利用でき、構成ごとにパフォーマンス特性が異なります。Azure ストアで提供されているオファリングの詳細については [Azure ストア][Azure ストアに含まれている ClearDB]を、ClearDB が公表しているオファリングについては [ClearDB の料金情報][ClearDB の料金情報]を参照してください。

-   **PHP 5.2.4 以降** - 現在、Azure Websites では [PHP Version 5.3、5.4、5.5][PHP Version 5.3、5.4、5.5] が利用できます。

    > [WACOM.NOTE] 最新のセキュリティ更新プログラムが適用されるよう、常に最新バージョンの PHP で実行することをお勧めします。

### 基本的なデプロイメント

基本要件だけを使用して、Azure のリージョン内に基本的なソリューションを作成できます。

![an Azure Website and MySQL Database hosted in a single Azure region][an Azure Website and MySQL Database hosted in a single Azure region]

このデプロイでは、Websites のインスタンスを複数作成することで、アプリケーションをスケール アウトできますが、いずれも特定リージョンのデータ センターにホストされます。このリージョンの外部からアクセスするユーザーがサイトを使用すると、応答時間が長くなることがあります。また、このリージョンのデータ センターがダウンした場合は、アプリケーションもダウンします。

### 複数リージョンのデプロイメント

Azure [Traffic Manager][Traffic Manager] を使用すると、WordPress サイトを複数のリージョンに展開する一方で、アクセスするユーザーには 1 つの URL のみを提示できます。すべてのユーザーは Traffic Manager を介してサイトにアクセスし、負荷分散構成に基づいて特定のリージョンにルーティングされます。

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions]

各リージョン内でも、WordPress サイトは複数の Web サイト インスタンス間に展開されることがありますが、こうしたスケーリングはリージョンに固有です。つまり、トラフィックの多いリージョンと少ないリージョンでは、異なるスケーリングが実施されます。

複数の MySQL データベースへのレプリケーションとルーティングは、ClearDB の [CDBR High Availability Router][CDBR High Availability Router] (前掲図を参照) または [MySQL Cluster CGE][MySQL Cluster CGE] を使用して実行できます。

### メディア ストレージとキャッシュを用いる複数リージョンのデプロイメント

サイトがアップロードを受け入れている場合、またはメディア ファイルをホストする場合は、Azure BLOB ストレージを使用します。キャッシュが必要な場合は、[Redis Cache][Redis Cache]、[Memcached Cloud][Memcached Cloud]、[MemCachier][MemCachier]、または [Azure ストア][Azure ストア]で取り扱われている別のキャッシュ オファリングの使用を検討してください。

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN]

BLOB ストレージは、既定では複数のリージョンにまたがって地理的に分散されるため、すべてのサイトにファイルをレプリケートしなくてもかまいません。また、BLOB ストレージ用に Azure [コンテンツ配信ネットワーク (CDN)][コンテンツ配信ネットワーク (CDN)] を有効にして、ユーザーに近いエンド ノードへとファイルを配信することもできます。

### 計画

#### その他の要件

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">目的</th>
<th align="left">方法</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>大きいファイルのアップロードまたは保存</strong></td>
<td align="left"><a href="https://wordpress.org/plugins/windows-azure-storage/">WordPress plugin for using Blob storage (BLOB ストレージを使うための WordPress プラグイン)</a></td>
</tr>
<tr class="even">
<td align="left"><strong>電子メールの送信</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ja-jp/gallery/store/sendgrid/sendgrid-azure/">SendGrid</a>、<a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">SendGrid を使うための WordPress プラグイン</a>についての説明</td>
</tr>
<tr class="odd">
<td align="left"><strong>カスタム ドメイン名</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-custom-domain-name/">Azure の Web Site のカスタム ドメイン名の構成</a></td>
</tr>
<tr class="even">
<td align="left"><strong>HTTPS</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/">Azure の Web サイトでの HTTPS の有効化</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>運用前検証</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-staged-publishing/">Microsoft Azure の Web サイトのステージングされた展開</a>
<p>サイトをステージングから運用に切り替えると、WordPress の構成も変化することに注意してください。ステージング中のサイトを運用段階に切り替える前に、すべての設定が本番サイトの要件に合わせて更新されていることを確認する必要があります。</p></td>
</tr>
<tr class="even">
<td align="left"><strong>監視とトラブルシューティング</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-enable-diagnostic-log/">Azure の Web サイトの診断ログを有効にする</a>、<a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-monitor/">Web サイトの監視方法</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>サイトのデプロイ</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-deploy/">Azure の Web サイトのデプロイ方法</a></td>
</tr>
</tbody>
</table>

#### 高可用性と障害復旧

| 目的                                           | 方法                                                                           |
|------------------------------------------------|--------------------------------------------------------------------------------|
| **サイトの負荷分散**、**サイトの地理的な分散** | [Azure Traffic Manager によるトラフィックのルーティング][Traffic Manager]      |
| **バックアップと復元**                         | [Azure の Web サイトのバックアップ][Azure の Web サイトのバックアップ]、[Microsoft Azure の Web サイトの復元][Microsoft Azure の Web サイトの復元] |

#### パフォーマンス

クラウド内のパフォーマンスは、主にキャッシュとスケール アウトによって達成されます。しかし、Web サイトのホスティングに関するメモリや帯域幅などの属性も、考慮に入れる必要があります。

| 目的                                        | 方法                                                                                                                                                                                                                          |
|---------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Web サイト インスタンス機能について学ぶ** | [Websites の各サイズやモードでの機能と料金の詳細][Websites の各サイズやモードでの機能と料金の詳細]                                                                                                                                                                           |
| **キャッシュ リソース**                     | [Redis Cache][Redis Cache]、[Memcached Cloud][Memcached Cloud]、[MemCachier][MemCachier]、または [Azure ストア][Azure ストア]で取り扱われている別のキャッシュ オファリング                                                                                                    |
| **アプリケーションのスケール**              | [Web サイトの規模の設定方法][Web サイトの規模の設定方法]、[ClearDB High Availability ルーティング][CDBR High Availability Router]。独自の MySQL インストールをホストし、管理する場合は、[MySQL Cluster CGE][MySQL Cluster CGE] によるスケール アウトを検討してください。 |

#### 移行

既存の WordPress サイトから Azure Websites に移行する方法は、次の 2 種類です。

-   **[WordPress のエクスポート][WordPress のエクスポート]** - この方法では、ブログのコンテンツをエクスポートし、次に [WordPress Importer プラグイン][WordPress Importer プラグイン]を使用して、Azure 上の新しい WordPress サイトにインポートします。

    > [WACOM.NOTE] このプロセスでブログのコンテンツは移行できますが、プラグインやテーマ、その他のカスタマイズ内容は移行できません。これらは、改めて手動でインストールする必要があります。

-   **手動での移行** - [バックアップしたサイト][バックアップしたサイト]と[データベース][データベース]を、手動で Azure Websites のサイトとそれに関連付けられた MySQL データベースとして復元します。これにより、高度にカスタマイズされたサイトを移行できると共に、プラグインやテーマ、さらには他のカスタマイズを手動でインストールする手間が大幅に軽減されます。

## 方法

### <span id="create"></span></a>新規 WordPress サイトを作成する

1.  [Azure ストア][Azure ストアに含まれている ClearDB]を使用して、サイトをホストするリージョンに「[アーキテクチャと計画][アーキテクチャと計画]」セクションで特定したサイズの MySQL データベースを作成します。

2.  「[Azure でギャラリーから WordPress Web サイトを作成する][Azure でギャラリーから WordPress Web サイトを作成する]」の手順で、新規 WordPress サイトを作成します。サイトを作成する場合は、**[既存の MySQL データベースを使用します]** を選択し、手順 1. で作成したデータベースを選択します。

既存の WordPress サイトを移行する場合は、新しいサイトを作成した後、「[既存の WordPress サイトを Azure に移行する][既存の WordPress サイトを Azure に移行する]」を参照してください。

### <span id="migrate"></span></a>既存の WordPress サイトを Azure に移行する

「[アーキテクチャと計画][アーキテクチャと計画]」セクションで説明したように、WordPress Web サイトは 2 つの方法で移行することができます。

-   **エクスポートとインポート** - カスタマイズの量が多くないサイトを移行する場合や、コンテンツだけを移行する場合に実行します。

-   **バックアップと復元** - 数多くのカスタマイズが施されたサイトを丸ごと移行する場合に実行します。

以下のセクションのいずれかに従って、サイトを移行します。

#### エクスポートとインポートによる移行

1.  [WordPress のエクスポート][WordPress のエクスポート]を実行して、既存のサイトをエクスポートします。

2.  「[新規 WordPress サイトを作成する][新規 WordPress サイトを作成する]」セクションの手順で、新しい Web サイトを作成します。

3.  Azure Websites で WordPress サイトにログインし、**[プラグイン]**、**[新規追加]** の順にクリックします。**WordPress Importer** プラグインを検索し、インストールします。

4.  プラグインのインストールが完了したら、**[ツール]**、**[インポート]** の順にクリックして **\[WordPress]** を選択し、WordPress Importer プラグインを使用します。

5.  **[WordPress のインポート]** ページで、**[ファイルの選択]** を選択します。既存の WordPress サイトからエクスポートした WXR ファイルを探して、**[ファイルのアップロードとインポート]** を選択します。

6.  **[Submit]** をクリックします。インポートに成功したことを示すメッセージが表示されます。

7.  以上の手順をすべて完了したら、[Azure 管理ポータル][Azure 管理ポータル]のダッシュボードから対象の Web サイトを再起動します。

サイトをインポートした後に、以下の手順を実行して、インポート ファイルに含まれない設定を有効にすることが必要になる場合があります。

| 使用している要素                  | これを行うには、次の手順を実行します。                                                                                                                                                           |
|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **固定リンク**                    | 新しいサイトの WordPress ダッシュボードで、**[設定]**、**[固定リンク]** の順にクリックし、固定リンクの構造を更新します。                                                                         |
| **イメージまたはメディア リンク** | リンク先を新しい場所に更新するには、検索置換ツールの [Velvet Blues Update URLs プラグイン][Velvet Blues Update URLs プラグイン]を使用するか、データベースを直接修正します。                                                         |
| **テーマ**                        | **[外観]** から **[テーマ]** に移動し、必要に応じて Web サイトのテーマを更新します。                                                                                                             |
| **メニュー**                      | 使用しているテーマがメニューをサポートしている場合は、ホーム ページへのリンクに使用していないサブディレクトリが残っていることがあります。**[外観]** から **[メニュー]** に移動して、更新します。 |

#### バックアップと復元による移行

1.  「[WordPress Backups (WordPress のバックアップ)][バックアップしたサイト]」の情報を参照して、既存の WordPress をバックアップします。

2.  「[Backing Up Your Database (データベースのバックアップ)][データベース]」の情報を参照して、既存のデータベースをバックアップします。

3.  新しいデータベースを作成し、バックアップを復元します。

    1.  [Azure ストア][Azure ストアに含まれている ClearDB]で新しいデータベースを購入するか、[Windows][Windows] または [Linux][Linux] VM で MySQL データベースを設定します。

    2.  [MySQL Workbench][MySQL Workbench] などの MySQL クライアントを使用して、新しいデータベースに接続し、WordPress データベースをインポートします。

    3.  データベースを更新して、ドメイン エントリを "mywordpress.azurewebsites.net" などのように、新しい Azure Web サイトのドメインに変更します。[WordPress データベースの検索と置換用スクリプト][WordPress データベースの検索と置換用スクリプト]を使用すると、すべてのインスタンスを安全に変更できます。

4.  新しい Web サイトを作成し、WordPress バックアップを発行します。

    1.  [Azure の管理ポータル][Azure 管理ポータル]で、**[新規]**、**[Web サイト]**、**[カスタム作成]** の順にクリックし、データベースと共に新しい Web サイトを作成します。これにより、空のサイトが作成されます。

    2.  WordPress バックアップで **wp-config.php** ファイルを見つけて、エディターで開きます。以下のエントリを新しい MySQL データベースの情報に変更します。

        -   **DB\_NAME** - データベースのユーザー名

        -   **DB\_USER** - データベースへのアクセスに使用するユーザー名

        -   **DB\_PASSWORD** - ユーザーのパスワード

        これらのエントリを変更したら、**wp-config.php** ファイルを保存して閉じます。

    3.  「[Azure の Web サイトのデプロイ方法][Azure の Web サイトのデプロイ方法]」の情報を参照して、利用するデプロイメント方法を有効にし、WordPress バックアップを Azure で作成した Web サイトにデプロイします。

5.  WordPress サイトのデプロイが完了したら、その新しいサイトに \*.azurewebsite.net という形式の URL でアクセスできるようになります。

### サイトの構成

WordPress サイトを作成、移行した後は、以下の情報を参照して、パフォーマンスの向上と追加機能の有効化を行います。

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">目的</th>
<th align="left">方法</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>Web サイトのモードやサイズの設定、スケーリングの有効化</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-scale/">Web サイトの規模の設定方法</a></td>
</tr>
<tr class="even">
<td align="left"><strong>永続的なデータベース接続の有効化</strong>
<p>既定では、WordPress は永続的なデータベース接続を使用しないため、複数接続を実行すると、データベースへの接続が制限される場合があります。</p></td>
<td align="left"><ol>
<li><p><strong>wp-includes/wp-db.php</strong> ファイルを編集します。</p></li>
<li><p>次の行を見つけます。</p>
<p><code data-inline="1">$this-&gt;dbh = mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags );</code></p></li>
<li><p>この行を次の行で置き換えます。</p>
<p><code>$this-&gt;dbh = mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); if ( false !== $error_reporting ) { /br/&gt;  error_reporting( $error_reporting ); } </code></p></li>
<li><p>次の行を見つけます。</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags ); </code></p></li>
<li><p>この行を次の行で置き換えます。</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); </code></p></li>
<li><p><strong>wp-includes/wp-db.php</strong> ファイルを保存して、サイトを再デプロイします。</p></li>
</ol>
<div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>注: </h5><p>これらの変更は、WordPress の更新時に上書きされます。</p><p>WordPress は既定で自動更新を行いますが、この設定は <strong>wp-config.php</strong> ファイルを編集し<code data-inline="1">define ( 'WP_AUTO_UPDATE_CORE', false );</code> を追加することで、無効にできます。</p><p>または、WebJob を使用して更新に対応する方法もあります。WebJob は <strong>wp-db.php</strong> ファイルを監視して、ファイルが更新されるたびに前述の変更を実行します。詳細については、<a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">WebJobs についての紹介記事</a>を参照してください。</p></div></td>
</tr>
<tr class="odd">
<td align="left"><strong>パフォーマンスの向上</strong></td>
<td align="left"><ul>
<li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">ARR クッキーの無効化</a> - 複数の Websites のインスタンスで WordPress を実行している場合に、パフォーマンスを向上させる方法です。</p></li>
<li><p>キャッシュの有効化。<a href="http://msdn.microsoft.com/ja-jp/library/azure/dn690470.aspx">Redis Cache</a> (プレビュー) は、<a href="https://wordpress.org/plugins/redis-object-cache/">WordPress プラグインの Redis Object Cache</a> で使用できます。また、<a href="http://azure.microsoft.com/ja-jp/gallery/store/">Azure ストア</a>から入手できる他のキャッシュ オファリングも使用できます。</p></li>
<li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">WordPress を WinCache で高速化する方法</a> - WinCache は既定で Websites 用に有効になっています。</p></li>
<li><p><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-scale/">Web サイトの規模の設定</a>を実施し、<a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB High Availability ルーティング</a>または <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a> を使用します。</p></li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><strong>ストレージ用 BLOB の使用</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/ja-jp/documentation/articles/storage-create-storage-account/">Azure のストレージ アカウントの作成</a></p></li>
<li><p>「<a href="http://azure.microsoft.com/ja-jp/documentation/articles/cdn-how-to-use/">Azure 用 CDN の使用</a>」の方法を理解し、BLOB を用いて地理的に分散したデータ保存を実施します。</p></li>
<li><p><a href="https://wordpress.org/plugins/windows-azure-storage/">Azure Storage for WordPress プラグイン</a>をインストールし、構成します。</p>
<p>このプラグインに関する詳細な設定と構成の情報については、<a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">ユーザー ガイド</a>を参照してください。</p></li>
</ol></td>
</tr>
<tr class="odd">
<td align="left"><strong>電子メールの有効化</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/ja-jp/gallery/store/sendgrid/sendgrid-azure/">Azure ストアで SendGrid を有効にします。</a></p></li>
<li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">WordPress 用の SendGrid プラグインをインストールします。</a></p></li>
</ol></td>
</tr>
<tr class="even">
<td align="left"><strong>カスタム ドメイン名の構成</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-custom-domain-name/">Azure Web サイトでのカスタム ドメイン名の使用</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>カスタム ドメイン名に対する HTTPS の有効化</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/">Azure Web サイトでの HTTPS の使用</a></td>
</tr>
<tr class="even">
<td align="left"><strong>サイトの負荷分散、または地理的な分散</strong></td>
<td align="left"><a href="http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/">Azure Traffic Manager によるトラフィックのルーティング</a>。カスタム ドメインを使用する場合は、カスタム ドメイン名と Traffic Manager の使用の詳細について、<a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-custom-domain-name/">Azure Web サイトでのカスタム ドメイン名の使用</a>に関するページを参照してください。</td>
</tr>
<tr class="odd">
<td align="left"><strong>自動化された Web サイトのバックアップの有効化</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-backup/">Azure の Web サイトのバックアップ</a></td>
</tr>
<tr class="even">
<td align="left"><strong>診断ログの有効化</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-enable-diagnostic-log/">Azure の Web サイトの診断ログを有効にする</a></td>
</tr>
</tbody>
</table>

## [][]その他のリソース

-   [WordPress の最適化][WordPress の最適化]

-   [WordPress サイトの Multisite への変換][WordPress サイトの Multisite への変換]

-   [Azure 用の ClearDB アップグレード ウィザード][Azure 用の ClearDB アップグレード ウィザード]

-   [Azure Web サイトのサブフォルダーに WordPress をホスティングする方法][Azure Web サイトのサブフォルダーに WordPress をホスティングする方法]

-   [Step-By-Step: Create a WordPress site using Azure (ステップ バイ ステップ: Azure で WordPress サイトを作成する)][Step-By-Step: Create a WordPress site using Azure (ステップ バイ ステップ: Azure で WordPress サイトを作成する)]

-   [Host your existing WordPress blog on Azure (Azure で既存の WordPress ブログをホストする)][Host your existing WordPress blog on Azure (Azure で既存の WordPress ブログをホストする)]

-   [Enabling pretty permalinks in WordPress (WordPress で見やすい固定リンクを有効にする)][Enabling pretty permalinks in WordPress (WordPress で見やすい固定リンクを有効にする)]

-   [How to migrate and run your WordPress blog on Azure Websites (WordPress ブログを Azure Websites に移行し、運用する方法)][How to migrate and run your WordPress blog on Azure Websites (WordPress ブログを Azure Websites に移行し、運用する方法)]

-   [How to run WordPress on Azure Websites for free (Azure Websites で、無料で WordPress を運用する方法)][How to run WordPress on Azure Websites for free (Azure Websites で、無料で WordPress を運用する方法)]

-   [WordPress on Azure in 2 minutes or less (Azure で 2 分以内に WordPress を稼動)][WordPress on Azure in 2 minutes or less (Azure で 2 分以内に WordPress を稼動)]

-   [Moving a WordPress blog to Azure - Part 1: Creating a WordPress blog on Azure (WordPress ブログを Azure に移転する - パート 1: Azure で WordPress ブログを作成)][Moving a WordPress blog to Azure - Part 1: Creating a WordPress blog on Azure (WordPress ブログを Azure に移転する - パート 1: Azure で WordPress ブログを作成)]

-   [Moving a WordPress blog to Azure - Part 2: Transferring your content (WordPress ブログを Azure に移転する - パート 2: コンテンツの転送)][Moving a WordPress blog to Azure - Part 2: Transferring your content (WordPress ブログを Azure に移転する - パート 2: コンテンツの転送)]

-   [Moving a WordPress blog to Azure - Part 3: Setting up your custom domain (WordPress ブログを Azure に移転する - パート 3: カスタム ドメインの設定)][Moving a WordPress blog to Azure - Part 3: Setting up your custom domain (WordPress ブログを Azure に移転する - パート 3: カスタム ドメインの設定)]

-   [Moving a WordPress blog to Azure - Part 4: Pretty permalinks and URL Rewrite rules (WordPress ブログを Azure に移転する - パート 4: 見やすい固定リンクと URL 書き換えのルール)][Moving a WordPress blog to Azure - Part 4: Pretty permalinks and URL Rewrite rules (WordPress ブログを Azure に移転する - パート 4: 見やすい固定リンクと URL 書き換えのルール)]

-   [Moving a WordPress blog to Azure - Part 5: Moving from a subfolder to the root (WordPress ブログを Azure に移転する - パート 5: サブフォルダーからルートへの移動)][Moving a WordPress blog to Azure - Part 5: Moving from a subfolder to the root (WordPress ブログを Azure に移転する - パート 5: サブフォルダーからルートへの移動)]

-   [Azure アカウントで WordPress Web サイトを設定する方法][Azure アカウントで WordPress Web サイトを設定する方法]

-   [Propping up WordPress on Azure (Azure で WordPress を設置する)][Propping up WordPress on Azure (Azure で WordPress を設置する)]

-   [Tips for WordPress on Azure (Azure で WordPress を運用するヒント)][Tips for WordPress on Azure (Azure で WordPress を運用するヒント)]

  [WordPress]: http://www.microsoft.com/web/wordpress
  [Office]: http://blogs.office.com/
  [Bing]: http://blogs.bing.com/
  [アーキテクチャと計画]: #planning
  [方法]: #howto
  [その他のリソース]: #resources
  [Azure ストアに含まれている ClearDB]: http://www.cleardb.com/store/azure
  [Windows]: http://azure.microsoft.com/ja-jp/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
  [Linux]: http://azure.microsoft.com/ja-jp/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
  [ClearDB の料金情報]: http://www.cleardb.com/pricing.view
  [PHP Version 5.3、5.4、5.5]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-php-configure/
  [an Azure Website and MySQL Database hosted in a single Azure region]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
  [Traffic Manager]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
  [an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
  [CDBR High Availability Router]: http://www.cleardb.com/developers/cdbr/introduction
  [MySQL Cluster CGE]: http://www.mysql.com/products/cluster/
  [Redis Cache]: http://msdn.microsoft.com/ja-jp/library/azure/dn690470.aspx
  [Memcached Cloud]: http://azure.microsoft.com/ja-jp/gallery/store/garantiadata/memcached/
  [MemCachier]: http://azure.microsoft.com/ja-jp/gallery/store/memcachier/memcachier/
  [Azure ストア]: http://azure.microsoft.com/ja-jp/gallery/store/
  [an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
  [コンテンツ配信ネットワーク (CDN)]: http://azure.microsoft.com/ja-jp/documentation/articles/cdn-how-to-use/
  [WordPress plugin for using Blob storage (BLOB ストレージを使うための WordPress プラグイン)]: https://wordpress.org/plugins/windows-azure-storage/
  [SendGrid]: http://azure.microsoft.com/ja-jp/gallery/store/sendgrid/sendgrid-azure/
  [SendGrid を使うための WordPress プラグイン]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
  [Azure の Web Site のカスタム ドメイン名の構成]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-custom-domain-name/
  [Azure の Web サイトでの HTTPS の有効化]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/
  [Microsoft Azure の Web サイトのステージングされた展開]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-staged-publishing/
  [Azure の Web サイトの診断ログを有効にする]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-enable-diagnostic-log/
  [Web サイトの監視方法]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-monitor/
  [Azure の Web サイトのデプロイ方法]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-deploy/
  [Azure の Web サイトのバックアップ]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-backup/
  [Microsoft Azure の Web サイトの復元]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-restore/
  [Websites の各サイズやモードでの機能と料金の詳細]: https://azure.microsoft.com/ja-jp/pricing/details/web-sites/
  [Web サイトの規模の設定方法]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-scale/
  [WordPress のエクスポート]: http://en.support.wordpress.com/export/
  [WordPress Importer プラグイン]: http://wordpress.org/plugins/wordpress-importer/
  [バックアップしたサイト]: http://codex.wordpress.org/WordPress_Backups
  [データベース]: http://codex.wordpress.org/Backing_Up_Your_Database
  [Azure でギャラリーから WordPress Web サイトを作成する]:  "http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-php-web-site-gallery/"
  [既存の WordPress サイトを Azure に移行する]: #migrate
  [新規 WordPress サイトを作成する]: #create
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [Velvet Blues Update URLs プラグイン]: https://wordpress.org/plugins/velvet-blues-update-urls/
  [MySQL Workbench]: http://www.mysql.com/products/workbench/
  [WordPress データベースの検索と置換用スクリプト]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
  [WebJobs についての紹介記事]: http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
  [ARR クッキーの無効化]: http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx
  [WordPress プラグインの Redis Object Cache]: https://wordpress.org/plugins/redis-object-cache/
  [WordPress を WinCache で高速化する方法]: http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/
  [Azure のストレージ アカウントの作成]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-create-storage-account/
  [ユーザー ガイド]: http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx
  []: resources
  [WordPress の最適化]: http://codex.wordpress.org/WordPress_Optimization
  [WordPress サイトの Multisite への変換]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-php-convert-wordpress-multisite/
  [Azure 用の ClearDB アップグレード ウィザード]: http://www.cleardb.com/store/azure/upgrade
  [Azure Web サイトのサブフォルダーに WordPress をホスティングする方法]: http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx
  [Step-By-Step: Create a WordPress site using Azure (ステップ バイ ステップ: Azure で WordPress サイトを作成する)]: http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx
  [Host your existing WordPress blog on Azure (Azure で既存の WordPress ブログをホストする)]: http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx
  [Enabling pretty permalinks in WordPress (WordPress で見やすい固定リンクを有効にする)]: http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress
  [How to migrate and run your WordPress blog on Azure Websites (WordPress ブログを Azure Websites に移行し、運用する方法)]: http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/
  [How to run WordPress on Azure Websites for free (Azure Websites で、無料で WordPress を運用する方法)]: http://architects.dzone.com/articles/how-run-wordpress-azure
  [WordPress on Azure in 2 minutes or less (Azure で 2 分以内に WordPress を稼動)]: http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/
  [Moving a WordPress blog to Azure - Part 1: Creating a WordPress blog on Azure (WordPress ブログを Azure に移転する - パート 1: Azure で WordPress ブログを作成)]: http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1
  [Moving a WordPress blog to Azure - Part 2: Transferring your content (WordPress ブログを Azure に移転する - パート 2: コンテンツの転送)]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content
  [Moving a WordPress blog to Azure - Part 3: Setting up your custom domain (WordPress ブログを Azure に移転する - パート 3: カスタム ドメインの設定)]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain
  [Moving a WordPress blog to Azure - Part 4: Pretty permalinks and URL Rewrite rules (WordPress ブログを Azure に移転する - パート 4: 見やすい固定リンクと URL 書き換えのルール)]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules
  [Moving a WordPress blog to Azure - Part 5: Moving from a subfolder to the root (WordPress ブログを Azure に移転する - パート 5: サブフォルダーからルートへの移動)]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root
  [Azure アカウントで WordPress Web サイトを設定する方法]: http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/
  [Propping up WordPress on Azure (Azure で WordPress を設置する)]: http://www.johnpapa.net/wordpress-on-azure/
  [Tips for WordPress on Azure (Azure で WordPress を運用するヒント)]: http://www.johnpapa.net/azurecleardbmysql/
