1. Android Studio のツールバーのアイコンをクリックするか、またはメニューから **[Tools]**、**[Android]** -> **[SDK Manager]** を順にクリックすることによって、Android SDK Manager を開きます。まだインストールしていない場合は、プロジェクトで使用する目的のバージョンの Android SDK を探して開き、**[Google APIs]** をオンにします。

2. **[ファイル]** 、**[プロジェクト構造]** に移動します。**[通知]** の下の [プッシュ通知] を有効にします。

3. **AndroidManifest.xml** を開き、このタグを *application* タグに追加します。

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_1203_2015-->