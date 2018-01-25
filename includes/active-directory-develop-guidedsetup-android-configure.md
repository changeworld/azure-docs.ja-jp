
## <a name="register-your-application"></a>アプリケーションの登録
次の 2 つのセクションで説明する方法のいずれかを使用して、アプリケーションを登録できます。

### <a name="option-1-express-mode"></a>オプション 1: 簡易モード
次の手順を実行すると、アプリケーションをすばやく登録できます。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)に移動します。
2.  **[アプリケーション名]** ボックスに、アプリケーションの名前を入力します。

3. **[Guided Setup]\(ガイド付きセットアップ\)** チェック ボックスがオンになっていることを確認し、**[作成]** を選択します。

4. 手順に従ってアプリケーション ID を取得し、それをコードに貼り付けます。

### <a name="option-2-advanced-mode"></a>オプション 2: 詳細モード
アプリケーションを登録し、ソリューションにアプリケーション登録情報を追加するには、次の手順を実行します。
1. アプリケーションをまだ登録していない場合は、[Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動します。
2. **[アプリケーション名]** ボックスに、アプリケーションの名前を入力します。 

3. **[Guided Setup]\(ガイド付きセットアップ\)** チェック ボックスがオフになっていることを確認し、**[作成]** を選択します。

4. **[プラットフォームの追加]**、**[ネイティブ アプリケーション]**、**[保存]** の順に選択します。

5. **[app]** > **[java]** > **[{host}.{namespace}]** で、`MainActivity` を開きます。 

6.  次の行の *[Enter the application Id here]* を、先ほど登録したアプリケーション ID に置き換えます。

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. **[app]** > **[manifests]** で、*AndroidManifest.xml* ファイルを開きます。

8. `manifest\application` ノードで、次のアクティビティを追加します。 これにより、`BrowserTabActivity` アクティビティが登録され、OS は認証の完了後にアプリケーションを再開することが可能になります。

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
<!-- Workaround for Docs conversion bug -->
9. `BrowserTabActivity` ノードで、`[Enter the application Id here]` をアプリケーション ID に置き換えます。
