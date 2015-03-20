<properties 
   pageTitle="Azure モバイル エンゲージメント関連のトラブルシューティング ガイド" 
   description="Azure モバイル エンゲージメント関連のトラブルシューティング ガイド" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure モバイル エンゲージメント - トラブルシューティング ガイド

## はじめに

次のトラブルシューティング ガイドは、Azure モバイル エンゲージメントに関する最も一般的な問題をカバーしています。だたし、[請求] に関する質問や、[Link 11] [プレビュー] へのサインアップのサポートは含まれません[Link 7]。トラブルシューティング ガイドで問題を解決できない場合は、Azure サービス要求を開く ([Azure サポート契約](http://azure.microsoft.com/support/options/)がある場合) か、[MSDN フォーラム] でサポートを要求ができます[Link 8]。問題を調査するテクニカル サポートのサービス要求を開く場合は、<a href="#SR" title="SR">SR 情報</a>のすべてのトラブルシューティング情報を記入してください。

## <a name="#ANALYTICS">分析、監視、セグメント化、ダッシュ ボード</a>

Azure モバイル エンゲージメントが、アプリケーション、デバイス、ユーザーの情報を収集する方法に関する問題です。

**症状の一覧:**

1. <a href="#ANALYTICS1">情報の不足 / 遅延</a>
2. <a href="#ANALYTICS2">UI で項目が見つからない</a>
3. <a href="#ANALYTICS3">トラブルシューティングのクラッシュ</a>
 
<a name="#ANALYTICS1">**症状:**
1.    情報の不足 / 遅延:</a>

- 分析、セグメント化、ダッシュ ボードに表示される際に情報が遅延します。
- 監視によって情報が失われます。
- 分析、セグメント化、ダッシュ ボードによって情報が失われます。
- セグメント化が限界に達しています。

**原因:**

- 分析 API、監視 API、セグメント API を使用して、UI で失われたデータが API で表示できるかどうかを確認できます。
- Azure モバイル エンゲージメント SDK がアプリに正しく統合されていない場合は、分析、セグメント化、監視、またはダッシュ ボードで情報を表示できません。
- セグメントは一度作成すると変更できませんが、「複製」(コピー) または「破棄」(削除) することはできます。
セグメントには、10 の条件のみを含めることができます。
- 監視によって失われた情報をテストする最適な方法 (テスト デバイスのセットアップ、テスト デバイスのアプリのアンインストール / 再インストール)。
- 分析、セグメント化、ダッシュ ボードに関する情報は、24 時間ごとに更新されます。
- 新しいセグメントの情報は、(セグメントが前の情報に基づいている場合でも) 作成後 24 時間は表示されません。
- UI の分析データのフィルター処理は、アプリのバージョンにかかわらず、この型のすべての例が表示されます (たとえば、名前でフィルター処理された「Crashes」は、バージョン 1 とバージョン 2 のアプリで表示されます)。
- 分析期間はユーザーのデバイス設定の日付に基づくため、電話の日付が正しく設定されていないユーザーには、誤った期間が表示されることがあります。
- サーバー側のデータは、ボタンを使用してプッシュをテストする場合には記録されません。実際のプッシュ キャンペーンの場合のみデータがログに記録されます。

**関連項目:**

- [トラブルシューティング ガイド - SDK][Link 2]、[API ドキュメント][Link 4]、[UI ドキュメント - セグメント][Link 1]

<a name="#ANALYTICS2">**症状:**
2. UI で項目が見つからない:</a>

- 特定の組み込みアプリまたはカスタム アプリの情報タグの条件に基づいて、セグメントを作成できません。
- 分析、監視、ダッシュ ボードで、組み込みアプリまたはカスタム アプリの情報タグの条件が見つかりません。
- 分析、監視、セグメント化、ダッシュ ボードでデータを解析できません。

**原因:**

- 一部の組み込みアイテムやアプリ情報タグは、プッシュ条件としてのみ利用できますが、セグメントに追加したり、分析、監視、ダッシュ ボードから表示したりすることはできません。 
- セグメントに追加できない組み込みアイテムとアプリ情報タグの場合は、セグメントに基づく対象と同じ機能を実行するように、各キャンペーンの対象条件の一覧を設定する必要があります。
- 詳細については、Azure モバイル エンゲージメント UI の分析、監視、セグメント化、ダッシュ ボードの各セクションにあるコンテキスト メニューをご覧ください。

**関連項目:**

- [UI ドキュメント - 対象ユーザーに新しいプッシュ条件を通知する][Link 1]
 
<a name="#ANALYTICS3">**症状:**
3. トラブルシューティングのクラッシュ:</a>

- アプリケーションのクラッシュが分析、監視、ダッシュ ボードに表示されます。

**原因:**

- 分析、監視、ダッシュ ボードに表示されるアプリケーションのクラッシュをトラブルシューティングするには、以前のバージョンの SDK に関する既知の問題についてのリリース ノートをご確認ください。
- アプリケーションのクラッシュをさらにトラブルシューティングするには、インストールされているアプリケーションを使用してテスト デバイスからイベントを実行し、Azure モバイル エンゲージメント UI の [Monitor - Events (監視 - イベント)] セクションでデバイス ID を検索します。次に、アプリケーションをクラッシュさせたイベントを実行し、Azure モバイル エンゲージメント UI の [Monitor - Crash (監視 - クラッシュ)] セクションで追加情報を検索します。 

**関連項目:**

- [概念 - よく寄せられる質問][Link 6]、[概念 - 用語集][Link 6]、[UI ドキュメント][Link 1]、[SDK ドキュメント - リリース ノート][Link 5]、[SDK ドキュメント - アップグレード ガイド][Link 5]、[方法 - デバイス ID の登録][Link 3]

## <a name="#APIS">API</a>

管理者が、API を使用して Azure モバイル エンゲージメントを操作する方法に関する問題です。

**症状の一覧:**

1. <a href="#APIS1">構文の問題</a>
2. <a href="#APIS2">Azure モバイル エンゲージメント UI で使用できるアクションと同じアクションを、API を使用して実行できません</a>
3. <a href="#APIS3">エラー メッセージ</a>
4. <a href="#APIS4">サイレント障害</a>
 
<a name="#APIS1">**症状:**
1. 構文の問題:</a>

- 構文エラーは API を使用して (または予期しない動作によって) 発生します。

**原因:**

- 構文の問題:
    - 使用している特定の API の構文を調べて、オプションを使用できることをご確認ください。
    - API の使用方法に関する一般的な問題は、リーチ API とプッシュ API を混同することです (多くのタスクは、プッシュ API の代わりにリーチ API を使用して実行する必要があります)。 
    - もう 1 つの SDK の統合と API の使用方法に関する一般的な問題は、SDK キーと API キーを混同することです。
    - API に接続するスクリプトは、少なくとも 10 分ごとにデータを送信する必要があります。これを行わないと、接続はタイムアウトします (特に、データを待機している監視 API では一般的です)。タイムアウトを回避するために、スクリプトは 10 分ごとに XMPP ping を送信してサーバーとのセッションを維持します。

**関連項目:**
 
- [概念 - 用語集][Link 6]、[API ドキュメント][Link 4]、[XMPP プロトコル情報]( http://xmpp.org/extensions/xep-0199.html)
 
<a name="#APIS2">**症状:**
2. Azure モバイル エンゲージメント UI で使用できるアクションと同じアクションを、API を使用して実行できません。</a>

- Azure モバイル エンゲージメント UI から実行するアクションは、関連する Azure モバイル エンゲージメント API では機能しません。

**原因:**

- Azure モバイル エンゲージメント UI から同じアクションを実行できることを確認すると、Azure モバイル エンゲージメントのこの機能と SDK が正しく統合されていることが分かります。

**関連項目:**
 
- [UI ドキュメント][Link 1]
 
<a name="#APIS3">**症状:**
3. エラー メッセージ:</a>

- エラー コードは、実行時またはログ中に表示される API を使用します。

**原因:**

- 次に、参照と事前のトラブルシューティングのために、一般的な API 状態コード番号の一覧を示します。

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response's body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently "in progress", campaign must be "in progress" and the campaign's property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn't retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504         The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

**関連項目:**

- [API ドキュメント - 特定の API に関する詳細なエラー情報][Link 4]
 
<a name="#APIS4">**症状:**
4. サイレント障害:</a>

- API のアクションは、実行時またはログ中にエラー メッセージが表示されずに失敗します。

**原因:**

- 正しく統合されていない場合は、Azure モバイル エンゲージメント UI の多くの項目が無効になりますが、API からエラー メッセージが表示されません。実行していることを確認するには、UI から同じ機能をテストしてください。
- Azure モバイル エンゲージメントと、使用する Azure モバイル エンゲージメントの多くの高度な機能を、事前に別の手順で SDK を使用して個別にアプリと統合する必要があります。

**関連項目:**

- [トラブルシューティング ガイド - SDK][Link 2]、[SDK ドキュメント][Link 5]
 
## <a name="#PUSH">プッシュ / リーチ</a>

Azure モバイル エンゲージメントがユーザーに情報を送信する方法に関する問題です。
 
**症状の一覧:**

1. <a href="#PUSH1">プッシュ エラー</a>
2. <a href="#PUSH2">プッシュ テストの問題</a>
3. <a href="#PUSH3">プッシュ カスタマイズの問題</a>
4. <a href="#PUSH4">プッシュ対象の問題</a>
5. <a href="#PUSH5">プッシュのスケジュール設定</a>
 
<a name="#PUSH1">**症状:**
1. プッシュ エラー:</a>

- プッシュが機能しません (アプリ内、アプリ外、または両方で)。

**原因:**

- プッシュ エラー:
    - Azure モバイル エンゲージメント、リーチ、Azure モバイル エンゲージメントの他の高度な機能が正しく統合されていない、または新しい OS やデバイス プラットフォームに関する既知の問題を解決するために SDK をアップグレードする必要がある、というプッシュ エラーが何度も表示されます。
    - In App プッシュまたは Out of App プッシュに問題があるかどうかを判断するために、In App プッシュのみと Out of App プッシュのみをテストします。
    - トラブルシューティングの手順として、UI と API の両方からテストし、両方の場所に含まれている追加のエラー情報を確認します。
    - Azure モバイル エンゲージメントとリーチが両方とも SDK に統合されていない場合、Out of App プッシュは機能しません。
    - 証明書が有効でない場合、または本番用と開発用の証明書を正しく使用していない場合、Out of App プッシュは機能しません (iOS のみ)。
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

**関連項目:**

- [トラブルシューティング ガイド - SDK][Link 2]、[トラブルシューティング ガイド - プッシュ][Link 2]、[SDK ドキュメント - iOS - Apple プッシュ通知用アプリケーションを準備する方法][Link 5]
 
<a name="#PUSH2">**症状:**
2. プッシュ テストの問題:</a>

- プッシュは、デバイス ID に基づいて特定のデバイスに送信されます。

**原因:**

- テスト デバイスは、プラットフォームによって異なる方法でセットアップされますが、テスト デバイス上のアプリでイベントを発生させ、ポータル内でデバイス ID を検索することで、すべてのプラットフォームでデバイス ID を検索する機能があります。
- テスト デバイスは、IDFV と IDFA で動作が異なります (iOS のみ)。

**関連項目:**

- [UI ドキュメント - リーチ][Link 1]
 
<a name="#PUSH3">**症状:**
3. プッシュ カスタマイズの問題:</a>

- 高度なプッシュ コンテンツ項目が機能しません (バッジ、着信音、バイブレーション、画像など)。
- プッシュからのリンクが機能しません (Out of App、In App、Web サイト、アプリ内の場所へのリンク)。
- プッシュの統計情報は、期待した数の人にプッシュが送信されなかったことを示しています (多すぎるか不十分です)。
- プッシュが 2 回複製され受信されました。
- Azure モバイル エンゲージメント プッシュのテスト デバイスを登録できません (自分の本番用または開発用アプリで)。

**原因:**

- アプリの特定の場所にリンクするには「カテゴリ」が必要です (Android のみ)。
- 外部の画像サーバーは、大きい画像のプッシュが機能するように、HTTP "GET"と "HEAD" を使用できる必要があります (Android のみ)。
- コードを使用して、Azure モバイル エンゲージメント エージェントをキーボードを開いたときに無効にし、閉じたときにアクティブできるため、キーボードは通知の表示には影響しません (iOS のみ)。
- 一部の項目は、テスト シミュレーションでは機能せず、実際のキャンペーンでのみ機能します (バッジ、着信音、バイブレーション、画像など)。
- サーバー側のデータは、ボタンを使用してプッシュをテストする場合に記録されません。実際のプッシュ キャンペーンの場合のみデータがログに記録されます。
- 問題の特定に役立てるため、テスト、シミュレーション、実際のキャンペーンを使用してトラブルシューティングしますが、これはそれぞれの機能が少し異なるためです。

**関連項目:**

- [方法 - プッシュ][Link 3]、[トラブルシューティング ガイド - プッシュ][Link 2]、[HTTP プロトコル情報]( http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
 
<a name="#PUSH4">**症状:**
4. プッシュ対象の問題:</a>

- 組み込みの対象が適切に機能しません。
- アプリ情報タグの対象が適切に機能しません。
- 位置情報の対象が適切に機能しません。
- 言語オプションが適切に機能しません。

**原因:**

- プッシュの対象:
    - Azure モバイル エンゲージメント UI または API を使用してアプリ情報タグをアップロードしていることを確認します。
    - アプリケーション レベルでプッシュ速度やプッシュ クォータを調整したり、キャンペーン レベルで対象ユーザーを制限したりすることで、他の対象条件を満たしている場合でも、特定のプッシュを受信できないようにします。 
    - 「言語」の設定は、国やロケールに基づく対象とは異なります。また、電話や GPS の位置に基づく位置情報からの対象とも異なります。
    - 「既定の言語」のメッセージは、デバイスを指定の第 2 言語に設定していないすべての顧客に送信されます。

**関連項目:**

- [UI ドキュメント - リーチ][Link 1]、[UI ドキュメント - 設定][Link 1]、[API ドキュメント - リーチ][Link 4]、[API ドキュメント - プッシュ][Link 4]、[API ドキュメント - デバイス][Link 4]
 
<a name="#PUSH5">**症状:**
5. プッシュのスケジュール設定:</a>

- プッシュのスケジュール設定が適切に機能しません (送信が早すぎるか遅延します)。

**原因:**

- タイム ゾーンは、特にエンド ユーザーのタイム ゾーンを使用する場合にスケジュールに関する問題が生じます。
- 高度なプッシュ機能がプッシュを遅らせることがあります。
- 電話設定 (アプリ情報タグの代わり) に基づく対象は、Azure モバイル エンゲージメントがプッシュを送信する前にリアルタイムで電話からデータを要求する必要があるため、プッシュを遅らせることがあります。
- 終了日を設定せずに作成されたキャンペーンは、プッシュをデバイス上にローカルに保存するため、手動でキャンペーンを終了させても、次回アプリを開いたときにそのプッシュを表示します。
- 2 つ以上のキャンペーンを同時に開始すると、ユーザー ベースのスキャンに時間がかかる場合があります (一度に 1 つのキャンペーンのみを最大で 4 回開始してみます。また、アクティブなユーザーのみを対象にすると、古いユーザーをスキャンする必要がありません)。
- リーチ キャンペーンの [キャンペーン] セクションにある [Ignore Audience, push will be sent to users via the API (対象ユーザーを無視し、API 経由でユーザーにプッシュを送信)] オプションを使用する場合、キャンペーンは自動的に送信されません。リーチ API を使用して手動で送信する必要があります。
- リーチのカスタム カテゴリを使用してアプリ内通知を表示する場合は、通知の正しいライフサイクルに従う必要があります。そうしないと、ユーザーが通知を閉じたときに、通知が消去されないことがあります。

**関連項目:**

- [方法 - スケジュール設定][Link 3]、[UI ドキュメント - リーチの新しいプッシュ キャンペーン][Link 1]
 
## <a name="#SERVICE">サービス</a>

Azure モバイル エンゲージメントの実行方法に関する問題。

**症状の一覧:**

1. <a href="#SERVICE1">サービスの停止</a>
2. <a href="#SERVICE2">接続と誤った情報に関する問題</a>
3. <a href="#SERVICE3">機能に関する要求</a>
 
<a name="#SERVICE1">**症状:**
1. サービスの停止:</a>

**原因:**

- Azure モバイル エンゲージメント サービスの停止に起因すると思われる問題は、いくつかの異なる問題が原因で発生します。
    - すべての Azure モバイル エンゲージメントに元からシステム的に表れる単独の問題
    - サーバーの停止に起因する既知の問題 (サーバー ステータスに表示されないことがあります)。
o スケジュールの遅延、ターゲティングのエラー、バッジ更新に関する問題、統計情報収集の停止、プッシュ機能の停止、API 機能の停止、新しいアプリまたはユーザーを作成できない、DNS エラー、デバイス上の UI、API、アプリのタイムアウト エラー。
1.    クラウドの依存関係の停止
<Azure Service Status><Amazon Web Services (AWS) Status>
2.    プッシュ通知サービス (PNS) の依存関係の停止
<Google - Service><Apple - Service><Android - Google GCM><Android - Amazon ADM><Apple - APNS><Windows Phone - WNS><Windows Phone - MPNS><Windows - WNS>
3.    アプリ ストアの停止
<GooglePlay><iTunes><Windows Phone Store><Windows Store>

    - クラウドの依存関係の停止
[Azure サービスの状態]( http://azure.microsoft.com/status/)、[Amazon Web Services (AWS) の状態]( http://status.aws.amazon.com/) 
    - プッシュ通知サービス (PNS) の依存関係の停止
[Google - サービス](http://www.google.com/appsstatus#hl=en&v=status)、[Apple - サービス]( http://www.apple.com/support/systemstatus/)、[Android - Google GCM]( http://developer.android.com/google/gcm/index.html)、[Android - Amazon ADM]( https://developer.amazon.com/appsandservices/apis/engage/device-messaging)、[Apple - APNS]( https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)、[Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx)、[Windows Phone - MPNS](http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx)、[Windows - WNS](https://developer.windows.com/)
    - アプリ ストアの停止
[GooglePlay](https://play.google.com/)、[iTunes](http://www.apple.com/itunes/charts/)、[Windows Phone ストア](http://www.windowsphone.com/)、[Windows ストア](http://windows.microsoft.com/)

*問題がシステム的かどうかをテストするには、次のような異なる方法で同じ機能をテストできます。*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*問題が UI または API にのみ影響するかどうかをテストします。*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[API ドキュメント][Link 4]、[UI ドキュメント][Link 1]
	
*携帯電話ネットワークに問題があるかどうかをテストします。*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*デバイスに問題があるかどうかをテストします。*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[UI ドキュメント - 設定][Link 1]

*アプリに問題があるかどうかをテストします。*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

エンド ユーザー デバイスへの OS のアップグレードに問題があるかどうかをテストします。解決するには、SDK のアップグレードが必要です。

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[トラブルシューティング ガイド - SDK][Link 2]
 
<a name="#SERVICE2">**症状:**
2. 接続と誤った情報に関する問題:</a>

- Azure モバイル エンゲージメント UI にログインする際の問題。
- Azure モバイル エンゲージメント API との接続エラー。
- デバイス API を使用してアプリ情報タグをアップロードする際の問題。
- Azure モバイル エンゲージメントからログをダウンロードまたはデータをエクスポートする際の問題。
- 正しくない情報が Azure モバイル エンゲージメント UI に表示される。
- 正しくない情報が Azure モバイル エンゲージメント ログに表示される。

**原因:**

- Azure モバイル エンゲージメントとの接続の問題:
    - ユーザー アカウントがタスクを実行するのに十分な権限があることを確認します。
    - 問題が 1 台のコンピューターまたはローカル ネットワークに分離されてないことを確認します。
    - Azure モバイル エンゲージメント サービスで障害が報告されていないことを確認します。
    - アプリ情報タグのファイルがすべてのルールに従っていることを確認します。
        - UTF8 文字セットのみを使用します (ANSI 文字セットはサポートされていません)。
        - 区切り文字としてコンマ「,」を使用します (サービス要求を開いて、.csv の区切り文字をコンマ「,」からセミコロン「;」などの別の文字に変更するように要求できます)。
        - ブール値の「TRUE」と「FALSE」には、すべて大文字を使用します。
        - 最大ファイル サイズの 35 MB よりも小さいファイルを使用します。

**関連項目:**

[API ドキュメント][Link 4]、[UI ドキュメント - ホーム][Link 1]
 
<a name="#SERVICE3">**症状:**
3. 機能に関する要求:</a>

- 使用したい機能が、まだ Azure モバイル エンゲージメントに存在しません。

**原因:**

まだ存在していない Azure モバイル エンゲージメントの新機能を提案するには、
    - サービス要求を開き、Azure モバイル エンゲージメントで実現したい新機能について、できるだけ詳しく記入します。
 
## <a name="#SDK">SDK</a>

Azure モバイル エンゲージメントをアプリケーションに統合する方法に関する問題。

**症状の一覧:**

1. <a href="#SDK1">アプリケーションの別領域の障害で検出された Azure モバイル エンゲージメント SDK に関する問題</a>
2. <a href="#SDK2">高度なコーディングの問題</a>
3. <a href="#SDK3">アプリケーション クラッシュの問題</a>
4. <a href="#SDK4">アプリ ストアのアップロード エラー</a>
 
<a name="#SDK1">**症状:**
1. アプリケーションの別領域の障害で検出された Azure モバイル エンゲージメント SDK に関する問題:</a>

- UI データ収集エラー (分析、監視、セグメント化、ダッシュ ボード)。
- プッシュ エラー (プッシュはアプリ内、アプリ外、または両方で機能しません)。
- 高度な機能のエラー (追跡、位置情報、プラットフォーム固有のプッシュが機能しません)。
- API エラー (多くの場合、API はエラー メッセージを表示せずに失敗します)。
- サービス エラー (Azure モバイル エンゲージメントでアプリケーションを使用できません)。

**原因:**

- Azure モバイル エンゲージメント SDK で解決する必要がある多くの問題が、アプリケーションの障害で検出されます (UI データ収集エラー、プッシュ エラー、高度な機能のエラー、API エラー、アプリケーション クラッシュ、見かけ上のサービス停止など)。  
- Azure モバイル エンゲージメントの特定の機能を、以前にアプリで使用したことがない場合は、統合を完了する必要があります。 
- 動作していた Azure モバイル エンゲージメントの特定の機能が停止した場合は、最新バージョンの Azure モバイル エンゲージメント SDK にアップグレードする必要があります。Azure モバイル エンゲージメントでサポートされているプラットフォーム (Android、iOS、Web、Windows、Windows Phone) によって、Azure モバイル エンゲージメント SDK のバージョンが異なりますので注意してください。

* SDK の統合 * 

- Azure モバイル エンゲージメントが SDK に正しく統合されていません (分析)。
- リーチが SDK に正しく統合されていません (In App プッシュと Out of App プッシュ)。
- 証明書の期限が切れているか、または本番用と開発用の証明書が正しくありません (iOS のみ)。
- GCM または ADM が SDK に正しく統合されていません (Android のみ - サービス固有のプッシュ)。
- 追跡が SDK に正しく統合されていません (ストア追跡のインストール)。
- 遅延位置または GPS 位置が SDK に正しく統合されていません (位置情報による対象化)。
[SDK ドキュメント - 統合ガイド][Link 5]、[トラブルシューティング ガイド - プッシュ][Link 2]

*SDK のアップグレード:*

- 古いバージョンの SDK に関する問題を解決するには、SDK をアップグレードする必要があります (多くの場合、新しいバージョンのデバイス OS に関連します)。
- デバイスが最新バージョンのアプリを使用していることを確認するには、以前のバージョンのアプリをデバイスからすべてアンインストールし、最新バージョンのアプリを再インストールし、Azure モバイル エンゲージメント UI からデバイス ID を再登録します。
[SDK ドキュメント - リリース ノート](http://go.microsoft.com/fwlink/?LinkId= 525554)、[SDK ドキュメント - アップグレード ガイド](http://go.microsoft.com/fwlink/?LinkId= 525554)、[UI ドキュメント - 設定][Link 1]

*SDK その他:*

- Azure モバイル エンゲージメントに関連するコードのセクション内のエラーが原因で、Azure モバイル エンゲージメントが機能しなくなる可能性があります。
- アプリケーション マニフェスト "AndroidManifest.xml" のエラーが原因で、Azure モバイル エンゲージメントが機能しなくなる可能性があります (Android のみ)。
- SDK の統合と 
- API の使用に共通する問題は、SDK キーと API キーを混同することです。
[概念 - 用語集][Link 6]
 
<a name="#SDK2">**症状:**
2. 高度なコーディングの問題:</a>

-  Azure モバイル エンゲージメントに直接関連しないプラットフォーム固有のコードが原因で、iOS、Android、Windows Phone で問題が発生する可能性があります。

**原因:**

- Azure モバイル エンゲージメントに関する多くの高度なコーディングの問題は、Azure モバイル エンゲージメントに直接関連しない不適切に記述されたプラットフォーム固有のコードが原因です。Azure モバイル エンゲージメントのドキュメント (Android、iOS、Web、Windows、Windows Phone) だけでなく、開発しているプラットフォーム固有のドキュメントを参照する必要があります。
- 「カテゴリ」を正しく構成しないと、通知からアプリ内外の別の場所にリンクできません (Android のみ)。 
- iOS コードで、[UIKit.framework] を [optional (任意)] に設定しないと、[Symbol not found error (シンボルが見つかりませんでした)] というエラーが表示され、古い iOS デバイスがクラッシュします (iOS のみ)。
- 証明書の期限が切れているか、開発用または本番用の証明書を正しく使用していないと、プッシュ問題を引き起こします (iOS のみ)。
- Azure モバイル エンゲージメントが制御できない、プラットフォーム固有のいくつかの制限があります (システム センターが Android や iOS の Out of App プッシュを操作する方法など)。
- Azure モバイル エンゲージメントは、参照用に iOS と Android の Azure モバイル エンゲージメントで使用される内部パッケージの完全な一覧を発行します。Azure モバイル エンゲージメントの一部の機能はプラットフォームに固有であることに注意してください (Android、iOS、Web、Windows、Windows Phone)。
- 各プラットフォームの SDK は、次のプログラミング言語で記述されています。
    -     Android SDK は、Java で記述されています
    -     iOS SDK は、Objective C で記述されています
    -     Web SDK は、JavaScript で記述されています
    -     Windows SDK は、C# と JavaScript で記述されています
    -     Windows Phone SDK は、C# と JavaScript で記述されています

**関連項目:**

 - [トラブルシューティング ガイド - プッシュ][Link 2]、[SDK ドキュメント - リリース ノート][Link 5]、[SDK ドキュメント - アップグレード ガイド][Link 5]、[SDK ドキュメント - Android - Azure モバイル エンゲージメントのテクニカル ドキュメントの概要][Link 5]、[SDK ドキュメント - iOS - Azure モバイル エンゲージメントのテクニカル ドキュメントの概要][Link 5]、[SDK ドキュメント - iOS - Apple プッシュ通知用アプリケーションを準備する方法][Link 5]、[Android の開発者](https://developer.android.com/)、[iOS 開発者](https://developer.apple.com/)、[Windows 開発者](https://developer.windows.com/) 
 
<a name="#SDK3">**症状:**
3.    アプリケーション クラッシュの問題</a>

- エンド ユーザーのデバイスで、アプリケーションがクラッシュします。

**原因:**

- クラッシュ情報は、分析 UI または分析 API に表示されます。
- エンドユーザーがクラッシュの原因を特定できるように、テスト デバイスのデバイス ID を検索し、アプリケーションがクラッシュする原因となった操作を同様に実行できます。
- アプリケーションをクラッシュさせた Azure モバイル エンゲージメント SDK の既知の問題は、最新バージョンの SDK にアップグレードすることで解決する場合があります。クラッシュを調査する際にプラットフォームのリリース ノートをご確認ください。

**関連項目:**

- [概念 - よく寄せられる質問][Link 6]、[概念 - 用語集][Link 6]、[API ドキュメント - 分析 API - クラッシュ][Link 4]、[UI ドキュメント - 分析 - クラッシュ][Link 1]、[UI ドキュメント - 設定][Link 1]、[SDK ドキュメント - リリース ノート][Link 5]、[SDK ドキュメント - アップグレード ガイド][Link 5]

<a name="#SDK4">**症状:**
4. アプリ ストアのアップロード エラー</a>

- 最新バージョンのアプリを ITunes、GooglePlay、Windows、または Windows Phone の各ストアにアップロードする際にエラーが発生します。

**原因:**

- アプリ ストアは特定の機能が有効になっているアプリをブロックする場合があります (iTunes ストアではストア内のアプリでの IDFV の使用を禁止しており、GooglePlay ストアではアプリ間でのアプリケーション情報の共有を禁止しています)。 
- アプリをストアにアップロードできなかった場合は、プラットフォームと現在のバージョンの SDK のリリース ノートをご確認ください。

**関連項目:**

- [SDK のドキュメント - リリース ノート][Link 5]、[SDK ドキュメント - アップグレード ガイド][Link 5] 

## <a name="#SR">SR トラブルシューティング情報</a>

Azure モバイル エンゲージメント サービス要求を開いたら、次の情報を記入してください。
 
**ID:問題に関連する適切な ID を記入してください。**

    - App ID
    - Campaign ID
    - Device ID
    - User ID
    - Username
    - App Info Tag
 
**エラー:問題に関連する適切なエラー情報を記入してください。**

    - The name of the API or UI section where the issue occurs
    - The text of any error message you receive
    - The results of any tests you have performed from the troubleshooting guides
 
-    [トラブルシューティング ガイド](http://go.microsoft.com/fwlink/?LinkId=524382)


**コード:問題に関連する適切なコーディング情報を記入してください。**

    - The SDK version and platform of your app (Android SDK 2.4.1, iOS 1.16.2, etc.)
    - The download location of your production app (or the APK/TGZ files of your development app)
    - The "AndroidManifest.xml" and/or any code snippet from your app related to Azure Mobile Engagement (for advanced troubleshooting)

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
