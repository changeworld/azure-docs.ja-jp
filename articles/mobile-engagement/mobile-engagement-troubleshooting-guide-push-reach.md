<properties 
   pageTitle="Azure モバイル エンゲージメント関連のトラブルシューティング ガイド - プッシュ/リーチ" 
   description="Azure モバイル エンゲージメントでのユーザーとの対話と通知の問題のトラブルシューティング" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>


# プッシュとリーチに関する問題のトラブルシューティング ガイド

次に説明するのは、Azure モバイル エンゲージメントが、ユーザーに情報を送信する方法に関して発生する可能性のある問題です。
 
## プッシュ エラー

### 問題
- プッシュが機能しません (アプリ内、アプリ外、または両方で)。

### 原因
- Azure モバイル エンゲージメント、リーチ、Azure モバイル エンゲージメントの他の高度な機能が正しく統合されていない、または新しい OS やデバイス プラットフォームに関する既知の問題を解決するために SDK をアップグレードする必要がある、というプッシュ エラーが何度も表示されます。
- In App プッシュまたは Out of App プッシュに問題があるかどうかを判断するために、In App プッシュのみと Out of App プッシュのみをテストします。
- トラブルシューティングの手順として、UI と API の両方からテストし、両方の場所に含まれている追加のエラー情報を確認します。
- Azure モバイル エンゲージメントとリーチが両方とも SDK に統合されていない場合、Out of App プッシュは機能しません。
- 証明書が有効でない場合や、本番用と開発用の証明書を正しく使用していない場合、Out of App プッシュは機能しません (iOS のみ)。(* * 注: * *同じデバイスに本番用と開発用のアプリケーションのバージョンをインストールしている場合は、証明書に関連付けられたセキュリティ トークンは Apple によって無効にされる場合があるため、"Out of app"通知は、 iOS に配信されない場合があります。この問題を解決するには、本番用と開発用のアプリケーションのバージョンをアンインストールして、デバイスに 1 つのバージョンのみを再インストールします)。
- Out of App プッシュ数は、プラットフォームによって異なる方法で処理されます (デバイス上でネイティブ プッシュが無効になっている場合、iOS は Android よりも少ない情報を表示します。API は、プッシュ統計上 UI よりも多くの情報を提供できます)。
- Out of App プッシュは、OS レベルで顧客がブロックできます (iOS と Android)。
- 正しく統合されていない場合、Out of App プッシュが Azure モバイル エンゲージメント UI で無効になっていれば表示されますが、API からのエラー メッセージの表示なしに失敗することがあります。
- Azure モバイル エンゲージメントとリーチが両方とも SDK に統合されていない場合、In App プッシュは機能しません。
- Azure モバイル エンゲージメントと特定のサーバーが SDK に統合されていない場合、GCM と ADM プッシュは機能しません (Android のみ)。
- In App プッシュと Out of App プッシュは、個別にテストを行って、プッシュまたはリーチの問題があるかどうかを判断する必要があります。
- In App プッシュは、アプリが受信できるように開いている必要があります。
- In App プッシュは、多くの場合、オプトインまたはオプトアウトのアプリ情報タグを使用してフィルター処理されるように設定されます。
- リーチのカスタム カテゴリを使用してアプリ内通知を表示する場合は、通知の正しいライフサイクルに従う必要があります。そうしないと、ユーザーが通知を閉じたときに、通知が消去されないことがあります。
- 終了日のないキャンペーンを開始し、デバイスでアプリ内通知を受信したが表示されない場合は、手動でキャンペーンを終了させても、ユーザーが次回アプリにログインしたときにその通知を受信します。
- プッシュ API の問題については、リーチ API ではなくプッシュ API の使用で間違いないこと (リーチ API が一般的に使用されるため)、「payload」パラメーターと「notifier」パラメーターを混同していないことを確認します。
- 問題の原因になり得るネットワーク接続を排除するには、WiFi と 3G を使用して接続された両方のデバイスでプッシュ キャンペーンをテストします。

### 関連項目

