---
title: Azure Communication Services - 既知の問題
description: Azure Communication Services について説明します
author: rinarish
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: a32b462b17a96eacb3858e7a22a27262046b8589
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491264"
---
# <a name="known-issues-in-the-sdks-and-apis"></a>SDK と API の既知の問題

この記事では、Azure Communication Services 通話 SDK と Communication Services Call Automation API に関連する制限事項と既知の問題について説明します。

> [!IMPORTANT]
> 通話エクスペリエンスの品質に影響する可能性のある要因が複数あります。 Communication Services のネットワーク構成とテストのベスト プラクティスの詳細については、「[ネットワークの推奨事項](./voice-video-calling/network-requirements.md)」を参照してください。

## <a name="javascript-sdk"></a>JavaScript SDK

以降のセクションでは、Communication Services の音声およびビデオ通話の JavaScript SDK に関連した既知の問題について説明します。

### <a name="ios-with-safari-crashes-and-refreshes-the-page-if-a-user-tries-to-send-video-in-a-call"></a>ユーザーが通話でビデオを送信しようとすると、Safari を搭載した iOS がクラッシュし、ページが更新される

iOS 15.1 では、Safari を搭載した iOS に格納されたビデオを使用した Communication Services 通話の大部分に影響するバグが導入されました。 具体的には、この問題は、ユーザーが、ビデオが有効になっている任意のブラウザーで iOS 15.1 上の Communication Services を使用して Microsoft Teams の Communication Services 通話または会議に参加した場合に発生します。 この一連の状況により、Safari ブラウザーがクラッシュします。

