1. Android Studio のツールバーのアイコンをクリックするか、またはメニューから **[Tools]**、**[Android]** -> **[SDK Manager]** を順にクリックすることによって、Android SDK Manager を開きます。**スタンドアロン SDK Manager の起動**リンクをクリックします。
2. プロジェクトで使用するバージョンの Android SDK を見つけて開きます。**Google API** がインストールされていない場合、**[Install packages...]** をクリックします。
3. Android Studio で **[File]**、**[Project Structure]** の順に選択します。**[Notifications]** をクリックし、**[Google Cloud Messaging]** を選択して **[OK]** をクリックします。

<!--
3. Open **AndroidManifest.xml** and add this tag to the *application* tag.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
-->

<!---HONumber=AcomDC_0309_2016-->