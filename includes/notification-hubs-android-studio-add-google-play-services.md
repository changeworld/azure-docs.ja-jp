1. Android Studio のツールバーのアイコンをクリックするかをクリックして、Android SDK Manager を開いて**ツール** -> **Android** -> **SDK Manager**メニュー。 プロジェクト内で使用される Android SDK のターゲット バージョンを検索をクリックして開く**パッケージの詳細を表示する**を選択し、 **Google APIs**がインストールされていない場合は、します。
2. クリックして、 **SDK ツール**タブです。 Google Play サービスが既にインストールしていない場合は、クリックして**Google Play サービス**次のようにします。 をクリックして**適用**をインストールします。 
   
    後の手順で使用するため、SDK のパスに注意してください。 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. 開く、 **build.gradle**アプリ ディレクトリ内のファイルです。
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. この行を追加*の依存関係*: 
   
           compile 'com.google.android.gms:play-services-gcm:9.2.0'
5. クリックして、 **Gradle ファイルと同期プロジェクト**ツール バーのアイコン。
6. 開いている**AndroidManifest.xml**このタグを追加し、*アプリケーション*タグ。
   
        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />

