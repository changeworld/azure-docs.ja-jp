---
title: Azure Communication Services - 通話ログ (プレビュー)
titleSuffix: An Azure Communication Services concept document
description: Azure Monitor の通話概要と通話診断のログの詳細について
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 7cf307fc3490af7f3517606932c1277cd58abcfa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716483"
---
# <a name="call-summary-and-call-diagnostic-logs-preview"></a>通話概要と通話診断のログ (プレビュー)

> [!IMPORTANT]
> 以下では、[Azure Monitor](../../../azure-monitor/overview.md) を使用して有効化するログについて言及しています (「[FAQ](../../../azure-monitor/faq.yml)」も参照)。 Communications Services 用にこれらのログを有効化する方法については、[診断設定でのログの有効化](./enable-logging.md)に関するページを参照してください。


## <a name="data-concepts"></a>データの概念
音声とビデオを使用した Communications Services 内での通話に固有のデータ概念について、以下で概要を説明します。ログにキャプチャされたデータの意味を理解するためには、以下の内容を確認することが重要です。

### <a name="entities-and-ids"></a>エンティティと ID

"*通話*" は、データで表されるエンティティに関連しており、`correlationId` によって表される抽象化です 。 `CorrelationId` は通話ごとに一意であり、 `callStartTime` と `callDuration` によって時間的に拘束されます。 すべての通話は、2 つ以上の "*エンドポイント*" からのデータを含むイベントです。これは、通話の中のさまざまな人間、ボット、またはサーバーの参加者を表します。

"*参加者*" (`participantId`) は、通話が "*グループ*"通話である場合にのみ存在します。これは、エンドポイントとサーバー間の接続を表します。 

"*エンドポイント*" は、`endpointId` で表される最も一意のエンティティです。 `EndpointType` は、エンドポイントが人間のユーザー (PSTN、VoIP) を表しているのか、ボット (Bot) を表しているのか、あるいは通話内の複数の参加者を管理しているサーバーを表しているのかを示しています。 `endpointType` が `"Server"` のとき、エンドポイントには一意の ID が割り当てられません。 `endpointType` と、`endpointId` の数を確認することで、通話に参加しているユーザーと他の人間以外の参加者 (ボット、サーバー) の数を常に把握できます。 ネイティブ SDK (Android Calling SDK など) では、同じ `endpointId` を複数の通話のユーザーに対して再利用するため、セッション間のエクスペリエンスを把握できます。 これは、新しい通話ごとに常に新しい `endpointId` を生成する Web ベースのエンドポイントとは異なります。

*Stream* は、方向 (受信/送信) と `mediaType` (オーディオ、ビデオなど) ごとに 1 つの Stream があるので、最も粒度の高いエンティティです。  

### <a name="p2p-vs-group-calls"></a>P2P とグループ通話

通話 (`callType` で表されます) には、P2P とグループの 2 種類があります。 

**P2P** 通話は、2 つのエンドポイント間のみの接続で、サーバー エンドポイントがありません。 P2P 通話は、それらのエンドポイント間の通話として開始され、接続前にグループ通話イベントとして作成されることはありません。

  :::image type="content" source="media\call-logs-azure-monitor\p2p-diagram.png" alt-text="P2P 通話":::


**グループ** 通話には、会議または予定表イベントとして前もって作成された通話と、2 つを超えるエンドポイントが接続されている通話が含まれます。 グループ通話にはサーバー エンドポイントが含まれます。各エンドポイントとサーバー間の接続が参加者を構成します。 通話中にさらにエンドポイントを追加する P2P 通話は、P2P ではなくなり、グループ通話になります。 `participantStartTime` と `participantDuration` を確認することで、各エンドポイントが通話に参加した時間のタイムラインを判断できます。


  :::image type="content" source="media\call-logs-azure-monitor\group-call-version-a.png" alt-text="グループ通話":::

## <a name="log-structure"></a>ログの構造
**通話概要** ログと **通話診断** ログという 2 種類のログが作成されます。 

通話概要ログには、関連するすべての ID、タイムスタンプ、エンドポイント、SDK 情報など、通話に関する基本情報が含まれています。 通話内のエンドポイント (サーバーを除く) ごとに、個別の通話概要ログが作成されます。

通話診断ログには、ストリームに関する情報と、エクスペリエンス品質の測定値を示す一連のメトリックが含まれます。 通話内のエンドポイント (サーバーを含む) ごとに、エンドポイント間の各データ ストリーム (オーディオ、ビデオなど) について、個別の通話診断ログが作成されます。 P2P 通話では、各ログに、各エンドポイントに関連する各送信ストリームに関するデータが含まれます。 グループ通話では、`endpointType`= `"Server"` に関連する各ストリームによって、受信ストリームのデータを含むログが作成され、他のすべてのストリームでは、すべての非サーバー エンドポイントの送信ストリームのデータを含むログが作成されます。 [グループ通話] で、キーとして `participantId` を使用して、関連する受信/送信ログを個別の参加者接続に結合します。

### <a name="example-1-p2p-call"></a>例 1: P2P 通話

下の図は、P2P 通話で直接接続されている 2 つのエンドポイントを表しています。 この例では、2 つの通話概要ログが作成され (`endpointId` ごとに 1 つ)、4 つの通話診断ログが作成されます (メディア ストリームごとに 1 つ)。 各ログには、`endpointId` の送信ストリームに関連するデータが含まれます。

:::image type="content" source="media\call-logs-azure-monitor\example-1-p2p-call.png" alt-text="P2P 通話ストリーム":::


### <a name="example-2-group-call"></a>例 2: グループ通話

下の図は 3 つの `particpantIds` を持つグループ通話の例を表しています。つまり、3 つの `endpointIds` (`endpointIds` は、同じデバイスから通話に再参加する場合など、複数の参加者に表示される可能性があります) とサーバー エンドポイントです。 `participantId` 1 の場合、2 つの通話概要ログが作成されます。1 つは `endpointId` 用で、もう 1 つはサーバー用です。 `participantId` 1 に関連する 4 つの通話診断ログが作成されます (メディア ストリームごとに 1 つ)。 `endpointId` 1 の 3 つのログには、送信メディア ストリームに関連するデータが含まれます。`endpointId = null, endpointType = "Server"` の 1 つのログには、受信ストリームに関連するデータが含まれます。

:::image type="content" source="media\call-logs-azure-monitor\example-2-group-call-version-a.png" alt-text="グループ通話の詳細":::

## <a name="data-definitions"></a>データ定義

### <a name="call-summary-log"></a>通話概要ログ
通話概要ログには、すべての通話の主要なプロパティを識別するのに役立つデータが含まれます。 通話の各 `participantId` (P2P 通話の場合は `endpointId`) ごとに、異なる通話概要ログが作成されます。

