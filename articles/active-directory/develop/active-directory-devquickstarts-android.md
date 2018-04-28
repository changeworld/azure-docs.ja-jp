---
title: Azure AD Android の概要 | Microsoft Docs
description: サインインと、OAuth 2.0 を使用して Azure AD で保護されている API を呼び出しのために Azure AD と統合する、Android アプリケーションを構築する方法を説明します。
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/30/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 25a908c542bf8fdd8008841a1865cdfb40d847fc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-android-getting-started"></a>Azure AD Android の概要
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

デスクトップ アプリケーションを開発する場合、Azure Active Directory (Azure AD) を使用すると、オンプレミスの Active Directory アカウントを使ってユーザーの認証処理を容易に行うことができます。 また、Office 365 API や Azure API などの Azure AD によって保護された任意の Web API をアプリケーションで安全に使用することもできます。

保護されたリソースにアクセスする必要がある Android クライアントに対しては、Azure AD は Active Directory 認証ライブラリ (ADAL) を提供します。 ADAL の唯一の目的は、アプリがアクセス トークンを容易に取得できるようにすることです。 それがどれほど簡単であるかを示すために、次の処理を実行する Android To-Do List アプリケーションを作成します。

* [OAuth 2.0 認証プロトコル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)を使用して To-Do List API を呼び出すためのアクセス トークンを取得します。
* ユーザーの To-Do List を取得します。
* ユーザーをサインアウトします。

作業を開始するには、ユーザーを作成し、アプリケーションを登録できるようにするために Azure AD テナントが必要になります。 テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>手順 1. Node.js REST API の TODO サンプル サーバーをダウンロードして実行する
この Node.js REST API の TODO サンプルは、Azure AD の単一のテナント To-Do REST API の作成用の既存のサンプルを扱うために、特別に作成されました。 これはクイック スタートの前提条件です。

これを設定する方法については、[Node.js 用の Azure Active Directory のサンプル REST API サービス](active-directory-devquickstarts-webapi-nodejs.md)内の既存のサンプルを参照してください。


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>手順 2. Web API を Azure AD テナントに登録する
Active Directory は、2 種類のアプリケーションの追加をサポートします。

- ユーザーにサービスを提供する Web API
- これらの Web API にアクセスする (Web またはデバイスのいずれかで実行される) アプリケーション

この手順では、このサンプルをテストするために、ローカルで実行する Web API を登録します。 この Web API は、通常、アプリがアクセスする機能を提供する REST サービスです。 Azure AD は、任意のエンドポイントを保護できます。

ここでは、前述の TODO REST API を登録していると仮定します。 ただし、この方法は、Azure Active Directory で保護するあらゆる Web API に対して機能します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 上部のバーで、自分のアカウントをクリックします。 **[ディレクトリ]** の一覧から、アプリケーションを登録する Azure AD テナントを選択します。
3. 左側のウィンドウで **[すべてのサービス]** をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックし、**[追加]** を選択します。
5. たとえば **TodoListService** など、アプリケーションのわかりやすい名前を入力し、**[Web アプリケーションや Web API]** を選択して、**[次へ]** をクリックします。
6. サインオン URL には、サンプルのベース URL を入力します。 既定では、これは `https://localhost:8080` です。
7. **[OK]** をクリックして登録を完了します。
8. 引き続き Azure Portal で、アプリケーション ページに移動し、アプリケーションの ID 値を探してコピーします。 この値は後でアプリケーションを構成するときに必要です。
9. **[設定]**  ->  **[プロパティ]** ページで、アプリ ID URI を更新し、「`https://<your_tenant_name>/TodoListService`」を入力します。 `<your_tenant_name>` を Azure AD テナントの名前に置き換えます。

## <a name="step-3-register-the-sample-android-native-client-application"></a>手順 3. サンプルの Android ネイティブ クライアント アプリケーションを登録する
このサンプルでは、Web アプリケーションを登録する必要があります。 これにより、アプリケーションは登録した Web API と通信できるようになります。 Azure AD は、登録されていないアプリケーションに対して、サインインを要求することさえも拒否します。 モデル化されたセキュリティの一環としてそのようになります。

ここでは、前述のサンプル アプリケーションを登録していると仮定します。 ただし、この手順は、開発しているあらゆるアプリに対して機能します。

