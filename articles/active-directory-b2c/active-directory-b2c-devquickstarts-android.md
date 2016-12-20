---
title: "Azure Active Directory B2C: Android アプリケーションから Web API を呼び出す | Microsoft Docs"
description: "この記事では、OAuth 2.0 ベアラー トークンを使用して Node.js Web API を呼び出す Android の &quot;To-Do List&quot; アプリを作成する方法について説明します。 Android アプリと Web API は、どちらも Azure Active Directory B2C を使用してユーザー ID を管理し、ユーザーを認証します。"
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 07/22/2016
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: a977cb509fb64d7c986e2e0f7e2b5e4e3e45dec0
ms.openlocfilehash: 6759ce37405daff5641baae47e27b64c322ca7a9


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C: Android アプリケーションから Web API を呼び出す
> [!WARNING]
> このチュートリアルには、重要な更新をいくつか加える必要があり、特に B2C 用の ADAL Android の使用についての記述は削除する必要があります。  Android アプリで Azure AD B2C を使用する方法について、最新版の手順の公開を来週に予定しているため、それまでお待ちいただくことをお勧めします。  ただし、単にいろいろな方法を試したいという場合でしたら、このまま以下の記事にお進みください。
>
>

Azure Active Directory (Azure AD) B2C を使用すると、強力なセルフサービス方式の ID 管理機能を、わずかな手順で Android アプリや Web API に追加できます。 この記事では、OAuth 2.0 ベアラー トークンを使用して Node.js Web API を呼び出す Android の "To-Do List" アプリを作成する方法を示します。 Android アプリと Web API は、どちらも Azure AD B2C を使用してユーザー ID を管理し、ユーザーを認証します。

このクイック スタートを正しく利用するには、Azure AD と B2C によって保護されている Web API を事前に用意する必要があります。 すぐに使用できる .NET と Node.js 用の Web API が既にビルドされています。 このチュートリアルでは、Node.js Web API のサンプルが構成済みであることを前提としています。 詳細については、 [Node.js 用の Azure AD B2C Web API](active-directory-b2c-devquickstarts-api-node.md)のチュートリアルを参照してください。

保護されたリソースにアクセスする必要がある Android クライアントに対しては、Azure AD は Active Directory 認証ライブラリ (ADAL) を提供します。 ADAL の唯一の目的は、アプリがアクセス トークンを容易に取得できるようにすることです。 それがどれほど簡単であるかを示すために、このガイドで、次を実行する Android To-Do List アプリケーションを作成します。

