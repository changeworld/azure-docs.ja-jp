<properties
   pageTitle="Visual Studio Code を使用した ASP.NET 5 API アプリの作成"
	description="このチュートリアルでは、Visual Studio Code を使用して ASP.NET 5 API アプリを作成する方法について説明します。"
	services="app-service\api"
	documentationCenter=".net"
	authors="erikre"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="erikre"/>

# Visual Studio Code を使用した ASP.NET 5 API アプリの作成

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## 概要

このチュートリアルでは、[Visual Studio Code](http://code.visualstudio.com//Docs/whyvscode) を使用して ASP.NET 5 API アプリを作成する方法について説明します。ASP.NET 5 は、ASP.NET の刷新版です。ASP.NET 5 は、.NET を使用して最新のクラウドベースの Web アプリケーションを構築するための、新しいオープン ソースのクロスプラットフォーム フレームワークです。詳細については、[ASP.NET 5 の概要](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html)に関するページをご覧ください。API アプリの詳細については、[API Apps の概要](app-service-api-apps-why-best-platform.md)に関するページをご覧ください。

> [AZURE.NOTE]このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、[無料評価版にサインアップする](/pricing/free-trial/)か、[MSDN サブスクライバーの特典を有効に](/pricing/member-offers/msdn-benefits-details/)してください。無料評価版の [App Service アプリのサンプル](http://tryappservice.azure.com)を試すこともできます。

## 前提条件  

* [Visual Studio Code](http://code.visualstudio.com/Docs/setup) をインストールしてセットアップします。
* [Node.js](http://nodejs.org/download/) をインストールします。<br> [Node](http://nodejs.org/) は、JavaScript を使用して高速かつスケーラブルなサーバー アプリケーションを構築するためのプラットフォームです。Node はランタイム (ノード) であり、[npm](http://www.npmjs.com/) は Node モジュールのパッケージ マネージャーです。このチュートリアルでは、npm を使用して、ASP.NET 5 API アプリをスキャフォールディングします。

## ASP.NET 5 と DNX のインストール
ASP.NET 5 と DNX は、OS X、Linux、Windows 上で動作する最新のクラウドや Web アプリを構築するための、効率の優れた .NET スタックです。ASP.NET 5 および DNX は、一から設計し直され、クラウドにデプロイされるアプリまたはオンプレミスで実行されるアプリ用に最適化された開発フレームワークを提供します。オーバーヘッドを最小に抑えたモジュラー コンポーネントから構成されるため、ソリューションを構築するときに柔軟性を保つことができます。

> [AZURE.NOTE]OS X と Linux の ASP.NET 5 と DNX (.NET Execution Environment) は、初期のベータかプレビュー状態にあります。

このチュートリアルでは、最新の開発バージョンの ASP.NET 5 と DNX を使用してアプリケーションの構築を開始する方法について説明します。より安定したリリース版のエクスペリエンスが必要な場合は、[http://www.asp.net/vnext](http://www.asp.net/vnext) をご覧ください。次の手順は、Windows に固有の手順です。OS X、Linux、Windows 用の詳細なインストール手順については、[ASP.NET 5 と DNX のインストール](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)に関するページをご覧ください。

1. .NET Version Manager (DNVM) をインストールするには、コマンド ウィンドウで次のコマンドを実行します。

	<pre class="prettyprint">
@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&amp;{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"
</pre>DNVM スクリプトがダウンロードされ、ユーザー プロファイルに配置されます。

2. PATH 環境変数に加えた変更を有効にするために、上記のコマンドを入力した後にログオフすることが必要になる場合があります。
3. コマンド ウィンドウで次のコマンドを実行して、DNVM の場所を確認します。 

	<pre class="prettyprint">
where dnvm
</pre>コマンド ウィンドウに、次のようなパスが表示されます。

	![dnvm の場所](./media/app-service-create-aspnet-api-app-using-vscode/00-where-dnvm.png)

4. これで、DNVM を利用できるようになりました。アプリケーションを実行するには、これを使って DNX をダウンロードする必要があります。コマンド ウィンドウで、次のコマンドを実行します。

	<pre class="prettyprint">
dnvm upgrade
</pre>

5. コマンド ウィンドウで次のコマンドを実行して、DNVM を確認し、アクティブなランタイムを表示します。

	<pre class="prettyprint">
dnvm list
</pre>コマンド ウィンドウに、アクティブなランタイムの詳細が表示されます。

	![dnvm の場所](./media/app-service-create-aspnet-api-app-using-vscode/00b-dnvm-list.png)

OS X、Linux、Windows 用の詳細なインストール手順については、[ASP.NET 5 と DNX のインストール](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)に関するページをご覧ください。

## API アプリの作成 

このセクションでは、新しい ASP.NET API アプリをスキャフォールディングする方法について説明します。ノード パッケージ マネージャー (npm) を使用して、[Yeoman](http://yeoman.io/)、[Grunt](http://gruntjs.com/)、[Bower](http://bower.io/) をインストールします。

1. Visual Studio Code と Node.js をインストールした後、管理者権限でコマンド プロンプトを開き、VSCode を使用するすべての ASP.NET プロジェクトを配置する場所に移動します。
2. コマンド ウィンドウで次のコマンドを入力して、Yeoman とサポート ツールをインストールします。

	<pre class="prettyprint">
npm install -g yo grunt-cli generator-aspnet bower
</pre>

3. コマンド ウィンドウで次のコマンドを入力して、プロジェクト フォルダーを作成し、アプリをスキャフォールディングします。

	<pre class="prettyprint">
yo aspnet
</pre>

4. 表示されるジェネレーターの指示に従ってスクロールし、**[Web API Application]** タイプを選択します。

	![Yoman - ASP.NET 5 generator](./media/app-service-create-aspnet-api-app-using-vscode/01-yo-aspnet.png)

5. 新しい ASP.NET API アプリの名前を **ContactsList** に設定します。この名前は、このチュートリアルの後半で示すコードで使用されます。<br>Yoman によって、**ContactsList** という名前の新しいフォルダーと新しいアプリに必要なファイルが作成されます。
6. **Visual Studio Code** を開きます。<br> コマンド ウィンドウで「**code .**」と入力すると、VSCode を開くことができます。
7. **[ファイル]** メニューの **[フォルダーを開く]** を選択し、ASP.NET API アプリが格納されているフォルダーを選択します。

	![[フォルダー] ダイアログ ボックスの選択](./media/app-service-create-aspnet-api-app-using-vscode/02-open-folder.png)

	VSCode にプロジェクトが読み込まれ、**エクスプローラー** ウィンドウに表示されます。

	![VSCode displaying Contact project](./media/app-service-create-aspnet-api-app-using-vscode/03-vscode-contacts-project.png)

8. **VSCode** の **[表示]** メニューの **[コマンド パレット]** を選択します。
9. **コマンド パレット**で、次のコマンドを入力します。

	<pre class="prettyprint">
dnx:dnu restore - (ContactsList)
</pre>入力を開始すると、一覧に完全なコマンド ラインが表示されます。

	![Restore コマンド](./media/app-service-create-aspnet-api-app-using-vscode/04-dnu-restore.png)

	Restore コマンドは、アプリケーションを実行するために必要な NuGet パッケージをインストールします。準備が整うと、コマンド ウィンドウに "**復元が完了しました**" と表示されます。

## API アプリの変更

ここでは、**ContactsList** アプリに **Contact** クラスと **ContactsController** クラスを追加して変更を加えます。

1. **ContactsList** プロジェクトにカーソルを置き、[新しいフォルダー] アイコンを使用して *Models* という名前の新しいフォルダーを作成します。

	![New Models folder](./media/app-service-create-aspnet-api-app-using-vscode/07-new-folder.png)

2. **Models** フォルダーを右クリックし、次のコードを含む新しいクラス *Contact.cs* を作成します。

	<pre class="prettyprint">
namespace ContactsList.Models
{
    public class Contact
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string EmailAddress { get; set; }
    {
{
</pre>

3. **Controllers** フォルダーを右クリックし、*ContactsController.cs* ファイルを追加します。次のような内容になります。

	<pre class="prettyprint">
	using System.Collections.Generic;
	using Microsoft.AspNet.Mvc;
	using ContactsList.Models;
	
	namespace ContactsList.Controllers
	{
	    [Route("api/[controller]")]
	    public class ContactsController : Controller
	    {
	        // GET: api/Contacts
	        [HttpGet]
	        public IEnumerable&lt;Contact&gt; Get()
	        {
	            return new Contact[]{
	                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
	                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
	                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
	            };
	        }
	    }
	}
	</pre>

4. **[ファイル]**、**[すべてを保存]** の順に選択して、すべてのファイルを保存します。
5. **コマンド パレット**で、次のコマンドを入力してアプリをローカルで実行します。

	<pre class="prettyprint">
dnx: kestrel - (ContactsList, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001
</pre>コマンド ウィンドウに "*開始しました*" と表示されます。コマンド ウィンドウに "*開始しました*" と表示されない場合は、VSCode の左下隅にプロジェクトのエラーが示されていないかどうかを確認します。

5. ブラウザーを開き、次の URL に移動します。

	****http://localhost:5001/api/Contacts**

	*Contacts.json* の内容を表示できます。このファイルを表示すると、次の内容が表示されます。

	![Returned json content](./media/app-service-create-aspnet-api-app-using-vscode/08-contacts-json.png)

## API アプリのメタデータの変更
ASP.NET API プロジェクトを API アプリとしてデプロイできるようにするメタデータは、プロジェクトのルートにある *apiapp.json* ファイルに格納する必要があります。

1. VSCode で、*wwwroot* フォルダーを右クリックし、**[新しいファイル]** オプションを選択します。
2. 新しいファイルに *apiapp.json* という名前を付けます。<br> *apiapp.json* が *wwwroot* フォルダー内にあることを確認します。
3. *apiapp.json* ファイルに次のコードを追加します。

	<pre class="prettyprint">
{
    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
    "id": "ContactsList",
    "namespace": "microsoft.com",
    "gateway": "2015-01-14",
    "version": "1.0.0",
    "title": "ContactsList",
    "summary": "",
    "author": "",
    "endpoints": null
{
</pre>

*apiapp.json* ファイルには、動的 Swagger API 定義 JSON のエンドポイントを指定できます。ただし、このチュートリアルでは、静的 API 定義ファイルを使用します。動的 Swagger 生成を使用する例については、[API アプリとしての Web API プロジェクトの構成](app-service-dotnet-create-api-app-visual-studio.md)に関するページをご覧ください。

## 静的 Swagger API 定義の追加
静的 Swagger 2.0 API 定義ファイルを指定するには、Web ルートにフォルダーを作成し、そのフォルダーに API 定義ファイルを配置する必要があります。

1. VSCode で、*wwwroot* フォルダー内に *metadata* という名前のフォルダーを作成します。
2. 新しい *metadata* フォルダーを右クリックし、*apiDefinition.swagger.json* という名前の新しいファイルを追加します。 
3. 次の json 構文を新しいファイルに追加します。

	<pre class="prettyprint">
{
  "swagger": "2.0",
  "info": {
    "version": "v1",
    "title": "ContactsList"
  },
  "host": "MUST REPLACE THIS WITH YOUR HOST URL",
  "schemes": [
    "https"
  ],
  "paths": {
    "/api/Contacts": {
      "get": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Get",
        "consumes": [],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "type": "array",
              "items": {
                "$ref": "#/definitions/Contact"
              {
            {
          {
        },
        "deprecated": false
      },
      "post": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Post",
        "consumes": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml",
          "application/x-www-form-urlencoded"
        ],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "parameters": [
          {
            "name": "contact",
            "in": "body",
            "required": true,
            "schema": {
              "$ref": "#/definitions/Contact"
            {
          {
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "$ref": "#/definitions/Object"
            {
          {
        },
        "deprecated": false
      {
    {
  },
  "definitions": {
    "Contact": {
      "type": "object",
      "properties": {
        "Id": {
          "format": "int32",
          "type": "integer"
        },
        "Name": {
          "type": "string"
        },
        "EmailAddress": {
          "type": "string"
        {
      {
    },
    "Object": {
      "type": "object",
      "properties": {}
    {
  {
{
</pre>

このチュートリアルの後の手順で、上記のホスト URL のプレースホルダー文字列を Azure ホスト URL で置き換えます (この URL は後で作成してコピーします)。

## Azure プレビュー ポータルでの API アプリの作成

> [AZURE.NOTE]このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、[無料評価版にサインアップする](/pricing/free-trial/)か、[MSDN サブスクライバーの特典を有効に](/pricing/member-offers/msdn-benefits-details/)してください。無料評価版の [App Service アプリのサンプル](http://tryappservice.azure.com)を試すこともできます。

1. [Azure プレビュー ポータル](https://portal.azure.com)にログインします。

2. ポータルの左下にある **[新規]** をクリックします。

3. **[Web + モバイル]、[API アプリ]** の順にクリックします。

	![Azure New API App](./media/app-service-create-aspnet-api-app-using-vscode/09-azure-newapiapp.png)

4. **[名前]** に ContactsList などの値を入力します。

5. App Service プランを選択するか、新しいプランを作成します。新しいプランを作成する場合は、価格レベル、および場所などのオプションを選択します。

	![Azure New API App Blade](./media/app-service-create-aspnet-api-app-using-vscode/10-azure-newappblade.png)

6. **[作成]** をクリックします。

	![API App Blade](./media/app-service-create-aspnet-api-app-using-vscode/11-azure-apiappblade.png)

	**[スタート画面にピン留めする]** チェック ボックスをオンにしてアプリを作成すると、**[ホーム]** か **[参照]** をクリックしてアプリを簡単に見つけることができるようになります。このチェック ボックスをオフにした場合は、左側の **[通知]** をクリックして API アプリの作成状態を確認し、通知をクリックして新しい API アプリのブレードに移動します。

7. **[設定]、[アプリケーションの設定]** の順にクリックします。

8. アクセス レベルを **[パブリック (匿名)]** に設定します。

9. **[保存]** をクリックします。

	![Azure App Settings](./media/app-service-create-aspnet-api-app-using-vscode/12-azure-appsettings.png)

## 新しい API アプリからの Git 発行の有効化

Git は、Azure の Web サイトをデプロイするために使用できる分散型バージョン コントロール システムです。API アプリ用に記述したコードはローカルの Git リポジトリに格納されます。このコードをリモート リポジトリにプッシュして Azure にデプロイします。このデプロイ方法は、API アプリが Web アプリに基づいているために API アプリで使用できる App Service Web アプリの機能です。つまり、Azure App Service の API アプリは、Web サービスをホストするための追加の機能を持つ Web アプリであるということです。

ポータルでは、**[API アプリ]** ブレードで API アプリ固有の機能を管理し、**[API アプリ ホスト]** ブレードで Web アプリと共通する機能を管理します。このセクションでは、**[API アプリ ホスト]** ブレードに移動して、Git デプロイメントの機能を構成します。

1. [API アプリ] ブレードの **[API アプリ ホスト]** をクリックします。

	![Azure API App Host](./media/app-service-create-aspnet-api-app-using-vscode/13-azure-apiapphost.png)

2. **[API アプリ]** ブレードの **[デプロイメント]** セクションを見つけて、**[継続的配置の設定]** をクリックします。必要に応じて、ブレードのこの部分まで下へスクロールしてください。

	![Azure API App Host](./media/app-service-create-aspnet-api-app-using-vscode/14-azure-deployment.png)

3. **[ソースの選択]、[ローカル Git リポジトリ]** の順にクリックします。

5. **[OK]** をクリックします。

	![Azure のローカル Git リポジトリ](./media/app-service-create-aspnet-api-app-using-vscode/15-azure-localrepository.png)

6. API アプリまたはその他の App Service アプリを発行するためのデプロイ資格情報をまだ設定していない場合は、ここで設定します。

	* **[デプロイ資格情報の設定]** をクリックします。

	* ユーザー名とパスワードを作成します。このパスワードは、後で Git をセットアップするときに必要になります。

	* **[保存]** をクリックします。

	![Azure デプロイメント資格情報](./media/app-service-create-aspnet-api-app-using-vscode/16-azure-credentials.png)

7. **[API アプリ ホスト]** ブレードで、**[設定]、[プロパティ]** の順にクリックします。デプロイ先のリモート Git リポジトリの URL は、[GIT URL] の下に表示されます。

8. チュートリアルの後の手順で使用するために、この **GIT URL** をコピーします。

	![Azure Git の URL](./media/app-service-create-aspnet-api-app-using-vscode/17-azure-giturl.png)

9. さらに、**[API アプリ]** ブレードで **URL** をコピーします。この URL は、*apiDefinition.swagger.json* ファイルの "host" 値を更新するために使用します。

	![Azure URL](./media/app-service-create-aspnet-api-app-using-vscode/18-azure-url.png)

10. VSCode で、*apiDefinition.swagger.json* ファイルを開き、"MUST REPLACE THIS WITH YOUR HOST URL" を前の手順でコピーした **URL** で置き換えます。
11. さらに、ホスト値の先頭から、"https://" 文字を削除します。
12. 変更内容を *apiDefinition.swagger.json* ファイルに保存します。

## Azure App Service への API アプリの発行

このセクションでは、Azure App Service で実行されている API アプリにサンプル アプリケーションをデプロイするために、ローカル Git リポジトリを作成してそのリポジトリから Azure にプッシュします。

1. VSCode の左側のナビゲーション バーで、[Git] オプションを選択します。
2. Git がまだインストールされていない場合は、示されたリンク ([Chocolatey](https://chocolatey.org/packages/git) か [git-scm.com](http://git-scm.com/downloads)) のいずれかを選択してインストールします。Git を初めて使う場合は、**git-scm.com** を選択し、Windows コマンド プロンプトから Git と GitBash を使用するオプションを選択します。 
3. Git がインストールされたら、VSCode を再起動し、左側のバーの [Git] オプションを選択します。
4. VSCode で、**[git リポジトリの初期化]** を選択して、ワークスペースが git によるソース管理の対象になるように設定します。 

	![Git の初期化](./media/app-service-create-aspnet-api-app-using-vscode/19-initgit.png)

5. 
6. コミット メッセージを追加し、**[すべてコミット]** チェック マークを選択します。

	![Git すべてコミット](./media/app-service-create-aspnet-api-app-using-vscode/20-git-commit.png)

6. **GitBash** を見つけて開きます。代わりに、Windows コマンド プロンプトを使用できます。
7. **GitBash** で、VSCode プロジェクト フォルダーに移動します。次に例を示します。

	<pre class="prettyprint">
cd c:\VSCodeProjects\ContactsList
</pre>

7. コピーしておいた (".git" で終わる) Git URL を使用して、作成した Web アプリ (API アプリ ホスト) に更新をプッシュするためのリモート参照を作成します。

	<pre class="prettyprint">
git remote add azure [URL for remote repository]
</pre>

8. 次のコマンドを入力して、変更内容を Azure にプッシュします。

	<pre class="prettyprint">
git push azure master
</pre>以前作成したパスワードを入力するように求められます。**注: パスワードは表示されません。**

	上記のコマンドの出力は、デプロイが成功したことを示すメッセージで終わります。

	<pre class="prettyprint">
remote: Deployment successful.
To https://user@testsite.scm.azurewebsites.net/testsite.git
[new branch]      master -> master
</pre>

> [AZURE.NOTE]アプリを変更した場合は再パブリッシュできます。そのためには、VSCode で **[すべてコミット]** チェック マークを選択して、**GitBash** で **git push azure master** コマンドを入力します。

## Azure プレビュー ポータルでの API 定義の表示
API を API アプリにデプロイしたので、Azure プレビュー ポータルで API 定義を確認できるようになりました。最初にゲートウェイを再起動します。これによって、API アプリの API 定義が変更されたことを Azure で認識できるようになります。ゲートウェイは、API の管理とリソース グループ内の API アプリの承認を処理する Web アプリです。

1. Azure プレビュー ポータルで、前に作成した API アプリの **[API アプリ]** ブレードに移動して、**[ゲートウェイ]** リンクをクリックします。
2. **[ゲートウェイ]** ブレードの **[再起動]** をクリックします。これでこのブレードを閉じることができます。
3. **[API アプリ]** ブレードの **[再起動]** をクリックします。 
4. **[API アプリ]** ブレードの **[API 定義]** をクリックします。<br> [API 定義] ブレードに 2 つのメソッドが表示されます。GET メソッドと POST メソッドがすぐに表示されない場合は、Azure によってアプリが更新されるまで数秒待ってください。次に、**[API アプリ]** ブレードの **[API 定義]** をクリックします。

## Azure でのアプリの実行
Azure プレビュー ポータルで、目的の API アプリの **[API アプリ ホスト]** ブレードに移動し、**[参照]** をクリックします。次に、連絡先の詳細を表示するために、URL の末尾に **api/Contacts** を追加します。


## まとめ
このチュートリアルでは、Visual Studio Code を使用して API アプリを作成する方法について説明しました。Visual Studio Code の詳細については、[Visual Studio Code](https://code.visualstudio.com/Docs/) に関するページをご覧ください。API アプリの詳細については、[API Apps の概要](app-service-api-apps-why-best-platform.md)に関するページをご覧ください。
 

<!----HONumber=September15_HO1-->