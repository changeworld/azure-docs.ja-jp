---
title: Android アプリで認証を有効にする - Azure AD B2C
description: Azure Active Directory B2C の構成要素を使用して Android アプリケーションで認証を有効にする Azure AD B2C を使用して、Android アプリケーションでユーザーをサインインおよびサインアップする方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/16/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support, has-adal-ref
ms.openlocfilehash: 74cb2971ee59a36e72a964a4b1b5202844801edd
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039640"
---
# <a name="enable-authentication-in-your-own-android-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して独自の Android アプリで認証を有効にする

この記事では、独自の Android モバイル アプリケーションに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法について説明します。 

この記事は、「[Azure Active Directory B2C を使用してサンプル Android アプリケーションで認証を構成する](./configure-authentication-sample-android-app.md)」に関する記事と共に使用してください。その際は、サンプル Android アプリを独自の Android アプリに置き換えてください。 この記事の手順を完了すると、アプリケーションで、Azure AD B2C を使用したサインインが受け付けられるようになります。

## <a name="prerequisites"></a>前提条件

「[Azure Active Directory B2C を使用してサンプル Android アプリケーションで認証を構成する](configure-authentication-sample-android-app.md)」の前提条件と統合の手順を確認します。

## <a name="create-an-android-app-project"></a>Android アプリ プロジェクトを作成する

Android アプリケーションがまだない場合は、以下を実行して新しいプロジェクトを設定します。

1. Android Studio で、 **[Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\)** を選択します。
1. **[Basic Activity]\(基本アクティビティ\)** を選択し、 **[Next]\(次へ\)** を選択します。
1. アプリケーションに名前を付けます。
1. パッケージ名を保存しておきます。 後ほど、Azure portal でそれを入力します。
1. 言語を **[Kotlin]** から **[Java]** に変更します。
1. **[最低 API レベル]** を **API 19** 以上に設定し、 **[完了]** を選択します。
1. プロジェクト ビューのドロップダウン リストで **[Project]\(プロジェクト\)** を選択して、ソースとソース以外のプロジェクト ファイルを表示し、**app/build.gradle** を開いて、**targetSdkVersion** を **28** に設定します。

## <a name="step-1-install-the-dependencies"></a>ステップ 1: 依存関係をインストールする

Android Studio のプロジェクト ウィンドウで、**app** > **build.gradle** に移動し、以下を追加します。

```gradle
apply plugin: 'com.android.application'

allprojects {
    repositories {
    mavenCentral()
    google()
    mavenLocal()
    maven {
        url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
    maven {
        name "vsts-maven-adal-android"
        url "https://identitydivision.pkgs.visualstudio.com/_packaging/AndroidADAL/maven/v1"
        credentials {
            username System.getenv("ENV_VSTS_MVN_ANDROIDADAL_USERNAME") != null ? System.getenv("ENV_VSTS_MVN_ANDROIDADAL_USERNAME") : project.findProperty("vstsUsername")
            password System.getenv("ENV_VSTS_MVN_ANDROIDADAL_ACCESSTOKEN") != null ? System.getenv("ENV_VSTS_MVN_ANDROIDADAL_ACCESSTOKEN") : project.findProperty("vstsMavenAccessToken")
        }
    }
    jcenter()
    }
}
dependencies{
    implementation 'com.microsoft.identity.client:msal:2.+'
    }
packagingOptions{
    exclude("META-INF/jersey-module-version")
}
```


## <a name="step-2-add-the-authentication-components"></a>ステップ 2: 認証コンポーネントを追加する

