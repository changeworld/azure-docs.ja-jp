<properties 
	pageTitle="Azure App Service での Web アプリの構成 #" 
	description="Azure App Service での Web アプリの構成方法" 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>


# Azure App Service での Web アプリの構成 #

このトピックでは、[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)で Web アプリを構成する方法について説明しますす。

## アプリケーションの設定

1. [Azure ポータル](https://portal.azure.com)で、Web アプリのブレードを開きます。
2. **[すべての設定]** をクリックします。
3. [**アプリケーション設定**] をクリックします。

![](./media/web-sites-configure/configure01.png)

[**アプリケーション設定**] ブレードには、いくつかのカテゴリにグループ化された設定があります。

### 全般設定

**Framework バージョン**アプリでこれらのフレームワークを使用する場合は、以下のオプションを設定します。 

- **.NET Framework**:.NET Framework のバージョンを設定します。 
- **PHP**:PHP のバージョンを設定するか、PHP を無効にする場合は **[オフ]** を設定します。 
- **Java**:Java のバージョンを選択するか、Java を無効にする場合は **[オフ]** を選択します。**[Web コンテナー]** オプションを使用して Tomcat か Jetty のバージョンを選択します。
- **Python**:Python のバージョンを選択するか、Python を無効にする場合は **[オフ]** を選択します。

技術的な理由で、Web アプリで Java を有効にすると、.NET、PHP、Python オプションが無効になります。

**プラットフォーム**Web アプリが 32 ビット環境で実行されるか 64 ビット環境で実行されるかを選択します。64 ビット環境では Basic モードまたは Standard モードを使用する必要があります。Free モードと Shared モードは常に 32 ビット環境で実行されます。

**Web ソケット**WebSocket プロトコルを有効にするには、[**ON**] に設定します (たとえば、Web アプリで [ASP.NET SignalR](http://www.asp.net/signalr) または [socket.io](web-sites-nodejs-chat-app-socketio.md) を使用する場合)。

**常時接続**既定では、アイドル状態がしばらく続くと Web アプリはアンロードされます。これにより、システムではリソースを節約できます。基本モードと標準モードでは、**[常時接続]** を有効にすると、アプリが常に読み込まれた状態になります。アプリで継続的な Web ジョブを実行する場合は、**[常時接続]** を有効にする必要があります。そうしないと、Web ジョブの実行の信頼性が低下する可能性があります。

**管理されたパイプライン バージョン**IIS [パイプライン モードを設定します。](http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application)この設定は、以前のバージョンの IIS を必要とするレガシ アプリを使用する場合を除いて、[統合] (既定) のままにしておきます。

**自動スワップ**デプロイ スロットの自動スワップを有効にした場合、アプリ サービスは、スロットに対して更新をプッシュしたときに、Web アプリを運用環境に自動的にスワップします。詳細については、「[Deploy to staging slots for web apps in Azure App Service (Azure App Service の Web アプリに対するステージング スロットへのデプロイ)]」(web-sites-staged-publishing.md) を参照してください。


### デバッグ

**リモート デバッグ**リモート デバッグを有効にします。これを有効にすると、Visual Studio でリモート デバッガーを使用して、Web アプリに直接接続できます。リモート デバッグは 48 時間有効です。 


### アプリ設定

このセクションには、起動時に Web アプリがロードする名前/値ペアが含まれます。 

- .NET アプリの場合、実行時にこれらの設定が .NET 構成 `AppSettings`に挿入され、既存の設定がオーバーライドされます。 

- PHP、Python、Java および Node アプリケーションでは、実行時に環境変数としてこれらの設定にアクセスできます。各アプリ設定で、2 つの環境変数が作成されます。1 つは、アプリ設定エントリで指定された名前になり、もう 1 つはプレフィックスとして APPSETTING_ が付加されます。両方に同じ値が格納されます。

### Connection strings

リンク済みリソースの接続文字列です。 

.NET アプリで、これらの接続文字列は、実行時に .NET 構成設定に挿入され、 `connectionStrings`キーがリンクされたデータベース名である既存のエントリがオーバーライドされます。 

PHP、Python、Java、および Node アプリケーションの場合、実行時にこれらの設定が環境変数として使用できるようになり、環境変数にはプレフィックスとして接続の種類が付加されます。環境変数使用されるプレフィックスは次のとおりです。 

- SQL Server:SQLCONNSTR_
- MySQL:MYSQLCONNSTR_
- SQL データベース:SQLAZURECONNSTR_
- カスタム:CUSTOMCONNSTR_

たとえば、MySql の接続文字列が「connectionstring1」という名前であれば、「MYSQLCONNSTR_connectionString1」という環境変数でアクセスされます。

### 既定のドキュメント

既定のドキュメントは、Web サイトのルート URL に表示される Web ページです。一覧で最初に一致するファイルが使用されます。 

Web アプリでは、静的コンテンツを提供する代わりに URL に基づいてルーティングするモジュールを使用することがあります。その場合には、このような既定のドキュメントはありません。    

### ハンドラー マッピング

この領域では、特定のファイル拡張子に関する要求を処理するカスタム スクリプト プロセッサを追加します。 

- **拡張子**処理するファイル拡張子 (*.php や handler.fcgi など) を指定します。 
- **スクリプト プロセッサ パス**スクリプト プロセッサの絶対パスを指定します。指定したファイル拡張子に一致するファイルに対する要求が、スクリプト プロセッサによって処理されます。アプリのルート ディレクトリは  `D:\home\site\wwwroot` というパスを使って参照します。
- **追加の引数**スクリプト プロセッサのオプションのコマンド ライン引数です。 


### 仮想アプリケーションとディレクトリ 
 
仮想アプリケーションとディレクトリを構成するには、各仮想ディレクトリとそれに対応する物理パス (Web サイトのルートに対する相対パス) を指定します。必要に応じて、**[アプリケーション]** チェック ボックスをオンにして、仮想ディレクトリをアプリケーションとしてマークすることもできます。


## 診断ログの有効化

診断ログを有効にするには

1. Web アプリのブレードで、[**すべての設定**] をクリックします。
2. [**診断ログの**] をクリックします。 

ログ記録をサポートする Web アプリケーションの診断ログの書き込みに関するオプション: 

- **アプリケーションのログ記録**ファイル システムに、アプリケーションのログを書き込みます。ログ記録は、12 時間継続されます。 

**レベル**アプリケーション ログが有効な場合、このオプションは記録する情報のレベルを指定します (エラー、警告、情報、または詳細)。

**Web サーバーのログ記録**ログは W3C 拡張ログ ファイル形式で保存されます。 

**詳細なエラー メッセージ**詳細なエラー メッセージの .htm ファイルを保存します。ファイルは、/LogFiles/DetailedErrors に保存されます。 

**失敗した要求トレース**XML ファイルへのログ要求が失敗しました。ファイルは /LogFiles/W3SVC*xxx* に保存されます (xxx は一意の識別子)。このフォルダーには、1 つの XSL ファイルと 1 つ以上の XML ファイルが格納されます。この XSL ファイルは、XML ファイルのコンテンツの書式設定とフィルター処理を行う役割を果たすため、必ずダウンロードしてください。

ログ ファイルを表示するには、次のように FTP の資格情報を作成する必要があります。

1. Web アプリのブレードで、[**すべての設定**] をクリックします。
2. [**デプロイ資格情報**] をクリックします。
3. ユーザー名とパスワードを入力します。
4. **[保存]** をクリックします

![](./media/web-sites-configure/configure03.png)


FTP ユーザーのフル_ネーム"app\username"をここでは *アプリ* web アプリの名前を指定します。 ユーザー名が記載されて web アプリ ブレードで、**Essentials** です。  

![](./media/web-sites-configure/configure02.png)

## その他の構成タスク

### SSL 

基本モードまたは標準モードでは、カスタム ドメインの SSL 証明書をアップロードすることができます。 詳細については  [web アプリケーションの HTTPS を有効にする](web-sites-configure-ssl-certificate.md) を参照してください。。 

アップロードされた証明書を表示するには、[**すべての設定]** > [**カスタム ドメインと SSL**] の順にクリックします。

### ドメイン名

Web アプリのカスタム ドメイン名を追加します。詳細については、「[Configure a custom domain name for a web app in Azure App Service (Azure App Service での Web アプリに対するカスタム ドメイン名の構成)]」(web-sites-custom-domain-name.md) を参照してください。

ドメイン名を表示するには、[**すべての設定]** > [**カスタム ドメインと SSL**] の順にクリックします。

### デプロイ

- 継続的なデプロイを設定します。「[Using Git to deploy Web Apps in Azure App Service (Git を使用した Azure App Service での Web アプリのデプロイ)]」(web-sites-publish-source-control.md) を参照してください。
- デプロイ スロット。「[Azure App Service での Web アプリに対するステージング環境へのデプロイ]」(web-sites-staged-publishing.md) を参照してください。

デプロイ スロットを表示するには、[**すべて設定**] > [**デプロイ スロット**] の順にクリックします。


### 監視

基本モードまたは標準モードでは、最大 3 つの地理的に分散した場所から HTTP または HTTPS エンドポイントの可用性をテストすることができます。HTTP 応答コードがエラー (4xx または 5xx) である場合、または、応答に 30 秒以上かかる場合、監視テストは失敗します。すべての指定した場所から監視テストが成功した場合、エンドポイントは利用可能と見なされます。 

詳細については、[次の方法を参照してください。Web エンドポイントの状態の監視](http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409)

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[Try App Service (アプリ サービスの試用)](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。アプリ サービスで、有効期間が短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 次のステップ

- [カスタム ドメイン名の構成] (web-sites-custom-domain-name.md)
- [HTTPS の有効化] (web-sites-configure-ssl-certificate.md)
- [Azure App Service での Web アプリのスケール](web-sites-scale.md)
- [Azure App Service での Web アプリの監視の基本](web-sites-monitor.md)

## 変更内容
* Web サイトからアプリ サービスへの変更に関するガイドについては、以下を参照してください。[Azure App Service and Its Impact on Existing Azure Services (Azure アプリ サービスと既存の Azure サービスへの影響)](http://go.microsoft.com/fwlink/?LinkId=529714)
* 以前のポータルから新しいポータルへの変更に関するガイドについては、以下を参照してください。[Reference for navigating the preview portal (プレビュー ポータルをナビゲートするためのリファレンス)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->