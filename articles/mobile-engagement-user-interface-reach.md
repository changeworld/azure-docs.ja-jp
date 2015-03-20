<properties 
   pageTitle="Azure モバイル エンゲージメント ユーザー インターフェイス - Reach (リーチ)" 
   description="モバイル エンゲージメントの Reach (リーチ) セクションのユーザー インターフェイスの概要" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="mattgre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="required" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure モバイル エンゲージメント - ユーザーインターフェイス

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">概要</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navigation (ナビゲーション)</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">Home (ホーム)</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">My Account (マイ アカウント)</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Analytics</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Monitor (モニター)</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Reach (リーチ)</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segments (セグメント)</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Dashboard (ダッシュボード)</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Settings (設定)</a>
</div>

# <a name="Reach"></a>Reach (リーチ)
 
UI の Reach (リーチ) セクションは、プッシュ キャンペーン管理ツールで、プッシュ通知キャンペーンや機能を作成、編集、アクティブ化、完了、モニターしたり、統計を取得したりできます。また、Reach API (と低レベルのプッシュ API の一部の要素) 経由でアクセスすることも可能です。API と UI のいずれを使用する場合でも、リーチ キャンペーンを使用する前に、Azure モバイル エンゲージメントと Reach の両方を、SDK を使用する各プラットフォーム用のアプリケーションに統合する必要があります。

**関連項目:**

-  [API ドキュメント - Reach API][Link 4]、[API ドキュメント - Push API][Link 4]、[トラブルシューティング ガイド - Push/Reach][Link 2]
-  <a href="#ReachCampaign" title="Reach Campaign">リーチ キャンペーン</a>、<a href="#ReachCriterion" title="Reach Criterion">リーチの条件</a>、<a href="#ReachContent" title="Reach Content">リーチのコンテンツ</a>
 
## 4 種類のプッシュ通知:
1.    アナウンス - ユーザーに広告メッセージを送信してアプリ内の他の場所へリダイレクトしたり、アプリ外の Web ページやストアに誘導したりできます。 
2.    アンケート - ユーザーに質問を投げかけることでエンド ユーザーからの情報を収集できます。
3.    データ プッシュ- バイナリのデータ ファイルや base 64 のデータ ファイルを送信できます。データ プッシュに含まれる情報はアプリケーションに送信され、ユーザーの現在のアプリの使用状況を修正できます。アプリケーション側でデータ プッシュのデータを処理する機能を備えている必要があります。
4.    タイル (Windows Phone のみ) - Microsoft Push Notification Service (MPNS) を使用して、XML データを含むネイティブの Windows Push を送信できます(SDK バージョン 0.9.0 以降でサポート。タイルの最終的なペイロードは 32 キロバイト以下である必要があります)。

**関連項目:**

-  [概念 - 用語集][Link 6]

## 各キャンペーンで表示されるリアルタイム統計の 3 つのカテゴリ: 
1.    Pushed (プッシュ済み) - キャンペーンで指定された条件に基づいて送信されたプッシュ数。 
2.    Replied (反応) - 通知をアプリ外で開いたり、アプリ内で閉じるなどの通知に対して行われたユーザーの反応の数。 
3.    Actioned (アクション) - ユーザーが通知のリンクをクリックしてアプリの新しい場所、ストア、Web ブラウザーにリダイレクトされた回数。 

> 注:キャンペーンの詳細な統計情報は、Reach API Stats 経由で確認できます。

**関連項目:**

-  [概念 - 用語集][Link 6]、[API ドキュメント - Reach API - Stats][Link 4]


## キャンペーンの詳細:
キャンペーンの名前の上にカーソルを合わせるか、クリックして開くとキャンペーンを編集、複製、削除したり、まだアクティブ化されていないキャンペーンをアクティブ化したりできます。すでにアクティブなキャンペーンの名前の上にカーソルを合わせるか、クリックして開くとそのキャンペーンの複製を作成できますが、一度アクティブ化したキャンペーンを変更することはできません。
 
