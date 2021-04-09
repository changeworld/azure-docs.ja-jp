---
title: Azure IoT Hub MQTT 5 API リファレンス (プレビュー)
description: IoT Hub の MQTT 5 API リファレンスについて説明します
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603052"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>IoT Hub データ プレーン MQTT 5 API リファレンス

このドキュメントでは、IoT Hub データ プレーン API のバージョン 2.0 (api-version: `2020-10-01-preview`) で使用できる操作を定義します。

## <a name="operations"></a>操作

### <a name="get-twin"></a>Get Twin

ツインの取得の状態

#### <a name="request"></a>Request

**トピック名:** `$iothub/twin/get`

**プロパティ**: なし

**ペイロード**: 空

#### <a name="success-response"></a>成功時の応答

**プロパティ**: なし

**ペイロード**:ツイン

#### <a name="alternative-responses"></a>代替応答

| Status | 名前 | 説明 |
| :----- | :--- | :---------- |
| 0100 |  正しくない要求 | 操作メッセージの形式が正しくないため、処理できません。 |
| 0101 |  権限なし | クライアントには操作を実行する権限がありません。 |
| 0102 |  禁止 | 操作は許可されていません。 |
| 0501 |  Throttled | SKU あたりの要求レートが高すぎます |
| 0502 |  クォータ超過 | 現在の SKU ごとの 1 日あたりのクォータを超えています |
| 0601 |  サーバー エラー | 内部サーバー エラー |
| 0602 |  タイムアウト | 操作が完了する前にタイムアウトになりました |
| 0603 |  サーバー ビジー | サーバーがビジー状態です |

#### <a name="pseudo-code-sample"></a>擬似コードのサンプル

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>パッチ ツインの報告

パッチ ツインの報告の状態

#### <a name="request"></a>Request

**トピック名:** `$iothub/twin/patch/reported`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| if-version | u64 | no |  |

**ペイロード**:TwinState

#### <a name="success-response"></a>成功時の応答

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| version | u64 | はい | パッチが適用された後に報告された状態のバージョン |

**ペイロード**: 空

#### <a name="alternative-responses"></a>代替応答

| Status | 名前 | 説明 |
| :----- | :--- | :---------- |
| 0104 |  前提条件の失敗 | 前提条件が満たされなかったため、要求が取り消されました |
| 0100 |  正しくない要求 | 操作メッセージの形式が正しくないため、処理できません。 |
| 0101 |  権限なし | クライアントには操作を実行する権限がありません。 |
| 0102 |  禁止 | 操作は許可されていません。 |
| 0501 |  Throttled | SKU あたりの要求レートが高すぎます |
| 0502 |  クォータ超過 | 現在の SKU ごとの 1 日あたりのクォータを超えています |
| 0601 |  サーバー エラー | 内部サーバー エラー |
| 0602 |  タイムアウト | 操作が完了する前にタイムアウトになりました |
| 0603 |  サーバー ビジー | サーバーがビジー状態です |

#### <a name="pseudo-code-sample"></a>擬似コードのサンプル

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>コマンドを受信する

コマンドを受信して処理する

#### <a name="message"></a>Message

**トピック名:** `$iothub/commands`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| sequence-no | u64 | はい | メッセージのシーケンス番号 |
| enqueued-time | time | はい | メッセージがシステムで受信されたときのタイムスタンプ |
| delivery-count | u32 | はい | メッセージ配信が試行された回数 |
| creation-time | time | no | メッセージが作成されたときのタイムスタンプ (送信者によって提供) |
| message-id | string | no | メッセージ ID (送信者によって提供) |
| user-id | string | no | ユーザー ID (送信者によって提供) |
| correlation-id | string | no | 相関 ID (送信者によって提供) |
| コンテンツ タイプ | string | no | ペイロードのコンテンツ タイプを決定します |
| content-encoding | string | no | ペイロードのコンテンツのエンコードを決定します |

