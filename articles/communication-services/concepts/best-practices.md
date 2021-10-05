---
title: Azure Communication Services - ベスト プラクティス
description: Azure Communication Service のベスト プラクティスについて説明します
author: srahaman
manager: akania
services: azure-communication-services
ms.author: srahaman
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 47d690b53b6e8fe9ccc2660e48283ce05e262d30
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154187"
---
# <a name="best-practices-azure-communication-services-calling-sdks"></a>ベスト プラクティス: Azure Communication Services の通話 SDK
この記事では、Azure Communication Services (ACS) の通話 SDK に関連するベスト プラクティスについて説明します。

## <a name="acs-web-javascript-sdk-best-practices"></a>ACS Web JavaScript SDK のベスト プラクティス
このセクションでは、Azure Communication Services の JavaScript 音声およびビデオ通話 SDK に関連するベスト プラクティスについて説明します。

## <a name="javascript-voice-and-video-calling-sdk"></a>JavaScript 音声およびビデオ通話 SDK

### <a name="plug-in-microphone-or-enable-microphone-from-device-manager-when-acs-call-in-progress"></a>ACS 通話の進行中にマイクを接続するかマイクをデバイス マネージャーから有効にする
通話の開始時に使用可能なマイクがなく、その後マイクが使用可能になった場合、"noMicrophoneDevicesEnumerated" 通話診断イベントが発生します。
この場合、アプリケーションは `askDevicePermission` を呼び出して、デバイスを列挙することについてユーザーの同意を取得する必要があります。 その後、ユーザーはマイクをミュートまたはミュート解除できるようになります。

### <a name="stop-video-on-page-hide"></a>ページが非表示になったときにビデオを停止する
ユーザーが通話タブから移動すると、ビデオ ストリーミングが停止します。 一部のデバイスでは、最後のフレームまでストリーミング配信が続けられます。 この問題を回避するために、開発者には、ユーザーがアクティブなビデオ対応の通話から離れるときにビデオ ストリーミングを停止することが推奨されます。 ビデオを停止するには、`call.stopVideo` API を呼び出します。
```JavaScript
document.addEventListener("visibilitychange", function() {
    if (document.visibilityState === 'visible') {
        // Start Video if it was stopped on visibility change (flag true)
    } else {
        // Stop Video if it's on and set flag = true to keep track
    }
});
```

### <a name="dispose-video-stream-renderer-view"></a>ビデオ ストリーム レンダラー ビューを破棄する
Communication Services アプリケーションは、`VideoStreamRendererView` またはその親 `VideoStreamRenderer` インスタンスが不要になったら破棄する必要があります。

### <a name="hang-up-the-call-on-onbeforeunload-event"></a>onbeforeunload イベントで通話を切る
アプリケーションは、`onbeforeunload` イベントが生成されたら `call.hangup` を呼び出す必要があります。

### <a name="handling-multiple-calls-on-multiple-tabs-on-mobile"></a>モバイルにおける複数タブでの複数の呼び出しへの対処
アプリケーションは、複数のブラウザー タブからの呼び出しに同時に接続することはできません。これが行われると、デバイス上のマイクとカメラのリソース割り当てに起因する未定義の動作が発生する可能性があるためです。 バックグラウンドで通話が完了した場合は、必ず通話を切ってから新しい通話を開始するよう設計してください。
```JavaScript 
document.addEventListener("visibilitychange", function() {
    if (document.visibilityState != 'visible') {
            // call.hangUp
    }
});
 ```

### <a name="handle-os-muting-call-when-phone-call-comes-in"></a>電話がかかってきたときに OS の通話ミュートを処理する。
ACS 通話中、(iOS と Android を問わず) 電話がかかってくると、OS によってユーザーのマイクとカメラが自動的にミュートされます。 Android の場合、通話が終了すると、自動的にミュートが解除され、ビデオが再開されます。 iOS の場合は、ユーザーが "ミュートを解除" して、再度 "ビデオを開始" する必要があります。 マイクが予期せずミュートされたという通知は、品質イベント `microphoneMuteUnexpectedly` によってリッスンできます。 適切に再通話するには、SDK 1.2.2-beta.1 以降を使用する必要があることに注意してください。
```JavaScript
const latestMediaDiagnostic = call.api(SDK.Features.Diagnostics).media.getLatest();
const isIosSafari = (getOS() === OSName.ios) && (getPlatformName() === BrowserName.safari);
if (isIosSafari && latestMediaDiagnostic.microphoneMuteUnexpectedly && latestMediaDiagnostic.microphoneMuteUnexpectedly.value) {
  // received a QualityEvent on iOS that the microphone was unexpectedly muted - notify user to unmute their microphone and to start their video stream
}
 ```
ビデオ ストリームを開始するには、お使いのアプリケーションで `call.startVideo(localVideoStream);` を呼び出す必要があります。また、オーディオのミュートを解除するには、`this.currentCall.unmute();` を使用する必要があります。

### <a name="device-management"></a>デバイス管理
Azure Communication Services SDK を使用して、デバイスとメディア操作を管理できます。
- アプリケーションでは、`getUserMedia` や `getDisplayMedia` などのネイティブ ブラウザー API を使用して SDK の外部でストリームを取得してはいけません。 そうした場合は、Communication Services SDK を介して `DeviceManager` または他のデバイス管理 API を使用する前に、メディア ストリームを手動で破棄する必要があります。

### <a name="request-device-permissions"></a>デバイスのアクセス許可を要求する
SDK を使用してデバイスのアクセス許可を要求できます。
- アプリケーションでは、`DeviceManager.askDevicePermission` を使用して、オーディオ デバイスやビデオ デバイスへのアクセスを要求する必要があります。
- ユーザーがアクセスを拒否した場合、ページが更新された後でも、`DeviceManager.askDevicePermission` は、後続の呼び出しで特定のデバイスの種類 (オーディオまたはビデオ) に対して "false" を返します。 このシナリオでは、アプリケーションは、ユーザーが以前にアクセスを拒否したことを検出し、特定のデバイスの種類へのアクセスを手動でリセットまたは明示的に許可するようユーザーに案内する必要があります。

## <a name="next-steps"></a>次のステップ
詳細については、次の記事を参照してください。

- [チャットをアプリに追加する](../quickstarts/chat/get-started.md)
- [音声通話をアプリに追加する](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- [リファレンス ドキュメント](reference.md)