[サンプル コード](configure-authentication-sample-android-app.md#step-3-get-the-android-mobile-app-sample)は、次のコンポーネントで構成されています。 サンプル Android アプリから独自のアプリにこれらのコンポーネントを追加します。 

|コンポーネント  |Type  | source |説明  |
|---------|---------|---------|---------|
| B2CUser| クラス| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CUser.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CUser.java)| B2C ユーザーを表します。 このクラスを使用すると、ユーザーは複数のポリシーを使用してサインインできます。 | 
| B2CModeFragment | Fragment クラス| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CModeFragment.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CModeFragment.java)| フラグメントは、メイン アクティビティ内の Azure AD B2C ユーザー インターフェイスを使用したサインインのモジュール部分を表します。 認証コードの大半は、このフラグメントに含まれています。 |
| fragment_b2c_mode.xml | フラグメント レイアウト| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/res/layout/fragment_b2c_mode.xml) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/res/layout/fragment_b2c_mode.xml) | B2CModeFragment フラグメント コンポーネントのユーザー インターフェイスの構造を定義します。 |
| B2CConfiguration| クラス| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CConfiguration.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CConfiguration.java)| Azure AD B2C ID プロバイダーに関する情報は、構成ファイルに含まれています。 モバイル アプリでは、この情報を使用して Azure AD B2C との信頼関係を確立し、ユーザーのサインインとサインアウトを行い、トークンを取得して検証します。 その他の構成設定については、auth_config_b2c.json ファイルを参照してください。  | 
|auth_config_b2c.json | JSON ファイル| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/res/raw/auth_config_b2c.json) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/res/raw/auth_config_b2c.json)| Azure AD B2C ID プロバイダーに関する情報は、構成ファイルに含まれています。 モバイル アプリでは、この情報を使用して Azure AD B2C との信頼関係を確立し、ユーザーのサインインとサインアウトを行い、トークンを取得して検証します。 その他の構成設定については、B2CConfiguration クラスを参照してください。 |
| | | 

## <a name="step-3-configure-your-android-app"></a>ステップ 3: Android アプリを構成する

[認証コンポーネントを追加](#step-2-add-the-authentication-components)したら、Azure AD B2C 設定を使用して Android アプリを構成します。 Azure AD B2C ID プロバイダー設定の構成は、*auth_config_b2c.json* ファイルと B2CConfiguration クラスで行います。 

ガイダンスについては、「[サンプル モバイル アプリを構成する](configure-authentication-sample-android-app.md#step-5-configure-the-sample-mobile-app)」を参照してください。

## <a name="step-4-set-the-redirect-uri"></a>ステップ 4: リダイレクト URI を設定する

アプリケーションで Azure AD B2C のトークン応答をリッスンする場所を構成します。 

1. 新しい開発署名ハッシュを生成します。 これは開発環境ごとに変わります。
    
    Windows の場合:
    
    ```
    keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
    ```
    
    iOS の場合:
    
    ```dotnetcli
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ```
    
    運用環境では、次のコマンドを使用します。
    
    ```
    keytool -exportcert -alias SIGNATURE_ALIAS -keystore PATH_TO_KEYSTORE | openssl sha1 -binary | openssl base64
    ```

    アプリの署名について詳しくは、[Android アプリの署名](https://developer.android.com/studio/publish/app-signing)に関するページを参照してください。 

1. **app** > **src** > **main** > **AndroidManifest.xml** を選択し、以下の `BrowserTabActivity` アクティビティをアプリケーションの本体に追加します。
    
    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Package_Name"
                android:path="/Signature_Hash" />
        </intent-filter>
    </activity>
    ```
1. `Signature_Hash` を、自分が生成したハッシュに置き換えます。
1. `Package_Name` を、自分の Android パッケージの名前に置き換えます。
 
モバイル アプリの登録をアプリのリダイレクト URI で更新するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **Azure AD B2C** を検索して選択します。
1. **[アプリの登録]** を選択し、「[手順 2.3 モバイル アプリを登録する](configure-authentication-sample-android-app.md#step-23-register-the-mobile-app)」で登録したアプリケーションを選択します。
1. **[認証]** を選択します。
1. **[Android]** で **[URI の追加]** を選択します。
1. **[パッケージ名]** と **[署名ハッシュ]** を入力します。
1. **[保存]** を選択します。

リダイレクト URI と `BrowserTabActivity` アクティビティは、次の例のようになっている必要があります。 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

サンプル Android のリダイレクト URL は次のようになります。

```
msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D
```

次の XML スニペットで示されているように、インテント フィルターにも同じパターンが使用されます。

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data
            android:host="com.azuresamples.msalandroidkotlinapp"
            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
            android:scheme="msauth" />
    </intent-filter>
</activity>
```



#### <a name="java"></a>[Java](#tab/java)

サンプル Android のリダイレクト URL は次のようになります。

```
msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D
```

次の XML スニペットで示されているように、インテント フィルターにも同じパターンが使用されます。

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
<intent-filter>
    <action android:name="android.intent.action.VIEW" />

    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />

    <data
        android:host="com.azuresamples.msalandroidapp"
        android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
        android:scheme="msauth" />
</intent-filter>
</activity>
```

--- 

## <a name="step-5-customize-your-code-building-blocks"></a>ステップ 5: コードの構成要素をカスタマイズする

このセクションでは、Android アプリの認証を可能にするコードの構成要素について説明します。 次の表は、B2CModeFragment のメソッドと、コードのカスタマイズ方法の一覧です。 

### <a name="step-51-instantiate-a-public-client-application"></a>ステップ 5.1: パブリック クライアント アプリケーションをインスタンス化する

パブリック クライアント アプリケーションは、アプリケーション シークレットが安全に保管されると信頼されておらず、そこにクライアント シークレットは置かれません。 [onCreate](https://developer.android.com/reference/android/app/Fragment#onCreate(android.os.Bundle)) または [onCreateView](https://developer.android.com/reference/android/app/Fragment#onCreateView(android.view.LayoutInflater,%20android.view.ViewGroup,%20android.os.Bundle)) で、MultipleAccountPublicClientApplication オブジェクトを使用して MSAL のインスタンスを作成します。

`MultipleAccountPublicClientApplication` クラスは、複数のアカウントが同時にサインインできるようにする MSAL ベースのアプリを作成するために使用されます。 このクラスにより、複数の Azure AD B2C ユーザー フローまたはカスタム ポリシーを使用したサインインが可能となります。 たとえば、ユーザーは、[サインアップまたはサインイン](add-sign-up-and-sign-in-policy.md) ユーザー フローを使用してサインインし、その後、[プロファイル編集](add-profile-editing-policy.md)ユーザー フローを実行します。 

次のコード スニペットは、`auth_config_b2c.json` 構成 JSON ファイルを使用して MSAL ライブラリを初期化する方法を示しています。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
PublicClientApplication.createMultipleAccountPublicClientApplication(context!!,
    R.raw.auth_config_b2c,
    object : IMultipleAccountApplicationCreatedListener {
        override fun onCreated(application: IMultipleAccountPublicClientApplication) {
            // Set the MultipleAccountPublicClientApplication to the class member b2cApp
            b2cApp = application
            // Load the account (if there is any)
            loadAccounts()
        }

        override fun onError(exception: MsalException) {
            // Error handling
            displayError(exception)
        }
    })
```

#### <a name="java"></a>[Java](#tab/java)

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
    R.raw.auth_config_b2c,
    new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication application) {
            // Set the MultipleAccountPublicClientApplication to the class member b2cApp
            b2cApp = application;

            // Load the account (if there is any)
            loadAccounts();
        }

        @Override
        public void onError(MsalException exception) {
            // Error handling
            displayError(exception);
        }
    });
