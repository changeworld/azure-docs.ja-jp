---
title: Azure Communication Services - 既知の問題
description: Azure Communication Services について説明します
author: rinarish
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: b9ed71a8fc9346ecd454eba98dcbb3b13186eba2
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276044"
---
# <a name="known-issues-azure-communication-services-calling-sdks"></a>既知の問題: Azure Communication Services の通話 SDK
この記事では、Azure Communication Services の通話 SDK に関連する制限事項と既知の問題について説明します。

> [!IMPORTANT]
> 通話エクスペリエンスの品質に影響する可能性のある要因が複数あります。 Communication Services のネットワーク構成とテストのベスト プラクティスの詳細については、 **[ネットワーク要件](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** に関するドキュメントを参照してください。


## <a name="javascript-sdk"></a>JavaScript SDK

このセクションでは、Azure Communication Services の音声およびビデオ通話の JavaScript SDK に関連した既知の問題について説明します。

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>ページを更新してもユーザーが通話からすぐに削除されない

通話中のユーザーがページを更新することにした場合、Communication Services メディア サービスでは、このユーザーを通話から直ちに削除しません。 ユーザーが再び参加するのを待ちます。 メディア サービスがタイムアウトになった後、ユーザーは通話から削除されます。

エンド ユーザーが通話中にアプリケーションのページを更新する必要がないユーザー エクスペリエンスを構築することをお勧めします。 ユーザーがページを更新した場合は、アプリケーションに戻った後、Communication Services の同じユーザー ID を再使用します。

通話の他の参加者の視点から見ると、そのユーザーは一定期間 (1 - 2 分間) 通話にとどまります。 ユーザーが Communication Services の同じユーザー ID で再び参加すると、`remoteParticipants` コレクション内の、同じ既存のオブジェクトとして表されます。

更新の前にユーザーがビデオを送信していた場合、`videoStreams` コレクションでは、サービスがタイムアウトになって削除されるまで、以前のストリーム情報が保持されます。 このシナリオの場合、アプリケーションでは、コレクションに追加された新しいストリームを確認し、最も高い `id` のものをレンダリングする場合があります。 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Web 上の複数のデバイスから複数のプレビューをレンダリングすることはできない
これは、既知の制限です。 詳細については、[通話 SDK の概要](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)に関する記事を参照してください。

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>アプリケーションが iOS または iPadOS で実行されている場合、Safari でデバイスを列挙できない

Safari iOS や iPad では、アプリケーションでマイクやスピーカー デバイス (Bluetooth など) を列挙したり選択したりできません。 これは、オペレーティング システムの既知の制限です。

macOS 上で Safari を使用している場合、アプリでは Communication Services のデバイス マネージャーを通じてスピーカーを列挙したり選択したりできません。 このシナリオでは、デバイスは OS を介して選択する必要があります。 macOS 上で Chrome を使用している場合、アプリでは Communication Services のデバイス マネージャーを通じてデバイスを列挙したり選択したりできます。

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>進行中の VoIP 通話中に SMS メッセージまたは通話を受信したときに音声の接続が失われる
この問題は、複数の理由により発生する可能性があります。

- 一部のモバイル ブラウザーでは、バックグラウンド状態での接続は維持されません。 このため、アプリケーションをバックグラウンドに押し出すイベントによって VoIP 通話が中断された場合、通話エクスペリエンスが低下する可能性があります。 
- 場合によっては、SMS または PSTN 通話によって音声がキャプチャされ、VoIP 通話に音声が返されないことがあります。 Apple では、iOS バージョン 14.4.1 以降でこの問題を修正しました。 

<br/>クライアント ライブラリ: 通話 (JavaScript)
<br/>ブラウザー: Safari、Chrome
<br/>オペレーティング システム: iOS、Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>ビデオ デバイスを繰り返し切り替えると、ビデオ ストリーミングが一時的に停止する場合がある

ビデオ デバイスを切り替えると、選択したデバイスからストリームが取得される間、ビデオ ストリームが一時停止する場合があります。

#### <a name="possible-causes"></a>考えられる原因
デバイス間の切り替えを頻繁を行うと、パフォーマンスが低下する場合があります。 開発者は、1 つのデバイス ストリームを停止してから別のストリームを開始することをお勧めします。

### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>iOS 上の Safari で通話しているときに、Bluetooth のヘッドセットのマイクが検出されないため、音声が聞こえない
Bluetooth のヘッドセットは、iOS の Safari ではサポートされていません。 Bluetooth のデバイスは、利用可能なマイクのオプションに表示されません。また、Safari で Bluetooth を使用しようとした場合、他の参加者にはお客様の声が聞こえません。

#### <a name="possible-causes"></a>考えられる原因
これは、macOS、iOS、および iPadOS オペレーティング システムの既知の制限です。 

**macOS** および **iOS と iPadOS** 上で Safari を使用した場合、Safari ではスピーカーの列挙または選択がサポートされていないため、Communication Services のデバイス マネージャーを使用してスピーカー デバイスを列挙したり選択したりできません。 このシナリオでは、デバイスの選択は、オペレーティング システムを使用して更新する必要があります。

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>デバイスのローテーションによってビデオ品質が低下することがある
デバイスをローテーションしたときにビデオ品質が低下する場合があります。

<br/>影響を受けるデバイス: Google Pixel 5、Google Pixel 3a、Apple iPad 8、Apple iPad X
<br/>クライアント ライブラリ: 通話 (JavaScript)
<br/>ブラウザー: Safari、Chrome
<br/>オペレーティング システム: iOS、Android


### <a name="camera-switching-makes-the-screen-freeze"></a>カメラを切り替えると画面がフリーズする 
Communication Services ユーザーが JavaScript 通話 SDK を使用して通話に参加し、カメラのスイッチ ボタンを押すと、アプリケーションが更新されるか、ユーザーによってブラウザーがバックグラウンドに押し出されるまで、UI が応答しなくなることがあります。

<br/>影響を受けるデバイス: Google Pixel 4a
<br/>クライアント ライブラリ: 通話 (JavaScript)
<br/>ブラウザー: Chrome
<br/>オペレーティング システム: iOS、Android


#### <a name="possible-causes"></a>考えられる原因
調査中。

### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>通話が "接続中" 状態のときにビデオ信号が停止された場合、通話が開始された後にビデオが送信されない 
通話が `Connecting` 状態のときにユーザーがビデオを素早くオンまたはオフにすると、通話用に取得されたストリームで問題が発生することがあります。 開発者は、通話が `Connecting` 状態のときにビデオをオンまたはオフにする必要がないような方法でアプリを構築することをお勧めします。 次のシナリオでは、この問題によりビデオのパフォーマンスが低下する可能性があります。

 - ユーザーが音声から開始し、通話が `Connecting` 状態のときにビデオを開始して停止した場合。
 - ユーザーが音声から開始し、通話が `Lobby` 状態のときにビデオを開始して停止した場合。

#### <a name="possible-causes"></a>考えられる原因
調査中。

### <a name="enumeratingaccessing-devices-for-safari-on-macos-and-ios"></a>MacOS および iOS 上の Safari でのデバイスの列挙またはアクセス 
デバイスへのアクセスが許可された場合、しばらくすると、デバイスのアクセス許可はリセットされます。 MacOS および iOS 上の Safari では、取得したストリームがない限り、アクセス許可は長い時間保持されません。 この問題を回避する最も簡単な方法は、デバイス マネージャーのデバイス列挙 API (DeviceManager.getCameras()、DeviceManager.getSpeakers()、および DeviceManager.getMicrophones()) を呼び出す前に、DeviceManager.askDevicePermission() API を呼び出すことです。 アクセス許可があれば、ユーザーには何も表示されません。ない場合は、入力を求めるメッセージが再び表示されます。

<br/>影響を受けるデバイス: iPhone
<br/>クライアント ライブラリ: 通話 (JavaScript)
<br/>ブラウザー: Safari
<br/>オペレーティング システム: iOS

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>リモート参加者のビデオをレンダリングするのに長い時間がかかることがある
進行中のグループ通話中に、"_ユーザー A_" がビデオを送信し、次に "_ユーザー B_" が通話に参加します。 場合によって、ユーザー B にユーザー A からのビデオが表示されなかったり、長い遅延後にユーザー A のビデオのレンダリングが開始されたりすることがあります。 この問題は、追加構成が必要なネットワーク環境が原因で発生する可能性があります。 ネットワーク構成のガイダンスについては、[ネットワーク要件](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)に関するドキュメントを参照してください。