![Reach1][18]

## リーチのフィードバック:
すでにアクティブなキャンペーンの詳細ビューから統計ビューに切り替えて、統計の簡易ビューから詳細ビューに切り替えると、詳細情報を確認できます (保有している権限に基づきます)。過去のキャンペーンからのリーチのフィードバック情報を新しいキャンペーンの対象条件として使用もできます。リーチのフィードバックの統計は、Reach API の "Stats" を使用して収集できます。また、過去のキャンペーンに基づいてプッシュ キャンペーンの対象をカスタマイズすることも可能です。


**関連項目:**

-  <a href="#ReachCampaign">UI ドキュメント - Reach - 新しいプッシュ キャンペーン</a>、[API ドキュメント - Reach API - Stats][Link 4]

![Reach2][19]

## <a name="ReachCampaign"></a>Reach - 新しいプッシュ キャンペーン
 
UI の [Reach (リーチ)] セクションを使用して、プッシュ通知の送信に必要なすべての情報を指定することで、複雑な式を用いた新しいプッシュ キャンペーンを作成できます。プッシュ キャンペーンのオプションは、アナウンス、アンケート、データ プッシュ、タイル (Windows Phone のみ) の 4 つのキャンペーンの種類にごとに多少異なります。

**オプションの適用対象:**

- 言語:    すべて (アナウンス、アンケート、データ プッシュ、タイル)
- キャンペーン:    すべて (アナウンス、アンケート、データ プッシュ、タイル)
- 通知:     アナウンス、アンケート
- 内容:    キャンペーンの種類ごとに異なります
- 対象:     すべて (アナウンス、アンケート、データ プッシュ、タイル)
- 期間:     アナウンス、アンケート、タイル
- テスト:    すべて (アナウンス、アンケート、データ プッシュ、タイル)
 
![Reach-Campaign1][20]

## 言語:
[Language (言語)] ドロップダウン メニューを使用して、異なる言語に設定されたデバイスに、異なるバージョンのプッシュを送信できます。既定では、デバイスに設定された言語に関係なくすべてのデバイスに同じプッシュが送信されます。異なる言語のデバイスを使用しているユーザーは、既定の言語バージョンのプッシュを受信します。プッシュ キャンペーンには、選択した追加言語ごとに別のコンテンツを指定できるオプションがたくさんあります。 
 
![Reach-Campaign2][21]

**言語ごとの違い:**

- 言語:    既定の言語に加えて固有の言語を選択できます
- キャンペーン:    すべての言語で同一
- 通知:    既定の言語に加えて言語ごとに固有
- 内容:    既定の言語に加えて言語ごとに固有
- 対象:     言語ごとの条件でフィルター処理できます
- 期間:     すべての言語で同一
- テスト:    言語ごとに一度に送信できます
 
**サポートされている言語:**

- アラビア語 (ar) 
- ブルガリア語 (bg) 
- カタルニア語 (ca) 
- 中国語 (zh) 
- クロアチア語 (hr) 
- チェコ語 (cs) 
- デンマーク語 (da) 
- オランダ語 (nl) 
- 英語 (en) 
- フィンランド語 (fi) 
- フランス語 (fr) 
- ドイツ語 (de) 
- ギリシャ語 (el) 
- ヘブライ語 (he) 
- ヒンディー語 (hi) 
- ハンガリー語 (hu) 
- インドネシア語 (id) 
- イタリア語 (it) 
- 日本語 (ja) 
- 韓国語 (ko) 
- ラトビア語 (lv) 
- リトアニア語 (lt) 
- マレー語 (マクロ言語) (ms) 
- ノルウェー ブークモール (nb) 
- ポーランド語 (pl) 
- ポルトガル語 (pt) 
- ルーマニア語 (ro) 
- ロシア語 (ru) 
- セルビア語 (sr) 
- スロバキア語 (sk) 
- スロベニア語 (sl) 
- スペイン語 (es) 
- スウェーデン語 (sv) 
- タガログ語 (tl) 
- タイ語 (th) 
- トルコ語 (tr) 
- ウクライナ語 (uk) 
- ベトナム語 (vi) 
 
