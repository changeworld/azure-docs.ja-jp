<properties 
   pageTitle="Azure モバイル エンゲージメント ユーザー インターフェイス - 分析" 
   description="Azure モバイル エンゲージメントを使用してアプリケーションに関する履歴データを分析する方法を説明します。" 
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

# アプリケーションに関する履歴データを分析する方法
UI の [分析] セクションでは、24 時間ごとに更新される履歴データに基づいて、アプリケーションに関する統計情報を提供します。この情報は、折れ線 / 棒 / 円グラフ、グリッド、マップからなる別のダッシュ ボードに表示されます。このデータを .csv ファイルとしてダウンロードすることもできます。これと同じ情報の多くは、UI の [モニタ] セクションからリアルタイムで入手できます。また、分析 API からアクセスすることもできます。「概念」の「用語集」では、次のような、分析と監視で使用される用語と省略形を定義しています。アクティブ ユーザー、新しいユーザー、リテンション ユーザー、セッション、ユーザー パス グラフ、ユーザー マップ、URL の追跡、傾向、アクティビティ、イベント、ジョブ、エラー、追加情報、クラッシュ、アプリ情報。

### 関連項目
-  [概念 - 用語集][Link 6]、[トラブルシューティング ガイド - 分析][Link 21]

## 標準分析とカスタム分析
Azure モバイル エンゲージメントは、SDK とアプリを統合するとすぐにグラフ化できる、アプリケーションに関する基本的かつ標準的な分析情報のセットを提供します。Azure モバイル エンゲージメントでは、エンドユーザーの動作について入手したい場合に追加のカスタム分析情報を収集する機能も備えています。これを行う場合は、[設定] で作成したカスタムの「アプリ情報タグ」のタグ プランを作成すると、Azure モバイル エンゲージメントがこの追加のデータを収集できます。

### 関連項目
-  [UI ドキュメント - 設定][Link 20]
 
## 分析ヘッダー
- 項目名: カウントされる項目にラベルを付けます
- ヘルプの表示: このセクションに関するコンテキスト情報を表示します
- バージョン: アプリケーションの各バージョンに関するさまざまな分析情報やすべてのバージョン情報を表示します。(注: UI の分析データのフィルター処理では、アプリのバージョンにかかわらず、この種類のすべての例が表示されます。たとえば、名前で「Crashes」をフィルター処理すると、バージョン 1 とバージョン 2 のアプリから表示されます)。
- 期間: 過去 7 日間、過去 30 日間、すべての時間、カスタム
- 料金: 時間、日、週、月単位
- 表示: 折れ線グラフ、グリッド、ダッシュボードに送信、CSV のダウンロード
 
![Analytics1][10]

## 分析
- ダッシュボード: 新しいユーザー、アクティブ ユーザー、これらのユーザーの傾向に関する一般的な情報を示します。
- ユーザー: ユーザーは、デバイス ID によって識別されます。この ID は各デバイスに固有です (1 人の新しいユーザーは実質的に 1 つの新しいデバイスです)。ユーザーがこの期間に最初のセッションを実行した場合、ある一定期間の新しいユーザーと見なされます。ユーザーが過去 7 日間に少なくとも 1 つのセッションを実行した場合、そのユーザーは保存されます。アクティブ ユーザーとは、一定期間に少なくとも 1 つのセッションを行ったユーザーです。月、週、日、または時間単位で並べ替えることができます。すべてのグラフは同じように見えますが、アプリケーションのバージョンなどのさまざまな機能でフィルター処理し、指定した期間で並べ替えることができます。SDK の統合によって収集される標準情報は、次のとおりです。アクティブ ユーザー、新しいユーザー、セッション数、各セッションの長さ、顧客の技術情報 (国、ローカル、場所、言語、通信事業者、デバイス、ファームウェア、ネットワーク (WiFi)、アプリと SDK のバージョン)。この情報は、[monitor (モニター)] セクションからリアルタイムで表示できます。 
- ソースによる追跡では、特定のプロモーション キャンペーンの結果としてアプリケーションをダウンロードした新しいユーザー数が表示されます。ユーザーは、デバイス ID によって識別されます。この ID は各デバイスに固有です (1 人の新しいユーザーは実質的に 1 つの新しいデバイスです)。ユーザーがこの期間に最初のセッションを実行した場合、ある一定期間の新しいユーザーと見なされます。
- ストアによる追跡では、特定のストアからアプリケーションをダウンロードした新しいユーザー数が表示されます。ユーザーは、デバイス ID によって識別されます。この ID は各デバイスに固有です (1 人の新しいユーザーは実質的に 1 つの新しいデバイスです)。ユーザーがこの期間に最初のセッションを実行した場合、ある一定期間の新しいユーザーと見なされます。

