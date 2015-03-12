<properties 
	pageTitle="Web サイトの構成方法 - Azure サービス管理" 
	description="ここでは SQL Database や MySQL データベースを使用するように Web サイトを構成する方法を含めて、Azure で Web サイトを構成する方法について説明します。" 
	services="web-sites" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/18/2014" 
	ms.author="mwasson"/>


# Web サイトの構成方法 #
Azure 管理ポータルでは、Web サイトの構成オプションを変更し、Web サイトをデータベースなどの他の Azure リソースにリンクできます。

## 目次 ##
- [方法:Web サイトの構成オプションの変更](#howtochangeconfig)
- [方法:SQL データベースを使用する Web サイトの構成](#howtoconfigSQL)
- [方法:MySQL データベースを使用する Web サイトの構成](#howtoconfigMySQL)
- [方法:カスタム ドメイン名の構成](#howtodomain)
- [方法:SSL を使用する Web サイトの構成](#howtoconfigSSL)
- [次のステップ](#next)


##<a name="howtochangeconfig"></a>方法:Web サイトの構成オプションの変更

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEBSITE -->

Web サイトの構成オプションを設定するには:

1. [管理ポータル](https://manage.windowsazure.com/)で、Web サイトの管理ページを開きます。
1. <strong>[構成]</strong> タブをクリックします。

**[構成]** タブには次のセクションがあります。

### 全般

**[Framework バージョン]**。アプリでこれらのフレームワークを使用する場合は、以下のオプションを設定します。 

- **.NET Framework**:.NET Framework のバージョンを設定します。 
- **PHP**:PHP のバージョンを設定するか、PHP を無効にする場合は **[オフ]** を設定します。 
- **Java**:Java を有効にする場合は、表示されているバージョンを選択します。Java を無効にする場合は <strong>[オフ]</strong> を選択します。 
- Java を有効にした場合は、<strong>[Web コンテナー]</strong> オプションを使用して Tomcat か Jetty のバージョンを選択します。
- **Python**:Python のバージョンを選択するか、Python を無効にする場合は **[オフ]** を選択します。

技術的な理由で、Web サイトで Java を有効にすると、.NET、PHP、Python オプションが無効になります。

<strong>[マネージ パイプライン モード]</strong>。IIS [パイプライン モード](http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application)を設定します。この設定は、以前のバージョンの IIS を必要とする従来の Web サイトを使用する場合を除いて、[統合] (既定) のままにしておきます。

<strong>[プラットフォーム]</strong>。アプリケーションが 32 ビット環境で実行されるか 64 ビット環境で実行されるかを選択します。64 ビット環境では Basic モードまたは Standard モードを使用する必要があります。Free モードと Shared モードは常に 32 ビット環境で実行されます。

<strong>[Web ソケット]</strong>。WebSocket プロトコルを有効にするには、**[オン]** を設定します (たとえば、Web サイトで [ASP.NET SignalR](http://www.asp.net/signalr) または [socket.io](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-chat-app-socketio/) を使用する場合)。

<strong>[常時接続]</strong>。既定では、アイドル状態がしばらく続くと Web サイトはアンロードされます。これにより、システムではリソースを節約できます。基本モードと標準モードでは、<strong>[常時接続]</strong> を有効にすると、サイトが常に読み込まれた状態になります。サイトで継続的な Web ジョブを実行する場合は、**[常時接続]** を有効にする必要があります。そうしないと、Web ジョブの実行の信頼性が低下する可能性があります。

<strong>[Visual Studio Online での編集]</strong>。Visual Studio Online でのライブ コード編集を有効にします。有効にすると、[ダッシュボード] タブの <strong>[概要]</strong> セクションの下に <strong>[Visual Studio Online での編集]</strong> というリンクが表示されます。Web サイトをオンラインで直接変更するには、このリンクをクリックします。認証が必要な場合は、基本的なデプロイの資格情報を使用できます。

注:"ソース管理からのデプロイ" を有効にすると、Visual Studio オンライン エディターで行った変更をデプロイで上書きできるようになります。 


### 証明書

基本モードまたは標準モードでは、カスタム ドメインの SSL 証明書をアップロードできます。詳細については、「[Azure Web サイトでの HTTPS の有効化](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/)」をご覧ください。 

アップロードした証明書はここに表示されます。証明書をアップロードすると、サブスクリプションとリージョンのすべての Web サイトにそれを割り当てることができます。ワイルドカード証明書は、その証明書が有効であるドメイン内の任意のサイトで使用できます。証明書は、その証明書に対するアクティブなバインドが存在しない場合にのみ、削除できます。

### ドメイン名

Web サイトの追加のドメイン名を表示または追加します。詳細については、「[Azure Web サイトのカスタム ドメイン名の構成](http://azure.microsoft.com/documentation/articles/web-sites-custom-domain-name/)」をご覧ください。

### SSL バインド

SSL 証明書をアップロードしている場合は、それらをカスタム ドメイン名にバインドできます。詳細については、「[Azure Web サイトでの HTTPS の有効化](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/)」をご覧ください。

### 展開

このセクションは、ソース管理からのデプロイを有効にした場合にのみ表示されます。これらの設定は、展開の構成に使用します。

- <strong>[Git URL]</strong>。Azure Web サイトに Git リポジトリを作成した場合は、これがその URL で、コンテンツをプッシュする場所です。
- <strong>[デプロイの開始 URL]</strong>。この URL は、GitHub、CodePlex、Bitbucket などのリポジトリに設定して、リポジトリにコミットがプッシュされたときにデプロイを開始できます。
- <strong>[デプロイする分岐]</strong>。コンテンツがプッシュされたときにデプロイされる分岐を指定します。

ソース管理からのデプロイを設定するには、**[ダッシュボード]**タブを表示し、**[ソース管理からのデプロイの設定]** をクリックします。 

### アプリケーション診断

ログ記録をサポートする Web アプリケーションの診断ログの書き込みに関するオプション: 

- <strong>[ファイル システム]</strong>。Web サイトのファイル システムにログを書き込みます。ファイル システム ログは 12 時間記録されます。Web サイトの FTP 共有からログにアクセスできます(「[FTP 資格情報](http://azure.microsoft.com/documentation/articles/web-sites-manage#ftp-credentials)」をご覧ください)。
- <strong>[テーブル ストレージ]</strong>。Azure テーブル ストレージにログを書き込みます。時間制限はなく、ログ記録は無効にするまで有効なままです。 
- <strong>[BLOB ストレージ]</strong>。Azure BLOB ストレージにログを書き込みます。時間制限はなく、ログ記録は無効にするまで有効なままです。

<strong>[ログ レベル]</strong>。ログが有効な場合、このオプションは記録する情報のレベルを指定します (エラー、警告、情報、または詳細)。

**[テーブル ストレージの管理]**。テーブル ストレージが有効になっている場合、このボタンをクリックしてストレージ アカウントとテーブル名を設定します。

**[BLOB ストレージの管理]**。BLOB ストレージが有効になっている場合、このボタンをクリックしてストレージ アカウントと BLOB ストレージ名を設定します。

### サイト診断

Web サイトの診断情報を収集するためのオプションです。

<strong>[Web サーバーのログ記録]</strong>。Web サーバーのログ記録を有効にします。ログは W3C 拡張ログ ファイル形式で保存されます。これらのログは Azure Storage または Web サイトのファイル システムに保存できます。
 
- <strong>[ファイル システム]</strong> を選択した場合、[ダッシュボード] ページの "[FTP 診断ログ]" に表示される FTP サイトにログが保存されます。(「[FTP 資格情報](http://azure.microsoft.com/documentation/articles/web-sites-manage#ftp-credentials)」をご覧ください)。 
- **[ファイル システム]** を選択した場合は、<strong>[クォータ]</strong> ボックスを使用して、ログ ファイルに割り当てる最大ディスク容量を設定します。指定可能な最小容量は 25 MB、最大容量は 100 MB。既定値は 35MB. です。設定したクォータに達すると、最も古いファイルから順に最新ファイルで上書きされます。100 MB を超える履歴を保存する場合は、ストレージ容量が大きい Azure Storage を使用してください。
- 必要に応じて、<strong>[保有期間の設定]</strong> をクリックすると、期間終了後にファイルが自動的に削除されます。既定では、ログは一切削除されません。   

<strong>[詳細なエラー メッセージ]</strong>。有効にすると、詳細なエラー メッセージが .htm ファイルとして保存されます。ファイルを表示するには、[ダッシュボード] ページの "[FTP 診断ログ]" に示されている FTP サイトに移動します。ファイルは FTP サイトの /LogFiles/DetailedErrors に保存されます。(「[FTP 資格情報](http://azure.microsoft.com/documentation/articles/web-sites-manage#ftp-credentials)」をご覧ください)。

<strong>[失敗した要求トレース]</strong>。有効にすると、失敗した要求が XML ファイルに記録されます。ファイルを表示するには、[ダッシュボード] ページの "[FTP 診断ログ]" に示されている FTP サイトに移動します。(「[FTP 資格情報](http://azure.microsoft.com/documentation/articles/web-sites-manage#ftp-credentials)」をご覧ください)。ファイルは /LogFiles/W3SVC*xxx* に保存されます (xxx は一意の識別子)。このフォルダーには、1 つの XSL ファイルと 1 つ以上の XML ファイルが格納されます。この XSL ファイルは、XML ファイルのコンテンツの書式設定とフィルター処理を行う役割を果たすため、必ずダウンロードしてください。

<strong>[リモート デバッグ]</strong> は、リモート デバッグを有効にします。これを有効にすると、Visual Studio でリモート デバッガーを使用して、Azure Web サイトに直接接続できます。リモート デバッグは 48 時間有効です。

**注**:サイト名やユーザー名が 20 文字を超える場合、リモート デバッグは機能しません。 

### 監視

基本モードまたは標準モードでは、最大 3 つの地理的に分散した場所から HTTP または HTTPS エンドポイントの可用性をテストできます。HTTP 応答コードがエラー (4xx または 5xx) である場合、または、応答に 30 秒以上かかる場合、監視テストは失敗します。すべての指定した場所から監視テストが成功した場合、エンドポイントは利用可能と見なされます。 

詳細については、「[方法:Web エンドポイントの状態の監視](http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409)」をご覧ください。


### 開発者分析

<strong>[アドオン]</strong> を指定した場合、分析アドオンを一覧から選択するか、Azure ストアへ移動して分析アドオンを選択します。<strong>[カスタム]</strong> を指定した場合は、New Relic などの分析プロバイダーを一覧から選択します。カスタム プロバイダーを使用する場合、<strong>[プロバイダー キー]</strong> ボックスにライセンス キーを入力する必要があります。 

Azure Websites で New Relic を使用する方法の詳細については、「<a href="http://azure.microsoft.com/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/">Azure Websites の New Relic によるアプリケーション パフォーマンス管理</a>」をご覧ください。

### アプリケーション設定

起動時に Web アプリケーションによって読み込まれる名前と値のペアです。 

- .NET サイトの場合、実行時にこれらの設定が .NET 構成 AppSettings に挿入され、既存の設定がオーバーライドされます。 

- PHP、Python、Java および Node アプリケーションでは、実行時に環境変数としてこれらの設定にアクセスできます。各アプリ設定で、2 つの環境変数が作成されます。1 つは、アプリ設定エントリで指定された名前になり、もう 1 つはプレフィックスとして APPSETTING_ が付加されます。両方に同じ値が格納されます。

### Connection strings

リンク済みリソースの接続文字列です。 

.NET サイトの場合、実行時にこれらの接続文字列が .NET 構成 connectionStrings 設定に挿入され、リンクされたデータベース名とキーが等しい既存のエントリがオーバーライドされます。 

PHP、Python、Java、および Node アプリケーションの場合、実行時にこれらの設定が環境変数として使用できるようになり、環境変数にはプレフィックスとして接続の種類が付加されます。環境変数使用されるプレフィックスは次のとおりです。 

- SQL Server:SQLCONNSTR_
- MySQL:MYSQLCONNSTR_
- SQL データベース:SQLAZURECONNSTR_
- カスタム:CUSTOMCONNSTR_

たとえば、MySql の接続文字列が connectionstring1 という名前であれば、<code>MYSQLCONNSTR_connectionString1</code>. という環境変数でアクセスされます。

<strong>注</strong>:接続文字列は、データベース リソースを Web サイトにリンクするときにも作成されます。この方法で作成された接続文字列は、 
[構成] 管理ページには読み取り専用で表示されます。

### 既定のドキュメント

Web サイトの既定のドキュメントは、Web サイトの特定のページを指定せずに Web サイトを開いたときに表示される Web ページです。Web サイトに一覧の複数のファイルが含まれている場合は、既定のドキュメントを一覧の先頭に置いてください。

Web アプリケーションでは、静的コンテンツを提供する代わりに URL に基づいてルーティングするモジュールを使用することがあります。その場合には、このような既定のドキュメントはありません。   

### ハンドラー マッピング

この領域では、特定のファイル拡張子に関する要求を処理するカスタム スクリプト プロセッサを追加します。 

- **[拡張子]**。処理するファイル拡張子 (*.php や handler.fcgi など) を指定します。 
- **[スクリプト プロセッサ パス]**。スクリプト プロセッサの絶対パスを指定します。指定したファイル拡張子に一致するファイルに対する要求が、スクリプト プロセッサによって処理されます。サイトのルート ディレクトリは <code>D:\home\site\wwwroot</code> というパスを使って参照します。
- **[追加の引数]**。スクリプト プロセッサのオプションのコマンド ライン引数です。 


### 仮想アプリケーションとディレクトリ 

Web サイトに関連付けられた仮想アプリケーションとディレクトリを構成するには、各仮想ディレクトリとそれに対応する物理パス (サイトのルートに対する相対パス) を指定します。必要に応じて、<strong>[アプリケーション]</strong> チェック ボックスをオンにして、サイト構成で仮想ディレクトリをアプリケーションとしてマークすることもできます。

	

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A SQL DATABASE -->
##<a name="howtoconfigSQL"></a>方法:SQL データベースを使用する Web サイトの構成

Web サイトを SQL データベースにリンクするには、次の手順に従います。

1. [管理ポータル](http://manage.windowsazure.com)で **[Web サイト]** を選択して、現在ログオン中のアカウントによって作成された Web サイトの一覧を表示します。

2. Web サイトの一覧から Web サイトを選択して、Web サイトの **[管理]** ページを開きます。

3. **[リンク済みリソース]** タブをクリックすると、**[リンク済みリソース]** ページに、"**リンク済みリソースがありません**" というメッセージが表示されます。

4. **[リソースをリンクします]** をクリックして、**リソースのリンク** ウィザードを開きます。

5. **[新しいリソースを作成する]** をクリックして、Web サイトにリンクできるリソースの種類の一覧を表示します。

6. **[SQL データベース]** をクリックして、**データベースのリンク** ウィザードを開きます。

7. **データベースのリンク** ウィザードの 3 ページ目と 4 ページ目にある必須フィールドに値を指定し、4 ページ目の **[完了]** チェック マークをクリックします。

Azure により、指定したパラメーターで SQL データベースが作成され、データベースが Web サイトにリンクされます。

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A MYSQL DATABASE -->
##<a name="howtoconfigMySQL"></a>方法:MySQL データベースを使用する Web サイトの構成##
MySQL データベースを使用するように Web サイトを構成するには、SQL データベースを使用するときと同じ手順に従います。ただし、**リソースのリンク ウィザード**で、**[SQL データベース]** の代わりに **[MySQL データベース]** を選択します。 

別の方法として、**[カスタム作成]** オプションを使って Web サイトを作成することもできます。**[データベース]** ボックスで **[新しい MySQL データベースを作成します]** または **[既存の MySQL データベースを使用します]** を選択します。 

##<a name="howtodomain"></a>方法:カスタム ドメイン名の構成

Azure の Web サイトのカスタム ドメイン名の構成の詳細については、「[Azure Web サイトのカスタム ドメイン名の構成](http://azure.microsoft.com/documentation/articles/web-sites-custom-domain-name/)」をご覧ください。

##<a name="howtoconfigSSL"></a>方法:SSL を使用する Web サイトの構成##

Azure でカスタム ドメインの SSL を構成する方法については、「[Enable HTTPS for an Azure web site (Azure の Web サイトでの HTTPS の有効化)](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/)」をご覧ください。 

##<a name="next"></a>次のステップ

* [How to scale Web SitesWeb (サイトの規模変更方法)](http://azure.microsoft.com/documentation/articles/web-sites-scale/)

* [How to monitor Web Sites (Web サイトの監視方法)](http://azure.microsoft.com/documentation/articles/web-sites-monitor/)



<!--HONumber=42-->
