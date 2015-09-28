> [AZURE.IMPORTANT]Mobile Engagement からプッシュ通知を受信するには、アプリケーションで `Silent Remote Notifications` を有効にする必要がありますInfo.plist ファイルの UIBackgroundModes 配列に、リモート通知の値を追加する必要があります。

1. プロジェクト内の `info.plist` ファイルを開きます
2. 一覧 (`Information Property List`) の一番上の項目を右クリックして、新しい行を追加します

	![][1]

3. 新しい行に `Required background modes` を入力します

	![][2]

4. 行を展開するには、左向きの矢印をクリックします
5. 項目 0 に、次の値を追加します `App downloads content in response to push notifications`

	![][3]

変更を加えた後、 info.plist XML には次のようなキーと値を含める必要があります。

    <key>UIBackgroundModes</key>
        <array>
            <string>remote-notification</string>
        </array>
    ...
    
6. Xcode 7 と iOS 9 を使用している場合、次の追加手順を実行する必要があります。 • [ターゲット] > [ビルド設定] で **[ビットコードの有効化]** を **[いいえ]** に設定します (上部のバーで [すべて] を選択してください)。 • [ターゲット] > [ターゲット名] > [機能] で **[プッシュ通知]** を有効にします。

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png
[2]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png
[3]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png

<!---HONumber=Sept15_HO3-->