**ペイロード**: 任意のバイト シーケンス

#### <a name="success-acknowledgment"></a>成功の受信確認

クライアントによる処理のためにコマンドが受け入れられたことを示します

**プロパティ**: なし

**ペイロード**: 空

#### <a name="alternative-acknowledgments"></a>代替受信確認

| 理由コード | Status | 名前 | 説明 |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Abandon | 現時点ではコマンドは処理されず、今後再配信される必要があることを示します。 |
| 131 | 0100 | Reject | コマンドがクライアントによって拒否され、再試行できないことを示します。 |

#### <a name="pseudo-code-sample"></a>擬似コードのサンプル

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>ダイレクト メソッドを受信する

ダイレクト メソッドの呼び出しを受信して処理する

#### <a name="request"></a>Request

**トピック名:** `$iothub/methods/{name}`

**プロパティ**: なし

**ペイロード**: 任意のバイト シーケンス

#### <a name="success-response"></a>成功時の応答

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| response-code | u32 | はい |  |

**ペイロード**: 任意のバイト シーケンス

#### <a name="alternative-responses"></a>代替応答

| Status | 名前 | 説明 |
| :----- | :--- | :---------- |
| 06A0 |  利用不可 | この接続を介してクライアントに到達できないことを示します。 |

#### <a name="pseudo-code-sample"></a>擬似コードのサンプル

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>ツインの望ましい状態の変更を受信する

ツインの望ましい状態の更新プログラムを受信する

#### <a name="message"></a>Message

**トピック名:** `$iothub/twin/patch/desired`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| version | u64 | はい | この更新プログラムに一致する望ましい状態のバージョン |

**ペイロード**:TwinState

#### <a name="pseudo-code-sample"></a>擬似コードのサンプル

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>テレメトリを送信する

テレメトリ チャネル (既定では EventHubs。ルーティング構成を使用する場合はその他のエンドポイント) にメッセージを投稿します。

#### <a name="message"></a>Message

**トピック名:** `$iothub/telemetry`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| コンテンツの種類 | string | no | 投稿されたメッセージの `content-type` システム プロパティに変換します |
| content-encoding | string | no | 投稿されたメッセージの `content-encoding` システム プロパティに変換します |
| message-id | string | no | 投稿されたメッセージの `message-id` システム プロパティに変換します |
| user-id | string | no | 投稿されたメッセージの `user-id` システム プロパティに変換します |
| correlation-id | string | no | 投稿されたメッセージの `correlation-id` システム プロパティに変換します |
| creation-time | time | no | 投稿されたメッセージの `iothub-creation-time-utc` プロパティに変換します |

**ペイロード**: 任意のバイト シーケンス

#### <a name="success-acknowledgment"></a>成功の受信確認

メッセージがテレメトリ チャネルに正常に投稿されました

**プロパティ**: なし

**ペイロード**: 空

#### <a name="alternative-acknowledgments"></a>代替受信確認

| 理由コード | Status | 名前 | 説明 |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | 正しくない要求 | 操作メッセージの形式が正しくないため、処理できません。 |
| 135 | 0101 | 権限なし | クライアントには操作を実行する権限がありません。 |
| 131 | 0102 | 禁止 | 操作は許可されていません。 |
| 131 | 0601 | サーバー エラー | 内部サーバー エラー |
| 151 | 0501 | Throttled | SKU あたりの要求レートが高すぎます |
| 151 | 0502 | クォータ超過 | 現在の SKU ごとの 1 日あたりのクォータを超えています |
| 131 | 0602 | タイムアウト | 操作が完了する前にタイムアウトになりました |
| 131 | 0603 | サーバー ビジー | サーバーがビジー状態です |

#### <a name="pseudo-code-sample"></a>擬似コードのサンプル

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>Responses

### <a name="bad-request"></a>正しくない要求

操作メッセージの形式が正しくないため、処理できません。

**理由コード:** `131`