> [!NOTE]
> アプリケーションと Web API の両方を 1 テナントに配置しているのはなぜなのか不思議に思われるかもしれません。 ご想像のとおり、別のテナントから Azure AD に登録されている外部 API にアクセスするアプリを構築することができます。 そのようにすると、お客様に対してアプリケーションでの API の使用に同意が求められることになります。 iOS 向け Active Directory 認証ライブラリはユーザーに代わって同意に対応します。 より高度な機能を知るにつれて、これが、Azure、Office、および他のすべてのサービス プロバイダーから一連の Microsoft API にアクセスするために必要な処理の重要な部分であることがわかるようになります。 この時点では、同じテナントに Web API とアプリケーションの両方を登録しているので、同意のプロンプトは表示されません。 これは、通常、自社のみで使用するアプリケーションを開発している場合に当てはまります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 上部のバーで、自分のアカウントをクリックします。 **[ディレクトリ]** の一覧から、アプリケーションを登録する Azure AD テナントを選択します。
3. 左側のウィンドウで **[すべてのサービス]** をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックし、**[追加]** を選択します。
5. アプリケーションのわかりやすい名前 (たとえば **TodoListClient-Android** など) を入力し、**[ネイティブ クライアント アプリケーション]** を選択して、**[次へ]** をクリックします。
6. リダイレクト URI には、「`http://TodoListClient`」を入力します。 **[完了]** をクリックします。
7. アプリケーション ページで、アプリケーション ID の値を探してコピーします。 この値は後でアプリケーションを構成するときに必要です。
8. **[設定]** ページで、**[必要なアクセス許可]** を選択し、**[追加]** を選択します。  TodoListService を見つけて選択し、**[TodoListService へのアクセス]** アクセス許可を **[デリゲートされたアクセス許可]** の下に追加し、**[完了]** をクリックします。

Maven を使用して構築するために、最上位レベルで pom.xml を使用することができます。

1. このリポジトリを任意のディレクトリに複製します。

  `$ git clone https://github.com/Azure-Samples/active-directory-android.git`  
2. [「Setting Up Maven for use with the Android environment (Android 環境で Maven を使用するためのセットアップ)」の「Prerequisites (前提条件)」](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Maven)の手順に従います。
3. SDK 19 でエミュレーターをセットアップします。
4. リポジトリを複製したルート フォルダーに移動します。
5. 次のコマンドを実行します。`mvn clean install`
6. ディレクトリをクイック スタート サンプル `cd samples\hello` に変更します。
7. 次のコマンドを実行します。`mvn android:deploy android:run`

   アプリが起動します。
8. テスト ユーザー資格情報を入力して、試行します。

AAR パッケージに加え、JAR パッケージが送信されます。

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>手順 4. Android ADAL をダウンロードして Eclipse ワークスペースに追加する
ADAL を Android プロジェクトで簡単に使用できるようにするために、複数のオプションが用意されています。

* ソース コードを使用して、このライブラリを Eclipse にインポートし、アプリケーションにリンクします。
* Android Studio を使用する場合は、AAR パッケージ形式を使用し、バイナリを参照します。