## キャンペーン:
[Campaign (キャンペーン)] セクションを使用して、キャンペーンの名前とカテゴリを設定したり、プッシュ キャンペーンの対象ユーザーを無視してこのキャンペーンを Reach API (と低レベル Push API の一部の要素) 経由で送信するよう選択したりできます。カテゴリをカスタムの通知テンプレートと共に使用すると、定義済みの設定に基づいてアプリ内の通知を管理できます。既存の "カテゴリ" の一覧は、Reach API 経由で取得できます。

> 警告:リーチ キャンペーンの [Campaign (キャンペーン)] セクションで [Ignore Audience, push will be sent to users via the API (対象ユーザーを無視して API 経由でユーザーにプッシュを送信する)] オプションを使用すると、キャンペーンは自動的に送信されず、Reach API 経由で手動で送信する必要があります。
 
![Reach-Campaign3][22]
 
**オプションの適用対象:**

- 名前:    すべて
- Category:    アナウンス、アンケート
- Ignore Audience, push will be sent to users via the API (対象ユーザーを無視して API 経由でユーザーにプッシュを送信する)    すべて
 
## 通知:
[Notification (通知)] セクションを使用して、次のようなプッシュの基本設定を設定できます。プッシュの件名、メッセージ、アプリ内画像、消去可能かどうか。ほとんどの通知設定は、デバイスのプラットフォーム固有です。プッシュを "アプリ内" に送るか、"アプリ外" に送るか、その両方に送るかを選択できます(ユーザーはデバイスのオペレーティング システム レベルで "アプリ外" プッシュを "オプトイン" するか "オプトアウト" にするかを選択でき、Azure モバイル エンゲージメントではこの設定をオーバーライドできません。また、Reach API では "アプリ内" プッシュと "アプリ外" プッシュを処理します。Push API を使用して "アプリ外" プッシュを処理することも可能です)。プッシュは画像や、アプリ外へのリンクやアプリ内の他の場所を示すディープ リンクなどの HTML コンテンツを使用してカスタマイズできます (Android SDK 2.1.0 以降のインテント カテゴリが必要)。アイコンや iOS バッジを変更したり、テキストまたは Web コンテンツ (html コンテンツ付きのポップアップ、アプリ内外の他の場所への URL リンク) を送信したりできます。また、Android デバイスでプッシュの受信時に着信音を鳴らしたりバイブレートさせることもできます(デバイスの着信音を鳴らしたりバイブレートさせたりするには、Android マニフェスト ファイルに対する正しい SDK の権限が必要です)。端末ごとに画面サイズが異なるため、現在 Android の「全体像」サイズの業界標準はありませんが、400ｘ100 の画像がほぼすべての画面サイズに適していると言われています。

## 配信の種類:
-    Out of app only (アプリ外のみ): ユーザーがアプリケーションを使用していないときに通知が配信されます。
- Out of app only の通知の場合、Apple や Google からの証明書 (APNS や GCM 証明書) が必要です。
- In-app only (アプリ内のみ):アプリケーションを実行中にのみ通知が表示されます。
- 通知は Capptain の配信システムを使用します。プッシュの視覚的なレイアウトや表示は自由にカスタマイズできます。
- Anytime (いつでも):このオプションでは、アプリケーションが実行されているかどうかにかかわらず通知が送信されます。

 
![Reach-Campaign4][23]

**オプションの適用対象:**

- 通知:     アナウンス、アンケート
 
## 内容:
[Content (コンテンツ)]セクションを使用して、アナウンス、アンケート、データ プッシュ、タイル (Windows Phone のみ) のコンテンツを変更できます。プッシュ キャンペーンの [Contents (コンテンツ)] 設定は、キャンペーンの種類に固有です。 

