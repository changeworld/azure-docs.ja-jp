<properties pageTitle="Azure AD B2C プレビュー: Android アプリケーションからの Web API の呼び出し | Microsoft Azure" description="この記事では、OAuth 2.0 ベアラー トークンを使用して node.js Web API を呼び出す Android の "To-Do List" アプリを作成する方法を示します。Android アプリと Web API は、どちらも Azure AD B2C を使用してユーザー ID を管理し、ユーザーを認証します。" services="active-directory-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# Azure AD B2C プレビュー: Android アプリケーションからの Web API の呼び出し

Azure AD B2C を使用すると、強力なセルフサービス方式の ID 管理機能をわずかな手順で Android アプリと Web API に追加できます。この記事では、OAuth 2.0 ベアラー トークンを使用して node.js Web API を呼び出す Android の "To-Do List" アプリを作成する方法を示します。Android アプリと Web API は、どちらも Azure AD B2C を使用してユーザー ID を管理し、ユーザーを認証します。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]このクイック スタートを完全に機能させるためには、前提条件として、Azure AD で B2C によって保護されている Web API が存在する必要があります。すぐに使用できる .Net と node.js 用の Web API が既にビルドされています。このチュートリアルでは、node.js Web API のサンプルが構成されていると見なします。「[Node.js 用 Azure AD B2C Web API チュートリアル](active-directory-b2c-devquickstarts-api-node.md)」を参照してください。

> [AZURE.NOTE]この記事では、サインイン、サインアップ、およびプロファイル管理を Azure AD B2C で実装する方法については説明しません。ユーザーが既に認証された後での Web API の呼び出しに焦点を合わせています。まだ行っていない場合は、先に「[.NET Web アプリ入門チュートリアル](active-directory-b2c-devquickstarts-web-dotnet.md)」で Azure AD B2C の基本を学習してください。

保護されたリソースにアクセスする必要がある Android クライアントに対しては、Azure AD は Active Directory 認証ライブラリ (ADAL) を提供します。ADAL の唯一の目的は、アプリがアクセス トークンを容易に取得できるようにすることです。それがどれほど簡単であるかを示すために、ここで、次を実行する Android To-Do List アプリケーションを作成します。