- [トラブルシューティング ガイド - SDK][Link 25]、[トラブルシューティング ガイド - プッシュ][Link 23]、[SDK ドキュメント - iOS - Apple プッシュ通知用にアプリケーションを準備する方法][Link 5]
 
## プッシュ テスト

### 問題
- プッシュは、デバイス ID に基づいて特定のデバイスに送信されます。

### 原因

- テスト デバイスは、プラットフォームによって異なる方法でセットアップされますが、テスト デバイス上のアプリでイベントを発生させ、ポータル内でデバイス ID を検索することで、すべてのプラットフォームでデバイス ID を検索する機能があります。
- テスト デバイスは、IDFV と IDFA で動作が異なります (iOS のみ)。

### 関連項目

- [UI ドキュメント - Reach][Link 17]
 
## プッシュ カスタマイズ

### 問題
- 高度なプッシュ コンテンツ項目が機能しません (バッジ、着信音、バイブレーション、画像など)。
- プッシュからのリンクが機能しません (Out of App、In App、Web サイト、アプリ内の場所へのリンク)。
- プッシュの統計情報は、期待した数の人にプッシュが送信されなかったことを示しています (多すぎるか不十分です)。
- プッシュが 2 回複製され受信されました。
- Azure モバイル エンゲージメント プッシュのテスト デバイスを登録できません (自分の本番用または開発用アプリで)。

### 原因

- アプリの特定の場所にリンクするには「カテゴリ」が必要です (Android のみ)。
- プッシュ通知をクリックした後に、別の場所にユーザーをリダイレクトするディープ リンクのスキームは、モバイル エンゲージメントから直接ではなく、アプリケーションとデバイス OS内で作成し、管理する必要があります。(* * 注: * * Out of app 通知は、Android とリンクするため、 iOS ではアプリ内の場所にはリンクできません)。
- 外部の画像サーバーは、大きい画像のプッシュが機能するように、HTTP "GET"と "HEAD" を使用できる必要があります (Android のみ)。
- コードを使用して、Azure モバイル エンゲージメント エージェントをキーボードを開いたときに無効にし、閉じたときにアクティブできるため、キーボードは通知の表示には影響しません (iOS のみ)。
- 一部の項目は、テスト シミュレーションでは機能せず、実際のキャンペーンでのみ機能します (バッジ、着信音、バイブレーション、画像など)。
- サーバー側のデータは、ボタンを使用してプッシュをテストする場合に記録されません。実際のプッシュ キャンペーンの場合のみデータがログに記録されます。
- 問題の特定に役立てるため、テスト、シミュレーション、実際のキャンペーンを使用してトラブルシューティングしますが、これはそれぞれの機能が少し異なるためです。
- "アプリ内"  と "いつでも" が実行するようにスケジュールされているキャンペーンの時間の長さは、キャンペーンは実行中には、 "アプリ内" のユーザー (さらに "アプリ外" の通知を受信するようにデバイス設定しているユーザー) にのみ配信されるため、配信回数に影響する可能性があります。
- Android と iOS のアプリケーション通知の処理方法が異なるので、アプリケーションの Android や iOS のバージョン間でのプッシュの統計情報を直接比較するのが困難になっています。Android は iOS より多くの OS レベル通知情報を提供します。Android では、通知センターでネイティブ通知が受信、クリック、削除された場合に、そのことを報告しますが、iOS では、通知がクリックされるまでこの情報を報告しません。 
- 「プッシュ」番号が各キャンペーンの「配信された」番号と異なる主な理由は、"アプリ内" 通知と "アプリ外" 通知が別々にカウントされるためです。"アプリ内" 通知は、モバイル エンゲージメントによって処理されますが、"アプリ外" 通知はデバイスの OS の通知センターによって処理されます。

### 関連項目

