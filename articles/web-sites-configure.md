<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="構成方法" pageTitle="Web サイトの構成方法 - Azure サービス管理" metaKeywords="Azure の Web サイト, Azure の Web サイトの構成, Azure SQL データベース, Azure MySQL" description="SQL データベースまたは MySQL データベースを使用するように Web サイトを構成する方法を含めて、Azure の Web サイトの構成方法について説明します。" metaCanonical="" services="web-sites" documentationCenter="" title="Web サイトの構成方法" authors="timamm" solutions="" manager="" editor="mollybos" />





# Web サイトの構成方法#
Azure の管理ポータルでは、Web サイトの構成オプションを変更し、Web サイトを他の Azure リソースにリンクすることができます。たとえば、Web サイトを SQL データベースにリンクし、追加機能を提供することができます。新規または既存の MySQL データベースを使用できるように Web サイトを構成することもできます。

## 目次##
- [方法: Web サイトの構成オプションを変更する](#howtochangeconfig)
- [方法: SQL データベースを使用する Web サイトを構成する](#howtoconfigSQL)
- [方法: MySQL データベースを使用する Web サイトを構成する](#howtoconfigMySQL)
- [方法: カスタム ドメイン名を構成する](#howtodomain)
- [方法: SSL を使用する Web サイトを構成する](#howtoconfigSSL)
- [次のステップ](#next)



##<a name="howtochangeconfig"></a>方法: Web サイトの構成オプションを変更する

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEB SITE -->

Web サイトの構成オプションを変更するには、次の手順に従います。

<ol>
	<li>管理ポータルで、Web サイトの管理ページを開きます。</li>
	<li><strong>[構成]</strong> タブをクリックして <strong>[構成]</strong> 管理ページを開きます。</li>
	<li>必要に応じて、次の構成オプションを Web サイトに設定します。
	<ul>

	<!-- GENERAL -->
	<li><strong>全般</strong>
<ul>
<li><strong>[.NET Framework バージョン]</strong> - Web アプリケーションで .NET Framework を使用する場合、Web アプリケーションが必要とする .NET Framework のバージョンを設定します。</li>

<li><strong>[PHP バージョン]</strong> - Web アプリケーションで PHP を使用する場合、Web アプリケーションが必要とする PHP のバージョンを設定します。</li>
<li><strong>[Java バージョン]</strong> - 表示されている Java のバージョンを選択して Web アプリケーションで有効にするか、<strong>[オフ]</strong> を選択して Java を無効にします。Web アプリケーションで Java を有効にすると、<strong>[Web コンテナー]</strong> オプションでは Tomcat や Jetty のバージョンを選択できます。
<p><strong>注</strong>: 技術的な理由で、Web サイトで Java を有効にすると、.NET、PHP、Python オプションが無効になります。</p>
</li>
<li><strong>[Python バージョン]</strong> - Python バージョンを表示します (構成不可能)。</li>
<li><strong>[マネージ パイプライン モード]</strong> - <strong>[クラシック]</strong> または <strong>[統合]</strong> を選択します。既定値は [統合] です。従来の Web サイトを古いバージョンの IIS 上で実行する必要がある場合のみ、[クラシック] オプションを選択してください。</li>

<li><strong>[プラットフォーム]</strong> - 標準モードのサイトについて、アプリケーションの実行環境 (32 ビットまたは 64 ビット) を選択できます。無料モードおよび共有モードのサイトは常に 32 ビット環境で実行されます。</li>

<li><strong>[Web Socket]</strong> - <strong>[オン]</strong> を選択すると、チャットなどのリアルタイム要求パターン アプリケーションを Web サイトで使用できるようになります。</li>

<li><strong>[常時接続]</strong> - 既定では、アイドル状態がしばらく続いている Web サイトがアンロードされます。これにより、システムではリソースを節約できます。<strong>[常時接続]</strong> 設定は標準モードのサイトでのみ使用でき、サイトが常にロードされている必要がある場合にオンにします。<strong>[常時接続]</strong> を無効にした場合、連続的に動作する Web ジョブが確実に実行されない可能性があるため、サイト上で連続的に動作する Web ジョブがあるときは、<strong>[常時接続]</strong> を有効にする必要があります。</li>

<li><strong>[Visual Studio Online での編集]</strong> - <strong>[オン]</strong> を選択すると、Visual Studio Online でコードを編集できるようになります。この構成変更を保存すると、[ダッシュボード] タブの <strong>[概要]</strong> セクションに <strong>[Visual Studio Online での編集]</strong> リンクが表示されます。Web サイトをオンラインで直接変更するには、このリンクをクリックします。認証が必要な場合は、基本的なデプロイの資格情報を使用できます。
<p><strong>注</strong>: "ソース管理からの展開" を有効にしている場合、Visual Studio オンライン エディターで行った変更を展開で上書きできるようになります。そのため、Visual Studio Online でサイト コンテンツを直接編集する場合は、"ソース管理からの展開" を使用しないことをお勧めします。</p>
</li>



</ul></li>

<!-- CERTIFICATES -->
<li><strong>[証明書]</strong> - 標準モードでのみ、<strong>[アップロード]</strong> をクリックして、カスタム ドメインの SSL 証明書をアップロードすることができます。アップロードする証明書はここに一覧表示されます。ワイルドカード ("スター") 証明書 (アスタリスク "*" を使った証明書) がサポートされています。証明書をアップロードすると、サブスクリプションとリージョンのすべての Web サイトにそれを割り当てることができます。スター証明書は 1 回アップロードするだけで、有効なドメイン内のすべてのサイトに使用できます。証明書を削除できるのは、その証明書についてサイトのアクティブなバインドがない場合だけです。
<br /><strong>注: </strong>
カスタム ドメインは共有モードと標準モードでのみ使用でき、カスタム ドメインの SSL サポートは標準モードでのみ使用できます。Azure でカスタム ドメインの SSL を構成する方法については、「<a href="http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/">Enable HTTPS for an Azure web site (Azure の Web サイトでの HTTPS の有効化)</a>」を参照してください。
</li>

<!-- DOMAIN NAMES -->
<li><strong>[ドメイン名]</strong> - Web サイトの追加のドメイン名をここで表示または追加します。<strong>[ドメインの管理]</strong> をクリックして、カスタム ドメインを追加できます。カスタム ドメインは、<strong>共有</strong>モードと<strong>標準</strong>モードでのみ使用できます。Web サイトのモードは、<strong>[スケール]</strong> 管理ページで変更できます。カスタム ドメインは無料モードでは使用できません。カスタム ドメインの構成の詳細については、「<a href="http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-custom-domain-name/">Configuring a custom domain name for an Azure web site (Azure の Web サイトのカスタム ドメイン名の構成)</a>」を参照してください。</li>

<!-- SSL BINDINGS -->
<li><strong>[SSL バインド]</strong> - カスタム ドメインの SSL バインドは標準モードでのみ使用できます。特定のドメイン名の SSL モード (<strong>SNI</strong>、<strong>IP</strong> または <strong>SSL なし</strong>) を選択します。SNI または IP を選択する場合は、アップロードした証明書からドメインの証明書を指定できます。Azure でカスタム ドメインの SSL を構成する方法については、「<a href="http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/">Enable HTTPS for an Azure web site (Azure の Web サイトでの HTTPS の有効化)</a>」を参照してください。SNI の詳細については、「<a href="http://en.wikipedia.org/wiki/Server_Name_Indication">Server Name Indication</a>」を参照してください。</li>

<!-- DEPLOYMENTS  -->
<li><strong>[展開]</strong> - これらの設定を使用して展開を構成します。
<ul>
<li><strong>[Git URL]</strong> - Azure の Web サイトに Git リポジトリを作成した場合は、これがその URL で、コンテンツをプッシュする場所です。</li>
<li><strong>[展開の開始 URL]</strong> - この URL は、GitHub、CodePlex、Bitbucket などのリポジトリに設定して、リポジトリにコミットがプッシュされたときに展開を開始することができます。</li>
<li><strong>[展開する分岐]</strong> - コンテンツがプッシュされたときに展開される分岐を指定できます。</li>
</ul>
</li>

<!-- APPLICATION DIAGNOSTICS  -->
<li><strong>[アプリケーション診断]</strong> - コードがトレースを使ってインストルメント化された Web アプリケーションから診断トレースを収集するためのオプションを設定します。アプリケーション診断のログ オプションは次のとおりです。
<ul>
<!-- APPLICATION LOGGING (FILE SYSTEM) -->
<li><strong>[アプリケーション ログ記録 (ファイル システム)]</strong> - アプリケーション ログを Web サイトのファイル システムに書き込む場合、<strong>[オン]</strong> を選択します。有効にした場合、ファイル システム ログは 12 時間記録されます。Web サイトの FTP 共有からログにアクセスできます。FTP 共有へのリンクは <strong>ダッシュボード</strong>にあります。<strong>[概要]</strong> で、<strong>[FTP 診断ログ]</strong> または <strong>[FTPS 診断ログ]</strong> を選択します。</li>

<!-- APPLICATION LOGGING (STORAGE) -->
<li><strong>[アプリケーション ログ記録 (ストレージ)]</strong> - アプリケーション ログを Azure のストレージ アカウントに書き込む場合、<strong>[オン]</strong> を選択します。ストレージ アカウントへのログ記録には時間制限がなく、無効にするまで有効なままです。既定では、ログは WAWSAppLogTable と呼ばれるテーブルに格納されます。</li>
<li><strong>[ログ レベル]</strong> - ログが有効な場合、このオプションは記録する情報のレベルを指定します (エラー、警告、情報、または詳細)。</li>

<!-- DIAGNOSTIC STORAGE -->
<li><strong>[診断ストレージ]</strong> - <strong>[接続の管理]</strong> をクリックすると、<strong>[診断ストレージの管理]</strong> ダイアログが開き、Azure ストレージ アカウントにログを保存するための次のオプションが表示されます。
<ul>
<li><strong>[ストレージ アカウント名]</strong> - ログの保存先となるストレージ アカウントを選択します。</li>
<li><strong>[ストレージ アクセス キー]</strong> - 選択したストレージ アカウントのキーが表示されます。ストレージ アカウントのキーを再生成した場合は、ここに新しいキーを手動で入力するか、<strong>同期</strong>ボタンの 1 つを使用します。同期ボタンは、現在ログオンしているユーザーが、選択したストレージ アカウントにアクセスできる場合にのみ使用できます。
</li>
<li><strong>[プライマリ キーの同期]</strong> - Azure ストレージ アカウントの最新のプライマリ キーを取得します。
</li>
<li><strong>[セカンダリ キーの同期]</strong> - Azure のストレージ アカウントの最新のセカンダリ キーを取得します。
<br /><strong>注:</strong> Azure ストレージ アクセス キーの詳細については、「<a href="http://www.windowsazure.com/ja-jp/documentation/articles/storage-manage-storage-account/#regeneratestoragekeys">方法: ストレージ アクセス キーの表示、コピーおよび再生成</a>」を参照してください。
</li></ul></li></ul></li>

<!-- SITE DIAGNOSTICS  -->
<li><strong>[サイト診断]</strong> - Web サイトの診断情報を収集するための
	次のオプションを設定します。
<ul>
<!-- WEB SERVER LOGGING -->
	<li><strong>[Web サーバーのログ記録]</strong> - Web サイトに対して Web サーバーのログ記録を有効にするかどうかを指定します。Web サーバーのログは W3C 拡張ログ ファイル形式で保存されます。これらのログは Azure のストレージまたはファイル システムに保存できます。
<br /><strong>ヒント</strong>: ファイル システムに保存できるログ ストレージの最大サイズは 100 MB です。このサイズを超える履歴を保存する場合は、ストレージ容量が大きい Azure のストレージを使用してください。
	<ul>
		<li>Web サーバーのログを Azure のストレージ アカウントに保存するには、<strong>[ストレージ]</strong>、<strong>[ストレージの管理]</strong> の順にクリックします。<strong>[サイト診断用のストレージの管理]</strong> ダイアログ ボックスで、<strong>[ストレージ アカウント]</strong> オプションを使用して、ログを保持するコンテナーの Azure のストレージ アカウントを選択します。<strong>[Azure BLOB コンテナー]</strong> オプションを使用して、ログを保持するコンテナーを選択します。または、<strong>[新しい BLOB コンテナーを作成する]</strong> を選択して、<strong>[BLOB 名]</strong> ボックスを有効にし、新しいコンテナーの名前を指定します。
<br /><strong>注</strong>: まだストレージ アカウントを取得していない場合は、Azure ポータルの <strong>[ストレージ]</strong> セクションに移動し、<strong>[新規]</strong> をクリックして、アカウントを作成できます。
</li>
		<li><strong>[ファイル システム]</strong> を選択した場合、[ダッシュボード] 管理ページの <strong>[FTP 診断ログ]</strong> に表示される FTP サイトにログが保存されます。ファイル システム ストレージを選択した場合は <strong>[クォータ]</strong> ボックスも有効になります。ここで、ログ ファイルに割り当てる最大ディスク容量を設定できます。指定可能な最小容量は 25 MB、最大容量は 100 MB。既定値は 35MB. です。設定したクォータに達すると、最も古いファイルから順に最新ファイルで上書きされます。</li>
		<li>既定では、Azure のストレージ アカウントの Web サーバー ログは削除されません。ログが自動的に削除されるまでの期間を指定するには、<strong>[保有期間の設定]</strong> を選択し、ログを保持する日数を <strong>[保有期間]</strong> ボックスに入力します。ファイル システム ストレージで <strong>[保有期間の設定]</strong> オプションを使用することもできます。</li></ul>
</li>

<!-- DETAILED ERROR MESSAGES -->
	<li><strong>[詳細なエラー メッセージ]</strong> - Web サイトに関する詳細なエラー メッセージをログに記録するかどうかを指定します。
	有効にすると、[ダッシュボード] 管理ページの [FTP 診断ログ] に示されている FTP サイトに、詳細なエラー メッセージが .htm ファイルとして保存されます。
	詳細なエラー メッセージが含まれた .htm ファイルを取得するには、指定されている FTP サイトに接続してから、/LogFiles/DetailedErrors/ に移動します。</li>

<!-- FAILED REQUEST TRACING -->
	<li><strong>[失敗した要求トレース]</strong> - 失敗した要求トレースを有効にするかどうかを指定します。有効にすると、
	失敗した要求トレースの出力が XML ファイルに書き込まれ、[ダッシュボード] 管理ページの [FTP 診断ログ] に示されている FTP サイトに保存されます。
	失敗した要求トレースの出力が含まれた XML ファイルを取得するには、指定されている FTP サイトに接続してから、/LogFiles/W3SVC######### (######### は、
	Web サイトを示す一意の識別子) に移動します。<br /><strong>重要</strong><br />/LogFiles/W3SVC#########/ 
	フォルダーには、1 つの XSL ファイルと 1 つ以上の XML ファイルが格納されます。この XSL ファイルは、XML ファイルが Internet Explorer で表示されるときに、
	コンテンツの書式設定とフィルター処理を行う役割を果たすため、必ず XML ファイルと同じディレクトリにダウンロードしてください。</li>

<!-- REMOTE DEBUGGING -->
<li><strong>[リモート デバッグ]</strong> - このオプションを <strong>[オン]</strong> に設定すると Visual Studio 2012 または Visual Studio 2013 のリモート デバッグが有効になり、Visual Studio のリモート デバッガーを使用して Azure の Web サイトへ直接接続できます。
<br />
<strong>注</strong>: リモート デバッグが有効なのは 48 時間のみです。また、20 文字を超えるサイト名やユーザー名は使用できません。
</li>
	</ul>
	</li>

<!-- MONITORING  -->
<li><strong>[監視]</strong> - 標準モードの Web サイトの場合、HTTP または HTTPS エンドポイントの可用性をテストします。最大 3 か所の地理的に分散した場所からエンドポイントをテストできます。HTTP 応答コードが 400 以上である場合、または、応答に 30 秒以上かかる場合、監視テストは失敗します。すべての指定した場所から監視テストが成功した場合、エンドポイントは利用可能と見なされます。
</li>

<!-- DEVELOPER ANALYTICS -->
<li><strong>[開発者分析]</strong> - <strong>[アドオン]</strong> を指定した場合、分析アドオンを一覧から選択するか、Azure ストアへ移動して分析アドオンを選択します。<strong>[カスタム]</strong> を指定した場合は、New Relic などの分析プロバイダーを一覧から選択します。カスタム プロバイダーを使用する場合、<strong>[プロバイダー キー]</strong> ボックスにライセンス キーを入力する必要があります。
<br /> <strong>注</strong>: Azure の Web サイトで New Relic を使用する方法の詳細については、「<a href="http://www.windowsazure.com/ja-jp/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/">Azure の Web サイトの New Relic によるアプリケーション パフォーマンス管理</a>」を参照してください。
</li>

<!-- APP SETTINGS -->
	<li><strong>[アプリケーション設定]</strong> - 起動時に Web アプリケーションに読み込まれる名前と値を指定します。.NET サイトの場合、実行時にこれらの設定が
	.NET 構成 AppSettings に挿入され、既存の設定がオーバーライドされます。PHP サイトおよび Node サイトの場合、
	実行時にこれらの設定が環境変数として使用できるようになります。</li>

<!-- CONNECTION STRINGS -->
	<li><strong>[接続文字列]</strong> - リンク済みリソースの接続文字列が表示されます。.NET サイトの場合、実行時にこれらの接続文字列が .NET 構成 connectionStrings 設定に挿入され、リンクされたデータベース名とキーが等しい既存のエントリがオーバーライドされます。PHP サイト
	および Node サイトの場合、実行時にこれらの設定が環境変数として使用できるようになり、環境変数にはプレフィックスとして接続の種類が付加されます。環境変数使用されるプレフィックスは次のとおりです。<br />
<ul><li>SQL Server: SQLCONNSTR_</li>
<li>MySQL: MYSQLCONNSTR_</li>
<li>SQL データベース: SQLAZURECONNSTR_</li>
<li>カスタム: CUSTOMCONNSTR_</li></ul>たとえば、MySql の接続文字列が connectionstring1 という名前の場合、<code>MYSQLCONNSTR_connectionString1</code> という環境変数でアクセスされます。
	<br /><strong>注</strong>: 接続文字列は、データベース リソースを Web サイトにリンクするときに作成され、
	[構成] 管理ページには読み取り専用で表示されます。</li>

<!-- DEFAULT DOCUMENTS -->
	<li><strong>[既定のドキュメント]</strong> - Web サイトの既定のドキュメントがこの一覧になければ追加します。Web サイトの既定の
	ドキュメントは、ユーザーが Web サイトの特定のページを指定せずに Web サイトを開いたときに表示される Web ページです。たとえば、
	Web サイト http://contoso.com で既定のドキュメントが default.htm に設定されている場合、ブラウザーから http://www.contoso.com を開いたユーザーは、
	http://www.contoso.com/default.htm にルーティングされます。この一覧内の複数のファイルが Web サイトに含まれている場合は、一覧内のファイルの順序を変更して、
	Web サイトの既定のドキュメントが一覧の先頭になるようにしてください。</li>

<!-- HANDLER MAPPINGS -->
<li><strong>[ハンドラー マッピング]</strong> - 特定のファイル拡張子に関する要求を処理するカスタム スクリプト プロセッサを追加します。処理するファイル拡張子を <strong>[拡張子]</strong> ボックスで指定します (たとえば、*.php や handler.fcgi)。このパターンに一致するファイルに対する要求は、<strong>[スクリプト プロセッサ パス]</strong> ボックスで指定されたスクリプト プロセッサによって処理されます。スクリプト プロセッサは絶対パスで指定する必要があります (サイトのルート ディレクトリは D:\home\site\wwwroot というパスを使って参照できます)。スクリプト プロセッサのオプションのコマンド ライン引数を <strong>[追加の引数 (省略可能)]</strong> ボックスで指定することもできます。
</li>

<!-- VIRTUAL APPLICATIONS AND DIRECTORIES -->
<li><strong>[仮想アプリケーションとディレクトリ]</strong> - Web サイトに関連付けられた仮想アプリケーションとディレクトリを構成するには、各仮想ディレクトリとそれに対応する物理パス (サイトのルートに対する相対パス) を指定します。必要に応じて、<strong>[アプリケーション]</strong> チェック ボックスをオンにして、サイト構成で仮想ディレクトリをアプリケーションとしてマークすることもできます。
</li>

	
</ul></li>
<li><strong>[構成]</strong> 管理ページの下部にある <strong>[保存]</strong> をクリックして、構成の変更を保存します。</li>
</ol>

<!-- HOW TO: CONFIGURE A WEB SITE TO USE A SQL DATABASE -->
##<a name="howtoconfigSQL"></a>方法: SQL データベースを使用する Web サイトを構成する

Web サイトを SQL データベースにリンクするには、次の手順に従います。

1. [管理ポータル](http://manage.windowsazure.com)で **[Web サイト]** を選択して、現在ログオン中のアカウントによって作成された Web サイトの一覧を表示します。

2. Web サイトの一覧から Web サイトを選択して、Web サイトの**管理**ページを開きます。

3. **[リンク済みリソース]** タブをクリックすると、**[リンク済みリソース]** ページに、"**リンク済みリソースがありません**" というメッセージが表示されます。

4. **[リソースをリンクします]** をクリックして、**リソースのリンク** ウィザードを開きます。

5. **[新しいリソースを作成する]** をクリックして、Web サイトにリンクできるリソースの種類の一覧を表示します。

6. **[SQL データベース]** をクリックして、**データベースのリンク** ウィザードを開きます。

7. **データベースのリンク** ウィザードの 3 ページ目と 4 ページ目にある必須フィールドに値を指定し、4 ページ目の **[完了]** チェック マークをクリックします。

Azure により、指定したパラメーターで SQL データベースが作成され、データベースが Web サイトにリンクされます。

<!-- HOW TO: CONFIGURE A WEB SITE TO USE A MYSQL DATABASE -->
##<a name="howtoconfigMySQL"></a>方法: MySQL データベースを使用する Web サイトを構成する##
MySQL データベースを使用するように Web サイトを構成するには、SQL データベースを使用するときと同じ手順に従います。ただし、**リソースのリンク ウィザード**で、**[SQL データベース]** の代わりに **[MySQL データベース]** を選択します。

別の方法として、**[カスタム作成]** オプションを使って Web サイトを作成することもできます。**[データベース]** ボックスで **[新しい MySQL データベースを作成します]** または **[既存の MySQL データベースを使用します]** を選択します。

##<a name="howtodomain"></a>方法: カスタム ドメイン名を構成する

Azure の Web サイトのカスタム ドメイン名の構成の詳細については、「[Azure の Web サイトのカスタム ドメイン名の構成](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-custom-domain-name/)」を参照してください。

##<a name="howtoconfigSSL"></a>方法: SSL を使用する Web サイトを構成する##

Azure でカスタム ドメインの SSL を構成する方法については、「[Enable HTTPS for an Azure web site (Azure の Web サイトでの HTTPS の有効化)](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/)」を参照してください。

##<a name="next"></a>次のステップ

* [Web サイトの規模の設定方法](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-scale/)

* [Web サイトの監視方法](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-monitor/)


