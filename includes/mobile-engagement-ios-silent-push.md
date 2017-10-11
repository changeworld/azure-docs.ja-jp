> [!IMPORTANT]
> Mobile Engagement からプッシュ通知を受信するには、有効にする必要があります`Silent Remote Notifications`アプリケーションでします。 Info.plist ファイルで UIBackgroundModes 配列にリモート通知の値を追加する必要があります。
> 
> 

1. 開いている`info.plist`プロジェクト内のファイル
2. 一覧の最上位の項目を右クリックして (`Information Property List`) し、新しい行を追加
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)
3. 新しい行に入力してください。`Required background modes`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)
4. 行を展開する左側の矢印をクリックします。
5. 項目の 0 に、次の値を追加します。`App downloads content in response to push notifications`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)
6. 変更を加えた後 info.plist XML は、次のキーと値を含める必要があります。
   
        <key>UIBackgroundModes</key>
        <array>
        <string>remote-notification</string>
        </array>
7. 使用している場合**Xcode 7 +**と**iOS 9 以降**:
   
   * 有効にする**プッシュ通知**ターゲットに >、ターゲット名 > 機能します。