|     プロパティ                  |     説明                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     time                      |     ログが生成された時刻のタイムスタンプ (UTC)。                                                                                                                                                                                                                                                                                                                                                                                                                       |
|     operationName             |     ログ レコードに関連付けられている操作。                                                                                                                                                                                                                                                                                                                                                                                                                                |
|     operationVersion          |     `operationName` が API を使用して実行された場合は、操作に関連付する api-version。 この操作に対応する API がない場合、バージョンは、操作に関連付けられているプロパティが今後、変更された場合、その操作のバージョンを表します。                                                                                                                                                                           |
|     category                  |     イベントのログ カテゴリ。 カテゴリは細分化されており、これを使用して、特定のリソースのログを有効または無効にすることができます。 イベントの `properties` BLOB 内に表示されるプロパティは、特定のログ カテゴリとリソースの種類内のものと同じです。                                                                                                                                                                                                    |
|     correlationIdentifier     |    `correlationIdentifier` は通話の一意の ID です。 `correlationIdentifier` は、1 回の通話中に接続するすべての参加者とエンドポイントからの相関イベントを識別し、さまざまなログのデータを結合するために使用できます。  Microsoft でサポート ケースを開く必要が生じた場合、`correlationID` を使用して、トラブルシューティングの対象となる通話を簡単に識別できます。                                                                                                                                                                      |
|     identifier                |     これはユーザーの一意の ID で、Communications Authentication サービスによって割り当てられた ID と一致します。 この ID を使用すると、さまざまなログを横断してユーザー イベントを関連付けることができます。 この ID は、このドキュメントで追って説明する Microsoft Teams の "相互運用性" シナリオを特定するためにも使用できます。                                                                                                                                                                                                                                                                                                                                                               |
|     callStartTime             |     いずれかのエンドポイントからの最初の接続試行に基づく、通話開始のタイムスタンプです。                                                                                                                                                                                                                                                                                                                                                                   |
|     callDuration              |     2 つのエンドポイント間での最初の接続試行と最後の接続の終了に基づく、通話の持続時間 (秒)。                                                                                                                                                                                                                                                                                                                         |
|     callType                  |     `"P2P"` と `"Group"` のいずれかを含みます。 `"P2P"` 通話は、サーバー以外の 2 つのエンドポイント間の 1 対 1 の直接接続です。 `"Group"` 通話は、2 つを超えるエンドポイントを持つ通話、または接続前に `"Group"` 通話として作成された通話です。                                                                                                                                                                                                                                 |
|     teamsThreadId             |     この ID は、通話が Microsoft Teams 会議として構成されている場合にのみ関係し、Microsoft Teams – Azure Communication Services 相互運用性のユースケースを表します。 この ID は、操作ログで公開されます。 この ID は、Chat API を通じて取得することもできます。                                                                                                                                                                                              |
|     participantId             |     この ID は、`"Participant"` エンドポイント (`endpointType` = `"Server"`) とサーバー間の双方向接続を表すために生成されます。 `callType` = `"P2P"` の場合、2 つのエンドポイントの間に直接接続があり、`participantId` は生成されません。                                                                                                                                                                                                               |
|     participantStartTime      |     参加者による最初の接続試行の開始を示すタイムスタンプです。                                                                                                                                                                                                                                                                                                                                                                                            |
|     participantDuration       |     各参加者接続の持続時間 (秒)。 `participantStartTime` から、接続が終了したときのタイムスタンプまでです。                                                                                                                                                                                                                                                                                                                                             |
|     participantEndReason      |     各 `participantId` に関連する場合に SDK によって出力される Calling SDK エラー コードが含まれます。 以下の Calling SDK エラー コードに関するページを参照してください。                                                                                                                                                                                                                                                                                                                          |
|     endpointId                |     通話に接続されている各エンドポイントを表す一意の ID。エンドポイントの種類が `endpointType` によって定義されます。 この値が `null` の場合、接続されているエンティティは Communication Services サーバー (`endpointType`= `"Server"`) になります。 `EndpointId` は、ネイティブ クライアントの場合は、複数の通話 (`correlationIdentifier`) にわたって同じユーザーに対して保持される場合があります。 `endpointId` の数によって、通話概要ログの数が決まります。 各 `endpointId` に対して個別の概要ログが作成されます。    |
|     endpointType              |     この値は、通話に接続されている各エンドポイントのプロパティを表します。 `"Server"`、`"VOIP"`、`"PSTN"`、`"BOT"`、または `"Unknown"` を含むことができます。                                                                                                                                                                                                                                                                                                                               |
|     sdkVersion                |     関連する各エンドポイントで使用される Communication Services Calling SDK バージョンを示すバージョン文字列。 (例: `"1.1.00.20212500"`)                                                                                                                                                                                                                                                                                                                                                          |
|     osVersion                 |     各エンドポイント デバイスのオペレーティング システムとバージョンを表す文字列。                                                                                                                                                                                                                                                                                                                                                                                       |


### <a name="call-diagnostic-log"></a>通話診断ログ
通話診断ログでは、各参加者のエンドポイントとメディア転送に関する重要な情報に加えて、品質の問題を把握するのに役立つ測定値も提供されます。 

