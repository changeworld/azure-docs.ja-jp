

1. Eclipse の上部のツール バーで **[ウィンドウ]** をクリックして Android SDK Manager を開きます。プロジェクトのプロパティで指定された目的のバージョンの Android SDK を探して開き、**[Google APIs]** をオンにします。

2. 下方向へスクロールして **[Extras]** を展開し、下に示すように **[Google Play Services]** をオンにします。**[Install Packages]** をクリックします。SDK のパスはこの後の手順で使用するので、メモしておいてください。Eclipse を再起動します。

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Google Play Services SDK をプロジェクトにインストールします。Eclipse で、**[File]** をクリックし、**[Import]** をクリックします。**[Android]** を選択し、**[Existing Android Code into Workspace]** を選択します。**[Next]** をクリックします。**[Browse]** をクリックし、Android SDK パス (通常は Eclipse を含むフォルダー内の  `adt-bundle-windows-x86_64` フォルダー) に移動します。次に、`\extras\google\google_play_services\libproject` サブフォルダーに移動し、google-play-services-lib フォルダーを選択して、**[OK]** をクリックします。**[Copy projects into workspace]** チェック ボックスをオンにし、**[Finish]** をクリックします。

	![](./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png)

4. 次に、プロジェクトからインポートした Google Play Services SDK ライブラリを参照する必要があります。 

5. **パッケージ エクスプローラー**でプロジェクトを右クリックし、[ *Properties*] をクリックします。
 
6. [Properties] ウィンドウで、左側の [Android] を選択します。

	![](./media/mobile-services-android-get-started-push/mobile-google-set-project-properties.png)


7. **[Project Build Target]** の下で、適切な SDK レベルとして  `Google APIs x86` (or `Google APIs` が選択されていることを確認します。

 
8. **[Library]** セクションで **[Add]** をクリックし、[Google Play Services] プロジェクト (*google-play-services-lib*) をオンにして **OK** をクリックします。

9. **[Apply]** をクリックし、**[OK]** をクリックします。



<!--HONumber=42-->