* [OAuth 2.0 認証プロトコル](https://msdn.microsoft.com/library/azure/dn645545.aspx)を使用して To-Do List API を呼び出すためのアクセス トークンを取得します。
* ユーザーの To-Do List を取得します。
* ユーザーをサインアウトします。

> [!NOTE]
> この記事では、Azure AD B2C を使用してサインイン、サインアップ、プロファイルの管理を実装する方法については説明しません。 ユーザーが認証された後の Web API の呼び出し方法を中心に説明します。 まだ Azure AD B2C の基本を理解していない場合は、 [.NET Web アプリ入門チュートリアル](active-directory-b2c-devquickstarts-web-dotnet.md) で学習してください。
>
>

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C ディレクトリの取得
Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。 ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。 まだディレクトリを作成していない場合は、先に進む前に [B2C ディレクトリを作成](active-directory-b2c-get-started.md) してください。

## <a name="create-an-application"></a>アプリケーションの作成
次に、B2C ディレクトリにアプリを作成する必要があります。 これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。 ここでは、アプリと Web API の両方が単一の**アプリケーション ID** で表されます。これは、アプリと Web API が 1 つの論理アプリを構成するためです。 アプリを作成するには、 [こちらの手順](active-directory-b2c-app-registration.md)に従ってください。 次を行ってください。

* アプリケーションに **Web アプリ**/**Web API** を含めます。
* **[応答 URL]** に「`urn:ietf:wg:oauth:2.0:oob`」と入力します。 これはこのサンプル コードで使用する既定の URL です。
* アプリケーション用の **アプリケーション シークレット** を作成し、それをメモしておきます。 この情報は後で必要になります。 この値は、使用する前に [XML エスケープ](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) する必要があることに注意してください。
* アプリに割り当てられた **アプリケーション ID** をコピーしておきます。 この情報も後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>ポリシーの作成
[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Azure AD B2C では、すべてのユーザー エクスペリエンスが [ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。 このアプリには、3 つの ID エクスペリエンス (サインアップ、サインイン、および Facebook を使用したサインイン) が含まれています。  [ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。 3 つのポリシーを作成するときは、以下の点に注意してください。

* サインアップ ポリシーで、 **[表示名]** と他のサインアップ属性を選択します。
* すべてのポリシーで、アプリケーション要求として **[表示名]** と **[オブジェクト ID]** を選択します。 その他のクレームも選択できます。
* ポリシーの作成後、各ポリシーの **名前** をコピーしておきます。 名前には、 `b2c_1_`というプレフィックスが付加されています。  これらのポリシー名は後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーを作成した後は、いつでもアプリをビルドできます。

この記事では、作成したポリシーの使用方法については説明しません。 ポリシーが Azure AD B2C でどのように機能するかを学習する場合は、「 [Azure AD B2C プレビュー: .NET Web アプリケーションを構築する](active-directory-b2c-devquickstarts-web-dotnet.md)」から始めてください。

## <a name="download-the-code"></a>コードのダウンロード
このチュートリアルのコードは、 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android)で管理されています。 [スケルトン プロジェクトを .zip ファイルとしてダウンロード](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip)し、手順に従ってサンプルを構築できます。 スケルトンを複製することもできます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [!NOTE]
> **このチュートリアルを完了するには、スケルトンをダウンロードする必要があります。**  完全に機能する Android アプリケーションを実装するのは複雑な作業であるため、スケルトンには、チュートリアルを完了した後で実行される UX コードが含まれています。 これは、開発者の時間を短縮するための手段です。 UX コードは、B2C を Android アプリケーションに追加する方法のトピックと完全には連動していません。
>
>

また、完成済みのアプリも、[.zip ファイルとして入手する](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)か、同じリポジトリの `complete` ブランチで入手できます。

Maven を使用して構築するために、最上位レベルで `pom.xml` を使用することができます。

1. [「Setting up maven environment for Android (Android の Maven 環境のセットアップ)」の「Prerequisites (前提条件)」](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)の手順に従います。
2. SDK 21 でエミュレーターをセットアップします。
3. リポジトリを複製したルート フォルダーに移動します。
4. コマンド `mvn clean install`を実行します。
5. ディレクトリをクイック スタート サンプル `cd samples\hello`に変更します。
6. コマンド `mvn android:deploy android:run`を実行します。

アプリが起動します。 テスト ユーザー資格情報を入力して試行します。

Android Archive (AAR) パッケージに加え、Java Archive (JAR) パッケージも送信されます。

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>Android ADAL をダウンロードして Android Studio ワークスペースに追加する
Android プロジェクトでこのライブラリを使用する方法にはオプションがあります。

* ソース コードを使用してライブラリを Eclipse にインポートし、アプリケーションにリンクできます。
* Android Studio を使用する場合は、AAR パッケージ形式を使用し、バイナリを参照します。

### <a name="option-1-binaries-via-gradle-recommended"></a>オプション 1: Gradle 経由のバイナリ (推奨)
Maven 中央リポジトリからバイナリを取得できます。 AAR パッケージは、次のようにして Android Studio でプロジェクトに含めることができます (例: `build.gradle`)。

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### <a name="option-2-aar-via-maven"></a>オプション 2. Maven 経由の AAR
Eclipse で `m2e` プラグインを使用する場合、`pom.xml` ファイルで次のように依存関係を指定できます。

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>オプション 3: Git 経由のソース (最終手段として)
SDK のソース コードを Git 経由で取得するには、次のように入力します。

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

ブランチの **収束**

## <a name="set-up-your-configuration-file"></a>構成ファイルを設定する
先ほど B2C ポータルでセットアップした構成を使用し、Android プロジェクトを構成します。

`helpes/Constants.java` を開き、次のように値を入力します。

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
* `SCOPES`: ユーザーのサインイン時にサーバーから要求し、サーバーに渡すスコープ。 B2C プレビューでは、 `client_id`を渡します。 ただし、これは今後、 `read scopes` に変更される予定です。 このドキュメントはその時点で更新されます。
* `ADDITIONAL_SCOPES`: アプリケーションで使用できるその他のスコープ。 将来的に使用されることが見込まれています。
* `CLIENT_ID`: ポータルから取得したアプリケーション ID。
* `REDIRECT_URL`: トークンが戻されるリダイレクト先。
* `EXTRA_QP`: URL エンコード形式でサーバーに渡す追加のパラメーター。
* `FB_POLICY`: 呼び出すポリシー。 このチュートリアルで最も重要な部分です。
* `EMAIL_SIGNIN_POLICY`: 呼び出すポリシー。 このチュートリアルで最も重要な部分です。
* `EMAIL_SIGNUP_POLICY`: 呼び出すポリシー。 このチュートリアルで最も重要な部分です。

## <a name="add-references-to-android-adal-to-your-project"></a>プロジェクトに Android ADAL への参照を追加する
> [!NOTE]
> ADAL for Android は、インテント ベースのモデルを使用して認証を呼び出します。 インテントは、アプリの橋渡し的役割を果たします。 このサンプル全体とすべての ADAL for Android では、インテントを管理し、インテントの間で情報を渡す方法に重点が置かれています。
>
>

最初に、使用するインテントを含む、アプリケーションのレイアウトを Android に伝えます。 これらのインテントについては、このチュートリアルの後半で詳しく説明します。

プロジェクトの `AndroidManifest.xml` ファイルを更新し、必要なインテントをすべて含めます。

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

ご覧のように、5 つのアクティビティを定義します。 これらすべてを使用します。

* `AuthenticationActivity`: これは ADAL からのアクティビティであり、サインイン Web ビューを提供します。
* `LoginActivity`: サインイン ポリシーと各ポリシーのボタンを表示します。
* `SettingsActivity`: これを使用して、実行時にアプリ設定を変更します。
* `AddTaskActivity`: これを使用して、Azure AD で保護されている REST API にタスクを追加します。
* `ToDoActivity`: これはタスクを表示するメイン アクティビティです。

## <a name="create-the-sign-in-activity"></a>サインイン アクティビティを作成する
メイン アクティビティを作成し、それを `LoginActivity`と呼ぶことにしましょう。

`LoginActivity.java`という名前でファイルを作成します。

アクティビティを初期化し、UI を制御するボタンをいくつか追加する必要があります。 以前に Android コードを記述した経験があれば、これはなじみのある措置です。

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
これで (トークンが必要なときに ADAL を呼び出す) `ToDoActivity` インテントを呼び出すボタンが作成されました。 その際、参照と追加パラメーターとしてアクティビティを利用します。 この追加パラメーターは、 `intent.putExtra()` メソッドで渡します。 `Constants.java` で指定したものを使用して、`"thePolicy"` を定義します。 これで認証時に呼び出すポリシーがインテントに伝えられます。

## <a name="create-the-settings-activity"></a>設定アクティビティを作成する
これは設定 UI の内容を作成するアクティビティです。

単純な CRUD (create, read, update, and delete/作成、読み取り、更新と削除) 操作のために `SettingsActivity.java` という名前のファイルを作成します。

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

    //private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        loadSettings();
//        checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//        checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


    }

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

    private void saveSettings(String key, boolean value) {
        SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
                Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
        Editor prefsEditor = prefs.edit();
        prefsEditor.putBoolean(key, value);
        prefsEditor.commit();
    }
}
```

## <a name="create-the-add-task-activity"></a>タスク追加アクティビティを作成する
このアクティビティを使用し、REST API エンドポイントにタスクを追加できます。

`AddTaskActivity.java` という名前のファイルを作成し、次を記述します。

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

## <a name="create-the-to-do-list-activity"></a>To Do リスト アクティビティを作成します。
これは最も重要なアクティビティです。 これを利用してポリシーのトークンを Azure AD から取得し、そのトークンを利用してタスク REST API サーバーを呼び出すことができます。

`ToDoActivity.java` という名前のファイルを作成し、次を記述します。 (呼び出しについては後で説明します。)

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}

```


 これはまだ記述されていないメソッドに依存します。 そのようなメソッドは `updateLoggedInUser()`、`clearSessionCookie()`、`getTasks()` などです。 これらはこのガイドの後半で記述します。 現時点では Android Studio でエラーが発生しますが、それらは無視してかまいません。

パラメーターの説明:

* `SCOPES`: 必須。要求しようとしているアクセスのスコープ。 B2C プレビューの時点では `client_id` と同じですが、将来変更される予定です。
* `POLICY`: ユーザーを認証するときのポリシー。
* `CLIENT_ID`: 必須。Azure AD ポータルからのパラメーター。
* `redirectUri`: パッケージ名として設定可能。 `acquireToken` 呼び出しの場合は、必須ではありません。
* `getUserInfo()`: ユーザーがキャッシュに入っているかどうかを調べる方法。 このパラメーターはまた、ユーザーが見つからない場合やユーザーのアクセス トークンが無効になっている場合にユーザーに指示を促す方法も表します。 このメソッドはこのガイドの後半で記述します。
* `PromptBehavior.always`: キャッシュと Cookie をスキップするための資格情報の入力を要求できます。
* `Callback`: 承認コードがトークンと交換された後に呼び出されます。 アクセス トークン、有効期限、ID トークン情報が含まれるオブジェクト `AuthenticationResult`が含まれます。

> [!NOTE]
> Microsoft Intune のポータル サイト アプリでは、ブローカー コンポーネントを提供しています。そのアプリがユーザーのデバイスにインストールされている可能性があります。 このアプリは、デバイス上のすべてのアプリケーションでシングル サインオン (SSO) アクセスを提供します。 開発者は Intune を考慮に入れる必要があります。 ADAL for Android は、Authenticator で作成されたユーザー アカウントが 1 つ存在する場合、ブローカー アカウントを使用します。 ブローカーを使用するには、開発者がブローカー用に特殊な `redirectUri` を登録する必要があります。 `redirectUri` の形式は msauth://packagename/Base64UrlencodedSignature になります。 スクリプト `brokerRedirectPrint.ps1` か API 呼び出し `mContext.getBrokerRedirectUri()` を使用して、アプリの `redirectUri` を取得できます。 署名は、Google Play ストアからの署名証明書に関連付けられています。
>
>

 次のように指定してブローカー ユーザーをスキップできます。

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [!NOTE]
> この B2C クイック スタートを簡潔にする目的で、サンプル コードではローカーをスキップしています。
>
>

次に、タスク API の認証呼び出し中にトークンだけを取得するヘルパー メソッドを作成します。

同じ `ToDoActivity.java` ファイルで、次を記述します。

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

`AuthenticationResult` (トークンを含む) を "取得" し、グローバル `Constants` に "設定" するメソッドも追加します。 `ToDoActivity.java` ではそのフローで `sResult` が使用されますが、これらのメソッドを追加する必要があります。 追加しない場合、他のアクティビティはトークンにアクセスして ( `AddTaskActivity.java`のタスクの追加などの) 作業を実行することができません。

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>ユーザー ID を返すメソッドを作成する
ADAL for Android は、 `UserIdentifier` オブジェクトの形式でユーザーを表します。 これはユーザーを管理します。 このオブジェクトを使用し、同じユーザーが呼び出しで使用されているかどうかを確認できます。 この情報を使用することで、サーバーを新しく呼び出す代わりに、キャッシュを利用できます。 これを簡単にするために、`UserIdentifier` を返す `getUserInfo()` メソッドを作成しました。 これは `acquireToken()`で使用できます。 キャッシュで `UserIdentifier` の ID を返す `getUniqueId()` メソッドも作成しました。

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }

```

### <a name="write-helper-methods"></a>ヘルパー メソッドを記述する
次に、Cookie を消去し、 `AuthenticationCallback`を提供するヘルパー メソッドをいくつか記述します。 これらのメソッドはサンプル目的のみで使用され、 `ToDo` アクティビティの呼び出し時に Cookie が消去されている状態にします。

同じ `ToDoActivity.java`という名前のファイルで、次を記述します。

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
```

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }

```   

## <a name="call-the-task-api"></a>タスク API を呼び出す
アクティビティでトークンを受け取る用意ができたら、タスク サーバーにアクセスする API を記述します。

`getTasks` により、サーバー内のタスクを表す配列が与えられます。

`getTasks`で開始します。

同じ `ToDoActivity.java`という名前のファイルで、次を記述します。

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```

初回実行時にテーブルを初期化するメソッドも作成します。

同じ `ToDoActivity.java`という名前のファイルで、次を記述します。

```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```

このコードが機能するためには、他にもいくつかのメソッドが必要になります。 次にこれらを記述します。

### <a name="create-an-endpoint-url-generator"></a>エンドポイント URL ジェネレーターを作成する
接続先となるエンドポイント URL を生成する必要があります。 同じクラス ファイルでこの処理を行います。

**同じ `ToDoActivity.java` という名前のファイル**で、次を記述します。

 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


次のセクションで説明するコードで要求にアクセス トークンを追加します。

## <a name="write-some-ux-methods"></a>UX メソッドを作成する
Android でアプリを動作させるためには、いくつかのコールバックを処理する必要があります。 それは `createAndShowDialog` と `onResume()` です。 以前に Android コードを記述した経験があれば、これはなじみのある措置です。

同じ `ToDoActivity.java`という名前のファイルで、次を記述します。

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }


```

次に、ダイアログ コールバックを管理します。

同じ `ToDoActivity.java`という名前のファイルで、次を記述します。

```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }

```

コンパイル可能な `ToDoActivity.java` ファイルが完成しました。 この時点で、プロジェクト全体もコンパイルできます。

## <a name="run-the-sample-app"></a>サンプル アプリを実行する
最後に、構築したアプリを Android Studio または Eclipse で実行します。 アプリにサインアップするか、サインインします。 サインインしたユーザーのタスクを作成します。 サインアウトし、別のユーザーとしてもう一度サインインし、そのユーザーのタスクを作成します。

API でタスクがユーザーごとに保存されたことを確認します。これは API が受信したアクセス トークンからユーザーID を抽出したためです。

参照用に、完成したサンプルが [ここに .zip として提供されています](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)。 GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```

## <a name="important-information"></a>重要な情報
### <a name="encryption"></a>暗号化
ADAL は、既定では `SharedPreferences` のトークンとストアを暗号化します。 `StorageHelper` クラスを参照して、詳細を確認できます。 Android には、秘密キーのセキュリティ保護された記憶域として **AndroidKeyStore for 4.3(API18)** が導入されています。 ADAL はこの API18 以降を使用します。 下位バージョンの SDK に ADAL を使用する場合は、 `AuthenticationSettings.INSTANCE.setSecretKey`で秘密キーを提供する必要があります。

### <a name="session-cookies-in-web-view"></a>Web ビューのセッション Cookie
Android Web ビューは、アプリを閉じた後、セッションの Cookie をクリアしません。 これは次のサンプル コードで処理できます。

```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[Cookie の詳細については、こちらを参照してください](http://developer.android.com/reference/android/webkit/CookieSyncManager.html)。



<!--HONumber=Nov16_HO3-->