```

--- 

### <a name="step-52-load-accounts"></a>ステップ 5.2: アカウントを読み込む

アプリはフォアグラウンドになると、既存のアカウントを読み込んで、ユーザーがサインイン済みかどうかを判断します。 この方法を使用して UI を認証状態で更新します。 たとえば、サインアウト ボタンを有効または無効にできます。

アカウントを読み込む方法を次のコード スニペットに示します。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
private fun loadAccounts() {
    if (b2cApp == null) {
        return
    }
    b2cApp!!.getAccounts(object : LoadAccountsCallback {
        override fun onTaskCompleted(result: List<IAccount>) {
            users = B2CUser.getB2CUsersFromAccountList(result)
            updateUI(users)
        }
    
        override fun onError(exception: MsalException) {
            displayError(exception)
        }
    })
    }
```

#### <a name="java"></a>[Java](#tab/java)

```java
private void loadAccounts() {
    if (b2cApp == null) {
        return;
    }

    b2cApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            users = B2CUser.getB2CUsersFromAccountList(result);
            updateUI(users);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

--- 

### <a name="step-53-start-an-interactive-authorization-request"></a>ステップ 5.3: 対話型承認要求を開始する

対話型承認要求は、ユーザーがサインアップまたはサインインを求められるフローです。 `runUserFlowButton` クリック イベントの構成は、`initializeUI` メソッドで行います。 ユーザーが **[ユーザー フローを実行します]** ボタンを選択すると、ユーザーはアプリによって Azure AD B2C に誘導され、サインイン フローが実行されます。 

`runUserFlowButton.setOnClickListener` メソッドにより、承認要求に関する関連データを使用して `AcquireTokenParameters` オブジェクトが準備されます。 その後、ユーザーはサインアップ フローまたはサインイン フローを実行するよう、`acquireToken` メソッドによって求められます。 

次のコード スニペットは、対話型承認要求の開始方法を示しています。 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority(getAuthorityFromPolicyName(policy_list.getSelectedItem().toString()))
        .withScopes(B2CConfiguration.scopes)
        .withPrompt(Prompt.LOGIN)
        .withCallback(authInteractiveCallback)
        .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(getActivity())
        .fromAuthority(B2CConfiguration.getAuthorityFromPolicyName(policyListSpinner.getSelectedItem().toString()))
        .withScopes(B2CConfiguration.getScopes())
        .withPrompt(Prompt.LOGIN)
        .withCallback(getAuthInteractiveCallback())
        .build();

b2cApp.acquireToken(parameters);
```