### <a name="option-1-source-zip"></a>オプション 1. ソース Zip
ソース コードのコピーをダウンロードするには、ページの右側にある **[ZIP のダウンロード]** をクリックします。 または、[GitHub からダウンロード](https://github.com/AzureAD/azure-activedirectory-library-for-android/releases)することもできます。

### <a name="option-2-source-via-git"></a>オプション 2. Git 経由のソース
SDK のソース コードを Git 経由で取得するには、次のように入力します。

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>オプション 3. Gradle 経由のバイナリ
Maven 中央リポジトリからバイナリを取得できます。 AAR パッケージは、次のようにして Android Studio でプロジェクトに含めることができます。

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>オプション 4. Maven 経由の AAR
M2Eclipse プラグインを使用する場合は、pom.xml ファイルで次のように依存関係を指定できます。

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>オプション 5. libs フォルダー内の JAR パッケージ
Maven リポジトリから JAR ファイルを取得して、プロジェクトの **libs** フォルダーにドロップできます。 JAR パッケージには含まれていないので、必要なリソースもプロジェクトにコピーする必要があります。

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>手順 5. プロジェクトに Android ADAL への参照を追加する
1. プロジェクトへの参照を追加し、Android ライブラリとして指定します。 手順が不明な場合は、[Android Studio サイト](http://developer.android.com/tools/projects/projects-eclipse.html)で詳細を確認してください。
2. デバッグのためのプロジェクト依存関係をプロジェクト設定に追加します。
3. プロジェクトの AndroidManifest.xml ファイルを更新して、次のものを含めます。

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
            ....
        <application/>

4. メイン アクティビティで AuthenticationContext のインスタンスを作成します。 この呼び出しの詳細についてはこのトピックの範囲外ですが、[Android ネイティブ クライアントのサンプル](https://github.com/AzureAD/azure-activedirectory-library-for-android)を参照することで適切に作業を開始できます。 次の例は、SharedPreferences が既定のキャッシュであり、機関の形式は `https://login.microsoftonline.com/yourtenant.onmicrosoft.com` です。

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. ユーザーが資格情報を入力して認証コードを受け取った後の AuthenticationActivity の終了を処理する、次のコード ブロックをコピーします。

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. トークンを要求するには、次のようにコールバックを定義します。

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. 最後に、そのコールバックを使用して次のようにトークンを要求します。

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

パラメーターの説明を次に示します。

* *resource* は必須です。これはアクセス先のリソースです。
* *clientid* は必須です。これは Azure AD から提供されます。
* *RedirectUri* は、acquireToken 呼び出しの場合は、必須ではありません。 これはパッケージ名として設定することができます。
* *PromptBehavior* は、キャッシュと Cookie をスキップして、資格情報の入力を要求するのに役立ちます。
* *callback* は、トークン用に承認コードが交換された後に、呼び出されます。 このパラメーターにはオブジェクト AuthenticationResult があり、アクセス トークン、期限切れの日付、ID トークンの情報が含まれています。
* *acquireTokenSilent* は省略可能です。 キャッシュとトークン更新を処理するために、これを呼び出すことができます。 また、同期バージョンも提供します。 *userId* をパラメーターとして受け入れます。

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

このチュートリアルを使って、Azure Active Directory と適正に統合するために必要な事柄を理解できました。 この作業の詳細な例については、GitHub が提供する AzureADSamples/リポジトリを参照してください。

## <a name="important-information"></a>重要な情報

### <a name="broker"></a>ブローカー
Intune ポータル サイトまたは Microsoft Authenticator アプリは、ブローカー コンポーネントを提供します。 アカウントは、AccountManager で作成されます。 アカウントの種類は、"com.microsoft.workaccount" です。 AccountManager では、単一の SSO アカウントのみが許可されます。 いずれかのアプリに対するデバイスのチャレンジが完了した後に、このユーザーの SSO Cookie が作成されます。

ブローカーを使用する構成の詳細については、[ブローカーの Wiki 記事](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Broker)を確認してください。 

### <a name="authority-url-and-ad-fs"></a>機関 URL と AD FS
Active Directory フェデレーション サービス (AD FS) は実稼働 STS とは認識されないため、インスタンス検出を無効にして、AuthenticationContext コンストラクターで false を渡す必要があります。

機関 URL には、STS インスタンスと[テナント名](https://login.microsoftonline.com/yourtenant.onmicrosoft.com)が必要です。

### <a name="querying-cache-items"></a>キャッシュ項目のクエリ
ADAL は SharedPreferences の既定のキャッシュに、いくつかの単純なキャッシュ クエリ関数を提供します。 AuthenticationContext から現在のキャッシュを取得するには、次の関数を使用します。

    ITokenCacheStore cache = mContext.getCache();

カスタマイズする場合、キャッシュの実装を指定することもできます。

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>プロンプトの動作
ADAL は、プロンプトの動作を指定するオプションを提供します。 PromptBehavior.Auto は、更新トークンが無効であり、ユーザー資格情報が必要である場合に、UI を表示します。 PromptBehavior.Always はキャッシュの使用をスキップし、常に UI を表示します。

### <a name="silent-token-request-from-cache-and-refresh"></a>キャッシュと更新からのサイレント トークン要求
サイレント トークン要求ではポップアップの UI が使用されないため、アクティビティは不要です。 使用可能な場合は、キャッシュからトークンを返します。 トークンが期限切れの場合、このメソッドは更新を試みます。 更新トークンが期限切れであるか、または更新トークンの取得に失敗した場合は、AuthenticationException を返します。

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

このメソッドを使用して同期呼び出しを行うこともできます。 コールバックに null を設定するか、または acquireTokenSilentSync を使用することができます。

### <a name="diagnostics"></a>診断
次に示すのは、問題を診断するための主要な情報ソースです。

* 例外
* ログ
* ネットワーク トレース

関連付け ID が、ライブラリでの診断の中心となることに注意してください。 ADAL 要求をコード内の他の操作と関連付けたい場合は、関連付け ID は要求ごとに設定できます。 関連付け ID を設定していない場合は、ADAL によってランダムに生成されます。 この場合、すべてのログ メッセージとネットワーク呼び出しがこの関連付け ID でマークされます。 自己生成 ID は、要求ごとに変更されます。

#### <a name="errors--exceptions"></a>エラーと例外
例外は、診断で使用される最初の情報です。 Microsoft では、役立つエラー メッセージを提供するための取り組みがなされています。 役に立たないエラー メッセージを見つけた場合は、問題を報告してください。 モデルや SDK 番号などのデバイス情報も記載してください。

アプリで処理する必要があるエラーの詳細については、[エラー処理のベスト プラクティス](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-adal-error-handling)に関するページを確認してください。 

#### <a name="logs"></a>ログ
問題の診断に役立てることができるログ メッセージを生成するようにライブラリを構成できます。 次の呼び出しを行って、生成時に各ログ メッセージを渡すために ADAL が使用するコールバックを構成することで、ログを構成します。

ログ記録を有効にする場合は、[ログ記録の Wiki 記事](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Logging)を確認してください。

### <a name="session-cookies-in-webview"></a>WebView のセッション Cookie
Android WebView は、アプリを閉じた後、セッションの Cookie をクリアしません。 次のサンプル コードを使用してこれを処理できます。

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

Cookie の詳細については、[Android サイトでの CookieSyncManager に関する情報](http://developer.android.com/reference/android/webkit/CookieSyncManager.html)を参照してください。

### <a name="ntlm-dialog-box"></a>NTLM ダイアログ ボックス
ADAL バージョン 1.1.0 は、WebViewClient からの onReceivedHttpAuthRequest イベントを通して処理される NTLM ダイアログ ボックスをサポートしています。 ダイアログ ボックスのレイアウトと文字列をカスタマイズすることができます。

### <a name="cross-app-sso"></a>クロス アプリ SSO
「[Android で ADAL を使用してクロス アプリ SSO を有効にする方法](active-directory-sso-android.md)」を確認してください。  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
