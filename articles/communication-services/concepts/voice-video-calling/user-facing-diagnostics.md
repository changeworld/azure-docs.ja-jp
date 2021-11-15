---
title: Azure Communication Services User Facing Diagnostics
titleSuffix: An Azure Communication Services concept document
description: User Facing Diagnostics 機能の概要を説明します。
author: probableprime
ms.author: rifox
manager: chpalm
services: azure-communication-services
ms.date: 10/21/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: ca0a60b1944dcbf037ecee0b012822a819bdb730
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269961"
---
# <a name="user-facing-diagnostics"></a>User Facing Diagnostics

Azure Communication Services で通話をしているとき、顧客に対して問題が発生する場合があります。 このシナリオに対処するため、通話のさまざまなプロパティを調べて問題の原因を探る、User Facing Diagnostics (対ユーザー診断) という機能を用意しています。

> [!NOTE]
> 対ユーザー診断は現在、Web SDK (JavaScript) でのみサポートしています。

## <a name="accessing-diagnostics"></a>診断へのアクセス

対ユーザー診断はコア `Call` API の拡張機能であり、実行中の通話の診断ができます。

```js
const userFacingDiagnostics = call.api(Features.UserFacingDiagnostics);
```

## <a name="diagnostic-values"></a>診断値

次の対ユーザー診断を利用できます。

### <a name="network-values"></a>ネットワーク値

| 名前                      | 説明                                                     | 設定可能な値                                                                                                                                                                                                                                  | ユース ケース                                           |
| ------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------- |
| noNetwork                 | 利用できるネットワークがありません。                                  | - 利用できるネットワークがないことを理由に通話に失敗した場合には、`True` に設定されます。 <br/> - ICE 候補が存在する場合には、`False` に設定されます。                                                                                                  | デバイスがネットワークに接続されていない。               |
| networkRelaysNotReachable | ネットワークに問題があります。                                        | - ACS リレーに到達できなくなるような制約がネットワークに存在する場合には、`True` に設定されます。 <br/> - 新しい通話を発信すると、`False` に設定されます。                                                                                                | 通話中に Wi-Fi 信号のオンとオフが切り替わったとき。 |
| networkReconnect          | 接続が失われ、ネットワークに再度接続しています。 | - ネットワークへの接続が切れると、`Bad` に設定されます <br/> - メディア転送接続が失われると、`Poor` に設定されます <br/> - 新しいセッションが接続済みになると、`Good` に設定されます。                                                                       | 帯域幅が狭い、インターネット接続がない                          |
| networkReceiveQuality     | 受信ストリームの品質に関するインジケーターです。                 | - ストリームの受信に深刻な問題が発生している場合には、`Bad` に設定されます。  <br/> - ストリームの受信に中等度の問題が発生している場合には、`Poor` に設定されます。 <br/> - ストリームの受信に問題がない場合には、`Good` に設定されます。 | 低帯域幅                                       |    
|   networkSendQuality     | 送信ストリームの品質の指標。                 | - ストリームの送信に重大な問題があると `Bad` になります。 <br/> - ストリームの送信に軽微な問題があると `Poor` になります。 <br/> - ストリームの送信に問題がなければ `Good` になります。 | 低帯域幅                                       |

### <a name="audio-values"></a>オーディオ値

| 名前                           | 説明                                                     | 設定可能な値                                                                                                                                                                                                                                                                                                   | ユース ケース                                                        |
| ------------------------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| noSpeakerDevicesEnumerated     | ユーザーのシステムにオーディオ出力デバイス (スピーカー) がありません。 | - システムにスピーカー デバイスがなく、スピーカー選択がサポートされている場合には、`True` に設定されます。 <br/> - システムにスピーカー デバイスが少なくとも 1 つあり、スピーカー選択がサポートされている場合には、`False` に設定されます。                                                                                             | スピーカーがすべて取り外されている                                       |
| speakingWhileMicrophoneIsMuted | 話をしているものの、ミュート中です。                                   | - ローカルのマイクがミュートになっている間にローカル ユーザーが話をしていると、`True` に設定されます。 <br/> - ローカルのユーザーが話すのをやめるか、マイクのミュートを解除すると `False` になります。 <br/> \* 注: WebRTC の統計情報から音声レベルのサンプルを取得するため、現在 Safari ではこの項目をサポートしていません。                 | 通話中にマイクをミュートした状態で話をする。           |
| noMicrophoneDevicesEnumerated  | ユーザーのシステムにオーディオ キャプチャ デバイス (マイク) がありません      | - システムにマイク デバイスがない場合には、`True` に設定されます。 <br/> - システムにマイク デバイスが 1 つ以上ある場合には、`False` に設定されます。                                                                                                                                                              | 通話中にマイクがすべて取り外される。                   |
| microphoneNotFunctioning       | マイクが機能していません。                                  | - マイク デバイスがシステムで無効になっている可能性があるか、別のプロセスで使用されていることが原因でローカル オーディオ ストリームの送信開始に失敗した場合には、`True` に設定されます。 この UFD の発生には、約 10 秒かかります。 <br/> - マイクがオーディオ ストリームの送信を正常に再開した場合には、`False` に設定されます。 | 利用できるマイクがない、システムでマイクへのアクセスが無効になっている |
| microphoneMuteUnexpectedly     | マイクがミュートになっています                                             | - マイクが予期せずミュート状態になった場合には、`True` に設定されます。 <br/> - マイクがオーディオ ストリームの送信を正常に開始した場合には、`False` に設定されます                                                                                                                                                                  | マイクがシステムからミュートに設定されています。                             |
| microphonePermissionDenied     | macOS でデバイスの音量が小さいか、ほぼ無音の状態になっています。 | - システム設定 (オーディオ) によりオーディオへのアクセス許可が拒否されている場合には、`True` に設定されます。 <br/> - ストリームの取得に成功した場合には、`False` に設定されます。 <br/> 注: この診断は、macOS でのみ機能します。                                                                                                                             | 設定でマイクへのアクセス許可が無効になっている。             |

