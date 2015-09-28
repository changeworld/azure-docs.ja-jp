<properties pageTitle="Azure AD B2C プレビュー: Android アプリケーションからの Web API の呼び出し | Microsoft Azure" description="この記事では、OAuth 2.0 ベアラー トークンを使用して node.js Web API を呼び出す Android の "To-Do List" アプリを作成する方法を示します。Android アプリと Web API は、どちらも Azure AD B2C を使用してユーザー ID を管理し、ユーザーを認証します。" services="active-directory-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="brandwe"/>

# Azure AD B2C プレビュー: Android アプリケーションからの Web API の呼び出し

Azure AD B2C を使用すると、強力なセルフサービス方式の ID 管理機能をわずかな手順で Android アプリと Web API に追加できます。この記事では、OAuth 2.0 ベアラー トークンを使用して node.js Web API を呼び出す Android の "To-Do List" アプリを作成する方法を示します。Android アプリと Web API は、どちらも Azure AD B2C を使用してユーザー ID を管理し、ユーザーを認証します。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]
	このクイック スタートを完全に機能させるためには、前提条件として、Azure AD で B2C によって保護されている Web API が存在する必要があります。
	すぐに使用できる .Net と node.js 用の Web API が既にビルドされています。このチュートリアルでは、node.js Web API のサンプルが構成されていると見なします。「[Node.js 用 Azure AD B2C Web API チュートリアル](active-directory-b2c-devquickstarts-api-node.md)」を参照してください。

> [AZURE.NOTE]
	この記事では、サインイン、サインアップ、およびプロファイル管理を Azure AD B2C で実装する方法については説明しません。ユーザーが既に認証された後での Web API の呼び出しに焦点を合わせています。
    まだ行っていない場合は、先に「[.NET Web アプリ入門チュートリアル](active-directory-b2c-devquickstarts-web-dotnet.md)」で Azure AD B2C の基本を学習してください。

保護されたリソースにアクセスする必要がある Android クライアントに対しては、Azure AD は Active Directory 認証ライブラリ (ADAL) を提供します。ADAL の唯一の目的は、アプリがアクセス トークンを容易に取得できるようにすることです。それがどれほど簡単であるかを示すために、ここで、次を実行する Android To-Do List アプリケーションを作成します。

