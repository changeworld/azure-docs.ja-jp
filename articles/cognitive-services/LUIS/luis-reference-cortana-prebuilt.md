---
title: 事前構築済みの Cortana アプリのリファレンス | Microsoft Docs
description: Language Understanding Intelligent Services (LUIS) で事前構築済みの Cortana パーソナル アシスタント アプリケーション用のリファレンス。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: cd808c30a6781fc0252992c13ba247486e35ad44
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373461"
---
# <a name="cortana-prebuilt-app-reference"></a>事前構築済みの Cortana アプリのリファレンス
このリファレンスでは、事前構築済みの Cortana アプリが認識する意図とエンティティの一覧を示します。

## <a name="cortana-prebuilt-intents"></a>事前構築済みの Cortana の意図
この事前構築済みのパーソナル アシスタント アプリケーションは、次の意図を識別します。

| 意図 | 発話の例 |
|--------| ------------------|
| builtin.intent.alarm.alarm_other|午前 7:30 のアラームを 8 時に更新して <br/>アラームを午前 8 時から午前 9 時に変更して |
| builtin.intent.alarm.delete_alarm|アラームを削除して <br/>「起きる時間」のアラームを削除して|
| builtin.intent.alarm.find_alarm|起きる時間が何時に設定されているか教えて <br/> 起きる時間のアラームがオンになっているか教えて |
| builtin.intent.alarm.set_alarm|起きる時間のアラームをオンにして<br/>抗生物質を飲む時間のアラームを 12 時に設定して|
| builtin.intent.alarm.snooze|アラームを 5 分スヌーズして<br/>アラームをスヌーズして|
| builtin.intent.alarm.time_remaining| 「起きる時間」までの時間を教えて <br/> 次のアラームまでの時間を教えて|
| builtin.intent.alarm.turn_off_alarm | 午前 7 時のアラームをオンにして <br/> 起きる時間のアラームをオンにして |
| builtin.intent.calendar.change_calendar_entry| 予定を変更して<br/>会議を今日から明日に移動して|
|builtin.intent.calendar.check_availability|Tim が金曜日は忙しいかどうか教えて<br/>Chris が土曜日に空いているかどうか教えて|
|builtin.intent.calendar.connect_to_meeting|会議に接続して<br/>オンライン ミーティングに参加して|
|builtin.intent.calendar.create_calendar_entry|金曜日に Tony との予定をスケジュールに設定して<br/>日曜日の午後 2 時に Lisa との予定を設定して|
|builtin.intent.calendar.delete_calendar_entry|予定を削除して<br/>明日午後 3 時の会議をカレンダーから削除して|
|builtin.intent.calendar.find_calendar_entry|カレンダーを表示して<br/>自分の週間カレンダーを表示して|
|builtin.intent.calendar.find_calendar_when|Jon との次回の会議はいつか教えて<br/>月曜日の Larry との予定は何時からか教えて|
|builtin.intent.calendar.find_calendar_where|明日 6 時の会議の場所を表示して<br/>Jon との会議の場所を教えて|
|builtin.intent.calendar.find_calendar_who|会議の参加者を教えて<br/>他に招待されている人を教えて|
|builtin.intent.calendar.find_calendar_why|11 時の会議の詳細を教えて<br/>Jon との会議の内容を教えて|
|builtin.intent.calendar.find_duration|次の会議の所要時間を教えて<br/>今日の午後の会議の所要時間を教えて|
|builtin.intent.calendar.time_remaining|次の予定までの時間を教えて<br/>会議までの時間を教えて|
|builtin.intent.communication.add_contact|この番号を保存して Jose と名前を付けて<br/>Jason を連絡先に追加して|
|builtin.intent.communication.answer_phone|応答して<br/>電話に応答して|
|builtin.intent.communication.assign_nickname|Nickolas のニックネームを編集して<br/>John Doe のニックネームを追加して|
|builtin.intent.communication.call_voice_mail|ボイス メールを送って<br/>ボイスメールを発信して|
|builtin.intent.communication.find_contact|連絡先を表示して<br/>連絡先を検索して|
|builtin.intent.communication.forwarding_off|着信の転送を停止して<br/>着信の転送をオフにして|
|builtin.intent.communication.forwarding_on|着信の転送を自宅の電話に設定して<br/>着信を自宅の電話に転送して|
|builtin.intent.communication.ignore_incoming|着信を拒否して<br/>今は忙しいので応答しないで|
|builtin.intent.communication.ignore_with_message|電話に出ないで、代わりにメッセージを送信して<br/>無視して、テキストを返信して|
|builtin.intent.communication.make_call|Bob と John を呼び出して<br/>父と母を呼び出して|
|builtin.intent.communication.press_key|アスタリスクを押して<br/>1 2 3 と押して|
|builtin.intent.communication.read_aloud|テキストを読んで<br/>メッセージの内容を読んで|
|builtin.intent.communication.redial|最後の発信をリダイヤルして<br/>リダイヤルして|
|builtin.intent.communication.send_email|Mike Waters に電子メール: Mike、先週のディナーは素晴らしかったですね<br/>Bob に電子メールを送信して|
|builtin.intent.communication.send_text|Bob と John にテキストを送信して<br/>Message|
|builtin.intent.communication.speakerphone_off|スピーカーを無効にして<br/>スピーカーフォンをオフにして|
|builtin.intent.communication.speakerphone_on|スピーカーフォン モード<br/>スピーカーフォンをオンにして|
|builtin.intent.mystuff.find_attachment|昨日 John からメールで届いたドキュメントを検索して <br/>John からのドキュメントを検索して|
|builtin.intent.mystuff.find_my_stuff|昨日の買い物リストを編集して<br/>9 月からの化学のノートを検索して
|builtin.intent.mystuff.search_messages|メッセージを開いて <br/>最大配信数|
|builtin.intent.mystuff.transform_my_stuff|夫と買い物リストを共有して<br/>買い物リストを削除して|
|builtin.intent.ondevice.open_setting|Cortana の設定を開いて <br/>通知に移動して|
|builtin.intent.ondevice.pause|音楽をオフにして<br/>音楽オフ|
|builtin.intent.ondevice.play_music|「owner of a lonely heart」をかけて<br/>ゴスペル音楽をかけて|
|builtin.intent.ondevice.recognize_song|この曲の名前を教えて<br/>曲のタイトルを分析して取得して|
|builtin.intent.ondevice.repeat|このトラックをリピート再生して<br/>この曲をもう一度かけて|
|builtin.intent.ondevice.resume|音楽を再生して<br/>音楽をもう一度再生して|
|builtin.intent.ondevice.skip_back|トラックを戻して<br/>前の曲に戻して|
|builtin.intent.ondevice.skip_forward|次の曲に進めて<br/>トラックをスキップして|
|builtin.intent.ondevice.turn_off_setting|WiFi をオフにして<br/>機内モードを無効にして|
|builtin.intent.ondevice.turn_on_setting|WiFi をオンにして<br/>Bluetooth をオンにして|
|builtin.intent.places.add_favorite_place|この住所をお気に入りに追加して<br/>この場所をお気に入りに保存して|
|builtin.intent.places.book_public_transportation|電車のチケットを購入して<br/>トラムのパスを予約して|
|builtin.intent.places.book_taxi|この 1 時間の交通手段を検索して<br/>タクシーを探して|
|builtin.intent.places.check_area_traffic|520 の交通状況を教えて<br/>i-5 の交通状況を教えて|
|builtin.intent.places.check_into_place|Joya にチェックインして<br/>ここにチェックインして|
|builtin.intent.places.check_route_traffic|Kirkland への行き方を表示して<br/>シアトルまでの交通状況を教えて|
|builtin.intent.places.find_place|今どこにいるか教えて <br/>人気の日本食レストランを 3 件教えて|
|builtin.intent.places.get_address|Robson 通りの Guu の住所を表示して<br/>最寄りのスターバックスの住所を教えて|
|builtin.intent.places.get_coupon|地元のテレビのセール情報を探して<br/>マウンテンビューのセール情報|
|builtin.intent.places.get_distance|ホリデイ インまでの距離を教えて<br/>タホまでの距離を教えて|
|builtin.intent.places.get_hours|バー Del Corso の月曜日の営業時間を教えて<br/>図書館の営業時間を教えて|
|builtin.intent.places.get_menu|アップルビーのメニューを表示して<br/>シズラーのメニューの内容を教えて|
|builtin.intent.places.get_phone_number|ホーム デポの番号を教えて<br/>最寄りのスターバックスの電話番号を教えて|
|builtin.intent.places.get_price_range|レッド ロブスターのディナーの予算を教えて<br/>パープル カフェの予算を教えて|
|builtin.intent.places.get_reviews|地元のホームセンターのレビューを表示して<br/>レストランのレビューを表示して|
|builtin.intent.places.get_route|道順を教えて|ピザハットまで徒歩で行くことができるかどうか教えて|
|builtin.intent.places.get_star_rating|フレンチ ランドリーの星はいくつか教えて<br/>モンテレイの水族館の評判を教えて|
|builtin.intent.places.get_transportation_schedule|サンフランシスコへのフェリーの出港時間を教えて<br/>シアトルへの最終電車の時間を教えて|
|builtin.intent.places.get_travel_time|サンフランシスコからデンバーまでの走行時間を教えて<br/>ここからサンフランシスコに行くにはどのくらいの時間がかかるか教えて|
|builtin.intent.places.make_call|ベルビューの Smith 博士に電話して<br/>1 番街のザ・ホーム・デポに電話して|
|builtin.intent.places.rate_place|このレストランを評価して<br/>Yelp で Il Fornaio に 5 つ星を付けて|
|builtin.intent.places.show_map|今どこにいるか教えて <br/>今の場所を教えて|
|builtin.intent.places.takes_reservations|オリーブ ガーデンを予約することができるか教えて<br/>アート ギャラリーが予約を受け付けているか教えて|
|builtin.intent.reminder.change_reminder|リマインダーを変更して<br/>写真の毎日のリマインダーを 30 分早めて|
|builtin.intent.reminder.create_single_reminder|午前 7 時に起こして<br/>午後 4:40 に Luca とトリック オア トリートに行くリマインダーを送信して|
|builtin.intent.reminder.delete_reminder|このリマインダーを削除して<br/>写真のリマインダーを削除して|
|builtin.intent.reminder.find_reminder|今日設定されているリマインダーがあるか教えて<br/>Luca のパーティのリマインダーが設定されているか教えて|
|builtin.intent.reminder.read_aloud|リマインダーを読み上げて<br/>そのリマインダーを読んで|
|builtin.intent.reminder.snooze|明日までそのリマインダーをスヌーズして<br/>このリマインダーをスヌーズして|
|builtin.intent.reminder.turn_off_reminder|リマインダーをオフにして<br/>空港ピックアップのリマインダーを無視して|
|builtin.intent.weather.change_temperature_unit|華氏からケルビンに変更して<br/>華氏から摂氏に変更して|
|builtin.intent.weather.check_weather|シアトルに旅行するときの天気予報を表示して<br/>今週末の天気を教えて|
|builtin.intent.weather.check_weather_facts|12 月のハワイの天気を教えて<br/>去年のこの時期の温度を教えて|
|builtin.intent.weather.compare_weather|テキサス州のダラスとヒューストンの最高気温と最低気温を比較して<br/>ソルトレイクシティとニューヨークシティの天気を比較して|
|builtin.intent.weather.get_frequent_locations|最も利用頻度の高い場所を教えて<br/>最も利用する場所を表示して|
|builtin.intent.weather.get_weather_advisory|気象警報<br/>サクラメントの気象注意報を表示して|
|builtin.intent.weather.get_weather_maps|天気図を表示して<br/>アフリカの天気図を表示して|
|builtin.intent.weather.question_weather|明日の朝に霧が出るか教えて<br/>この週末は雪かきが必要か教えて|
|builtin.intent.weather.show_weather_progression|地元の気象レーダーを表示して<br/>レーダーを開始して|
|builtin.intent.none|何歳ですか<br/>カメラを起動して|

## <a name="prebuilt-entities"></a>事前構築済みのエンティティ 

次に事前構築済みのパーソナル アシスタント アプリケーションが認識できるエンティティの例をいくつか紹介します。

|エンティティ |発話中のエンティティの例 |
|-------|------------------|
|builtin.alarm.alarm_state | 起きる時間のアラームを `off` にして    <br/> 起きる時間のアラームが `on` になっているか教えて  | 
|builtin.alarm.duration |`10 minutes` 後にスヌーズして    <br/> アラームを `5 minutes` 後にスヌーズして  |
|builtin.alarm.start_date | アラームを `monday` の午前 7 時に設定して   <br/> アラームを `tomorrow` の正午に設定して   |
|builtin.alarm.start_time | `30 minutes` 後にアラームを鳴らして    <br/> `in 20 minutes` 後にアラームを設定して   |
|builtin.alarm.title | `wake up` のアラームがオンになっているか教えて <br/> `take antibiotics` という名前のアラームを月曜日から金曜日の 12 時の 15 分前に設定して |
|builtin.calendar.absolute_location | 明日 `123 main street` で行われる予定を作成して   <br/> 会議を 6 月 5 日 `cincinnati` に設定して    |
|builtin.calendar.contact_name|カレンダーにマーケティング会議を設定して、`joe` が必ず参加するようにして <br/>Il Fornaio でのランチを設定して、`paul` を招待して |   
|builtin.calendar.destination_calendar|これを `work` のスケジュールに追加して   <br/>これを `personal` のカレンダーに入れて |
|builtin.calendar.duration| 今夜 6 時から `an hour` の予定を設定して <br/>  Joe との `2 hour` の会議を予約して |  
|builtin.calendar.end_date | 明日から `next monday` まで休暇というカレンダーのエントリを作成して <br/>    `monday, october 5th` まで取り込み中として時間をブロックして | 
|builtin.calendar.end_time | 会議を `5:30 PM` に終了して <br/> 11 時から `noon` までスケジュールを設定して  |   
|builtin.calendar.implicit_location | dmv の予定をキャンセルして <br/> Miles の誕生日会の場所を `poppy restaurant` に変更して |    
|builtin.calendar.move_earlier_time | 会議を `an hour` 前倒しにして <br/> 歯科の予約を `30 minutes` 繰り上げて       |
|builtin.calendar.move_later_time | 歯科の予約を `30 minutes` 移動して    <br/> 会議を `an hour` 先送りして |  
|builtin.calendar.original_start_date | 美容院の予約のスケジュールを '火曜日' から水曜日に再設定して <br/> Ken との会議を `monday` から火曜日に移動して |
|builtin.calendar.original_start_time | 会議のスケジュールを `2:00` 時から 3 時に再設定して  <br/> 歯科の予約を `3:30` 時から 4 時に変更して |  
|builtin.calendar.start_date | `flag day` のパーティが始まる時間を教えて <br/> `friday after next` 正午にランチのスケジュールを設定して 
|builtin.calendar.start_time | このスケジュールを `this morning` に設定して <br/> このスケジュールを `morning` に設定して |  
|builtin.calendar.title | `vet appointment`  <br/> 火曜日に `dentist` |
|builtin.communication.audio_device_type | `bluetooth` を使用して呼び出して  <br/> `headset` を使用して呼び出して | 
|builtin.communication.contact_name | `bob jones` にテキストを送信して  <br/> | `sarah` を呼び出して|
|builtin.communication.destination_platform | `london` にいる Dave を呼び出して <br/> 彼の `work line` を呼び出して |    
|builtin.communication.from_relationship_name | `daughter` からの呼び出しを表示して <br/> `mom` からの電子メールを読み上げて   |   
|builtin.communication.key | `star` にダイヤルして <br/>  `hash` キーを押して    |
|builtin.communication.message | Carly に `i'm running late` とメールして <br/> Angus Smith に `good luck on your exam` と SMS を送信して |    
|builtin.communication.message_category | `follow up` とマークされた新しい電子メール  <br/> `high priority` とマークされた電子メール |    
|builtin.communication.message_type | `email` を送信して   <br/> `text` メッセージを読み上げて |
|builtin.communication.phone_number | `1-800-328-9459` にダイヤルして <br/>     `555-555-5555` を呼び出して |   
|builtin.communication.relationship_name | `husband` に SMS を送信して <br/>  `family` に電子メールを送信して| 
|builtin.communication.slot_attribute | `recipient` を変更して <br/>    `text` を変更して | 
|builtin.comminication.source_platform | `skype` から彼を呼び出して <br/> `personal line` から彼を呼び出して |
|builtin.mystuff.attachment| ドキュメントを `attached` <br/> Bob が送信した電子メールの `attachment` を検索して |
|builtin.mystuff.contact_name| Lisa が `me` に送信したスプレッドシートを探して <br/> 昨晩 `susan` に送信したドキュメントはどこか教えて |
|builtin.mystuff.data_source | `c:\dev\` <br/> `desktop` <br/> |`"resolution": { "resolution_type": "metadataItems", "metadataType": CanonicalEntity", "value": "desktop" }`|
|builtin.mystuff.data_type | 昨晩作業した `document` を探して <br/> |`"resolution": {"resolution_type": "metadataItems", "metadataType": "CanonicalEntity", "value":Document" }` <br/> Mike の `visio diagram` を表示して  |
|builtin.mystuff.end_date| `between yesterday and today` に作業したドキュメントを表示して   <br/> `before thursday the 31st` に作業していたドキュメントは何か教えて |
|builtin.mystuff.end_time| `before noon` に保存したファイルを検索して <br/> `before 4pm` に作業していたドキュメントは何か教えて|      
|builtin.mystuff.file_action| 昨日 `saved` スプレッドシートを開いて <br/> Kevin が `created` スプレッドシートを検索して| 
|builtin.mystuff.from_contact_name | `jason` が送信した提案を検索して <br/> `isaac` の最後の電子メールを開いて |
|builtin.mystuff.keyword | `french conjugation` のファイルを表示して <br/> 昨日ドラフトを作成した `marketing plan` を検索して |
|builtin.mystuff.location | `work` で編集したドキュメント <br/> `paris` で撮影した写真 |
|builtin.mystuff.message_category | `new` 電子メールを検索して <br/> `high priority` 電子メールを検索して |
|builtin.mystuff.message_type | `email` を確認して <br/>  `text` メッセージを表示して  |
|builtin.mystuff.source_platform | John からの `hotmail` の電子メールを検索して    <br/> `work` から送信したドキュメントを検索して |
|builtin.mystuff.start_date | `january` からのメモを検索して <br/> `after january 1st` に Rob に送信した電子メールを検索して |
|builtin.mystuff.start_time | `after noon` から午後 2 時までに Rob に送信したその電子メールを検索して <br/> Kristin が私に送信して私が `last night` に編集したワークシートを検索して | 
|builtin.mystuff.title | `c:\dev\mystuff.txt` <br/> `*.txt` |
|builtin.mystuff.transform_action | John が私に送信したファイルを `download` して <br/> 注釈のガイドライン ドキュメントを `open` して |
|builtin.note.note_text | `pork chops, applesauce and milk` が含まれる食料品リストを作成して <br/> `buy milk` というメモを作成して |
|builtin.note.title | `grocery list` というメモを作成して <br/> `people to call` というメモを作成して |
|builtin.ondevice.music_artist_name | `rufus wainwright` のすべての曲を再生して <br/> `garth brooks` の曲を再生して |   
|builtin.ondevice.music_genre | `classic rock` の曲を表示して <br/> バロック時代の `classical` の曲を再生して |
|builtin.ondevice.music_playlist | `workout` のプレイリストからブリトニー スピアーズのすべての曲をシャッフルして <br/> `breakup` のプレイリストを再生して
|builtin.ondevice.music_song_name | `summertime` を再生して <br/> `me and bobby mcgee` を再生して | 
|builtin.ondevice.setting_type | `quiet hours` <br/> `airplane mode` |
|builtin.places.absolute_location | `5th and pike` の交差点までの道順を教えて <br/> いいえ、`1 microsoft way redmond wa 98052` までの道順を教えてください |
|builtin.places.atmosphere | 出かけるのに `interesting` 場所を検索して    <br/> `casual` のレストランがある場所を教えて |  
|builtin.places.audio_device_type |`hands free` で郵便局を呼び出して <br/> `speakerphone` で Papa John's を呼び出して |    
|builtin.places.avoid_route | `toll road` を迂回して <br/> `construction on 101` を迂回してサンフランシスコに案内して |  
|builtin.places.cuisine | マウンテンビュー近くの `halal` デリ   <br/> ザ・ペニンシュラにある `kosher` 料理の名店 |
|builtin.places.date | `next friday the 12th` に予約して <br/>  マシコが `mondays` に営業しているか教えて |
|builtin.places.discount_type | Macy's の `coupon` を探して <br/> `coupon` を探して |
|builtin.places.distance | ここから `within 5 miles` にある評判のよいダイナーを教えて <br/> `within 15 miles` を検索して |   
|builtin.places.from_absolute_location | `45 elm street` から自宅までの道順 <br/> `san francisco` からパロ アルトまで案内して  |
|builtin.places.from_place_name | `post office` から 56 センター ストリートまで車で移動 <br/> `home depot` からロウズまで案内して |
|builtin.places.from_place_type | `work` から繁華街までの道順 <br/>  `drug store` から自宅まで案内して |
|builtin.places.meal_type | 最寄りの `dinner` を楽しめる場所 <br/> ビジネス `lunch` に適した場所を検索して | 
|builtin.places.nearby | `near` の評判のよいショップを表示して  <br/> `around` の評判のよいレバノン料理の店を教えて |
|builtin.places.open_status | ショッピング モールの `closed` 時間を教えて <br/> ストアの `opening` 時間を教えて | 
|builtin.places.place_name | `central park` に案内して <br/> `eiffel tower` を検索して |   
|builtin.places.place_type | `atms` <br/> `post office` |
|builtin.places.prefer_route | `shortest` ルートの交通案内を表示して <br/> `fastest` ルートで案内して | 
|builtin.places.price_range| `moderately affordable` 場所を教えて <br/>  `expensive` ものが欲しい   |
|builtin.places.product | この辺で `fresh fish` を入手できる場所を教えて  <br/> この辺で `bare minerals` を販売している場所を教えて |
|builtin.places.public_transportation_route | `m2` バスの運行スケジュール <br/> バス ルート `3x` |  
|builtin.places.rating | `3 star` のレストランを表示して <br/> `3 stars or higher` の結果を表示して  |
|builtin.places.reservation_number | `seven` 人で席を予約して <br/>  Il Fornaio に `two` 人で予約して |
|builtin.places.results_number | 最寄りのコーヒー ショップを `10` 件表示して <br/> 人気の水族館を `3` 件表示して  
|builtin.places.service_provided | バスで行くことができる `whale watch` を教えて <br/> `fix my brakes` ができる整備士を教えて |    
|builtin.places.time | 土曜日の `8 am` に営業している場所を教えて| マシコが `now` に営業しているか教えて |
|builtin.places.transportation_company | `new jersey transit` の運行状況 <br/> `bart` で行くことができるか教えて | 
|builtin.places.transportation_type | `on foot` で行くことができる音楽ショップを教えて <br/>  マシコに `biking` で行く道順を教えて|
|builtin.places.travel_time | 車で `less than 15 minutes` に行きたい <br/>   `under 15 minutes` で行くことができる場所を教えて |   
|builtin.reminder.absolute_location | `chicago` に着いたときに父に電話するようリマインダーを送信して <br/> `seattle` に戻ったときにガソリンを給油するようリマインダーを送信して |
|builtin.reminder.contact_name | `bob` からの電話があったら、ジョークを伝えるようにリマインダーを送信して <br/> `arthur` と話すときにスクール バスに触れるようリマインダーを作成して |
|builtin.reminder.leaving_absolute_location | `1200 main` から帰るときに Craig を拾うようリマインダーを送信して |
|builtin.reminder.leaving_implicit_location | `work` から帰るときにガソリンを給油するようリマインダーを送信して  |
|builtin.reminder.original_start_date | 芝刈りのリマインダーを `saturday` から日曜日に変更して <br/> 学校のリマインダーを `monday` から火曜日に移動して   |
|builtin.reminder.relationship_name | `husband` から電話があったときに、PTA 会議について伝えるようリマインダーを送信して <br/> `mom` から電話があったときに、もう一度リマインダーを送信して|
|builtin.reminder.reminder_text | Smith 博士から電話があったときに `bring up my small spot of patchy skin` ようにリマインダーを送信して  <br/> 4:40 に `pick up dry cleaning` ようリマインダーを送信して   |
|builtin.reminder.start_date | `thursday after next` の午後 8 時にリマインダーを送信して  <br/> `next thursday the 18th` の午後 8 時にリマインダーを送信して    |
|builtin.reminder.start_time | `in 30 minutes` にリマインダーを作成して <br/> `this evening at 7` に植物に水をやるリマインダーを作成して |  
|builtin.weather.absolute_location | `boston` に雨が降るか教えて <br/> `seattle` の天気予報を教えて  |
|builtin.weather.date_range | `this weekend` のニューヨークシティの天気予報 <br/>   フロリダ州ハリウッドの `five day` の天気予報を検索して |
|builtin.weather.suitable_for | 今週末 `hiking` はショートパンツで過ごせるか教えて   <br/> 今日の試合に `walk` 行くのに適した天気か教えて | 
|builtin.weather.temperature_unit | `kelvin` の今日の気温を教えて   <br/> `celsius` の気温を表示して |  
|builtin.weather.time_range | `tonight` は雪が降りそうか教えて <br/> `now` は風が強いか教えて  |
|builtin.weather.weather_condition | `precipitation` を表示して <br/> タホ湖の現在の `snow` の厚みを教えて  |