### <a name="camera-values"></a>カメラの値

| 名前                   | 説明                                            | 設定可能な値                                                                                                                                                                                                                                                                                              | ユース ケース                                                                       |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| cameraFreeze           | カメラのフレーム生成が 5 秒以上停止しています。 | - ローカル ビデオ ストリームが停止していると、`True` に設定されます。 これは、自分のビデオがリモートの相手側では停止して見えているか、リモート参加者がビデオを画面にレンダリングできていないことを意味します。 <br/> - 停止状態が解消し、相手側でビデオが通常どおり表示されるようになると、`False` に設定されます。 | 通話中にカメラが失われたか、ネットワークの状態が悪いためにカメラが停止した。 |
| cameraStartFailed      | カメラの障害全般です。                                | - カメラ デバイスがシステムで無効になっている可能性があるか、別のプロセスで使用されていることが原因でローカル ビデオの送信開始に失敗した場合には、`True` に設定されます。 <br/> - 選択したカメラ デバイスがローカル ビデオの送信を正常に再開した場合には、`False` に設定されます。                                                  | カメラの障害                                                                 |
| cameraStartTimedOut    | カメラの状態が悪い場合に共通するシナリオです。          | - カメラ デバイスがビデオ ストリームの送信を開始する際にタイムアウトになった場合には、`True` に設定されます。 <br/> - 選択したカメラ デバイスがローカル ビデオの送信を正常に再開した場合には、`False` に設定されます。                                                                                                                                         | カメラの障害                                                                 |
| cameraPermissionDenied | カメラへのアクセス許可が設定で拒否されました。            | - システム設定 (ビデオ) によりカメラへのアクセス許可が拒否されている場合には、`True` に設定されます。 <br/> - ストリームの取得に成功した場合には、`False` に設定されます。 <br> 注: この診断は、macOS の Chrome でのみ機能します                                                                                                                  | 設定でカメラへのアクセス許可が無効になっている。                                |
| cameraStoppedUnexpectedly | カメラの不具合             | - カメラが予期しない停止状態になると `True` になります。 <br/> - カメラが起動して動画ストリームの送信を再開すると `False` になります。    | カメラが正しく動作しているかどうかを確認します     |

### <a name="misc-values"></a>その他の値

| 名前                         | 説明                                                  | 設定可能な値                                                                                                                                                                                         | ユース ケース                                 |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| screenshareRecordingDisabled | システムの画面共有が、設定によって拒否されました。 | - システム設定 (共有) により画面共有のためのアクセス許可が拒否されている場合には、`True` に設定されます。 <br/> - ストリームの取得に成功した場合には、`False` に設定されます。 <br/> 注: この診断は、macOS の Chrome でのみ機能します。 | 設定で画面のレコーディングが無効になっている。 |
| capturerStartFailed | システム画面の共有に失敗しました。 | - 画面の録画の開始に失敗すると `True` になります。 <br/> - 画面の録画を開始すると `False` になります。 |  |
| capturerStoppedUnexpectedly | システム画面の共有の不具合             | - 画面の録画が予期しない停止状態になると `True` になります。 <br/> - 画面の録画を再開すると `False` になります。    | 画面共有が正しく機能しているかどうかを確認します     |


## <a name="user-facing-diagnostic-events"></a>User Facing Diagnostic イベント

- `diagnosticChanged` イベントをサブスクライブして、対ユーザー診断の変化をモニターします。

```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` +
        `Value type: ${diagnosticInfo.valueType}`);

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

userFacingDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
userFacingDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

## <a name="get-the-latest-user-facing-diagnostics"></a>最新の User Facing Diagnostics を取得する

- 出力された最新の診断値を取得します。 診断が定義されていないときは、一度も発生していないことを意味します。

```js
const latestNetworkDiagnostics = userFacingDiagnostics.network.getLatest();

console.log(
  `noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`
);

console.log(
  `networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`
);

console.log(
  `networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`
);

const latestMediaDiagnostics = userFacingDiagnostics.media.getLatest();

console.log(
  `speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`
);

console.log(
  `cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`
);

console.log(
  `microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`
);
```
