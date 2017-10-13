
### <a name="update-manifest-file-to-enable-notifications"></a>マニフェスト ファイルを更新して通知を有効にする
Manifest.xml の `<application>` タグと `</application>` タグの間に、次のアプリ内メッセージングのリソースをコピーします。

        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
        </receiver>

### <a name="specify-an-icon-for-notifications"></a>通知のアイコンを指定します。
次の XML スニペットを Manifest.xml の `<application>` タグと `</application>` タグの間に貼り付けます。

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

これにより、システムおよびアプリ内通知の両方に表示されるアイコンが定義されます。 これはアプリ内通知では任意ですが、システム通知では必須です。 Android では無効なアイコンが設定されたシステム通知は拒否されます。

いずれかの**描画可能な**フォルダーにあるアイコン (``engagement_close.png`` など) を使用してください。 **mipmap** フォルダーはサポートされていません。

> [!NOTE]
> **ランチャー** アイコンは使用しないでください。 このアイコンの解像度は異なっており、通常はサポート対象外の mipmap フォルダー内にあります。
> 
> 

実際のアプリでは、 [Android の設計ガイドライン](http://developer.android.com/design/patterns/notifications.html)に従って、通知に適したアイコンを使用します。

> [!TIP]
> 適切な解像度のアイコンを使用するには、 [これらの例](https://www.google.com/design/icons)を参考にしてください。
> **[Notification]** セクションまでスクロールして 1 つのアイコンをクリックし、`PNGS` をクリックして描画可能なアイコンのセットをダウンロードします。 アイコンのバージョンごとに、使用する描画可能なフォルダーと解像度を確認できます。
> 
> 

### <a name="enable-your-app-to-receive-gcm-push-notifications"></a>GCM のプッシュ通知を受信するようにアプリを設定する
1. Firebase プロジェクト コンソールから取得した **Sender ID** を置き換えた後、Manifest.xml の `<application>` タグと `</application>` タグの間に次を貼り付けます。 \n は意図的に付けられています。プロジェクト番号の末尾には必ずこれを付けてください。
   
        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
2. 次のコードを、Manifest.xml の `<application>` タグと `</application>` タグの間に貼り付けます。 <Your package name>をパッケージ名に置き換えます。
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
        android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<Your package name>" />
            </intent-filter>
        </receiver>
3. 強調表示された最新のアクセス権限セットを、 `<application>` タグの前に追加します。 `<Your package name>` をアプリケーションの実際のパッケージ名で置き換えます。
   
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

