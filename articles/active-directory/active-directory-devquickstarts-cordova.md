<properties
	pageTitle="Azure AD Cordova の概要 | Microsoft Azure"
	description="サインインし、OAuth を使用して Azure AD で保護されている API を呼び出すために Azure AD と統合する Cordova アプリケーションを構築する方法を説明します。"
	services="active-directory"
	documentationCenter=""
	authors="vibronet"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="vittorib"/>

# Azure AD と Apache Cordova アプリとの統合

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova により、完全なネイティブ アプリケーションとしてモバイル デバイス上で実行できる、HTML5/JavaScript アプリケーションを開発できます。Azure AD を使用すると、Cordova アプリケーションにエンタープライズ レベルの認証機能を追加できます。iOS、Android、Windows ストア、および Windows Phone 上で Azure AD のネイティブ SDK をラップする Cordova プラグインにより、ユーザーの AD アカウントを用いたサインオンのサポート、Office 365 と Azure API へのアクセス権の取得、さらにはユーザー独自のカスタム Web API の呼び出し保護を行うように、アプリケーションを拡張できます。

このチュートリアルでは、Active Directory 認証ライブラリ (ADAL) の Apache Cordova プラグインを使用して、簡単なアプリケーションに次の機能を付加して能力を向上させます。

-	少数行のコードで、AD ユーザーを認証し、Azure AD Graph API を呼び出すためのトークンを取得する。
-	そのトークンを使用して Graph API を呼び出し、そのディレクトリのクエリを実行して結果を表示する。
-	ユーザーに対する認証プロンプトを最小限に抑えるために、ADAL トークン キャッシュを利用する。

これを行うには、次の手順を実行する必要があります。

2. アプリケーションを Azure AD に登録する
2. トークンを要求するコードをアプリケーションに追加する
3. トークンを使用して Graph API のクエリを実行して結果を表示するコードを追加する
4. 対象とするすべてのプラットフォームでの Cordova デプロイメント プロジェクトと、Cordova ADAL プラグインを作成し、エミュレーターでソリューションをテストする

## *0.前提条件*

このチュートリアルを実行するには、以下が必要です。

- アプリケーション開発権限があるアカウントが登録されている Azure AD テナント
- Apache Cordova を使用するように構成された開発環境

両方とも設定済みの場合は、手順 1. に直接進んでください。

Azure AD テナントがない場合は、[取得方法の手順](active-directory-howto-tenant.md)を参照できます。

Apache Cordova がコンピューターにセットアップされていない場合は、以下をインストールしてください。

