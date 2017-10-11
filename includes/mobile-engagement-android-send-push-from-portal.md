### <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>GCM API キーに Mobile Engagement のアクセスを許可します。
Mobile Engagement、代わりにプッシュ通知を送信するには、API キーへのアクセスを許可する必要があります。 これは、Mobile Engagement ポータルに、キーを入力して、構成して行います。

1. Azure クラシック ポータルから、アプリケーションはこのプロジェクトを使用しているし、をクリックすることを確認します、**の関心を引く**下部にあるボタンをクリックします。
   
    ![](./media/mobile-engagement-android-send-push/engage-button.png)
2. をクリックして、**設定** -> **ネイティブ プッシュ通知**GCM キーを入力するセクション。
   
    ![](./media/mobile-engagement-android-send-push/engagement-portal.png)
3. クリックして、**編集**の前にアイコン**API キー**で、 **GCM 設定**セクションの次のように。
   
    ![](./media/mobile-engagement-android-send-push/native-push-settings.png)
4. ポップアップには、前に取得した、GCM サーバー キーを貼り付け、をクリックして**Ok**です。
   
    ![](./media/mobile-engagement-android-send-push/api-key.png)

## <a id="send"></a>アプリに通知を送信します。
アプリにプッシュ通知を送信する単純なプッシュ通知キャンペーンを作成します。

1. 移動し、**到達** タブで、Mobile Engagement ポータル。
2. をクリックして**新しいお知らせ**プッシュ通知のキャンペーンを作成します。
   
    ![](./media/mobile-engagement-android-send-push/new-announcement.png)
3. 次の手順に従って、キャンペーンの最初のフィールドを設定します。
   
    ![](./media/mobile-engagement-android-send-push/campaign-first-params.png)
   
    」を参照します。 キャンペーンの名前を付けます。
   
    b. 選択、**配信の種類**として*システム通知 Simple]-> [*: これは、機能のタイトルとテキストの小さな線を単純な Android プッシュ通知の種類。
   
    c. 選択**配信時刻**として*いつでも*アプリか、アプリは起動するかどうか、通知に使用できるようにします。
   
    d. 通知テキスト型で、**タイトル**になる予定太字にプッシュします。
   
    e. 入力、**メッセージ**
4. スクロール ダウン、および、**コンテンツ**セクションで、**通知のみ**です。
   
    ![](./media/mobile-engagement-android-send-push/campaign-content.png)
5. 完了したら、最も基本的なキャンペーンを可能に設定します。 ここでもう一度下へスクロールし、クリックして、**作成**キャンペーンを保存するボタンをクリックします。
6. 最後のステップ: をクリックして**Activate**プッシュ通知を送信する、キャンペーンをアクティブにします。
   
    ![](./media/mobile-engagement-android-send-push/campaign-activate.png)