**関連項目:**

- <a href="#ReachContent">UI ドキュメント- Reach - プッシュのコンテンツ</a>
 
![Reach-Campaign5][24]

## 対象:
[Audience (対象ユーザー)] セクションを使用して、カスタマイズした条件に基づいてキャンペーンを制限する項目の標準リストを定義できます。対象ユーザーを制限するオプションでは、プッシュの送信先を、新規ユーザー、既存ユーザー、ネイティブ プッシュのユーザーのみ、のいずれかに設定できます。また、プッシュを受信するユーザー数を制限するクォータを設定することも可能です。キャンペーンの式を手動で編集して、対象ユーザーを指定する 1 つ以上の条件を追加してキャンペーンのフィルター方法を決めることができます。対象ユーザーの式は手動で入力できます。この式では、条件間のリレーションシップを明示的に定義する必要があります。条件は、大文字で始まる識別子を使用する必要があり、スペースを含めることはできません。条件間のリレーションシップは、 'and'、 'or'、 'not' 演算子と '(', ')' を使用して記述します。例:"Criterion1 or (Criterion1 and not Criterion2)"

> 注:キャンペーンに多数の対象ユーザーが含まれる場合、特に同時に複数のキャンペーンを開始しようとした場合には、サーバー側のターゲティング スキャンの動作が遅くなることがあります。

- 可能な限り、一度に 1 つのキャンペーンのみを開始するようにしてください。
- 最大でも、一度に開始するキャンペーンは 4 つまでにしてください。
- アクティブなユーザーにのみプッシュを送信するようにすると ([Engage only users who can be reached using Native Push (ネイティブ プッシュでリーチできるユーザーのみを対象にする)] チェック ボックスと [Engage only active users (アクティブなユーザーのみを対象にする)] チェック ボックス)、現時点でアプリをインストール、使用しているユーザーのみをスキャンできます。
対象ユーザーを定義したら、シミュレート ボタンを使用してこの通知の受信者数を確認できます。この操作では、設定した対象ユーザーに当てはまる可能性のある既知のユーザー数が算出されます (ユーザーのランダム サンプルに基づいた概算です)。アプリケーションをアンインストールしたユーザーもこの対象ユーザーに含まれますが、リーチはできませんのでご注意ください。

**関連項目:** 

- <a href="#ReachCriterion">UI ドキュメント - Reach - 新しいプッシュの条件</a>

![Reach-Campaign6][25]

## 式を編集する
 
![Reach-Campaign7][26]
 
**対象ユーザーの制限オプションの適用対象:**

- Engage only a subset of users (ユーザーのサブセットのみを対象にする):    すべて (アナウンス、アンケート、データ プッシュ、タイル)
- Engage only old users (既存ユーザーのみを対象にする):    すべて (アナウンス、アンケート、データ プッシュ、タイル)
- Engage only new users (新規ユーザーのみを対象にする):    すべて (アナウンス、アンケート、データ プッシュ、タイル)
- Engage only idle users (アイドル状態のユーザーのみを対象にする):    アナウンス、アンケート、タイル
- Engage only active users (アクティブなユーザーのみを対象にする):    すべて (アナウンス、アンケート、データ プッシュ、タイル)
- Engage only users who can be reached using Native Push (ネイティブ プッシュでリーチできるユーザーのみを対象にする):     アナウンス、アンケート
 
## 期間:
[Time Frame (期間)] セクションでは、プッシュの送信時期を設定したり、期間を空白にしてすぐにキャンペーンを開始したりできます。エンド ユーザーのタイムゾーンを使用した場合、アジアのエンド ユーザーには予想よりも 1 日早く送信される場合があり、世界中の全タイムゾーンがキャンペーンで設定した期間に入るまで、プッシュは少しずつ送信されます。また、エンド ユーザーのタイムゾーンに設定すると、プッシュの開始前に携帯端末に時刻を要求する必要があるため、キャンペーンに遅れが生じる可能性があります。

