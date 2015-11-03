1. Android Studio のツールバーのアイコンをクリックするか、またはメニューから **[Tools]**、**[Android]** -> **[SDK Manager]** を順にクリックすることによって、Android SDK Manager を開きます。まだインストールしていない場合は、プロジェクトで使用する目的のバージョンの Android SDK を探して開き、**[Google APIs]** をオンにします。

2. **[SDK Tools]** タブをクリックします。Google Play Services をまだインストールしていない場合は、次に示すように **[Google Play Services]** をクリックします。次に、**[適用]** をクリックしてインストールします。
 
	SDK のパスは後の手順で使用するので、メモしておいてください。

   	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)


3. アプリ ディレクトリの **build.gradle** ファイルを開きます。

	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)

4. 次の行を *android* の下に追加します。

		useLibrary 'org.apache.http.legacy'

5. 次の行を *dependencies* の下に追加します。

   		compile 'com.google.android.gms:play-services-base:6.5.87'

7. *DefaultConfig* で、*minSdkVersion* を 9 に変更します。
 
8. ツール バーで **[Sync Project with Gradle Files]** をクリックします。

9. **AndroidManifest.xml** を開き、このタグを *application* タグに追加します。

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=Nov15_HO1-->