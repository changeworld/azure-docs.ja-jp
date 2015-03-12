1. Android Studio のツール バーで右端のアイコン (マウス ポインターをアイコンに合わせることで確認できます) をクリックします。まだインストールしていない場合は、プロジェクトで使用する目的のバージョンの Android SDK を探して開き、**[Google APIs]** をオンにします。

2. 下方向へスクロールして **[Extras]** を展開し、下に示すように **[Google Play Services]** をオンにします。**[Install Packages]** をクリックします。SDK のパスはこの後の手順で使用するので、メモしておいてください。 

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. アプリ ディレクトリで **build.gradle** ファイルを開きます。

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4.  *dependencies*に次の行を追加します。 

   		compile 'com.google.android.gms:play-services-base:6.5.87'

5.  *defaultConfig*で  *minSdkVersion*を「9」に変更します。
 
6. ツール バーで **[Sync Project with Gradle Files]** をクリックします。

7. **AndroidManifest.xml** を開き、このタグを *application* タグに追加します。

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 





<!--HONumber=45--> 
