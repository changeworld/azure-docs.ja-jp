---
title: Azure Communication Services 通話の診断
titleSuffix: An Azure Communication Services concept document
description: 通話の診断機能の概要について説明します。
author: probableprime
ms.author: rifox
manager: chpalm
services: azure-communication-services
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 23b75ff29e32bafdce320d334d9236902cbe4b2c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667613"
---
# <a name="call-diagnostics"></a>通話の診断

Azure Communication Services で通話を処理する際に、顧客の問題を引き起こす問題が発生する場合があります。 これを支援するために、"通話の診断" と呼ばれる機能があります。これにより、通話のさまざまなプロパティを調べて問題を判別できます。

**通話の診断は現在、JS または Web SDK でのみサポートされています。**

## <a name="accessing-diagnostics"></a>診断へのアクセス

通話診断は、コアとなる `Call` API の拡張機能の 1 つで、現在の通話を診断できるというものです。

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

## <a name="diagnostic-values"></a>診断値

ユーザー向けの診断として利用可能なものは次のとおりです。

### <a name="network-values"></a>ネットワーク値

| 名前                      | 説明                                                     | 設定可能な値                                                                                                                                                                                                                                  | ユース ケース                                           |
| ------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------- |
| noNetwork                 | 利用できるネットワークがありません。                                  | - 利用できるネットワークがないことを理由に通話に失敗した場合には、`True` に設定されます。 <br/> - ICE 候補が存在する場合には、`False` に設定されます。                                                                                                  | デバイスがネットワークに接続されていない。               |
| networkRelaysNotReachable | ネットワークに問題があります。                                        | - ACS リレーに到達できなくなるような制約がネットワークに存在する場合には、`True` に設定されます。 <br/> - 新しい通話を発信すると、`False` に設定されます。                                                                                                | 通話中に Wi-Fi 信号のオンとオフが切り替わったとき。 |
| networkReconnect          | 接続が失われ、ネットワークに再度接続しています。 | - ネットワークへの接続が切れると、`Bad` に設定されます <br/> - メディア転送接続が失われると、`Poor` に設定されます <br/> - 新しいセッションが接続済みになると、`Good` に設定されます。                                                                       | 帯域幅が狭い、インターネット接続がない                          |
| networkReceiveQuality     | 受信ストリームの品質に関するインジケーターです。                 | - ストリームの受信に深刻な問題が発生している場合には、`Bad` に設定されます。 品質 <br/> - ストリームの受信に中等度の問題が発生している場合には、`Poor` に設定されます。 品質 <br/> - ストリームの受信に問題がない場合には、`Good` に設定されます。 | 低帯域幅                                       |

### <a name="audio-values"></a>オーディオ値

| 名前                           | 説明                                                     | 設定可能な値                                                                                                                                                                                                                                                                                                   | ユース ケース                                                        |
| ------------------------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| noSpeakerDevicesEnumerated     | ユーザーのシステムにオーディオ出力デバイス (スピーカー) がありません。 | - システムにスピーカー デバイスがなく、スピーカー選択がサポートされている場合には、`True` に設定されます。 <br/> - システムにスピーカー デバイスが少なくとも 1 つあり、スピーカー選択がサポートされている場合には、`False` に設定されます。                                                                                             | スピーカーがすべて取り外されている                                       |
| speakingWhileMicrophoneIsMuted | 話をしているものの、ミュート中です。                                   | - ローカルのマイクがミュートになっている間にローカル ユーザーが話をしていると、`True` に設定されます。 <br/> - ローカル ユーザーが話をやめるか、マイクのミュートを解除すると `False` に設定されます。 <br/> \* 注: 現時点において、オーディオ レベルのサンプルを WebRTC の統計情報から取得している関係上、Safari ではまだこれがサポートされていません。                 | 通話中にマイクをミュートした状態で話をする。           |
| noMicrophoneDevicesEnumerated  | ユーザーのシステムにオーディオ キャプチャ デバイス (マイク) がありません      | - システムにマイク デバイスがない場合には、`True` に設定されます。 <br/> - システムにマイク デバイスが 1 つ以上ある場合には、`False` に設定されます。                                                                                                                                                              | 通話中にマイクがすべて取り外される。                   |
| microphoneNotFunctioning       | マイクが機能していません。                                  | - マイク デバイスがシステムで無効になっている可能性があるか、別のプロセスで使用されていることが原因でローカル オーディオ ストリームの送信開始に失敗した場合には、`True` に設定されます。 この UFD の発生には、約 10 秒かかります。 <br/> - マイクがオーディオ ストリームの送信を正常に再開した場合には、`False` に設定されます。 | 利用できるマイクがない、システムでマイクへのアクセスが無効になっている |
| microphoneMuteUnexpectedly     | マイクがミュートになっています                                             | - マイクが予期せずミュート状態になった場合には、`True` に設定されます。 <br/> - マイクがオーディオ ストリームの送信を正常に開始した場合には、`False` に設定されます                                                                                                                                                                  | マイクがシステムからミュートに設定されています。                             |
| microphonePermissionDenied     | macOS でデバイスの音量が小さいか、ほぼ無音の状態になっています。 | - システム設定 (オーディオ) によりオーディオへのアクセス許可が拒否されている場合には、`True` に設定されます。 <br/> - ストリームの取得に成功した場合には、`False` に設定されます。 <br/> 注: この診断は、macOS でのみ機能します。                                                                                                                             | 設定でマイクへのアクセス許可が無効になっている。             |