-	[OAuth 2.0 認証プロトコル](https://msdn.microsoft.com/library/azure/dn645545.aspx)を使用して To-Do List API を呼び出すためのアクセス トークンを取得します。
-	ユーザーの To-Do List を取得します。
-	ユーザーのサインアウト処理を行います。



### 手順 1: Azure AD B2C ディレクトリの取得

Azure AD B2C を使用するには、ディレクトリまたはテナントを作成しておく必要があります。ディレクトリは、ユーザー、アプリ、グループなどのすべてを格納するコンテナーです。まだディレクトリを作成していない場合は、先に進む前に「[Azure AD B2C プレビュー: Azure AD B2C ディレクトリの作成方法](active-directory-b2c-get-started.md)」を参照してください。

### 手順 2: アプリケーションの作成

次に、B2C ディレクトリ内にアプリを作成する必要があります。これによって、 アプリと安全に通信するために必要ないくつかの情報が Azure AD に提供されます。ここでは、アプリと Web API の両方が単一の**アプリケーション ID**で表されます。これは、アプリと Web API が 1 つの論理アプリを構成するためです。アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従います。このとき、

- アプリケーションに **Web アプリまたは Web API** を含めます。
- `http://localhost:3000/auth/openid/return` を**応答 URL** として入力します。これはこのサンプル コードで使用する既定の URL です。
- アプリケーション用の**アプリケーション シークレット**を作成し、それをメモしておきます。このプロジェクトはすぐに必要になります。
- アプリに割り当てられた**アプリケーション ID** をメモしておきます。こちらもすぐに必要になります。

### 手順 3: ポリシーを作成する

Azure AD B2C では、すべてのユーザー エクスペリエンスが[**ポリシー**](active-directory-b2c-reference-policies.md)によって定義されます。このアプリには、3 つの ID エクスペリエンス (サインアップ、サインイン、および Facebook でのサインイン) が含まれます。[ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。3 つのポリシーを作成するときは、以下の点に注意してください。

- サインアップ ポリシーで、**[表示名]** と他のいくつかのサインアップ属性を選択します。
- すべてのポリシーで、**表示名**と**オブジェクト ID** のアプリケーション クレームを選択します。その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの**名前**をメモしておきます。名前には、プレフィックス `b2c_1_` が付加されます。これらのポリシー名はすぐに必要になります。 

3 つのポリシーの作成が正常に完了したら、いつでもアプリをビルドできます。

この記事では、作成したポリシーの使用方法については説明しません。ポリシーが Azure AD B2C でどのように機能するかを学習する場合は、「[Azure AD B2C プレビュー: .NET Web アプリをビルドする](active-directory-b2c-devquickstarts-web-dotnet.md)」から始めてください。

### 手順 4: コードをダウンロードする

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android) で管理されています。この後サンプルを構築するために、[スケルトン プロジェクトを .zip 形式でダウンロードする](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip)か、次のようにスケルトンのクローンを作成できます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**スケルトンをダウンロードすることは、このチュートリアルを完了するために必要な作業です。** 完全に機能するアプリケーションを Android 上に実装するのは複雑な作業であるため、**スケルトン**には、この後のチュートリアルを完了した後で実行される UX コードが含まれています。これは、開発者の時間を短縮するための手段です。UX コードは B2C を Android アプリケーションに追加するトピックと深い関わりはありません。

また、完成済みのアプリも、[.zip 形式](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)または同じリポジトリの `complete` ブランチから利用できます。


Maven を使用して構築するために、最上位レベルで pom.xml を使用することができます。


  * [「Setting up maven environment for Android」の「Prerequests」セクション](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)の手順に従います。
  * SDK 21 でエミュレーターをセットアップします。
  * リポジトリを複製したルート フォルダーに移動します。
  * コマンド mvn clean install を実行します。
  * cd samples\\hello を実行して、ディレクトリをクイック スタート サンプルに変更します。
  * コマンド mvn android:deploy android:run を実行します。
  * アプリが起動します。
  * テスト ユーザー資格情報を入力して、試行します。

aar パッケージに加え、jar パッケージも送信されます。

### 手順 5. Android ADAL をダウンロードして Eclipse ワークスペースに追加する

このライブラリを Android プロジェクトで簡単に使用できるようにするために、複数のオプションが用意されています。

* ソース コードを使用して、このライブラリを Eclipse にインポートし、アプリケーションにリンクします。
* Android Studio を使用する場合は、*aar* パッケージ形式を使用して、バイナリを参照します。

####オプション 1. Git 経由のソース

SDK のソース コードを Git 経由で取得するには、次のように入力します。

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"

####オプション 2. Gradle 経由のバイナリ

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
    compile('com.microsoft.aad:adal:2.0-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0-alpha
}
```

####オプション 3. Maven 経由の aar

Eclipse で m2e プラグインを使用する場合は、pom.xml ファイルで次のように依存関係を指定できます。

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0-alpha</version>
    <type>aar</type>
</dependency>
```


####オプション 5. libs フォルダー内の jar パッケージ
Maven リポジトリから jar ファイルを取得して、プロジェクトの *libs* フォルダーにドロップできます。jar パッケージには含まれていないので、必要なリソースもプロジェクトにコピーする必要があります。


### 手順 6. プロジェクトに Android ADAL への参照を追加する


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

### 手順 7: Android 用 ADAL を呼び出すコードを追加します。

それではメイン アクティビティを作成し、それを「`ToDoActivity`」と呼ぶことにしましょう。

アクティビティを初期化し、UI を制御するボタンをいくつか追加する必要があります。この作業も非常に単純であり、Android コードの記述経験があれば馴染みのあるものです。

```
public class ToDoActivity extends Activity {

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

    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();
```

次に、同じクラスで (括弧はまだ閉じないでください)、UI のボタンを追加します。Facebook のサインインと電子メールのサインインのためのボタンを追加します。`constants.java` ファイルに定義されているポリシーが使用されます。

```
        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

    ```
Create an instance of AuthenticationContext at your main Activity:

    ```Java
 		 mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false, InMemoryCacheStore.getInstance());
            mAuthContext.getCache().removeAll();

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
    ```
  * NOTE: mContext is a field in your activity
 
 Let's also define our Result method `onActivityResult` that will be called when we have a result from the callback we'll write later:
 
 ```
 @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
 ```

Next, let's define our callback:

    ```Java
    mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY, Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
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

                            if (result != null && !result.getAccessToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
        }
        Toast.makeText(getApplicationContext(), TAG + "done", Toast.LENGTH_SHORT).show();
    }
        ```
        
 You may notice that this relies on methods we haven't written yet, such as `updateLoggedInUser()` and `getTasks()`. We'll write those below.	You can safely ignore the errors in Android Studio for now.

Explanation of the parameters:

  * ***SCOPES*** is required and is the scopes you are trying to reqeust access for. For the B2C preview this is the same as the Clientid but will change in the future.
  * ***POLICY*** is the policy for while you wish to authenticate the user. 
  * ***CLIENT_ID*** is required and comes from the AzureAD Portal.
  * You can setup redirectUri as your packagename. It is not required to be provided for the acquireToken call.
  * ***USER_HINT*** is the way we look up if the user is already in the cache and prompt the user if they are not found or the access token is invalid.
  * ***PROMPT*** helps to ask for credentials to skip cache and cookie.
  * ***Callback*** will be called after authorization code is exchanged for a token.

  The Callback will have an object of AuthenticationResult which has accesstoken, date expired, and idtoken info.

> [AZURE.NOTE]	Microsoft Intune's Company portal app provides the broker component and may be installed on the user's device. Developers should be prepared to allow for it's use as it provides SSO across all applications on the device. ADAL for Android will use the broker account if there is one user account created in the Authenticator. To use the broker, the developer needs to register a special redirectUri for broker usage. RedirectUri is in the format of msauth://packagename/Base64UrlencodedSignature. You can get your redirecturi for your app using the script `brokerRedirectPrint.ps1` or use API call `mContext.getBrokerRedirectUri()`. The signature is related to your signing certificates from the Google Play store.

 A Developer can skip the broker user with:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.WARNING] In order to reduce the complexity of this B2C Quickstart, we have opted in our sample to skip the broker. However, this is not recommended.

