<properties
	pageTitle="Azure AD Android の概要 | Microsoft Azure"
	description="サインインと、OAuth を使用して Azure AD で保護されている API を呼び出しのために Azure AD と統合する、Android アプリケーションを構築する方法を説明します。"
	services="active-directory"
	documentationCenter="android"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

# Azure AD の Android アプリへの統合

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

デスクトップ アプリケーションを開発する場合、Azure AD を使用すると、Active Directory アカウントを使ってユーザーの認証処理を容易に行うことができます。また、Office 365 API や Azure API などの Azure AD によって保護された任意の Web API をアプリケーションで安全に使用することもできます。

保護されたリソースにアクセスする必要がある Android クライアントに対しては、Azure AD は Active Directory 認証ライブラリ (ADAL) を提供します。ADAL の唯一の目的は、アプリケーションがアクセス トークンを容易に取得できるようにすることです。それがどれほど簡単であるかを示すために、ここで、次を実行する Android To-Do List アプリケーションを作成します。

-	[OAuth 2.0 認証プロトコル](https://msdn.microsoft.com/library/azure/dn645545.aspx)を使用して To-Do List API を呼び出すためのアクセス トークンを取得します。
-	ユーザーの To-Do List を取得します。
-	ユーザーのサインアウト処理を行います。

作業を開始するには、ユーザーを作成し、アプリケーションを登録できるようにするために Azure AD テナントが必要になります。テナントを所有していない場合は、「[How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

## 手順 1. Node.js REST API の TODO サンプル サーバーをダウンロードして実行する

このサンプルは、Microsoft Azure Active Directory の単一のテナント To-Do REST API の作成用の既存のサンプルを扱うために、特別に作成されました。これは、クイック スタートの前提条件です。

この設定方法については、次の既存のサンプルを参照してください。

* [Node.js 用の Microsoft Azure Active Directory のサンプル REST API サービス](active-directory-devquickstarts-webapi-nodejs.md)

## 手順 2. Web API を Microsoft Azure AD テナントに登録する

**実行する事柄**

*Microsoft Active Directory は、2 種類のアプリケーションの追加をサポートします。サービスをユーザーに提供する Web API と、それらの Web API にアクセスする (Web 上またはデバイス上のいずれかで実行する) アプリケーションです。この手順では、このサンプルをテストするために、ローカルで実行している Web API を登録します。この Web API は、通常、アプリがアクセスする機能を提供する REST サービスです。Microsoft Azure Active Directory は、あらゆるエンドポイントを保護できます。*

*ここでは前述の TODO REST API を登録することを想定していますが、この方法は、Azure Active Directory で保護するすべての Web API に有効です。*

Microsoft Azure AD を使用して Web API を登録する手順

1. [Microsoft Azure の管理ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側のナビゲーションで [Active Directory] をクリックします。
3. サンプル アプリケーションを登録するディレクトリ テナントをクリックします。
4. [アプリケーション] タブをクリックします。
5. ドロアーで、[追加] をクリックします。
6. [組織で開発中のアプリケーションを追加] をクリックします。
7. たとえば "TodoListService" など、アプリケーションのわかりやすい名前を入力し、[Web アプリケーションや Web API] を選択して、[次へ] をクリックします。
8. サインオン URL には、サンプルのベース URL を入力します。既定では `https://localhost:8080` となります。
9. アプリ ID URI には、「`https://<your_tenant_name>/TodoListService`」と入力します。これは `<your_tenant_name>` を Azure AD テナントの名前に置き換えます。[OK] クリックして登録を完了します。
10. 引き続き Azure ポータルで、アプリケーションの [構成] タブをクリックします。
11. **クライアント ID 値を見つけて、コピーして保持しておきます**。これは後でアプリケーションを構成するときに必要になります。

## 手順 3. サンプルの Android ネイティブ クライアント アプリケーションを登録する

最初の手順として、Web アプリケーションを登録します。次に、アプリケーションについて Azure Active Directory に通知する必要があります。これにより、アプリケーションは登録した Web API と通信できるようになります。

**実行する事柄**

*前述のとおり、Microsoft Azure Active Directory は、2 種類のアプリケーションの追加をサポートします。サービスをユーザーに提供する Web API と、それらの Web API にアクセスする (Web 上またはデバイス上のいずれかで実行する) アプリケーションです。この手順では、このサンプルのアプリケーションを登録します。このアプリケーションが、登録した Web API へのアクセスを要求できるようにするために、この手順は必要です。Azure Active Directory は、登録されていないアプリケーションに対して、サインインを要求することさえも拒否します。 モデル化されたセキュリティの一環としてそのようになります。*

*ここでは上述のサンプル アプリケーションを登録することを想定していますが、手順は開発するすべてのアプリケーションに有効です。*

**アプリケーションと Web API の両方を 1 テナントに配置する理由**

*ご想像どおり、別のテナントから Azure Active Directory に登録されている外部 API にアクセスするアプリを構築することができます。そのようにすると、お客様に対してアプリケーションでの API の使用に同意が求められることになります。このようにする利点として、iOS 向け Active Directory 認証ライブラリがユーザーに代わって同意に対応することになります。 より高度な機能を利用するようになるにつれて、これが、Azure、Office、および他のすべてのサービス プロバイダーから一連の Microsoft API にアクセスするために必要な処理の重要な部分であることがわかるようになります。この時点では、同じテナントに Web API とアプリケーションの両方を登録しているので、同意のプロンプトは表示されません。これは、通常、自社のみで使用するアプリケーションを開発している場合に当てはまります。*

1. [Microsoft Azure の管理ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側のナビゲーションで [Active Directory] をクリックします。
3. サンプル アプリケーションを登録するディレクトリ テナントをクリックします。
4. [アプリケーション] タブをクリックします。
5. ドロアーで、[追加] をクリックします。
6. [組織で開発中のアプリケーションを追加] をクリックします。
7. たとえば "TodoListClient-Android" など、アプリケーションのわかりやすい名前を入力し、[ネイティブ クライアント アプリケーション] を選択して、[次へ] をクリックします。
8. リダイレクト URI には、「`http://TodoListClient`」を入力します。[完了] をクリックします。
9. アプリケーションの [構成] タブをクリックします。
10. クライアント ID 値を見つけて、コピーして保持しておきます。これは後でアプリケーションを構成するときに必要になります。
11. [その他のアプリケーションに対するアクセス許可] で、[アプリケーションの追加] をクリックします。 [表示] ドロップダウンで [その他] を選択し、上のチェック マークをクリックします。TodoListService を見つけてクリックし、下のチェック マークをクリックしてアプリケーションを追加します。[委任されたアクセス許可] ドロップダウンで [TodoListService へのアクセス (Access TodoListService)] を選択し、構成を保存します。



Maven を使用して構築するために、最上位レベルで pom.xml を使用することができます。

  * このリポジトリを任意のディレクトリに複製します。

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`

  * [「Setting up maven environment for Android」の「Prerequests」セクション](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)の手順に従います。
  * SDK 19 でエミュレーターをセットアップします。
  * リポジトリを複製したルート フォルダーに移動します。
  * コマンド mvn clean install を実行します。
  * cd samples\\hello を実行して、ディレクトリをクイック スタート サンプルに変更します。
  * コマンド mvn android:deploy android:run を実行します。
  * アプリが起動します。
  * テスト ユーザー資格情報を入力して、試行します。

aar パッケージに加え、jar パッケージも送信されます。

### 手順 4. Android ADAL をダウンロードして Eclipse ワークスペースに追加する

このライブラリを Android プロジェクトで簡単に使用できるようにするために、複数のオプションが用意されています。

* ソース コードを使用して、このライブラリを Eclipse にインポートし、アプリケーションにリンクします。
* Android Studio を使用する場合は、*aar* パッケージ形式を使用して、バイナリを参照します。

####オプション 1. ソース Zip

ソース コードのコピーをダウンロードするには、ページの右側にある [ZIP のダウンロード] をクリックするか、[ここ](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz)をクリックします。

####オプション 2. Git 経由のソース

SDK のソース コードを Git 経由で取得するには、次のように入力します。

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####オプション 3. Gradle 経由のバイナリ

Maven 中央リポジトリからバイナリを取得できます。aar パッケージは、次のようにして AndroidStudio でプロジェクトに含めることができます。

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

####オプション 4. Maven 経由の aar

Eclipse で m2e プラグインを使用する場合は、pom.xml ファイルで次のように依存関係を指定できます。

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####オプション 5. libs フォルダー内の jar パッケージ
Maven リポジトリから jar ファイルを取得して、プロジェクトの *libs* フォルダーにドロップできます。jar パッケージには含まれていないので、必要なリソースもプロジェクトにコピーする必要があります。


### 手順 5. プロジェクトに Android ADAL への参照を追加する


2. プロジェクトへの参照を追加し、Android ライブラリとして指定します。この実行方法がわからない場合は、[詳細についてここをクリックしてください](http://developer.android.com/tools/projects/projects-eclipse.html)。

3. デバッグのためのプロジェクト依存関係をプロジェクト設定に追加します。

4. プロジェクトの AndroidManifest.xml ファイルを更新して、次のものを含めます。

    ```Java
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
    ```

7. メイン アクティビティで AuthenticationContext のインスタンスを作成します。この呼び出しの詳細についてはこの README の範囲外ですが、[Android ネイティブ クライアントのサンプル](https://github.com/AzureADSamples/NativeClient-Android)を参照することで適切に作業を開始できます。次に例を示します。

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * 注: mContext はアクティビティ内のフィールドです。

8. ユーザーが資格情報を入力して認証コードを受け取った後の AuthenticationActivity の終了を処理する、次のコード ブロックをコピーします。

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. トークンを要求するには、次のようにコールバックを定義します。

    ```Java
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
    ```
10. 最後に、そのコールバックを使用して次のようにトークンを要求します。

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

パラメーターの説明:

  * resource は必須です。これはアクセス先のリソースです。
  * clientid は必須です。これは AzureAD ポータルから提供されます。
  * redirectUri はパッケージ名として設定できます。acquireToken 呼び出しの場合は、必須ではありません。
  * PromptBehavior は、キャッシュと Cookie をスキップして、資格情報の入力を要求するのに役立ちます。
  * callback は、トークン用に承認コードが交換された後に、呼び出されます。

  callback はオブジェクト AuthenticationResult を返します。これには accesstoken、期限切れの日付、idtoken の情報が含まれています。

省略可能: **acquireTokenSilent**

キャッシュとトークン更新を処理するために、**acquireTokenSilent** を呼び出すことができます。これは同期バージョンも提供します。ユーザー ID をパラメーターとして受け入れます。

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **ブローカー**: 
Microsoft Intune の企業ポータル アプリは、ブローカー コンポーネントを提供します。この認証システムで作成された 1 つのユーザー アカウントがあり、開発者がそれをスキップしないことを選択すると、ADAL はブローカー アカウントを使用します。開発者は、次のように指定してブローカー ユーザーをスキップすることができます。

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 開発者は、ブローカーを使用するには特別な redirectUri を登録する必要があります。redirectUri の形式は msauth://packagename/Base64UrlencodedSignature です。スクリプト "brokerRedirectPrint.ps1" を使用して、アプリ用の redirectUri を取得することができます。または API 呼び出し mContext.getBrokeｒｒedirectUri を使用することができます。署名は、署名証明書に関連付けられています。

 現在のブローカー モデルは、1 ユーザー用です。AuthenticationContext は、ブローカー ユーザーを取得するための API メソッドを提供します。

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 ブローカー ユーザーは、アカウントが有効であれば返されます。

 アプリケーション マニフェストは、AccountManager アカウントを使用する許可を持つ必要があります。詳細については、http://developer.android.com/reference/android/accounts/AccountManager.html を参照してください。

 * GET\_ACCOUNTS
 * USE\_CREDENTIALS
 * MANAGE\_ACCOUNTS


このチュートリアルを使って、Azure Active Directory と適正に統合するために必要な事柄を理解できました。この作業の詳細な例については、GitHub が提供する AzureADSamples/リポジトリを参照してください。

## 重要な情報

### カスタマイズ

ライブラリ プロジェクトのリソースは、アプリケーションのリソースにより上書きされることがあります。これは、アプリを構築するときに生じます。このため、認証アクティビティのレイアウトを希望に応じてカスタマイズできます。ADAL が Webview で使用するコントロールの ID を確実に維持する必要があります。

### ブローカー

ブローカー コンポーネントは、Microsoft Intune のポータル サイト アプリを使用して配信されます。アカウントは、アカウント マネージャーで作成されます。アカウントの種類は、"com.microsoft.workaccount" です。これは単一の SSO アカウントのみを許可します。いずれかのアプリに対するデバイスのチャレンジを完了した後に、このユーザーの SSO Cookie を作成します。

### 機関 URL と ADFS

ADFS は実稼働 STS とは認識されないため、インスタンス検出を無効にして、AuthenticationContext コンストラクターで false を渡す必要があります。

機関 URL には、STS インスタンスとテナント名が必要です。詳細については、https://login.windows.net/yourtenant.onmicrosoft.com を参照してください。

### キャッシュ項目のクエリ

ADAL は SharedPreferences の既定のキャッシュに、いくつかの単純なキャッシュ クエリ関数を提供します。AuthenticationContext から現在のキャッシュを取得するには、次の関数を使用します。
```Java
 ITokenCacheStore cache = mContext.getCache();
```
カスタマイズする場合、キャッシュの実装を指定することもできます。
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### PromptBehavior

ADAL は、プロンプトの動作を指定するオプションを提供します。PromptBehavior.Auto は、更新トークンが無効であり、ユーザー資格情報が必要である場合に、UI をポップアップ表示します。PromptBehavior.Always はキャッシュの使用をスキップし、常に UI を表示します。

### キャッシュと更新からのサイレント トークン要求

このメソッドは UI ポップアップを使用せず、アクティビティは不要です。トークンが使用可能な場合は、キャッシュからトークンを返します。トークンが有効期限切れの場合は、更新を試みます。更新トークンが期限切れであるか、または更新トークンの取得に失敗した場合は、AuthenticationException を返します。

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

このメソッドとの同期呼び出しを行うこともできます。コールバックに null を設定するか、または acquireTokenSilentSync を使用することができます。

### 診断

次に示すのは、問題を診断するための主要な情報ソースです。

+ 例外
+ ログ
+ ネットワーク トレース

また、関連付け ID が、ライブラリでの診断の中心となることに注意してください。ADAL 要求をコード内の他の操作と関連付けたい場合には、関連付け ID は要求ごとに設定できます。関連付け ID を設定しない場合、ADAL はランダムに 1 つの関連付け ID を生成し、すべてのログ メッセージとネットワーク呼び出しはその関連付け ID でスタンプ付けされます。自己生成 ID は、要求ごとに変更されます。

#### 例外

これが診断を行う際の最初の情報であることは明白です。Microsoft では、役立つエラー メッセージを提供するための取り組みがなされています。役に立たないエラー メッセージを見つけた場合は、問題を報告してください。モデルや SDK 番号などのデバイス情報も提供してください。

#### Logs (ログ)

問題の診断に役立てることができるログ メッセージを生成するようにライブラリを構成できます。次の呼び出しを行って、生成時に各ログ メッセージを渡すために ADAL が使用するコールバックを構成することで、ログを構成します。


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
メッセージは、次に示すように、カスタム ログ ファイルに書き込むことができます。残念ながら、デバイスからログを取得する標準的な方法はありません。ただし、役立つサービスがいくつかあります。ファイルをサーバーに送信するなど、独自の方法を考案することもできます。

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### ログ レベル

+ Error (例外)
+ Warn (警告)
+ Info (情報通知)
+ Verbose (詳細)

ログ レベルは、次のように設定します。
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 すべてのログ メッセージは、任意のカスタム ログのコールバックだけでなく、logcat にも送信されます。次に示すとおり、ログをファイル形式の logcat で取得することもできます。

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 adb コマンドの他の例については、https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat を参照してください。

#### ネットワーク トレース

さまざまなツールを使用して、ADAL が生成する HTTP トラフィックをキャプチャすることができます。これは、OAuth プロトコルを使い慣れている場合、または Microsoft や他のサポート チャネルに診断情報を提供する必要がある場合に、最も役立ちます。

Fiddler は、最も簡単な HTTP トレース ツールです。これが ADAL ネットワーク トラフィックを正しく記録するように、次のリンク先にある情報を用いて設定します。Fiddler、または Charles などのツールはいずれも、役立つものとするには、暗号化されていない SSL トラフィックを記録するように構成することが必要です。注: この方法で生成されたトレースには、アクセス トークン、ユーザー名、パスワードなどの、非常に機密性の高い情報が含まれている可能性があります。運用環境のアカウントを使用している場合は、それらのトレースを第三者と共有することがないようにしてください。サポートを受けるためにトレースを他者に提供する必要がある場合は、共有しても問題がないユーザー名とパスワードを使う一時的なアカウントを使用して問題を再現します。

+ [Configure Fiddler For Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Configure Fiddler Rules For ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### ダイアログ モード
アクティビティがない acquireToken メソッドは、ダイアログ プロンプトをサポートします。

### Encryption

ADAL は、既定では SharedPreferences のトークンとストアを暗号化します。詳細を確認するには、StorageHelper クラスを参照できます。Android は、秘密キーのセキュリティ保護された記憶域として AndroidKeyStore for 4.3(API18) を導入しています。ADAL はこの API18 以降を使用します。下位バージョンの SDK に ADAL を使用する場合は、AuthenticationSettings.INSTANCE.setSecretKey で秘密キーを提供する必要があります。

### Oauth2 ベアラー チャレンジ

AuthenticationParameters クラスは、Oauth2 ベアラー チャレンジから authorization\_uri を取得する機能を提供します。

### Webview のセッション Cookie

Android Webview は、アプリを閉じた後、セッションの Cookie をクリアしません。これは次のサンプル コードで処理できます。
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Cookie の詳細については、http://developer.android.com/reference/android/webkit/CookieSyncManager.html を参照してください。

### リソースの上書き

ADAL ライブラリには、次の 2 つの ProgressDialog メッセージの英語文字列が含まれています。

ローカライズされた文字列を希望する場合は、アプリケーションで英語文字列を上書きする必要があります。

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### NTLM ダイアログ
ADAL バージョン 1.1.0 は、WebViewClient からの onReceivedHttpAuthRequest イベントを通して処理される NTLM ダイアログをサポートしています。ダイアログのレイアウトと文字列はカスタマイズできます。

### クロス アプリ SSO
[Android で ADAL を使用してクロス アプリ SSO を有効にする方法](active-directory-sso-android.md)


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->