### <a name="camera-values"></a>カメラの値

| 名前                   | 説明                                            | 設定可能な値                                                                                                                                                                                                                                                                                              | ユース ケース                                                                       |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| cameraFreeze           | カメラのフレーム生成が 5 秒以上停止しています。 | - ローカル ビデオ ストリームが停止していると、`True` に設定されます。 これは、自分のビデオがリモートの相手側では停止して見えているか、リモート参加者がビデオを画面にレンダリングできていないことを意味します。 <br/> - 停止状態が解消し、相手側でビデオが通常どおり表示されるようになると、`False` に設定されます。 | 通話中にカメラが失われたか、ネットワークの状態が悪いためにカメラが停止した。 |
| cameraStartFailed      | カメラの障害全般です。                                | - カメラ デバイスがシステムで無効になっている可能性があるか、別のプロセスで使用されていることが原因でローカル ビデオの送信開始に失敗した場合には、`True` に設定されます。 <br/> - 選択したカメラ デバイスがローカル ビデオの送信を正常に 再開した場合には、 に設定されます。                                                  | カメラの障害                                                                 |
| cameraStartTimedOut    | カメラの状態が悪い場合に共通するシナリオです。          | - カメラ デバイスがビデオ ストリームの送信を開始する際にタイムアウトになった場合には、`True` に設定されます。 <br/> - 選択したカメラ デバイスがローカル ビデオの送信を正常に再開した場合には、`False` に設定されます。                                                                                                                                         | カメラの障害                                                                 |
| cameraPermissionDenied | カメラへのアクセス許可が設定で拒否されました。            | - システム設定 (ビデオ) によりカメラへのアクセス許可が拒否されている場合には、`True` に設定されます。 <br/> - ストリームの取得に成功した場合には、`False` に設定されます。 <br> 注: この診断は、macOS の Chrome でのみ機能します                                                                                                                  | 設定でカメラへのアクセス許可が無効になっている。                                |

### <a name="misc-values"></a>その他の値

| 名前                         | 説明                                                  | 設定可能な値                                                                                                                                                                                         | ユース ケース                                 |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| screenshareRecordingDisabled | システムの画面共有が、設定によって拒否されました。 | - システム設定 (共有) により画面共有のためのアクセス許可が拒否されている場合には、`True` に設定されます。 <br/> - ストリームの取得に成功した場合には、`False` に設定されます。 <br/> 注: この診断は、macOS の Chrome でのみ機能します。 | 設定で画面のレコーディングが無効になっている。 |

## <a name="diagnostic-events"></a>診断イベント

- 通話の診断に変更が発生したタイミングを監視するには、`diagnosticChanged` イベントをサブスクライブします。

```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` +
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

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

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

## <a name="get-the-latest-diagnostics"></a>最新の診断情報を取得する

- 発生した通話診断の最新の値を取得します。 診断が定義されていないときは、一度も発生していないことを意味します。

```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();

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

const latestMediaDiagnostics = callDiagnostics.media.getLatest();

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