--- 

 
### <a name="step-54-make-an-interactive-authorization-request-callback"></a>ステップ 5.4: 対話型承認要求のコールバックを行う

ユーザーが成功または失敗で承認フローを完了した後、`getAuthInteractiveCallback()` コールバック メソッドに結果が返されます。 

このコールバック メソッドによって `AuthenticationResult` オブジェクトが渡されるか、`MsalException` オブジェクトにエラー メッセージが渡されます。 このメソッドを使用して以下を実行します。

- サインイン完了後の情報でモバイル アプリの UI を更新します。
- アカウント オブジェクトを再度読み込みます。
- アクセス トークンを使用して Web API サービスを呼び出します。
- 認証エラーを処理します。

次のコード スニペットは、対話型認証コールバックの使用例を示しています。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
private val authInteractiveCallback: AuthenticationCallback
    private get() = object : AuthenticationCallback {
        override fun onSuccess(authenticationResult: IAuthenticationResult) {
            /* Successfully got a token, use it to call a protected resource; web API  */
            Log.d(TAG, "Successfully authenticated")

            /* display result info */
            displayResult(authenticationResult)

            /* Reload account asynchronously to get the up-to-date list. */
            loadAccounts()
        }

        override fun onError(exception: MsalException) {
            val B2C_PASSWORD_CHANGE = "AADB2C90118"
            if (exception.message!!.contains(B2C_PASSWORD_CHANGE)) {
                txt_log!!.text = """
                    Users click the 'Forgot Password' link in a sign-up or sign-in user flow.
                    Your application needs to handle this error code by running a specific user flow that resets the password.
                    """.trimIndent()
                return
            }

            /* Failed to acquireToken */Log.d(TAG, "Authentication failed: $exception")
            displayError(exception)
            if (exception is MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception is MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        override fun onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.")
        }
    }
```

#### <a name="java"></a>[Java](#tab/java)

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");

            /* display result info */
            displayResult(authenticationResult);

            /* Reload account asynchronously to get the up-to-date list. */
            loadAccounts();
        }

        @Override
        public void onError(MsalException exception) {
            final String B2C_PASSWORD_CHANGE = "AADB2C90118";
            if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
                logTextView.setText("Users click the 'Forgot Password' link in a sign-up or sign-in user flow.\n" +
                        "Your application needs to handle this error code by running a specific user flow that resets the password.");
                return;
            }

            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

--- 

## <a name="step-6-call-a-web-api"></a>ステップ 6: Web API を呼び出す

[トークンベースの承認 Web API](enable-authentication-web-api.md) を呼び出すには、有効なアクセス トークンがアプリにある必要があります。 アプリでは以下が実行されます。


1. Web API エンドポイントで必要なアクセス許可 (スコープ) を持つアクセス トークンを取得します。
1. この形式を使用して、HTTP 要求の Authorization ヘッダーでベアラー トークンとしてアクセス トークンを渡します。

```http
Authorization: Bearer <access-token>
```

ユーザーが[対話形式でサインイン](#step-53-start-an-interactive-authorization-request)すると、アプリは `getAuthInteractiveCallback` コールバック メソッド内でアクセス トークンを取得します。 連続する Web API 呼び出しの場合は、このセクションで説明するように、トークンをサイレントに取得するプロシージャを使用します。 

Web API を呼び出す前に、Web API エンドポイントに対する適切なスコープで `acquireTokenSilentAsync` メソッドを呼び出します。 MSAL ライブラリによって以下のことが行われます。

1. 要求されたスコープを持つアクセス トークンをトークン キャッシュから取り込むことを試みます。 トークンが存在する場合は、そのトークンが返されます。 
1. トークンがトークン キャッシュに存在しない場合、MSAL は更新トークンを使用して新しいトークンを取得しようと試みます。 
1. 更新トークンが存在しないか期限切れになっている場合は、例外が返されます。 [対話形式でのサインイン](#step-53-start-an-interactive-authorization-request)をユーザーに求めることをお勧めします。  

次のコード スニペットは、アクセス トークンの取得方法を示しています。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

`acquireTokenSilentButton` ボタン クリック イベントは、指定されたスコープのアクセス トークンを取得します。 

```kotlin
btn_acquireTokenSilently.setOnClickListener(View.OnClickListener {
    if (b2cApp == null) {
        return@OnClickListener
    }
    val selectedUser = users!![user_list.getSelectedItemPosition()]
    selectedUser.acquireTokenSilentAsync(b2cApp!!,
            policy_list.getSelectedItem().toString(),
            B2CConfiguration.scopes,
            authSilentCallback)
})
```

`authSilentCallback` コールバック メソッドは、アクセス トークンを返して Web API を呼び出します。

```kotlin
private val authSilentCallback: SilentAuthenticationCallback
    private get() = object : SilentAuthenticationCallback {
        override fun onSuccess(authenticationResult: IAuthenticationResult) {
            Log.d(TAG, "Successfully authenticated")

            /* Call your web API here*/
            callWebAPI(authenticationResult)
        }

        override fun onError(exception: MsalException) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: $exception")
            displayError(exception)
            if (exception is MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception is MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception is MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    }
```

次の例は、保護された Web API をベアラー トークンで呼び出す方法を示しています。

```kotlin
@Throws(java.lang.Exception::class)
private fun callWebAPI(authenticationResult: IAuthenticationResult) {
    val accessToken = authenticationResult.accessToken
    val thread = Thread {
        try {
            val url = URL("https://your-app-service.azurewebsites.net/helo")
            val conn = url.openConnection() as HttpsURLConnection
            conn.setRequestProperty("Accept", "application/json")
            
            // Set the bearer token
            conn.setRequestProperty("Authorization", "Bearer $accessToken")
            if (conn.responseCode == HttpURLConnection.HTTP_OK) {
                val br = BufferedReader(InputStreamReader(conn.inputStream))
                var strCurrentLine: String?
                while (br.readLine().also { strCurrentLine = it } != null) {
                    Log.d(TAG, strCurrentLine)
                }
            }
            conn.disconnect()
        } catch (e: IOException) {
            e.printStackTrace()
        } catch (e: Exception) {
            e.printStackTrace()
        }
    }
    thread.start()
}
```

#### <a name="java"></a>[Java](#tab/java)

`acquireTokenSilentButton` ボタン クリック イベントは、指定されたスコープのアクセス トークンを取得します。 

```java
acquireTokenSilentButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        if (b2cApp == null) {
            return;
        }

        final B2CUser selectedUser = users.get(b2cUserList.getSelectedItemPosition());
        selectedUser.acquireTokenSilentAsync(b2cApp,
                policyListSpinner.getSelectedItem().toString(),
                B2CConfiguration.getScopes(),
                getAuthSilentCallback());
    }
});
```

`authSilentCallback` コールバック メソッドは、アクセス トークンを返して Web API を呼び出します。

```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Call your web API here*/
            callWebAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