**状態:** `0100`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| reason | string | no | メッセージに関して有効でない具体的な情報が含まれます |

**ペイロード**: 空

### <a name="conflict"></a>Conflict

操作は、実行中の別の操作と競合しています。

**理由コード:** `131`

**状態:** `0103`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | エラーの追加の診断と相関関係用のトレース ID |
| reason | string | no | メッセージに関して有効でない具体的な情報が含まれます |

**ペイロード**: 空

### <a name="not-allowed"></a>禁止

操作は許可されていません。

**理由コード:** `131`

**状態:** `0102`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| reason | string | no | メッセージに関して有効でない具体的な情報が含まれます |

**ペイロード**: 空

### <a name="not-authorized"></a>権限なし

クライアントには操作を実行する権限がありません。

**理由コード:** `135`

**状態:** `0101`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | エラーの追加の診断と相関関係用のトレース ID |

**ペイロード**: 空

### <a name="not-found"></a>見つかりません

要求されたリソースは存在しません

**理由コード:** `131`

**状態:** `0504`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| reason | string | no | メッセージに関して有効でない具体的な情報が含まれます |

**ペイロード**: 空

### <a name="not-modified"></a>変更されていません

指定された前提条件に基づいてリソースが変更されませんでした。

**理由コード:** `0`

**状態:** `0001`

**プロパティ**: なし

**ペイロード**: 空

### <a name="precondition-failed"></a>前提条件の失敗

前提条件が満たされなかったため、要求が取り消されました

**理由コード:** `131`

**状態:** `0104`

**プロパティ**: なし

**ペイロード**: 空

### <a name="quota-exceeded"></a>クォータ超過

現在の SKU ごとの 1 日あたりのクォータを超えています

**理由コード:** `151`

**状態:** `0502`

**プロパティ**: なし

**ペイロード**: 空

### <a name="resource-exhausted"></a>リソースを使い果たしました

リソースには操作を完了するための容量がありません

**理由コード:** `131`

**状態:** `0503`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| reason | string | no | メッセージに関して有効でない具体的な情報が含まれます |

**ペイロード**: 空

### <a name="server-busy"></a>サーバー ビジー

サーバーがビジー状態です

**理由コード:** `131`

**状態:** `0603`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | エラーの追加の診断と相関関係用のトレース ID |

**ペイロード**: 空

### <a name="server-error"></a>サーバー エラー

内部サーバー エラー

**理由コード:** `131`

**状態:** `0601`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | エラーの追加の診断と相関関係用のトレース ID |

**ペイロード**: 空

### <a name="target-failed"></a>ターゲットが失敗しました

ターゲットは応答しましたが、その応答が無効であるか、形式が正しくありませんでした

**理由コード:** `131`

**状態:** `06A2`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| reason | string | no | メッセージに関して有効でない具体的な情報が含まれます |

**ペイロード**: 空

### <a name="target-timeout"></a>ターゲットのタイムアウト

ターゲットで要求が完了するまで待機している間にタイムアウトになりました

**理由コード:** `131`

**状態:** `06A1`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | エラーの追加の診断と相関関係用のトレース ID |
| reason | string | no | メッセージに関して有効でない具体的な情報が含まれます |

**ペイロード**: 空

### <a name="target-unavailable"></a>ターゲットを使用できません

要求を完了するためにターゲットに到達できません

**理由コード:** `131`

**状態:** `06A0`

**プロパティ**: なし

**ペイロード**: 空

### <a name="throttled"></a>Throttled

SKU あたりの要求レートが高すぎます

**理由コード:** `151`

**状態:** `0501`

**プロパティ**: なし

**ペイロード**: 空

### <a name="timeout"></a>タイムアウト

操作が完了する前にタイムアウトになりました

**理由コード:** `131`

**状態:** `0602`

**[プロパティ]** :

| 名前 | Type | 必須 | 説明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | no | エラーの追加の診断と相関関係用のトレース ID |

**ペイロード**: 空

