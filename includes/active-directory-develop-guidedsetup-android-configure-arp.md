## <a name="add-the-applications-registration-information-to-your-app"></a>アプリへのアプリケーション登録情報の追加

この手順では、アプリケーション ID をプロジェクトに追加する必要があります。

1.  `MainActivity` を開きます ([`app`]  >  [`java`]  >  [*`{host}.{namespace}`*] の順)。
2.  `final static String CLIENT_ID` で始まる行を以下に置き換えます。
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. [`app`]  >  [`manifests`] \(マニフェスト)  >  [`AndroidManifest.xml`] の順に開きます。
4. ノード `manifest\application` に次のアクティビティを追加します。 これにより `BrowserTabActivity` が登録され、OS は認証の完了後、アプリケーションを再開することが可能になります。

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