> 注:キャンペーンに終了日を設定しない場合、プッシュがローカルに保存され、キャンペーンを手動で終了したあとも表示される可能性があります。このような動作を避けるには、キャンペーンの終了日を指定してください。

**関連項目:**

- [方法 - スケジュール設定][Link 3] 
 
![Reach-Campaign8][27]

**設定の適用対象:**

- 期間:     アナウンス、アンケート、タイル
 
## テスト:
[Test (テスト)] セクションを使用して、キャンペーンを保存する前にこのプッシュを独自のテスト デバイスに送信できます。キャンペーンにカスタムの言語を構成している場合は、各言語でプッシュをテストできます。テスト デバイスは、[My Account (マイ アカウント)] でセットアップできます。
> 注:プッシュを "テスト" するボタンを使用するときは、サーバー側のデータは記録されません。データの記録は、実際のプッシュ キャンペーンでのみ行われます。

**関連項目:**

- [UI ドキュメント - My Account (マイ アカウント)][Link 14]
 
![Reach-Campaign9][28]

## <a name="ReachCriterion"></a>Reach - 新しいプッシュの条件 (対象ユーザーの設定)

[New Criteria (新しい条件)] ボタンを使用して特定の条件を指定することで対象ユーザーを絞り込む方法は、Azure モバイル エンゲージメントの最も重要な概念の 1 つであり、すべてのユーザーにプッシュ通知を送る代わりに、ユーザーに関連するプッシュ通知を送ることで反応を得やすくなります。標準的な条件に基づいて対象ユーザーを限定し、プッシュをシミュレートすることで、通知を受信するユーザー数を確認できます。

**関連項目:**

- <a href="#ReachCampaign">UI ドキュメント - Reach - 新しいプッシュ キャンペーン</a>

## 対象ユーザーの条件には次のようなものを使用できます:

- **テクニカル:** [Analytics (分析)] と [Monitor (モニター)] セクションに表示されるのと同一の技術情報に基づいて対象を絞り込むことができます。**関連項目:** [UI ドキュメント - Analytics][Link 15]、[UI ドキュメント- Monitor][Link 16]
- **位置:** Geo-Fencing を用いた "Rea time location reporting (リアルタイムの位置報告)" を使用するアプリケーションでは、Geo-Location を条件として使用して GPS の位置から対象ユーザーを設定できます。"Lazy Area Location Reporting (大まかなエリアの位置報告)" の呼び出しを使用して携帯電話の位置から対象ユーザーを決定することも可能です ("Real time location reporting" と "Lazy Area Location Reporting" は、SDK からアクティブ化する必要があります)。**関連項目:**[SDK ドキュメント - iOS - 統合][Link 5]、[SDK ドキュメント - Android - 統合][Link 5]
- **Reach のフィードバック:** アナウンス、アンケート、データ プッシュからのリーチ フィードバックを通じて、過去のリーチ通知へのユーザーのフィードバックに基づいて対象ユーザーを設定できます。この方法は、初めてのキャンペーンよりも数回リーチ キャンペーンを行った後に使用することでより的確な対象ユーザーを絞り込むことができます。また、特定の過去のキャンペーンをすでに受信したユーザーにキャンペーンを送信しないよう設定すると、すでに同様のコンテンツの通知を受け取ったユーザーを除外できます。さらに、現在アクティブな特定のキャンペーンに含まれているユーザーを新しいプッシュから除外することも可能です。**関連項目:**「<a href="#ReachContent">UI ドキュメント - Reach - プッシュのコンテンツ</a>」
- **インストールの追跡:** ユーザーがアプリをインストールした場所に基づいて情報を追跡できます。**関連項目:** [UI ドキュメント - 設定][Link 20]
- **ユーザー プロファイル:** 標準のユーザー情報に基づいたターゲティングや、作成したカスタムのアプリ情報に基づいたターゲティングも可能です。これには、過去のキャンペーンへの反応だけでなく、現在ログインしているユーザーと、特定の質問に応答したユーザーが含まれます。アプリで定義したすべてのアプリ情報はこの一覧に表示されます。
- セグメント:複数の条件を含む特定のユーザー操作に基づいて作成したセグメントから対象ユーザーを設定することも可能です。アプリで定義したすべてのセグメントはこの一覧に表示されます。**関連項目:**[UI ドキュメント - セグメント][Link 18]
- **アプリ情報:** カスタムのアプリ情報タグを [Settings (設定)] で作成すると、ユーザーの挙動を追跡できます。**関連項目:**[UI ドキュメント - 設定][Link 20]
## 例: 
アプリ内購入の操作を行ったユーザーのみにアナウンスをプッシュする場合。