これは、[Safari を搭載した iOS 15.1 の既知のバグ](https://bugs.webkit.org/show_bug.cgi?id=231505)です。 Safari for Communication Services のビデオ通話で iOS 15.1 を使用する場合、ユーザーがビデオを使用しない (オンにしない) ようにする必要があります。 また、Microsoft Teams と Communication Services 間のビデオ通話会議ではこのアクティビティを防止する必要があります。

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>ページを更新してもユーザーが通話からすぐに削除されない

通話中のユーザーがページを更新することにした場合、Communication Services メディア サービスでは、このユーザーを通話から直ちに削除しません。 ユーザーが再び参加するのを待ちます。 メディア サービスがタイムアウトになった後、ユーザーは通話から削除されます。

エンド ユーザーが通話中にアプリケーションのページを更新する必要がないユーザー エクスペリエンスを構築することをお勧めします。 ユーザーがページを更新した場合、そのユーザーがアプリケーションに戻った後、Communication Services の同じユーザー ID を再使用します。 ユーザーは、同じユーザー ID で再度参加することにより、`remoteParticipants` コレクション内の同じ既存のオブジェクトとして表されます。 他の通話参加者から見ると、ユーザーは、ページの更新にかかる時間の間 (最大 1 分から 2 分)、通話中のままになります。

更新の前にユーザーがビデオを送信していた場合、`videoStreams` コレクションでは、サービスがタイムアウトになって削除されるまで、以前のストリーム情報が保持されます。 このシナリオの場合、アプリケーションでは、コレクションに追加された新しいストリームを確認し、`id` が最も高いものをレンダリングする場合があります。 

### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Web 上の複数のデバイスから複数のプレビューをレンダリングすることはできない

これは、既知の制限です。 詳細については、「[通話 SDK の概要](./voice-video-calling/calling-sdk-features.md)」を参照してください。

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>アプリケーションが iOS または iPadOS で実行されている場合、Safari でデバイスを列挙できない

Safari iOS または iPadOS では、アプリケーションでマイクやスピーカーのデバイス (Bluetooth など) を列挙したり選択したりすることはできません。 これは、これらのオペレーティング システムの既知の制限です。

macOS 上で Safari を使用している場合、アプリでは、Communication Services のデバイス マネージャーを通じてスピーカーを列挙したり選択したりすることはできません。 このシナリオでは、オペレーティング システムを介してデバイスを選択する必要があります。 macOS 上で Chrome を使用している場合、アプリでは Communication Services のデバイス マネージャーを通じてデバイスを列挙したり選択したりすることができます。

### <a name="device-mutes-and-incoming-video-stops-rendering-when-certain-interruptions-occur"></a>特定の割り込みが発生した場合、デバイスがミュートになり、受信ビデオのレンダリングが停止する

この問題は、別のアプリケーションまたはオペレーティング システムによってマイクまたはカメラの制御が引き継がれる場合に発生する可能性があります。 ユーザーが通話中に発生する可能性があるいくつかの例を次に示します。

- PSTN (公衆交換電話網) を介して着信通話があると、マイク デバイスへのアクセスがキャプチャされます。
- たとえば、ユーザーが YouTube 動画を再生するか、FaceTime 通話を開始します。 別のネイティブ アプリケーションに切り替えると、マイクまたはカメラへのアクセスがキャプチャされる可能性があります。
- ユーザーが Siri を有効にすると、マイクへのアクセスがキャプチャされます。

これらのすべてのケースで復旧するには、ユーザーは、アプリケーションに戻ってミュートを解除する必要があります。 ビデオの場合、中断後にオーディオやビデオを流し始めるには、ユーザーはビデオを開始する必要があります。

マイクやカメラのデバイスが時間どおりに解放されない場合があり、そのために元の通話で問題が発生する可能性があります。 たとえば、YouTube 動画の視聴中にミュートを解除しようとする場合や、PSTN 通話が同時にアクティブな場合などです。 

この問題が発生する環境は、次のとおりです。

- クライアント ライブラリ: 通話 (JavaScript)
- ブラウザー: Safari
- オペレーティング システム: iOS

### <a name="repeatedly-switching-video-devices-might-cause-video-streaming-to-stop-temporarily"></a>ビデオ デバイスを繰り返し切り替えると、ビデオ ストリーミングが一時的に停止する場合がある

ビデオ デバイスを切り替えると、選択したデバイスからストリームが取得される間、ビデオ ストリームが一時停止する場合があります。 デバイス間の切り替えを頻繁を行うと、パフォーマンスが低下する場合があります。 開発者は、1 つのデバイス ストリームを停止してから別のストリームを開始することをお勧めします。

### <a name="bluetooth-headset-microphone-isnt-detected-or-audible-during-the-call-on-safari-on-ios"></a>iOS 上の Safari で通話しているときに、Bluetooth のヘッドセットのマイクが検出されない、または音声が聞こえない

Bluetooth のヘッドセットは、iOS の Safari ではサポートされていません。 Bluetooth デバイスは、利用可能なマイク オプションの一覧に表示されません。Safari で Bluetooth を使用しようとした場合、他の参加者は音声を聞くことができません。

これは、オペレーティング システムの既知の制限です。 macOS および iOS または iPadOS 上の Safari では、Communication Services のデバイス マネージャーを使用してスピーカー デバイスを列挙したり選択したりすることはできません。 これは、Safari ではスピーカーの列挙または選択がサポートされていないためです。 このシナリオでは、オペレーティング システムを使用してデバイスの選択を更新します。

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>デバイスのローテーションによってビデオ品質が低下することがある

ユーザーがデバイスを回転させると、ストリーミング中のビデオの品質が低下する可能性があります。

この問題が発生する環境は、次のとおりです。

- 影響を受けるデバイス: Google Pixel 5、Google Pixel 3a、Apple iPad 8、Apple iPad X
- クライアント ライブラリ: 通話 (JavaScript)
- ブラウザー: Safari、Chrome
- オペレーティング システム: iOS、Android

### <a name="camera-switching-makes-the-screen-freeze"></a>カメラを切り替えると画面がフリーズする 

Communication Services ユーザーが JavaScript 通話 SDK を使用して通話に参加し、カメラのスイッチ ボタンを押すと、UI が応答しなくなることがあります。 ユーザーは、アプリケーションを更新するか、ブラウザーをバックグラウンドにプッシュする必要があります。

この問題が発生する環境は、次のとおりです。

- 影響を受けるデバイス: Google Pixel 4a
- クライアント ライブラリ: 通話 (JavaScript)
- ブラウザー: Chrome
- オペレーティング システム: iOS、Android

### <a name="video-signal-problem-when-the-call-is-in-connecting-state"></a>通話が接続中状態のときのビデオ信号の問題 

通話が "*接続中*" 状態のときにユーザーがビデオを素早くオンまたはオフにすると、通話用に取得されたストリームで問題が発生することがあります。 開発者は、通話が "*接続中*" 状態のときにビデオをオンまたはオフにする必要がないような方法でアプリを構築することをお勧めします。 次のシナリオでは、ビデオのパフォーマンスが低下する可能性があります。

 - 通話が "*接続中*" 状態のときに、ユーザーが音声から開始し、ビデオを開始して停止した場合。
 - 通話が "*ロビー*" 状態のときに、ユーザーが音声から開始し、ビデオを開始して停止した場合。

### <a name="enumerating-or-accessing-devices-for-safari-on-macos-and-ios"></a>macOS および iOS 上の Safari でのデバイスの列挙またはアクセス 

特定の環境では、デバイスのアクセス許可が一定期間後にリセットされる場合があります。 macOS および iOS の Safari では、取得されたストリームがある場合を除いて、アクセス許可を長期間保持しません。 この問題を回避する最も簡単な方法は、デバイス マネージャーのデバイス列挙 API を呼び出す前に、`DeviceManager.askDevicePermission()` API を呼び出すことです。 これらの列挙 API としては、`DeviceManager.getCameras()`、`DeviceManager.getSpeakers()`、`DeviceManager.getMicrophones()` があります。 アクセス許可がある場合、ユーザーには何も表示されません。 アクセス許可がない場合、ユーザーには、再度アクセス許可の入力を求めるメッセージが表示されます。

この問題が発生する環境は、次のとおりです。

- 影響を受けるデバイス: iPhone
- クライアント ライブラリ: 通話 (JavaScript)
- ブラウザー: Safari
- オペレーティング システム: iOS

### <a name="delay-in-rendering-remote-participant-videos"></a>リモート参加者のビデオをレンダリングで遅延が生じる

グループ通話の進行中に、"_ユーザー A_" がビデオを送信し、次に "_ユーザー B_" が通話に参加するとします。 場合によって、ユーザー B にユーザー A からのビデオが表示されなかったり、長い遅延後にユーザー A のビデオのレンダリングが開始されたりすることがあります。 ネットワーク環境の構成の問題によってこの遅延が発生する可能性があります。 詳細については、「[ネットワークの推奨事項](./voice-video-calling/network-requirements.md)」を参照してください。

### <a name="using-third-party-libraries-during-the-call-might-result-in-audio-loss"></a>通話中にサード パーティのライブラリを使用すると、オーディオが失われる可能性がある

アプリケーション内で `getUserMedia` を個別に使用すると、オーディオ ストリームが失われる場合があります。 これは、サード パーティ製のライブラリによって Azure Communication Services ライブラリからのデバイス アクセスが引き継がれるためです。

- 通話中に内部で `getUserMedia` API を使用しているサード パーティ製のライブラリを使用しないでください。
- それでもサード パーティのライブラリを使用する必要がある場合、音声ストリームを復旧させる唯一の方法は、選択したデバイスを変更するか (ユーザーが複数のデバイスを持っている場合)、通話を再度開始することです。

この問題が発生する環境は、次のとおりです。

- ブラウザー: Safari
- オペレーティング システム: iOS

この問題の原因としては、同じデバイスから独自のストリームを取得すると、競合状態になるという副作用があることが考えられます。 他のデバイスからストリームを取得すると、ユーザーの USB または IO 帯域幅が不足し、`sourceUnavailableError` 率が急上昇します。  

### <a name="support-for-simulcast"></a>サイマルキャストのサポート

サイマルキャストは、クライアントが同じビデオストリームを異なる解像度とビットレートで 2 回エンコードする手法です。 この後、クライアントは、Communication Services によって、クライアントが受信する必要があるストリームを決定できるようにします。 Windows、Android、または iOS 用の Communication Services 通話ライブラリ SDK では、サイマルキャスト ストリームの送信がサポートされています。 Communication Services Web SDK では、現在、サイマルキャスト ストリームの送信はサポートされていません。

## <a name="communication-services-call-automation-apis"></a>Communication Services Call Automation API

Communication Services Call Automation API の既知の問題を次に示します。

- サーバー アプリケーションで現在サポートされている認証は、接続文字列の使用のみです。

- 通話は、同じ Communication Services リソースのエンティティ間でのみ行う必要があります。 リソース間通信はブロックされます。

- Microsoft Teams のテナント ユーザーと Communication Services ユーザー間、またはサーバー アプリケーション エンティティ間の通話は許可されません。

- アプリケーションで 2 つ以上の PSTN ID にダイヤル アウトした後、通話を中止した場合、他の PSTN エンティティとの間の通話が切断されます。