|     プロパティ              |     説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     operationName         |     ログ レコードに関連付けられている操作。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     operationVersion      |     API を使用して `operationName` が実行された場合は、その操作に関連付けられている `api-version`。 この操作に対応する API がない場合、バージョンは、操作に関連付けられているプロパティが今後、変更された場合、その操作のバージョンを表します。                                                                                                                                                                                                                                                                                                                                                                  |
|     category              |     イベントのログ カテゴリ。 カテゴリは細分化されており、これを使用して、特定のリソースのログを有効または無効にすることができます。 イベントの `properties` BLOB 内に表示されるプロパティは、特定のログ カテゴリとリソースの種類内のものと同じです。                                                                                                                                                                                                                                                                                                                                                                                         |
|     correlationIdentifier     |     `correlationIdentifier` は、1 回の通話中に接続するすべての参加者とエンドポイントからの相関イベントを識別します。 `correlationIdentifier` は通話の一意の ID です。 Microsoft でサポート ケースを開く必要が生じた場合、`correlationID` を使用して、トラブルシューティングの対象となる通話を簡単に識別できます。                                                                                                                                                                                                                                                                                                                                                                         |
|     participantId         |     この ID は、"参加者"エンドポイント (`endpointType` =  `“Server”`) とサーバー間の双方向接続を表すために生成されます。 `callType`   = `"P2P"` の場合、2 つのエンドポイントの間に直接接続があり、`participantId` は生成されません。                                                                                                                                                                                                                                                                                                                                                                                                |
|     identifier            |     この ID は、認証サービスによって定義されたユーザー ID を表します。 この ID を使用して、通話とサービスの間の異なるイベントを関連付けます。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     endpointId            |     通話に接続している各エンドポイントを表す一意の ID。エンドポイントの種類が `endpointType` によって定義されます。   値が `null` の場合、接続されているエンティティは Communication Services サーバーであることを意味します。  `EndpointId` は、ネイティブ クライアントの場合は、複数の通話 (`correlationIdentifier`) にわたって同じユーザーに対して保持される場合がありますが、クライアントが Web ブラウザーである場合は、通話ごとに一意です。                                                                                                                                                                                                                                                                                  |
|     endpointType          |     この値は、各 `endpointId` のプロパティを表します。 `“Server”`、`“VOIP”`、`“PSTN”`、`“BOT”`、または `“Unknown”` を含むことができます。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|     mediaType             |     この文字列値は、各ストリーム内のエンドポイント間で送信されるメディアの種類を示します。 使用できる値には `“Audio”`、`“Video”`、`“VBSS”` (ビデオベースのスクリーン共有)、および `“AppSharing”` があります。                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|     streamId              |     `mediaType` と共に、同じ `participantId` のストリームを一意に識別するために使用できる一意でない整数。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     transportType         |     `participantId` のネットワーク トランスポート プロトコルを示す文字列値。 `"UDP”`、`“TCP”`、または `“Unrecognized”` を含むことができます。 `"Unrecognized"` は、`transportType` が TCP と UDP のどちらであるかをシステムが判断できなかったことを示します。                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     roundTripTimeAvg      |     これは、`participantDuration` 内のあるエンドポイントから別のエンドポイントに IP パケットが届くまでにかかる平均時間です。 このネットワーク伝達の遅延は、本質的に 2 つのポイント間の物理的距離および光速に結び付いており、さらにその間のさまざまなルーターによる追加のオーバーヘッドが加わります。 待ち時間は、一方向またはラウンドトリップ時間 (RTT) として測定されます。  この値はミリ秒単位で表され、RTT が 500 ミリ秒を超える場合は通話の品質に悪影響があると見なす必要があります。                                                                                                                                                                      |
|     roundTripTimeMax      |     グループ通話の `participantDuration` または P2P 通話の `callDuration` の間の、メディアストリームごとに測定される最大 RTT (ミリ秒単位)。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|     jitterAvg             |     これは、連続するパケット間の遅延の平均的な変化です。 Azure Communication Services では、バッファーリングによって一定レベルのジッターに対応できます。 これは、ジッターがバッファーリングを超過した場合にのみ発生します。これはおよそ `jitterAvg` が 30 ミリ秒を超えたときで、品質に悪影響が発生する可能性があります。 パケットが異なる速度で到着することで、スピーカーの音声がロボットのように聞こえます。 これは、メディア ストリームごとに、グループ通話の `participantDuration` または P2P 通話の `callDuration` で測定されます。                                                                                                                                              |
|     jitterMax             |     メディア ストリームごとにパケット間で測定される最大ジッター値です。 ネットワーク状態がバーストすると、オーディオまたはビデオのトラフィック フローで問題が発生する可能性があります。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|     packetLossRateAvg     |     これは、失われたパケットの平均割合です。 パケット損失は音声の品質に直接的に影響します。個々のパケットが失われる小規模なケースではほとんど影響はありませんが、連続して爆発的な損失が発生すると音声が完全に途切れます。 パケット損失が発生し、意図した宛先に到着しないと、メディアにギャップが生じ、音節や単語が失われたり、ビデオや共有が途切れたりすることがあります。 パケット損失率が10% (0.1) を超えると、品質に悪影響を与える可能性が高いと考えられます。 これは、メディア ストリームごとに、グループ通話の `participantDuration` または P2P 通話の `callDuration` で測定されます。    |
|     packetLossRateMax     |     この値は、グループ通話の `participantDuration` または P2P 通話の `callDuration` のメディア ストリームあたりの最大パケット損失率 (%) を表します。 ネットワーク状態がバーストすると、オーディオまたはビデオのトラフィック フローで問題が発生する可能性があります。                                                                                                                                                                                                                                                                                                                                                                                                                  |
|                           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |

### <a name="error-codes"></a>エラー コード
`participantEndReason` には、一連の Calling SDK エラー コードの値が含まれます。 これらのコードを参照して、エンドポイントごとに通話中の問題のトラブルシューティングを行うことができます。

|     エラー コード                 |     説明                                                                                                       |     実行するアクション                                                                                                                                                                                                                                                             |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     0                          |     成功                                                                                                           |     通話 (P2P) または参加者 (グループ) が正しく終了しました。                                                                                                                                                                                                                    |
|     403                        |     禁止または認証エラー。                                                                               |     Communication Services トークンが有効であり、有効期限が切れていないことを確認します。 Teams の相互運用性を使用している場合は、Teams テナントがプレビュー アクセス許可リストに追加されていることを確認します。 チームのテナント相互運用性を有効または無効にするには、このフォームを完成させてください。    |
|     404                        |     通話が見つかりません。                                                                                                   |     通話先の番号 (または参加している通話) が存在することを確認します。                                                                                                                                                                                                   |
|     408                        |     通話コントローラーがタイムアウトしました。                                                                                        |     ユーザー エンドポイントからのプロトコル メッセージの待機中に通話コントローラーがタイムアウトしました。 クライアントが接続され、使用可能であることを確認します。                                                                                                                                                 |
|     410                        |     ローカル メディア スタックまたはメディア インフラストラクチャ エラー。                                                                  |     サポートされている環境で最新の SDK を使用していることを確認します。                                                                                                                                                                                                      |
|     430                        |     クライアント アプリケーションにメッセージを配信できません。                                                                  |     クライアント アプリケーションが実行されていて使用可能であることを確認します。                                                                                                                                                                                                             |
|     480                        |     リモート クライアント エンドポイントが登録されていません。                                                                            |     リモート エンドポイントが使用可能であることを確認します。                                                                                                                                                                                                                              |
|     481                        |     着信通話の処理に失敗しました。                                                                                   |     Azure portal からサポート リクエストを提出します。                                                                                                                                                                                                                           |
|     487                        |     エンドポイントの不一致の問題が原因で通話がキャンセルされたか、ローカルで拒否されたか、終了しました。または、メディア プランの生成に失敗しました。    |     想定されている動作です。                                                                                                                                                                                                                                                         |
|     490、491、496、487、498    |     ローカル エンドポイント ネットワークの問題。                                                                                    |     ネットワークを確認します。                                                                                                                                                                                                                                                        |
|     500、503、504              |     Communication Services インフラストラクチャ エラー。                                                                      |     Azure portal からサポート リクエストを提出します。                                                                                                                                                                                                                           |
|     603                        |     Communication Services のリモート参加者によって、通話がグローバルに拒否されました。                                             |     想定されている動作です。                                                                                                                                                                                                                                                         |
|     Unknown                    |     標準以外の終了理由 (標準の SIP コードに含まれていません)。                                                      |                                                                                                                                                                                                                                                                                |

## <a name="call-examples-and-sample-data"></a>通話の例とサンプル データ

### <a name="p2p-call"></a>P2P 通話

:::image type="content" source="media\call-logs-azure-monitor\call-examples-and-sample-data-p2p-call.png" alt-text="P2P サンプル ログの例":::

通話のすべてのログの共有フィールド:

```json
"time":                     "2021-07-19T18:46:50.188Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "8d1a8374-344d-4502-b54b-ba2d6daaf0ae",
```

#### <a name="call-summary-logs"></a>通話概要ログ
通話概要ログでは、操作とカテゴリに関する情報が共有されています。

```json
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",

```
VoIP ユーザー 1 の通話概要
```json
"properties": {
    "identifier":               "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "callStartTime":            "2021-07-19T17:54:05.113Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",    
    "participantStartTime":     "2021-07-19T17:54:06.758Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.1.0",
    "osVersion":                "Windows 10.0.17763 Arch: x64"
}
```

