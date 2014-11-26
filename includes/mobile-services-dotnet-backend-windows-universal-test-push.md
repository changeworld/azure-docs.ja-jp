1.  Windows ストア プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックしてから、F5 キーを押して Windows ストア アプリを実行します。

    アプリが起動すると、デバイスがプッシュ通知に登録されます。

2.  Windows ストア アプリを停止し、Windows Phone ストア アプリを実行する前の手順を繰り返します。

    この時点で、両方のデバイスがプッシュ通知を受信するように登録されています。

3.  再度 Windows ストア アプリを実行し、**[TodoItem の挿入]** にテキストを入力してから、**[保存]** をクリックします。

    ![][0]

    挿入が完了すると、Windows ストアと Windows Phone アプリの両方が WNS からのプッシュ通知を受信します。

    ![][1]

    通知は、アプリが実行していなくても Windows Phone に表示されます。

    ![][2]

  [0]: ./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push1.png
  [1]: ./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push2.png
  [2]: ./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push5-wp8.png
