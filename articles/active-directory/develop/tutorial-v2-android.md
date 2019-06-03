---
title: Android の概要 - Microsoft ID プラットフォーム | Azure
description: Android アプリがアクセス トークンを取得し、Microsoft ID プラットフォームからのアクセス トークンを必要とする Microsoft Graph API を呼び出す方法。
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6112facfc0c10d7a0a0495cd778fa6c3cb6130a7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962139"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Android アプリケーションからユーザーにサインインし、Microsoft Graph を呼び出す

このチュートリアルでは、Android アプリを Microsoft ID プラットフォームに統合する方法を学習します。 具体的には、お使いのアプリは、ユーザーのサインイン、Microsoft Graph API を呼び出すアクセス トークンの取得、および Microsoft Graph API への要求を行います。  

このガイドを完了すると、アプリケーションは、個人用の Microsoft アカウント (outlook.com、live.com など) と、Azure Active Directory を使用する会社や組織の職場または学校アカウントのサインインを受け入れるようになります。

## <a name="how-this-tutorial-works"></a>このチュートリアルのしくみ

![このチュートリアルで生成されたサンプル アプリの動作の紹介](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

このサンプルのアプリケーションは、ユーザーにサインインし、ユーザーに代わってデータを取得します。  このデータは、承認を必要とする保護された API (この場合は Microsoft Graph API) を通じてアクセスされます。

具体的には次のとおりです。

* お使いアプリで、ブラウザーまたは Microsoft Authenticator と Intune ポータル サイトを介してユーザーをサインインさせます。
* エンド ユーザーが、アプリケーションから要求されたアクセス許可を受け入れます。 
* アプリケーションには、Microsoft Graph API 用のアクセス トークンが発行されます。
* アクセス トークンは、Web API への HTTP 要求に含められます。
* Microsoft Graph の応答を処理します。

このサンプルでは、Android 用の Microsoft Authentication Library (MSAL) を使用して認証を実装します。MSAL は自動的にトークンを更新し、デバイス上の他のアプリとの間で SSO を提供し、アカウントを管理します。

## <a name="prerequisites"></a>前提条件

* このガイド付きセットアップでは、Android Studio を使用します。
* Android 16 以降が必要です (19 以降を推奨)。

## <a name="library"></a>ライブラリ

このガイドでは、次の認証ライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

## <a name="set-up-your-project"></a>プロジェクトの設定

このチュートリアルでは新しいプロジェクトを作成します。 代わりに完了したチュートリアルをダウンロードする場合は、[コードをダウンロード](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)してください。

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

1. Android Studio を開き、**[Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\)** を選択します。
    - Android Studio を既に開いている場合は、**[File]\(ファイル\)** > **[New]\(新規\)** > **[New Project]\(新しいプロジェクト\)** を選択します。
2. **[Empty Activity]\(空のアクティビティ\)** をそのままにして、**[Next]\(次へ\)** を選択します。
3. アプリケーションに名前を付け、`Minimum API level` を **API 19 以降**に設定して、**[Finish]\(完了\)** をクリックします。
5. `app/build.gradle` で、`targetedSdkVersion` を 27 に設定します。 

## <a name="register-your-application"></a>アプリケーションの登録

次の 2 つのセクションで説明する方法のいずれかを使用して、アプリケーションを登録できます。

### <a name="register-your-app"></a>アプリの登録

1. [Azure portal](https://aka.ms/MobileAppReg) にアクセスし、`New registration` を選択します。 
2. お使いのアプリ > `Register` で **名前**を入力します。 **この段階ではリダイレクト URI を設定しないでください**。 
3. `Manage` セクションで `Authentication`  >  `Add a platform`  >  `Android` の順に移動します。
    - プロジェクトのパッケージ名を入力します。 コードをダウンロードした場合、この値は `com.azuresamples.msalandroidapp` です。 
    - デバッグ/開発の署名ハッシュを入力します。 ポータルで KeyTool コマンドを使用して、署名ハッシュを生成します。 
4. `Configure` をクリックし、後で使用できるように ***MSAL の構成***を保存します。 

## <a name="build-your-app"></a>アプリの構築

### <a name="configure-your-android-app"></a>お使いの Android アプリを構成する

1. **[res]** を右クリックし、 > **[New]\(新規\)** > **[Folder]\(フォルダー\)** > **[Raw Resources Folder]\(生のリソース フォルダー\)** を選択します。
2. **[app]\(アプリ\)** > **[res]** > **[raw]\(生\)** で、`auth_config.json` という新しい JSON ファイルを作成して、お使いの ***MSAL の構成***を貼り付けます。 詳細については、[MSAL の構成](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app)に関するページを参照してください。
   <!-- Workaround for Docs conversion bug -->
3. **[app]\(アプリ\)** > **[manifests]\(マニフェスト\)** > **[AndroidManifest.xml]** で、以下の `BrowserTabActivity` アクティビティを追加します。 このエントリにより、Microsoft は認証の完了後にアプリケーションにコールバックできます。

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    使用される署名ハッシュは **AndroidManifest.xml** で URL エンコードされてはならないことに注意してください。 

4. **AndroidManifest.xml** 内の `<application>` タグのすぐ上に、次のアクセス許可を追加します。

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. `BrowserTabActivity` 内の ***[Package Name]\(パッケージ名\)*** と ***[Signature Hash]\(署名ハッシュ\)*** を Azure portal で登録された値に置き換えます。

### <a name="create-the-apps-ui"></a>アプリケーションの UI を作成する

1. **[res]** > **[layout]** に移動し、**activity_main.xml** を開きます。
2. アクティビティのレイアウトを `android.support.constraint.ConstraintLayout` などから `LinearLayout` に変更します。
3. `android:orientation="vertical"` プロパティを `LinearLayout` ノードに追加します。
4. `LinearLayout` ノードに次のコードを貼り付け、現在の内容を置き換えます。

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>プロジェクトに MSAL を追加する

1. Android Studio で、**[Gradle Scripts]\(Gradle スクリプト\)** > **build.gradle (Module: app)** を選択します。
2. **[Dependencies]\(依存関係\)** で、次のコードを貼り付けます。

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>MSAL の使用 

次のいくつかのセクションでは、`MainAcitivty.java` 内で変更を行います。 お使いのアプリで MSAL を追加して使用するのに必要な各手順を説明します。

#### <a name="required-imports"></a>必要なインポート

以下のインポートをご使用のプロジェクトに追加します。 

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiating-msal"></a>MSAL のインスタンス化 

`MainActivity` クラス内で、MSAL をインスタンス化するとともに、アクセスしたいスコープや Web API など、アプリが何をするのかについての構成をいくつか行う必要があります。 

`MainActivity` 内に次の変数をコピーします。

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

次に、MSAL をインスタンス化するために、`onCreate(...)` メソッド内に次のコードをコピーします。

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

上記のコード ブロックは、`getAccounts(...)` を介してアプリケーションのオープン時にサイレントにユーザーをサインインさせようとします。成功した場合は `acquireTokenSilentAsync(...)` を使用します。  次のいくつかのセクションでは、サインインしたアカウントがない場合のためにコールバック ハンドラーを実装します。 

#### <a name="use-msal-to-get-tokens"></a>MSAL を使用してトークンを取得する

これで、アプリの UI 処理ロジックを実装し、MSAL を介して対話的にトークンを取得できるようになりました。 

MSAL では、トークンを取得するための主要なメソッドとして `acquireTokenSilentAsync` と `acquireToken` が公開されています。  

`acquireTokenSilentAsync` では、ユーザーのサインインが行われ、アカウントが存在すればユーザーとの対話なしにトークンが取得されます。 成功した場合、MSAL はトークンをお使いのアプリに渡します。失敗した場合、`MsalUiRequiredException` を生成します。  この例外が生成された場合、または対話型サインインのエクスペリエンス (資格情報、mfa またはその他の条件付きアクセス ポリシーが必要なこともあれば、不要なこともあります) をユーザーに提供する場合は、`acquireToken` を使用できます。  

`acquireToken` ではユーザーのサインインとトークンの取得が行われるときに必ず UI が表示されますが、ブラウザーのセッション Cookie または Microsoft Authenticator のアカウントを使用して対話形式で SSO が行われる場合もあります。 

開始するには、`MainActivity` クラス内に次の 3 つの UI メソッドを作成します。

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

次に、現在のアクティビティを取得して、サイレント型および対話型のコールバックを処理するメソッドを追加します。

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>サインアウトに MSAL を使用する

次に、サインアウトのサポートをアプリに追加します。 

重要な点として、MSAL でサインアウトするとユーザーに関する既知の情報がすべてこのアプリケーションから削除されますが、ユーザーのデバイスではアクティブなセッションが維持されます。 ユーザーが再びサインインを試みた場合に対話画面が表示される可能性がありますが、デバイス セッションがアクティブであるため、資格情報を再入力する必要はありません。 

サインアウトを追加するには、すべてのアカウントを循環してそれらを削除する次のメソッドをアプリにコピーします。

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API を呼び出す

トークンの取得が成功すると、Microsoft Graph API に対して要求を実行できます。 アクセス トークンは、認証コールバックの `onSuccess(...)` メソッド内の `AuthenticationResult` にあります。 承認された要求を作成するには、お使いのアプリで、アクセス トークンを HTTP ヘッダーに追加する必要があります。

| ヘッダー キー    | value                 |
| ------------- | --------------------- |
| Authorization | Bearer <access-token> |

コードでこれを行うには、次の 2 つのメソッドをお使いのアプリに追加し、グラフを呼び出して UI を更新します。 

```java
    /* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

[Microsoft Graph API](https://graph.microsoft.com) の詳細についてご確認ください。

#### <a name="multi-account-applications"></a>マルチアカウント アプリケーション

このアプリは、シングル アカウントのシナリオを対象にビルドされています。 MSAL ではマルチアカウントのシナリオもサポートされますが、その場合はアプリで追加の作業が必要となります。 トークンを必要とする各アクションに使用するアカウントをユーザーが選択するための UI を作成する必要があります。 あるいは、アプリでヒューリスティックを実装して、`getAccounts(...)` メソッドによって使用するアカウントを選択することもできます。 

## <a name="test-your-app"></a>アプリをテストする

### <a name="run-locally"></a>ローカルで実行する

上記のコードに従っている場合は、アプリをビルドしてテスト デバイスまたはエミュレーターにデプロイします。 サインインして Azure AD または個人用 Microsoft アカウントのトークンを取得できるようになります。 ユーザーのサインイン後に、Microsoft Graph の `/me` エンドポイントから返されたデータがこのアプリに表示されます。 

問題が発生した場合は、このドキュメントまたは MSAL ライブラリで問題を開いてご連絡ください。 

### <a name="consent-to-your-app"></a>アプリに同意する

ユーザーは、アプリに初めてサインインするときに、Microsoft Identity から、要求されたアクセス許可に同意するよう求められます。  ほとんどのユーザーはこれに同意できますが、一部の Azure AD テナントではユーザーによる同意が無効になっており、全ユーザーに代わって管理者が同意を行う必要があります。  このシナリオに対応できるように、必ず Azure portal でアプリのスコープを登録しておいてください。

## <a name="help-and-support"></a>ヘルプとサポート

このチュートリアルまたは Microsoft ID プラットフォームで問題が発生した場合は、 [ヘルプとサポート](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)に関するページを参照してください。
