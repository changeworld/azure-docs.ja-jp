
### <a name="update-manifest-file-to-enable-notifications"></a>通知を有効にマニフェスト ファイルを更新します。
リソースをコピー、アプリ内メッセージングの下、Manifest.xml 間に、`<application>`と`</application>`タグ。

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
間、Manifest.xml で次の XML スニペットを貼り付け、`<application>`と`</application>`タグ。

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

これは、システムとアプリ内通知の両方に表示されるアイコンを定義します。 ただし、システム通知の必須アプリ内通知では省略可能であります。 Android は無効なアイコンとシステム通知を拒否します。

いずれかに存在するアイコンを使用しているかどうかを確認、**ドロウアブル**フォルダー (など``engagement_close.png``)。 **mipmap**フォルダーはサポートされていません。

> [!NOTE]
> 使用しないで、**ランチャー**アイコン。 別の解像度があり、ある通常はサポートされていない mipmap のフォルダーにします。
> 
> 

実際のアプリでは、通知ごとに適切なアイコンを使用する[Android デザイン ガイドライン](http://developer.android.com/design/patterns/notifications.html)です。

> [!TIP]
> 使用するには正しいアイコンの解像度を確認できます[これらの例](https://www.google.com/design/icons)です。
> 下方向にスクロール、**通知**セクションで、アイコンをクリック`PNGS`アイコン ドロウアブル セットをダウンロードします。 アイコンの各バージョンに使用する解像度を持つドロウアブルどのフォルダーを表示できます。
> 
> 

### <a name="enable-your-app-to-receive-gcm-push-notifications"></a>GCM プッシュ通知を受信するアプリを有効にします。
1. 間、Manifest.xml に貼り付け、`<application>`と`</application>`に置き換えた後にタグ、**送信者 ID** Firebase プロジェクト コンソールから取得します。 \N が意図的な関連付け番号のプロジェクトを終了するようにします。
   
        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
2. 次のコードを貼り付けます、Manifest.xml 間、`<application>`と`</application>`タグ。 パッケージ名を置き換える<Your package name>です。
   
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
3. 最後の前に強調表示されているアクセス許可のセットを追加、`<application>`タグ。 置き換える`<Your package name>`アプリケーションの実際のパッケージの名前。
   
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