1. アプリケーションの設定ページに移動し、[App Info (アプリ情報)] メニューを選択して [New app info (新しいアプリ情報)] を選択します。
2. "inAppPurchase" という新しいブール型のアプリ情報を登録します。
3. ユーザーが正常にアプリ内購入を実行したときにアプリケーションがこのアプリ情報を "true" に設定するようにします (sendAppInfo("inAppPurchase", ...) 関数を使用)。
4. この操作をアプリケーションで行いたくない場合は、device API を使用してバックエンドで行うことができます。
5. その後、アナウンスを作成して、"inAppPurchase" が "true" になっているユーザーのみを対象とする条件を付けます。
 
> 注:アプリ情報のタグ以外の条件に基づいたターゲティングを行う場合は、プッシュの送信前に Azure モバイル エンゲージメントがユーザーのデバイスから情報を収集する必要があるため、送信に遅延が生じる場合があります。複雑なプッシュ構成のオプション (バッジのアップデートなど) の場合も、プッシュに遅延が生じる可能性があります。Push API から "ワンショット" キャンペーンを使用する方法が、Azure モバイル エンゲージメントで最もすばやくプッシュを送信できる方法です。その次にすばやく送信できる方法は、アプリ情報のタグのみをリーチ キャンペーンのプッシュ条件として使用する方法です。これは、アプリ情報のタグはサーバー側に保存されるためです。プッシュ キャンペーンにその他のターゲティング条件を使用する場合は、柔軟性は非常に高くなりますが、キャンペーンの送信に Azure モバイル エンゲージメントからデバイスを照会する必要があるため、送信までに最も時間がかかる方法になります。
 
![Reach-Criterion1][29] 

**条件オプションの適用対象:**

- **テクニカル**     
- ファームウェア名:    ファームウェア名
- ファームウェアのバージョン:    ファームウェアのバージョン
- デバイスのモデル:    デバイスのモデル
- デバイスの製造元:    デバイスの製造元
- アプリケーションのバージョン:    アプリケーションのバージョン
- 通信会社名:    通信会社名、未定義
- 通信会社の国:    通信会社の国、未定義
- ネットワークの種類:    ネットワークの種類
- ロケール:    ロケール
- 画面サイズ:    画面サイズ
- **位置**      
- 最後に検出された場所:    国、地域、市区町村
- リアルタイム ジオフェンシング    POI の一覧 (名前、操作内容)、円形 POI (名前、緯度、経度、半径メートル)
- **Reach のフィードバック**     
- アナウンスのフィードバック:    アナウンス、フィードバック
- アンケートのフィードバック:    アンケート、フィードバック
- アンケートの回答のフィードバック:    アンケートの回答のフィードバック、質問、選択
- データ プッシュのフィードバック:    データ プッシュ、フィードバック
- **インストールの追跡**     
- ストア:    ストア、未定義
- ソース:    ソース、未定義
- **ユーザー プロファイル**     
- 性別:    男性、女性、未定義
- 誕生日:    演算子、日付、未定義
- オプトイン:    true または false、未定義
- **アプリ情報**      
- 文字列:    文字列、未定義
- Date:    演算子、日付、未定義
- 整数:    演算子、数字、未定義
- ブール値:    true または false、未定義
- **セグメント**    
- セグメントの名前 (ドロップダウン リストから)、除外リスト (セグメントに含まれない対象ユーザー)。