> 注: 期間はユーザーのデバイス設定の日付に基づくため、電話の日付が正しく設定されていないユーザーには、誤った期間が表示されることがあります。

- 保有期間: ユーザーがこの期間に最初のセッションを実行した場合、そのユーザーは保存されます。保存されたユーザー (と新しいユーザー) をカウントする期間を、時間、日、週、月に変更できます。ユーザーの保有期間の分析は、コホートの上部に構築されます。コホートとは、一定期間に検出された、一連のすべて新しいユーザー (この期間中に最初のセッションを実行した一連のユーザー) です。1 日、2 日、4 日、7 日、または 1 か月のコホートを使用します。コホートを 1 日、2 日、4 日、7 日、または 1 か月ごとに指定すると、Azure モバイル エンゲージメントは、コホートに属しているアクティブな一連のすべてのユーザー (この期間中に少なくとも 1 つのセッションを実行した一連のユーザー) を計算します。この一連のユーザーは、コホート バージョンと呼ばれます。(Azure モバイル エンゲージメントは、アプリを使用しているユーザー数を表示できますが、アプリをアンインストールしたユーザー数を確認できるのは、GooglePlay iTunes、Windows ストアなどのプラットフォーム固有のストアのみです)。 
- セッション: 1 人のユーザーが 1 つのアプリケーションを使用。セッションは、ユーザーが行う一連のアクティビティから生成されます (通常、アクティビティはアプリケーションの 1 つの画面の使用に関連付けられますが、これは、SDK がアプリケーションに統合されている方法によって異なります)。ユーザーは一度に 1 つのアクティビティをのみ実行できます。セッションは、ユーザーが最初のアクティビティを始めるとすぐに開始され、ユーザーが最後のアクティビティを終了すると停止します。アクティビティを実行しない状態が数秒以上続くと、一連のアクティビティは 2 つの個別のセッションに分割されます。
- アクティビティ: アプリケーションの各画面の名前と、ユーザーが各画面で費やした時間。アクティビティは、自分のアプリ用に設定した「アプリ情報」タグに対応するカスタムの分析オプションです。
- ユーザー パス: ユーザーがアプリケーションのアクティビティ (画面) を検索する方法を示します。スライダーを動かして詳細のレベルを調整できます。青のノードは、アプリケーションのアクティビティを表します。その大きさは、ユーザーが費やした時間に比例します。白のノードは、セッションの開始と停止を表します。赤のノードは、クラッシュを表します。リンクは、アプリケーションのアクティビティ間 (またはアクティビティとクラッシュ間) の移行を表します。ノードまたはリンクをクリックすると、ツールヒントにデータの詳細情報 (特定の画面で費やされた時間、移行の数、アクティビティからアクティビティへの移行の比率) が表示されます。(A ---60% ---> B は、ユーザーは、60% の確率でアクティビティ A からアクティビティ B に移行した、という意味です)。 グラフを分かりやすいように再編成できます。グラフの位置は変更を加えるたびに保存されます。クラッシュを表示または非表示にしてグラフの負荷を減らすことができます。
- イベント: アプリケーションでユーザーが実行した特定のアクション。イベントの分布は、1 セッション 1 ユーザーあたりのイベント数として表示されます。イベントは、ボタンのクリックや通知の受信などの即時の動作を表します。(イベントの意味は、SDK がアプリケーションに統合された方法によって異なります)。 イベントは、セッション中やジョブ中に発生したり、スタンド アロンで発生したりすることもあります。
- ジョブ: アクションの長さに重点を置くことを除いて、イベントに似ています。たとえば、ジョブは、コンテンツの読み込みや Web サービスの呼び出しにかかる時間に関する技術情報を通知します。また、ユーザーがフォームの記入、アカウントの作成、購入を行う際にかかる時間も示します。ジョブは、ダウンロード タスクの時間やバナーが画面に表示される時間などのタスクの期間を表します。(ジョブの意味は、SDK がアプリケーションに統合された方法によって異なります)。 ジョブは通常、セッションの範囲外で実行されるバックグラウンド タスクに関連付けられます (ユーザーは操作しません)。
- テクニカル: 追跡できるアプリのユーザーのデバイスに関する技術情報 (ロケール、通信事業者、ネットワーク、デバイス、ファームウェア、ユーザーのデバイスの画面サイズ、アプリのバージョン、アプリで使用される SDK バージョンなど)。
- エラー: アプリケーションをクラッシュさせないアプリケーション内部の技術的なエラーに関する情報。エラーは、ネットワーク障害や不適切な操作などの即時の問題を表します。(イベントの意味は、SDK がアプリケーションに統合された方法によって異なります)。 エラーは、セッション中やジョブ中に発生したり、スタンド アロンで発生したりすることもあります。
- クラッシュ: アプリケーションをクラッシュさせるエラーに関する情報。クラッシュは、アプリケーションがその期待される機能の実行を停止し、中止する必要がある予期しない状態です。クラッシュは、通常、アプリケーションのバグが原因です。
 