- [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova CLI](https://cordova.apache.org/) (NPM パッケージ マネージャー `npm install -g cordova` を使用して簡単にインストールできます)

これらは PC と Mac のどちらでも動作することに注意してください。

各ターゲット プラットフォームには、それぞれ異なる前提条件があります。

- Windows タブレット/PC またはスマートフォンのアプリケーション バージョンを構築して実行する場合
	- [Visual Studio 2013 for Windows Update 2 以降](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express または別のバージョン)。
- iOS 向けに構築して実行する場合
	-   Xcode 5.x 以上。http://developer.apple.com/downloads で、または [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12) からダウンロードします。
	-   [ios sim](https://www.npmjs.org/package/ios-sim) – コマンド ラインから iOS シミュレーターで iOS アプリケーションを起動することができます (`npm install -g ios-sim` として、端末経由で簡単にインストールできます)。

- Android 向けにアプリケーションを構築して実行する場合
	- [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 以上をインストールします。`JAVA_HOME` (環境変数) が JDK インストール パス (たとえば、C:\\Program Files\\Java\\jdk1.7.0\_75) に従って正しく設定されていることを確認します。
	- [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools) をインストールし、`<android-sdk-location>\tools` の場所 (たとえば、C:\\tools\\Android\\android-sdk\\tools) を `PATH` 環境変数に追加します。
	- Android SDK Manager を開き (たとえば、端末 `android` 経由で)、以下をインストールします。
    - *Android 5.0.1 (API 21)* プラットフォーム SDK
    - *Android SDK ビルド ツール* バージョン 19.1.0 以上
    - *Android Support Repository* (追加)

  Android SDK は、既定のエミュレーター インスタンスを提供しません。エミュレーターで Android アプリを実行する場合は、端末から `android avd` を実行してエミュレーター インスタンスを作成し、次に *[作成...]* を選択します。推奨 *API レベル*は 19 以上です。Android エミュレーターおよび作成のオプションの詳細については、[AVD Manager](http://developer.android.com/tools/help/avd-manager.html) を参照してください。


## *1.アプリケーションを Azure AD に登録する*

注: この__手順は省略可能__です。このチュートリアルでは、事前に準備された値を用意しています。これにより独自のテナントで何もプロビジョニングしなくても、実行するサンプルを参照できます。ただし、この手順を必ず実行し、プロセスをよく理解することをお勧めします。このプロセスは、独自のアプリケーションを作成する際に必要になります。

Azure AD は、認識しているアプリケーションに対してのみトークンを発行します。アプリケーションから Azure AD を使用するには、アプリケーション用のエントリをテナントで事前に作成しておく必要があります。新しいアプリケーションをテナントに登録するには、次の手順を実行します。

-	[Microsoft Azure の管理ポータル](https://manage.windowsazure.com)にサインインします。
-	左側のナビゲーションで **[Active Directory]** をクリックします。
-	アプリケーションの登録先となるテナントを選択します。
-	**[アプリケーション]** タブをクリックし、下部のドロアーで **[追加]** をクリックします。
-	画面の指示に従って、新しい**ネイティブ クライアント アプリケーション**を作成します (Cordova アプリは HTML ベースですが、ここではネイティブ クライアント アプリケーションを作成するため、`Native Client Application` オプションを選択する必要があります。そうしないと、アプリケーションが機能しません)。
    -	アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
    -	**リダイレクト URI** は、トークンをアプリに返すために使用される URI です。「`http://MyDirectorySearcherApp`」を入力します。

登録が完了すると、AAD により、アプリに一意のクライアント ID が割り当てられます。この値は、以降のセクションで必要になります。この値は、新しく作成されたアプリの **[構成]** タブに表示されます。

`DirSearchClient Sample` を実行するには、_Azure AD Graph API_ をクエリするための新しく作成されたアプリのアクセス許可を付与します。
-	**[構成]** タブで、[他のアプリケーションに対するアクセス許可] セクションに移動します。"Azure Active Directory" アプリケーションに対して、**[デリゲートされたアクセス許可]** で **[サインイン ユーザーとしてディレクトリにアクセスする]** アクセス許可を追加します。これにより、アプリケーションが Graph API を使用してユーザーをクエリできるようになります。

## *2.チュートリアルに必要なサンプル アプリ リポジトリを複製する*

シェルまたはコマンド ラインから、次のコマンドを入力します。

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## *3.Cordova アプリケーションを作成する*

Cordova アプリケーションを作成するには、複数の方法を使用できます。このチュートリアルでは、Cordova コマンド ライン インターフェイス (CLI) を使用します。シェルまたはコマンド ラインから、次のコマンドを入力します。


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

このコマンドは、Cordova プロジェクト用のフォルダー構造とスキャフォールディングを作成し、スタート プロジェクトのコンテンツを www サブフォルダーにコピーします。新しい DirSearchClient フォルダーに移動します。

    cd .\DirSearchClient

Graph API を呼び出すために必要な、ホワイトリスト プラグインを追加します。

     cordova plugin add cordova-plugin-whitelist

次に、すべてのサポート対象プラットフォームを追加します。実稼働するサンプルにするために、少なくとも次の 1 つのコマンドを実行する必要があります。iOS を Windows 上で、または Windows/Windows Phone を Mac 上でエミュレートすることはできないことに注意してください。

    cordova platform add android
    cordova platform add ios
    cordova platform add windows

最後に、Cordova プラグイン用の ADAL をプロジェクトに追加できます。

    cordova plugin add cordova-plugin-ms-adal

## *3.ユーザーを認証して AAD からトークンを取得するコードを追加する*

このチュートリアルで開発するアプリケーションは、ベアボーン ディレクトリ検索機能を提供します。この機能を使用すると、エンド ユーザーは、ディレクトリ内の任意のユーザーのエイリアスを入力し、いくつかの基本的な属性を表示できます。スタート プロジェクトには、アプリケーションの基本的なユーザー インターフェイスの定義が (www/index.html に) 含まれています。さらに基本的なアプリケーションのイベント サイクル、ユーザー インターフェイスのバインド、結果表示ロジックを関連付けるスキャフォールディングが (www/js/index.js に) 含まれています。残されている作業は、ID タスクを実装するロジックを追加することだけです。

最初に、アプリと対象のリソースとを識別するために AAD が使用するプロトコル値を、コードに設定する必要があります。これらの値は、後でトークン要求を作成するために使用されます。index.js ファイルの最上部に、以下のスニペットを挿入します。

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

`redirectUri` と `clientId` の値は、AAD でアプリを記述する値と一致している必要があります。これらの値は、このチュートリアルの手順 1 で説明しているように、Azure ポータルの [構成] タブに表示されます。注: 独自のテナントに新しいアプリを登録しないことを選択した場合は、上記の事前構成値を単に貼り付けるだけで、サンプルを実行することができます。ただし、運用環境向けのアプリには、必ず独自の項目を作成する必要があります。

次に、実際のトークン要求コードを追加する必要があります。`search ` と `renderdata ` の定義の間に以下のスニペットを挿入します。

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
この関数を、2 つの主要な部分に分解することによって調べてみましょう。このサンプルは、特定の 1 テナントに関連付けるのではなく、どのテナントでも機能するように設計されています。これは "/common" エンドポイントを使用します。これによってユーザーは、認証時に任意のアカウントを入力することができ、認証要求は、そのアカウントが所属しているテナントに渡されます。メソッドの最初の部分では、ADAL キャッシュに既に保管されたトークンが存在するかどうかを検査し、存在する場合には、そのトークンの送信元のテナントを使用して ADAL を再初期化します。これは追加のプロンプトを回避するために必要です。"/common" を使用すると、必ずユーザーに新しいアカウントの入力が求められるからです。
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
メソッドの 2 番目の部分では、適切なトークン要求を実行します。`acquireTokenSilentAsync` メソッドは、ADAL に、UX を表示せずに指定されたリソースのトークンを返すように要求します。これが起きる可能性があるのは、キャッシュに既に適切なアクセス トークンが保管されているか、またはプロンプトを表示させずに新しいアクセス トークンを取得するために使用できる更新トークンがある場合です。試行に失敗する場合は、`acquireTokenAsync` に戻ります。これはユーザーにはっきりと認証を求めるプロンプトを表示します。
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
トークンを取得したので、最後に Graph API を呼び出し、必要な検索クエリを実行することができます。次のスニペットを、`authenticate` 定義のすぐ下に挿入します。

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
開始ポイント ファイルにより、テキスト ボックスにユーザーのエイリアスを入力するためのベアボーン UX が提供されています。このメソッドではその値を使用して、クエリを作成し、アクセス トークンと組み合わせ、Graph に送信し、結果を解析します。開始ポイント ファイル内に事前に存在している renderData メソッドによって、結果が処理されて表示されます。

## *4.実行*
これでアプリは実行準備完了です。 操作は非常に簡単です。アプリが起動したら、表示したいユーザーのエイリアスをテキスト ボックスに入力して、ボタンをクリックするだけです。認証情報が求められます。認証と検索に成功すると、検索対象のユーザーの属性が表示されます。その後の実行では、前に取得したトークンがキャッシュ内に存在しているので、プロンプトが表示されずに検索が行われます。アプリを実行するための具体的な手順は、プラットフォームごとに異なります。

####Windows 10:

   タブレット/PC: `cordova run windows --archs=x64 -- --appx=uap`

   モバイル (PC に接続されている Windows10 モバイル デバイスが必要): `cordova run windows --archs=arm -- --appx=uap --phone`

   __注__: 最初の実行時に、開発者ライセンスのサインインが求められることがあります。詳細については、「[開発者用ライセンスの取得 (ストア アプリ)](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx)」を参照してください。

####Windows 8.1 タブレット/PC:

   `cordova run windows`

   __注__: 最初の実行時に、開発者ライセンスのサインインが求められることがあります。詳細については、「[開発者用ライセンスの取得 (ストア アプリ)](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx)」を参照してください。

####Windows Phone 8.1:

   コネクテッド デバイスで実行するには、`cordova run windows --device -- --phone` を実行します。

   既定のエミュレーターで実行するには、`cordova emulate windows -- --phone` を実行します。

   `cordova run windows --list -- --phone` を使用すると、選択可能なすべての対象を表示できます。`cordova run windows --target=<target_name> -- --phone` を使用すると、特定のデバイスまたはエミュレーターでアプリケーションを実行できます (たとえば、`cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`)。

####Android:

   コネクテッド デバイスで実行するには、`cordova run android --device` を実行します。

   既定のエミュレーターで実行するには、`cordova emulate android` を実行します。

   __注__: 「*前提条件*」セクションでの説明に従って、*AVD Manager* を使用してエミュレーター インスタンスを作成したことを確認します。

   `cordova run android --list` を使用すると、選択可能なすべての対象を表示できます。`cordova run android --target=<target_name>` を使用すると、特定のデバイスまたはエミュレーターでアプリケーションを実行できます (たとえば、`cordova run android --target="Nexus4_emulator"`)。

####iOS:

   コネクテッド デバイスで実行するには、`cordova run ios --device` を実行します。

   既定のエミュレーターで実行するには、`cordova emulate ios` を実行します。

   __注__: エミュレーターで実行する `ios-sim` パッケージがインストールされていることを確認します。詳細については、「*前提条件*」セクションを参照してください。

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

`cordova run --help` を使用すると、追加の構築オプションと実行オプションを参照できます。

完全なサンプル (構成値を除く) を取得するには、[ここ](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient)をクリックしてください。ここからは上級のさらに興味深いシナリオに移動することができます。次のチュートリアルを試してみてください。

[Azure AD による Node.JS Web API のセキュリティ保護 >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->