次の例は、保護された Web API をベアラー トークンで呼び出す方法を示しています。

```java
private void callWebAPI(IAuthenticationResult authenticationResult) throws Exception {
    final String accessToken = authenticationResult.getAccessToken();
    
    
    Thread thread = new Thread(new Runnable() {
    
        @Override
        public void run() {
            try {
                URL url = new URL("https://your-app-service.azurewebsites.net/helo");
                HttpsURLConnection conn = (HttpsURLConnection)url.openConnection();
                conn.setRequestProperty("Accept", "application/json");
                
                // Set the bearer token
                conn.setRequestProperty("Authorization", "Bearer " +  accessToken);
    
                if (conn.getResponseCode() == HttpURLConnection.HTTP_OK) {
                    BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
                    String strCurrentLine;
                    while ((strCurrentLine = br.readLine()) != null) {
                        Log.d(TAG, strCurrentLine);
                    }
                }
                conn.disconnect();
            } catch (IOException e) {
                e.printStackTrace();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    });
    
    thread.start();
    
    }
```

--- 

### <a name="add-permission-to-perform-network-operations"></a>ネットワーク操作を実行するためのアクセス許可を追加する

アプリケーションでネットワーク操作を実行するには、マニフェストに次のアクセス許可を追加する必要があります。 詳細については、「[ネットワークに接続する](https://developer.android.com/training/basics/network-ops/connecting)」を参照してください。

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。
* [Azure AD B2C を使用して Android アプリで認証オプションを構成する](enable-authentication-android-app-options.md)
* [Azure AD B2C を使用して独自の Web API で認証を有効にする](enable-authentication-web-api.md)
