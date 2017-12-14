
## <a name="create-an-application-express"></a>アプリケーションの作成 (簡易)
次の手順に従って *Microsoft アプリケーション登録ポータル*でアプリケーションを登録する必要があります。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)でアプリケーションを登録します。
2.  アプリケーションの名前とお使いのメール アドレスを入力します
3.  ガイド付きセットアップのオプションがオンになっていることを確認します。
4.  手順に従ってアプリケーション ID を取得し、それをコードに貼り付けます。

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>ソリューションへのアプリケーション登録情報の追加 (上級)
次の手順に従って *Microsoft アプリケーション登録ポータル*でアプリケーションを登録する必要があります。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します
2. アプリケーションの名前とお使いのメール アドレスを入力します 
3. ガイド付きセットアップのオプションがオフになっていることを確認します。
4. [`Add Platform`] \(プラットフォームの追加) をクリックしてから [`Native Application`] \(ネイティブ アプリケーション) を選択し、[保存] をクリックします。
5.  `MainActivity` を開きます ([`app`]  >  [`java`]  >  [*`{host}.{namespace}`*] の順)。
6.  "`final static String CLIENT_ID`" で始まる行の *[Enter the application Id here]* (アプリケーション ID を入力してください) を、登録したアプリケーション ID に置き換えます。

```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
`AndroidManifest.xml` を開きます ([`app`]  >  [`manifests`] の順)。以下のアクティビティをノード `manifest\application` に追加します。 これにより `BrowserTabActivity` が登録され、OS は認証の完了後、アプリケーションを再開することが可能になります。
</li>
</ol>

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
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
<ol start="8">
<li>
`BrowserTabActivity` で、`[Enter the application Id here]` (アプリケーション ID を入力してください) をアプリケーション ID に置き換えます。
</li>
</ol>
