このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上の Android Studio プロジェクトのディレクトリに展開します。

2. Android Studio を起動します。プロジェクトを使用していて、それが表示された場合は、そのプロジェクトを閉じます ([ファイル] => [プロジェクトを閉じる])。

3. **[Open an existing Android Studio project (既存の Android Studio プロジェクトを開く)]** を選択し、プロジェクトの場所を参照して、**[OK]** をクリックします。 

 	![][14]

4. 左側の **[Project Explorer]** ウィンドウで、 *Project* タブが選択されていることを確認し、**[app]**、**[src]**、**[java]** の順に開いて、**[ToDoactivity]** をダブルクリックします。

   	![][8]


4. SDK のバージョン 2.0 をダウンロードした場合は、Mobile Services の URL とキーを含むようにコードを更新する必要があります。
	- 	**TodoActivity.java** の **OnCreate** メソッド内で、Mobile Services  クライアントをインスタンス化するコードを見つけます。前の図にこのコードが示されています。
	- 	"MobileServiceUrl" を、Mobile Services の実際の URL に置き換えます。
	- 	"AppKey" を、Mobile Services のキーに置き換えます。
	- 	詳細については、チュートリアル「<a href="http://azure.microsoft.com/documentation/articles/mobile-services-android-get-started-data/">既存のアプリケーションへの  Mobile Services の追加</a>」をご覧ください。 

5. **[Run]** メニューの **[Run]** をクリックして、Android エミュレーター内でプロジェクトを開始します。

	> [AZURE.IMPORTANT]プロジェクトを Android エミュレーターで実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成と管理するには、AVD Manager を使用します。

6. アプリケーションで、意味のあるテキスト (たとえば、_チュートリアルの完了_) を入力し、**[Add]** をクリックします。

   	![][10]

   	これで、Azure でホストされている新しい Mobile Services に POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目が Mobile Services によって返され、データが一覧に表示されます。

	> [AZURE.NOTE] Mobile Services にアクセスして ToDoActivity.java ファイルにあるデータを照会と挿入するコードを確認できます。

6. 管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

   	![][11]

   	これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

   	![][12]


<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/Android-Studio-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/android-studio-import-project.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[データの使用]:/documentation/articles/mobile-services-android-get-started-data/
[認証の使用]:/documentation/articles/mobile-services-android-get-started-users/
[プッシュ通知の使用]:/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services  Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[管理ポータル]: https://manage.windowsazure.com/
<!--HONumber=47-->
