
* [サーバーへのクライアント SSL 署名 ID のインストール](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW15)手順に従って、前の手順でダウンロードした証明書を .p12 ファイルにエクスポートします。

* Azure クラシック ポータルで、**[Mobile Services]**、使用するアプリ、**[プッシュ]** タブ、**[Apple プッシュ通知の設定]**、**[アップロード]** の順にクリックします。.p12 ファイルをアップロードし、正しい **[モード]** が選択されていることを確認します (生成したクライアント SSL 証明書が開発用か配布用かに応じて、[サンドボックス] または [運用])。 これで、iOS のプッシュ通知と連携するようにモバイル サービスが構成されました。

<!---HONumber=AcomDC_1203_2015-->