![Analytics2][11]

## 保有期間の概要にアクセスする
![Analytics3][12]

保有期間の概要は、中央の複数のカードに分類され、それぞれに特定の保有期間の概要が表示されます。例では、2 日の保有期間が表示されています。他のカードは、7 日と 4 日の保有期間を表示しています。

## 保有期間の概要カードを理解する
![Analytics4][13]

### 各カードは、3 つの部分で構成されています。
1. 1: コホートと期間
2. 2-4: 現在の期間の保有期間
3. 5: 履歴のスパークライン

### 次に、各要素について説明します。
1.    コホートと期間: この見出しは、コホートの種類を示します。[2-DAY PERIOD (2 日間)] は、2 日間ユーザーの動作を調査したこと、2 日間にわたって訪れたユーザー数、そのユーザーが次の 2 日間に接続したかどうかを意味します。上の例は、11 月 21 日～ 22 日のユーザーのアクティビティを示しています。
2.    これは、11 月 19 日～ 20 日に訪れたユーザー数に対する 11 月 21 日～ 22 日の維持率を示します。この例では、21 日～ 22 日に 1 名のアクティブ ユーザーがいて、19 日～ 20 日の新しいユーザーが 3 名だったこと示しています。
3.    この視覚的なインジケーターは、上のグラフ表示と同じ情報を示しています。(円の 3 分の 1 は数字の 33% からきています。) 色は追加情報を示します。緑は、この数字が前回の計算から増えていることを示しています。黄は安定、赤は減少を意味します。
4.    これは、計算に使用される値を示します。
5.    これは、保有期間の値の履歴のスパークラインです。これまでの値を認識して展開に対する幅広い視野を持つことができます。

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
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
[Link 27]: mobile-engagement-how-tos-first-push.md
[Link 28]: mobile-engagement-how-tos-test-campaign.md
[Link 29]: mobile-engagement-how-tos-personalize-push.md
[Link 30]: mobile-engagement-how-tos-differentiate-push.md
[Link 31]: mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: mobile-engagement-how-tos-text-view.md
[Link 33]: mobile-engagement-how-tos-web-view.md

<!--HONumber=54-->