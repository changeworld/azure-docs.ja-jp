
## <a id="register"></a>アプリをプッシュ通知に登録する

* [アプリのアプリ ID を登録します](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW991)。アプリを登録するときに、**[App Services]** の **[プッシュ通知]** チェック ボックスをオンにします (このチェックボックスはオプションです)。

> [AZURE.NOTE]ワイルドカード アプリ ID ではなく明確なアプリ ID を作成し、**バンドル ID** では、Xcode クイックスタート プロジェクト内に含まれる **バンドル ID** を使用します。アプリ ID を登録するときに、**[App Services]** の **[プッシュ通知]** オプションを有効にすることも重要です。このチェックボックスをオンにしない場合、プッシュ通知は機能しません。

* 次に、[アプリのプッシュ通知を有効にします](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW6)。"開発用" または "配布用" の SSL 証明書を作成できます (後で Mobile Services ポータルの中で、該当するオプション ([サンドボックス] または [運用] を選択してください)。

* 次に、[アプリ ID でプッシュ通知が有効であることを確認します](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW8)。

* 最後に、[Xcode クイックスタート プロジェクト内のプロビジョニング プロファイルを更新](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW10)した後、[プロビジョニング プロファイルがプッシュ通知を有効にするように作成されたか登録されたことを確認します](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW12)。

<!---HONumber=62-->