-	[OAuth 2.0 認証プロトコル](https://msdn.microsoft.com/library/azure/dn645545.aspx)を使用して To-Do List API を呼び出すためのアクセス トークンを取得します。
-	ユーザーの To-Do List を取得します。
-	ユーザーのサインアウト処理を行います。



### 手順 1: Azure AD B2C ディレクトリの取得

Azure AD B2C を使用するには、ディレクトリまたはテナントを作成しておく必要があります。ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。まだディレクトリを作成していない場合は、先に進む前に「[Azure AD B2C プレビュー: Azure AD B2C ディレクトリの作成方法](active-directory-b2c-get-started.md)」を参照してください。

### 手順 2: アプリケーションの作成

次に、B2C ディレクトリ内にアプリを作成する必要があります。これによって、 アプリと安全に通信するために必要ないくつかの情報が Azure AD に提供されます。ここでは、アプリと Web API の両方が単一の**アプリケーション ID**で表されます。これは、アプリと Web API が 1 つの論理アプリを構成するためです。アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従います。このとき、

- アプリケーションに **Web アプリまたは Web API** を含めます。
- `urn:ietf:wg:oauth:2.0:oob` を**応答 URL** として入力します。これはこのサンプル コードで使用する既定の URL です。
- アプリケーション用の**アプリケーション シークレット**を作成し、それをメモしておきます。このプロジェクトはすぐに必要になります。
- アプリに割り当てられた**アプリケーション ID** をメモしておきます。こちらもすぐに必要になります。

    > [AZURE.IMPORTANT][Azure ポータル](https://manage.windowsazure.com/)の **[アプリケーション]** タブで登録されたアプリケーションは、ここでは使用できません。

### 手順 3: ポリシーを作成する

> [AZURE.NOTE]この B2C プレビューでは、クライアントとサーバーの両方の設定に同じポリシーを使います。既にチュートリアルを行っていて、これらのポリシーを作成してある場合は、再度作成する必要はありません。ポータルで前に作成したポリシーがアプリケーションの要件に一致する場合は、それを再利用してかまいません。

Azure AD B2C では、すべてのユーザー エクスペリエンスは[**ポリシー**](active-directory-b2c-reference-policies.md)によって定義されます。このアプリには、3 つの ID エクスペリエンス (サインアップ、サインイン、および Facebook でのサインイン) が含まれます。[ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。3 つのポリシーを作成するときは、以下の点に注意してください。

- サインアップ ポリシーで、**[表示名]** と他のいくつかのサインアップ属性を選択します。
- すべてのポリシーで **[表示名]** と **[オブジェクト ID]** のアプリケーション クレームを選択します。その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの **[名前]** をメモしておきます。名前には、プレフィックス `b2c_1_` が付加されます。これらのポリシー名はすぐに必要になります。 

3 つのポリシーの作成が正常に完了したら、いつでもアプリをビルドできます。

この記事では、作成したポリシーの使用方法については説明しません。ポリシーが Azure AD B2C でどのように機能するかを学習する場合は、「[Azure AD B2C プレビュー: .NET Web アプリをビルドする](active-directory-b2c-devquickstarts-web-dotnet.md)」から始めてください。

### 手順 4: コードをダウンロードする

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android) で管理されています。手順に従ってサンプルを構築するために、[スケルトン プロジェクトを .zip 形式でダウンロードする](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip)か、次のようにスケルトンを複製できます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**スケルトンをダウンロードすることは、このチュートリアルを完了するために必要な作業です。** 完全に機能するアプリケーションを Android 上に実装するのは複雑な作業であるため、**スケルトン**には、この後のチュートリアルを完了した後で実行される UX コードが含まれています。これは、開発者の時間を短縮するための手段です。UX コードは B2C を Android アプリケーションに追加するトピックと深い関わりはありません。

また、完成済みのアプリも、[.zip 形式で入手する](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)か、同じリポジトリの `complete` ブランチで利用できます。


Maven を使用して構築するために、最上位レベルで pom.xml を使用することができます。


  * [「Setting up maven environment for Android」の「Prerequisites」セクション](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)の手順に従います。
  * SDK 21 でエミュレーターをセットアップします。
  * リポジトリを複製したルート フォルダーに移動します。
  * コマンド mvn clean install を実行します。
  * cd samples\\hello を実行して、ディレクトリをクイック スタート サンプルに変更します。
  * コマンド mvn android:deploy android:run を実行します。
  * アプリが起動します。
  * テスト ユーザー資格情報を入力して、試行します。

aar パッケージに加え、jar パッケージも送信されます。

### 手順 5: Android ADAL をダウンロードして Android Studio ワークスペースに追加する

このライブラリを Android プロジェクトで簡単に使用できるようにするために、複数のオプションが用意されています。

* ソース コードを使用して、このライブラリを Eclipse にインポートし、アプリケーションにリンクします。
* Android Studio を使用する場合は、*aar* パッケージ形式を使用して、バイナリを参照します。



####オプション 1: Gradle 経由のバイナリ (推奨)

Maven 中央リポジトリからバイナリを取得できます。aar パッケージは、次のようにして AndroidStudio でプロジェクトに含めることができます (たとえば `build.gradle`)。

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
    compile('com.microsoft.aad:adal:2.0-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0-alpha
}
```

####オプション 2. Maven 経由の aar

Eclipse で m2e プラグインを使用する場合は、`pom.xml` ファイルで次のように依存関係を指定できます。

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0-alpha</version>
    <type>aar</type>
</dependency>
```

####オプション 3: Git 経由のソース (最終手段として)

SDK のソース コードを Git 経由で取得するには、次のように入力します。

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"


### 手順 6: 構成ファイルを設定する

先ほど B2C ポータルでセットアップした構成を使用して、Android プロジェクトを構成します。

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
**SCOPES** - ユーザーがログインしているサーバーから要求するサーバーに渡すスコープです。B2C プレビューでは client\_id を渡します。ただし、これは、読み取りスコープに代わる予定です。このドキュメントはその時点で更新されます。**ADDITIONAL\_SCOPES** - アプリケーションで使用できるその他のスコープです。これは将来使用される予定です。**CLIENT\_ID** - ポータルから取得したアプリケーション ID。**REDIRECT\_URL** - トークンが戻されるリダイレクト先。**EXTRA\_QP** - URL エンコード形式でサーバーに渡す追加のパラメーター。**FB\_POLICY** -呼び出すポリシー。このチュートリアルで最も重要な部分です。**EMAIL\_SIGNIN\_POLICY** - 呼び出すポリシー。このチュートリアルで最も重要な部分です。**EMAIL\_SIGNUP\_POLICY** - 呼び出すポリシー。このチュートリアルで最も重要な部分です。

### 手順 7: プロジェクトに Android ADAL への参照を追加する


> [AZURE.NOTE]ADAL for Android は、インテント ベースのモデルを使用して認証を呼び出します。インテントは、アプリの橋渡し的役割を果たします。このサンプルでは (ADAL for Android の使用全般に言えることですが) インテントを利用し、アプリ間で情報を受け渡ししています。


最初に、使用する Intents() を含め、対象アプリケーションのレイアウトを Android に伝える必要があります。これらのインテントについては後で詳しく説明します。

プロジェクトの AndroidManifest.xml ファイルを更新して、必要なインテントをすべて含めます。

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

ここでは 5 つのアクティビティを使用します。ご覧のとおり、次のアクティビティが定義されています。

**AuthenticationActivity** - ADAL によって提供されるアクティビティです。ログインの Webview として機能します。

**LoginActivity** - サインイン ポリシーと各ポリシーのボタンを表示します。

**SettingsActivity** - アプリの設定を実行時に変更するために必要となります。

**AddTaskActivity** - Azure AD によって保護された REST API にタスクを追加するために必要となります。

**ToDoActivity** - タスクを表示するメイン アクティビティです。



### 手順 8: ログイン アクティビティを作成する

それではメイン アクティビティを作成し、それを「`LoginActivity`」と呼ぶことにしましょう。

`LoginActivity.java` という名前でファイルを作成してください。

アクティビティを初期化し、UI を制御するボタンをいくつか追加する必要があります。この作業も非常に単純であり、Android コードの記述経験があれば馴染みのあるものです。

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
ここで作成しているのは、(トークンが必要になったときに ADAL を呼び出す) ToDoActivity インテントを呼び出すボタンです。独自のアクティビティを参照として指定したうえで、追加パラメーターを指定しています。この追加パラメーターは、`intent.putExtra()` メソッドで渡します。ここに定義されている "thePolicy" は、`Constants.java` で指定したものです。インテントが認証時に呼び出すポリシーを把握するために必要となります。

### 手順 9: Settings アクティビティを作成する

これは、Settings UI の内容を作成するアクティビティです。

`SettingsActivity.java` という名前でファイルを作成してください。

設定の読み取りや保存など単純な処理を行っています。

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
//		checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//		checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

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

### 手順 10: AddTask アクティビティを作成する

このアクティビティは、REST API エンドポイントにタスクを追加する目的で使用されます。これも非常に単純です。

`AddTaskActivity.java` という名前でファイルを作成してください。

次のコードを記述します。

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

### 手順 11: ToDoList アクティビティを作成する

いよいよ最も重要なアクティビティについて取り上げます。このアクティビティによって、ポリシーに対応するトークンを Azure AD から取得し、そのトークンを使用して Task REST API サーバーを呼び出すことができます。

`ToDoActivity.java` という名前でファイルを作成してください。

次のコードを記述します (各呼び出しについては後で説明します)。

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

        
 お気付きの方もいらっしゃると思いますが、まだ作成していないメソッドが使われています。`updateLoggedInUser()`、`clearSessionCookie()`、`getTasks()` などがそうです。それらのメソッドは、この後で作成します。現時点では Android Studio でエラーが発生しますが、それらは無視してかまいません。

パラメーターの説明:

  * ***SCOPES***: (必須) 要求するアクセスのスコープです。B2C プレビューの時点では Clientid と同じですが、将来変更される予定です。
  * ***POLICY***: ユーザーを認証する際に使用するポリシーです。 
  * ***CLIENT\_ID***: (必須) AzureAD ポータルから取得します。
  * redirectUri はパッケージ名として設定できます。acquireToken 呼び出しの場合は、必須ではありません。
  * ***getUserInfo()***: ユーザーが既にキャッシュに存在するかどうかを調べるための手段です。キャッシュに存在しない場合またはアクセス トークンが無効である場合、ユーザーに確認のメッセージを表示します。このメソッドは後で作成します。
  * ***PromptBehavior.always***: キャッシュと Cookie をスキップして、資格情報の入力を要求することができます。
  * ***callback***: トークン用に承認コードが交換された後に、呼び出されます。

  callback はオブジェクト AuthenticationResult を返します。これには accesstoken、期限切れの日付、idtoken の情報が含まれています。

> [AZURE.NOTE]Microsoft Intune のポータル サイト アプリは、ブローカー コンポーネントを備えています。そのアプリが、ユーザーのデバイスにインストールされている可能性があります。それによってデバイス上のすべてのアプリケーションに対する SSO が可能となるため、その点を見越した開発を行うようお勧めします。ADAL for Android は、Authenticator で作成されたユーザー アカウントが 1 つ存在する場合、ブローカー アカウントを使用します。ブローカーを使用するためには開発者が、ブローカー用に特殊な redirectUri を登録する必要があります。redirectUri の形式は msauth://packagename/Base64UrlencodedSignature です。アプリの redirecturi は、`brokerRedirectPrint.ps1` スクリプトを使用して取得できるほか、API 呼び出し `mContext.getBrokerRedirectUri()` を使用することもできます。署名は、Google Play ストアからの署名証明書に関連付けられています。

 ブローカー ユーザーをスキップするには、次のようにコードを記述します。

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE]この B2C クイック スタートのサンプル コードは簡潔にするために、ブローカーをスキップしています。

次に、いくつかのヘルパー メソッドを作成します。Task API の認証メソッドを呼び出すとき、トークンの取得だけは、これらのヘルパー メソッドで行います。

**同じファイル** (`ToDoActivity.java`) に次のコードを記述します。

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

さらに、目的のトークンが格納される AuthenticationResult をグローバル定数に設定するメソッドと、グローバル定数から取得するメソッドを追加します。`ToDoActivity.java` は、そのフローの中で **sResult** を使用しますが、他のアクティビティからは、作業 (タスクを `AddTaskActivity.java` に追加するなど) に必要なトークンにアクセスできません。その点を補うのが、これらのメソッドです。

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }

    
```
### 手順 12: UserIdentifier を取得するメソッドを作成する

ADAL for Android は、**UserIdentifier** オブジェクトの形式でユーザーを表します。このようにユーザーを管理することで、呼び出しに使用されているのが同じユーザーであるかどうかを把握し、キャッシュを利用するか、新たな呼び出しをサーバーに対して行うかの判断を行うことができます。これを簡単に行うために `getUserInfo()` を作成します。このメソッドから、`acquireToken()` に使用できる UserIdentifier が返されます。さらに、キャッシュに格納されている UserIdentifier の ID を効率よく取得する getUniqueId() メソッドを作成します。

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
 
### 手順 13: ヘルパー メソッドを作成する

Cookie をクリアしたり、AuthenticationCallback を実装したりするためのヘルパー メソッドを作成する必要があります。これらは、ToDo アクティビティを呼び出す際にクリーンな状態を形成する目的で、このサンプルでのみ使用されます。

**同じファイル** (`ToDoActivity.java`) に次のコードを記述します。

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

### 手順 14: Task API を呼び出す

必要な Activity はこれで完成です。あとは肝心なトークンを取得する必要があります。タスク サーバーにアクセスするための API を作成しましょう。

この `getTasks` メソッドには、サーバー内のタスクを表す配列が使用されています。

まず `getTask` を作成しましょう。

**同じファイル** (`ToDoActivity.java`) に次のコードを記述します。

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


 
 初回実行時にテーブルを初期化するメソッドも作成しましょう。
 
 **同じファイル** (`ToDoActivity.java`) に次のコードを記述します。
 
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
 
 このコードが機能するためには、他にもいくつかのメソッドが必要になります。それを次に記述しましょう。
 
### エンドポイント URL ジェネレーターを作成する
 
 接続先となるエンドポイント URL を生成する必要があります。同じクラス ファイルでこの処理を行うことにしましょう。
 
 **同じファイル** (`ToDoActivity.java`) に次のコードを記述します。
 
 ``` private URL getEndpointUrl() { URL endpoint = null; try { endpoint = new URL(Constants.SERVICE\_URL); } catch (MalformedURLException e) { e.printStackTrace(); } return endpoint; }

 ```


以降のコードで要求にアクセス トークンを追加します。

### 手順 15:UX メソッドを作成する

Android でアプリを動作させるためには、いくつかのコールバックを処理する必要があります。それが `createAndShowDialog` と `onResume()` です。この作業は非常に単純であり、Android コードの記述経験があれば馴染みのあるものです。

では、実際に記述してみましょう。

**同じファイル** (`ToDoActivity.java`) に次のコードを記述します。

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

今度は、ダイアログ コールバックの処理です。

**同じファイル** (`ToDoActivity.java`) に次のコードを記述します。


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

これで完了です。 コンパイル可能な `ToDoActivity.java` ファイルが完成しました。この時点で、プロジェクト全体も問題なくコンパイルできます。
    


### 手順 16: サンプル アプリを実行する

最後に、構築したアプリを Android Studio と Eclipse の両方で実行します。アプリにサインアップまたはサインインし、サインインしているユーザーのタスクを作成します。サインアウトして、別のユーザーとしてもう一度サインインし、そのユーザーのタスクを作成します。

API でタスクがユーザーごとに保存されたことを確認します。これは、API が、受信したアクセス トークンからユーザーID を抽出したためです。

参照用に、完全なサンプルが[ここに .zip として提供されています](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)。または、GitHub からクローンを作成できます。

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


### Important Information


#### Encryption

ADAL encrypts the tokens and store in SharedPreferences by default. You can look at the StorageHelper class to see the details. Android introduced AndroidKeyStore for 4.3(API18) secure storage of private keys. ADAL uses that for API18 and above. If you want to use ADAL for lower SDK versions, you need to provide secret key at AuthenticationSettings.INSTANCE.setSecretKey

#### Session cookies in Webview

Android webview does not clear session cookies after app is closed. You can handle this with sample code below:
```
CookieSyncManager.createInstance(getApplicationContext()); CookieManager cookieManager = CookieManager.getInstance(); cookieManager.removeSessionCookie(); CookieSyncManager.getInstance().sync(); ``` More about cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=Sept15_HO4-->