VoIP ユーザー 2 の通話概要
```json
"properties": {
    "identifier":               "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "callStartTime":            "2021-07-19T17:54:05.335Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",
    "participantStartTime":     "2021-07-19T17:54:06.335Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.1.0.0",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>通話診断ログ
通話診断ログでは、操作情報が共有されています。
```json
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
VoIP エンドポイント 1 から VoIP エンドポイント 2 へのオーディオ ストリームの診断ログ:
```json
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "82",
    "roundTripTimeMax":     "88",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
VoIP エンドポイント 2 から VoIP エンドポイント 1 へのオーディオ ストリームの診断ログ:
```json
"properties": {
    "identifier":           "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "participantId":        "null",
    "endpointId":           "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1363841599",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "78",
    "roundTripTimeMax":     "84",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
VoIP エンドポイント 1 から VoIP エンドポイント 2 へのビデオ ストリームの診断ログ:
```json
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Video",
    "streamId":             "2804",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "103",
    "roundTripTimeMax":     "143",
    "jitterAvg":            "0",
    "jitterMax":            "4",
    "packetLossRateAvg":    "3.146336E-05",
    "packetLossRateMax":    "0.001769911"
}
```
### <a name="group-call"></a>グループ通話
次の例では、グループ通話に 3 人のユーザーがいます。2 人は VOIP 経由で接続し、もう 1 人は PSTN 経由で接続しています。 全員がオーディオのみを使用しています。 

:::image type="content" source="media\call-logs-azure-monitor\call-examples-and-sample-data-group-call.png" alt-text="グループ通話のサンプル ログの例":::

データは、3 つの通話概要ログと 6 つの通話診断ログに生成されます。

通話のすべてのログの共有フィールド:
```json
"time":                     "2021-07-05T06:30:06.402Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "341acde7-8aa5-445b-a3da-2ddadca47d22",
```

#### <a name="call-summary-logs"></a>通話概要ログ
通話概要ログでは、操作とカテゴリに関する情報が共有されています。
```json
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",
```

VoIP エンドポイント 1 の通話概要:
```json
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "participantStartTime":     "2021-07-05T06:16:44.235Z",
    "participantDuration":      "82",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Darwin Kernel Version 18.7.0: Mon Nov 9 15:07:15 PST 2020; root:xnu-4903.272.3~3/RELEASE_ARM64_S5L8960X"
}
```
VoIP エンドポイント 3 の通話概要:
```json
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "participantStartTime":     "2021-07-05T06:16:40.240Z",
    "participantDuration":      "87",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Android 11.0; Manufacturer: Google; Product: redfin; Model: Pixel 5; Hardware: redfin"
}
```
PSTN エンドポイント 2 の通話概要:
```json
"properties": {
    "identifier":               "null",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "515650f7-8204-4079-ac9d-d8f4bf07b04c",
    "participantStartTime":     "2021-07-05T06:17:10.447Z",
    "participantDuration":      "52",
    "participantEndReason":     "0",
    "endpointId":               "46387150-692a-47be-8c9d-1237efe6c48b",
    "endpointType":             "PSTN",
    "sdkVersion":               "null",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>通話診断ログ
通話診断ログでは、操作情報が共有されています。
```json
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
VoIP エンドポイント 1 からサーバー エンドポイントへのオーディオ ストリームの診断ログ:
```json
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "14884",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "46",
    "roundTripTimeMax":     "48",
    "jitterAvg":            "0",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
サーバー エンドポイントから VoIP エンドポイント 1 へのオーディオ ストリームの診断ログ:
```json
"properties": {
    "identifier":           null,
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           null,
    "endpointType":         "Server",
    "mediaType":            "Audio",
    "streamId":             "2001",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "42",
    "roundTripTimeMax":     "44",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
VoIP エンドポイント 3 からサーバー エンドポイントへのオーディオ ストリームの診断ログ:
```json
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "13783",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "2",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
サーバー エンドポイントから VoIP エンドポイント 3 へのオーディオ ストリームの診断ログ:
```json
"properties": {
    "identifier":           "null",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           null,
    "endpointType":         "Server"    
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "4",
    "packetLossRateAvg":    "0",
```