- [方法 - 最初のプッシュ][Link 27]、[トラブルシューティング ガイド - プッシュ][Link 23]、[HTTP プロトコル情報](http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
- [Apple 開発者 - "無効" UI 情報](http://support.apple.com/kb/HT3576)、[Apple 開発者 - "無効" 開発者情報](https://developer.apple.com/notifications/)、[Apple 開発者 - "無効" トラブルシューティング](https://developer.apple.com/library/ios/technotes/tn2265/)、[Apple 開発者 - URL スキーム](https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html) 
- [Android 開発者 - "無効" UI 情報](http://developer.android.com/about/versions/jelly-bean.html)、[Android 開発者 - "無効" パターン](http://developer.android.com/design/patterns/notifications.html)、[Android 開発者 - "無効" 通知](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)、[Android 開発者 - "無効" プレビュー](https://developer.android.com/preview/notifications.html)、[Android 開発者 - "無効" リファレンス](http://developer.android.com/reference/android/app/Notification.html)、[Android 開発者 - インテント フィルター スキーム](http://developer.android.com/guide/components/intents-filters.html#DataTest)、[Android 開発者 - インテント フィルター リファレンス ガイド](http://developer.android.com/reference/android/content/Intent.html#toUri)

## プッシュの対象

### 問題
- 組み込みの対象が適切に機能しません。
- アプリ情報タグの対象が適切に機能しません。
- 位置情報の対象が適切に機能しません。
- 言語オプションが適切に機能しません。

### 原因

- Azure モバイル エンゲージメント UI または API を使用してアプリ情報タグをアップロードしていることを確認します。
- アプリケーション レベルでプッシュ速度やプッシュ クォータを調整したり、キャンペーン レベルで対象ユーザーを制限したりすることで、他の対象条件を満たしている場合でも、特定のプッシュを受信できないようにします。 
- 「言語」の設定は、国やロケールに基づく対象とは異なります。また、電話や GPS の位置に基づく位置情報からの対象とも異なります。
- 「既定の言語」のメッセージは、デバイスを指定の第 2 言語に設定していないすべての顧客に送信されます。

### 関連項目

- [UI ドキュメント - Reach][Link 17]、 [UI ドキュメント - 設定][Link 20]、[API ドキュメント - Reach][Link 4]、[API ドキュメント - プッシュ][Link 4]、[API ドキュメント - デバイス][Link 4]
 
## プッシュのスケジュール

### 問題
- プッシュのスケジュール設定が適切に機能しません (送信が早すぎるか遅延します)。

### 原因

- タイム ゾーンは、特にエンド ユーザーのタイム ゾーンを使用する場合にスケジュールに関する問題が生じます。
- 高度なプッシュ機能がプッシュを遅らせることがあります。
- 電話設定 (アプリ情報タグの代わり) に基づく対象は、Azure モバイル エンゲージメントがプッシュを送信する前にリアルタイムで電話からデータを要求する必要があるため、プッシュを遅らせることがあります。
- 終了日を設定せずに作成されたキャンペーンは、プッシュをデバイス上にローカルに保存するため、手動でキャンペーンを終了させても、次回アプリを開いたときにそのプッシュを表示します。
- 2 つ以上のキャンペーンを同時に開始すると、ユーザー ベースのスキャンに時間がかかる場合があります (一度に 1 つのキャンペーンのみを最大で 4 回開始してみます。また、アクティブなユーザーのみを対象にすると、古いユーザーをスキャンする必要がありません)。
- リーチ キャンペーンの [Campaign (キャンペーン)] セクションで [Ignore Audience, push will be sent to users via the API (対象ユーザーを無視して API 経由でユーザーにプッシュを送信する)] オプションを使用すると、キャンペーンは自動的に送信されず、Reach API 経由で手動で送信する必要があります。
- リーチのカスタム カテゴリを使用してアプリ内通知を表示する場合は、通知の正しいライフサイクルに従う必要があります。そうしないと、ユーザーが通知を閉じたときに、通知が消去されないことがあります。

### 関連項目

- [方法 - スケジュール設定][Link 3]、[AUI ドキュメント - リーチの新しいプッシュ キャンペーン][Link 27]
 
<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

<!--HONumber=54--> 