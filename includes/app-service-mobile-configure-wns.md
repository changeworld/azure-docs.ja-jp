
1. [Azure Portal](https://portal.azure.com/) で **[すべて参照]** > **[App Services]** の順に選択します。 次に、Mobile Apps バックエンドを選択します。 **[設定]** で **[App Service Push]\(App Service プッシュ\)** を選択します。 次に、通知ハブの名前を選択します。
2. **[Windows (WNS)]** に移動します。 次に、Live サービス サイトから取得した**セキュリティ キー** (クライアント シークレット) と**パッケージ SID** を入力します。 次に、**[保存]** を選択します。

    ![ポータルで WNS キーを設定する](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

バックエンドは WNS を使用してプッシュ通知を送信するよう構成されました。