## <a name="ReachContent"></a>Reach - プッシュのコンテンツ (キャンペーン種類ごと)
 
新しいキャンペーンの [Contents (コンテンツ)] セクションを使用して、アナウンス、アンケート、データ プッシュ、タイル (Windows Phone のみ) のコンテンツを修正できます。プッシュ キャンペーンの [Contents (コンテンツ)] 設定は、キャンペーンの種類に固有です。 
 
## コンテンツの種類
- アナウンス
- アンケート
- データ プッシュ
- タイル (Windows Phone のみ)
 
## アナウンスのコンテンツ
 
 ![Reach-Content1][30] 

## アナウンスの種類を選択します:
-    通知のみ:シンプルな標準的な通知です。ユーザーがクリックすると、他のビューが表示されず、操作に関連付けられた内容のみが発生します。
-    テキストのアナウンス:ユーザーにテキスト ビューを閲覧するよう促す通知です。
-    Web アナウンス:ユーザーに Web ビューを閲覧するよう促す通知です。

**関連項目:**

- [方法 - アナウンス][Link 3] 

**Web ビューのアナウンスについて:**

ここで指定する HTML コードや JavaScript コードの発生パターン "{deviceid}" は、アナウンスを表示するデバイスの識別子に自動的に置き換えられます。この方法では、Azure モバイル エンゲージメント デバイスの識別子を、バック オフィスでホストする 外部 Web サービスで簡単に取得できます。
全画面表示の Web ビューを作成する場合は (既定の [Action (アクション)] と [Exit (終了) ボタンなし)、Web ビューのアナウンスの JavaScript コードから次の関数を使用できます: 

-    アナウンスの操作を実行する:ReachContent.actionContent()
-    アナウンスを終了する:ReachContent.exitContent()
 
## アクションを選択する:

**アクションの URL について:**

対象デバイスのオペレーティング システムで解釈できる URL はすべて、アクション URL として使用できます。
アプリケーションがサポートする専用の URL (ユーザーを特定の画面にジャンプさせるなど) をアクション URL として使用することも可能です。
発生する {deviceid} パターンはすべて、アクションを実行するデバイスの識別子に自動的に置き換えられます。これを使用して、Azure モバイル エンゲージメントのデバイス識別子を、バック オフィスでホストする外部 Web サービス経由で簡単に取得できます。

- **Android + iOS のアクション**
    - Web ページを開く
    - http://[web-site-domain] 
    - 例:http://www.azure.com
    - メールを送信する
    - mailto:[e-mail-recipient]?subject=[subject]&body=[message] 
    - 例: mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - SMS を送信する
    - sms:[phone-number] 
    - 例: sms:2125551212
    - 電話をかける
    - tel:[phone-number] 
    - 例: tel:2125551212
- **Android のみのアクション**
    - Play Store でアプリケーションをダウンロードする
    - market://details?id=[app package] 
    - 例: market://details?id=com.microsoft.office.word
    - 地理的にローカライズされた検索を開始する
    - geo:0,0?q=[search query] 
    - 例: geo:0,0?q=starbucks,paris
- **iOS のみのアクション**
    - App Store でアプリケーションをダウンロードする
    - http://itunes.apple.com/[country]/app/[app_name]/id[app_id]?mt=8 
    - 例:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Windows のアクション
    - Web ページを開く
    - http://[web-site-domain] 
    - 例:http://www.azure.com
    - メールを送信する
    - mailto:[e-mail-recipient]?subject=[subject]&body=[message] 
    - 例: mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - SMS を送信する (Skype Store アプリが必要)
    - sms:[phone-number] 
    - 例: sms:2125551212
    - 電話をかける (Skype Store アプリが必要)
    - tel:[phone-number] 
    - 例: tel:2125551212
    - Play Store でアプリケーションをダウンロードする
    - ms-windows-store:PDP?PFN=[app package ID] 
    - 例: ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Bingmaps 検索を開始する
    - bingmaps:?q=[search query] 
    - 例: bingmaps:?q=starbucks,paris
    - カスタムのスキームを使用する
    - [custom scheme]://[custom scheme params] 
    - 例: myCustomProtocol://myCustomParams
    - パッケージ データを使用する (拡張機能読み取りのための Store App が必要)
    - [folder][data].[extension] 
    - 例: myfolderdata.txt
 
## 追跡用 URL を構築する:
-    <UI ドキュメント> の「設定」セクションで、ユーザーが他のアプリケーションをダウンロードできるようにする追跡用の URL を構築する方法をご覧ください。
 
## アナウンスのテキストを定義する
アナウンスの件名、コンテンツ、ボタンのテキストを入力します。 
このキャンペーンに対するユーザーの反応のリーチ フィードバックに基づいて、今後のキャンペーンの対象ユーザーを決めることができます。対象ユーザーの設定は、このキャンペーンはプッシュされただけか、応答があったか、アクションはあったか、終了されたかといったフィードバックに基づいて行うことができます。

**関連項目:**
- <a href="#ReachCriterion">UI ドキュメント - Reach - 新しいプッシュの条件</a>

## アンケートのコンテンツ
 
![Reach-Content2][31] 
アナウンスの件名、説明、ボタンのテキストを入力します。 
次に、質問と質問への回答の選択肢を追加します。
このキャンペーンに対するユーザーの反応のリーチ フィードバックに基づいて、今後のキャンペーンの対象ユーザーを決めることができます。対象ユーザーの設定は、このキャンペーンはプッシュされただけか、応答があったか、アクションはあったか、終了されたかに基づいて行うことができます。また、質問への回答の選択内容を条件として使用して、アンケートの回答フィードバックに基づいて対象ユーザーを設定することも可能です。

**関連項目:**

- <a href="#ReachCriterion">UI ドキュメント - Reach - 新しいプッシュの条件</a>
 
## データ プッシュのコンテンツ
 
![Reach-Content3][32] 

## データの種類を選択します
- テキスト
- バイナリ データ
- Base64 データ

## データのコンテンツを定義します
- テキスト データのプッシュを選択した場合は、[Contents (コンテンツ)] ボックスにテキストをコピーして貼り付けます。
- バイナリ データや base64 データのプッシュを選択した場合は、[upload your file (ファイルをアップロード)] ボタンを使用してファイルをアップロードします。
- このキャンペーンに対するユーザーの反応のリーチ フィードバックに基づいて、今後のキャンペーンの対象ユーザーを決めることができます。対象ユーザーの設定は、このキャンペーンはプッシュされただけか、応答があったか、アクションはあったか、終了されたかに基づいて行うことができます。

**関連項目:**

- <a href="#ReachCriterion">UI ドキュメント - Reach - 新しいプッシュの条件</a>

## タイルのコンテンツ (Windows Phone のみ)

![Reach-Content4][33]

## タイルのコンテンツを定義します
タイルのペイロードは、Windows Phone デバイスでアプリのタイルに表示されるテキストです。
タイルのプッシュは、Windows Phone のネイティブ プッシュの Microsoft Push Notification Service (MPNS) バージョンです。タイル プッシュの種類は唯一、応答のない種類であるため、タイル プッシュ キャンペーンの結果に基づいて今後のキャンペーンの対象ユーザーは設定できません。 

**関連項目:**

- [API ドキュメント - Reach API - ネイティブ プッシュ][Link 4]

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
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
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
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