Next, let's create some helper methods that will get the token alone during our authentication calls to the Task API:

```
private void getToken(final AuthenticationCallback callback) {

        // one of the acquireToken overloads
        mAuthContext.acquireToken(onnstants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY,
                Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                "nux=1&" + Constants.EXTRA_QP, callback);
    }

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {
        Constants.CURRENT_RESULT = newToken;
    }
    
```

Finally, Your app manifest should have permissions to use AccountManager accounts: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


### Step 8. Call the Task API

Now that we have our Activity wired up and ready to go to do the heavy lifting of grabbing tokens, let's write our API to access the task server.

Our `getTasks` provides an array that represents the tasks in our server 
Our `addTask` and `deleteTask` do the subsequent action and return TRUE or FALSE if it was successful.

Let's write our `getTask` first:

```
private void getTasks() { if (Constants.CURRENT\_RESULT == null || Constants.CURRENT\_RESULT.getAccessToken().isEmpty()) return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(Constants.CURRENT_RESULT.getAccessToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }
 ```
 
 最初の実行でテーブルを初期化するメソッドも記述しましょう。
 
 ```
     private void initAppTables() {
        try {
            // Get the Mobile Service Table instance to use
//            mToDoTable = mClient.getTable(WorkItem.class);
//            mToDoTable.TABvLES_URL = "/api/";
            //mTextNewToDo = (EditText)findViewById(R.id.listViewToDo);

            // Create an adapter to bind the items with the view
            //mAdapter = new WorkItemAdapter(ToDoActivity.this, R.layout.listViewToDo);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 You'll see that this code requires some additional methods to do it's work. Let's write those now.
 
 ### Create endpoint URL generator
 
 We need to generate the endpoint URL that we'll be connecting to. Let's do that in the same class file:
 
 
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


Note that we add the access token to the request in the following code:

### Step 9: Let's write some UX methods

Android requires us to handle some callbacks in order to operate the app. These are `createAndShowDialog` and `onResume()`. This is pretty simple and very familiar if you've written Android code before. 

Let's write those now:

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

And now manage our dialog callbacks:


```
/** * Creates a dialog and shows it * * @param exception The exception to show in the dialog * @param title The dialog title */ private void createAndShowDialog(Exception exception, String title) { createAndShowDialog(exception.toString(), title); }

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
    


### 手順 10: サンプル アプリを実行する

最後に、構築したアプリを Android Studio と Eclipse の両方で実行します。アプリにサインアップまたはサインインし、サインインしているユーザーのタスクを作成します。サインアウトして、別のユーザーとしてもう一度サインインし、そのユーザーのタスクを作成します。

API は、受け取ったアクセス トークンからユーザー ID を抽出するため、ユーザーごとにタスクが API にどのように保存されるかを確認してください。

参照用に、完成したサンプルが[ここに .zip として用意されています](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip)。あるいは、GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


<!--

### Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

### Important Information


#### Encryption

ADAL encrypts the tokens and store in SharedPreferences by default. You can look at the StorageHelper class to see the details. Android introduced AndroidKeyStore for 4.3(API18) secure storage of private keys. ADAL uses that for API18 and above. If you want to use ADAL for lower SDK versions, you need to provide secret key at AuthenticationSettings.INSTANCE.setSecretKey

#### Session cookies in Webview

Android webview does not clear session cookies after app is closed. You can handle this with sample code below:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
More about cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=Sept15_HO3-->