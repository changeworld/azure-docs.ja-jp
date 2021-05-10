---
title: Azure Communication Services Calling SDK の概要
titleSuffix: An Azure Communication Services concept document
description: Calling SDK の概要について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 28072184d47beb32dc03e0d6ba52328bfceb5b73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364874"
---
# <a name="calling-sdk-overview"></a>Calling SDK の概要

Calling SDK を使用すると、エンド ユーザーのデバイスで音声とビデオの通信エクスペリエンスを実現できます。 このページでは、プラットフォームとブラウザーのサポート情報を含め、通話機能について詳しく説明します。 すぐに開始するには、[通話のクイックスタート](../../quickstarts/voice-video-calling/getting-started-with-calling.md)または[通話のヒーロー サンプル](../../samples/calling-hero-sample.md)をご覧ください。 

開発を開始したら、[既知の問題に関するページ](../known-issues.md)を確認して、対応中のバグを見つけてください。

Calling SDK の主な機能は次のとおりです。

- **アドレス指定** - Azure Communication Services は、通信エンドポイントとのやり取りに使用される汎用的な [ID](../identity-model.md) を提供します。 クライアントは、これらの ID を使用してサービスに対する認証を行い、相互に通信します。 これらの ID は、通話に接続されているユーザーがだれなのか (名簿) をクライアントが可視化できるようにする通話 API で使用されます。
- **暗号化** - Calling SDK によってトラフィックが暗号化され、送信中の改ざんを防ぎます。 
- **デバイス管理とメディア** - Calling SDK は、オーディオ デバイスとビデオ デバイスへのバインド機能、通信データプレーンを介した効率的な送信のためのコンテンツのエンコード機能、指定した出力デバイスおよび出力ビューへのコンテンツのレンダリング機能を提供します。 画面とアプリケーションの共有用 API も用意されています。
- **PSTN** - Calling SDK は、プログラムまたは [Azure portal で取得した電話番号を使用](../../quickstarts/telephony-sms/get-phone-number.md)することにより、従来の公衆交換電話網を使用して音声通話を受信し、開始することができます。
- **Teams の会議** - Calling SDK により、[Teams の会議に参加](../../quickstarts/voice-video-calling/get-started-teams-interop.md)し、Teams の音声またはビデオ データ プレーンと対話できます。 
- **通知** - Calling SDK から API が提供され、通話の着信がクライアントに通知されるようになります。 アプリがフォアグラウンドで実行されていない状況では、[ポップアップ通知を起動](../notifications.md)して ("トースト")、通話の着信をエンド ユーザーに通知するというパターンを使用できます。 

## <a name="detailed-capabilities"></a>機能の詳細 

次の一覧は、Azure Communication Services Calling SDK で現在使用できる機能のセットを示しています。

| 機能のグループ | 機能                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| コア機能 | 2 人のユーザー間で一対一の通話を行う                                                                           | ✔️   | ✔️            | ✔️
|                   | 3 人以上のユーザーでグループ通話を行う (最大 350 ユーザー)                                                       | ✔️   | ✔️            | ✔️
|                   | 2 人のユーザーによる一対一の通話を 3 人以上のユーザーによるグループ通話に昇格させる                                 | ✔️   | ✔️            | ✔️
|                   | 開始後にグループ通話に参加する                                                                              | ✔️   | ✔️            | ✔️
|                   | 別の VoIP 参加者を招待して、進行中のグループ通話に参加させる                                                       | ✔️   | ✔️            | ✔️
|  通話中の制御 | ビデオをオンまたはオフにする                                                                                              | ✔️   | ✔️            | ✔️
|                   | マイクをミュート/ミュート解除する                                                                                                     | ✔️   | ✔️            | ✔️
|                   | カメラを切り替える                                                                                              | ✔️   | ✔️            | ✔️
|                   | ローカルの保留/保留解除                                                                                                  | ✔️   | ✔️            | ✔️
|                   | アクティブなスピーカー                                                                                                      | ✔️   | ✔️            | ✔️
|                   | 通話用のスピーカーを選択する                                                                                            | ✔️   | ✔️            | ✔️
|                   | 通話用のマイクを選択する                                                                                         | ✔️   | ✔️            | ✔️
|                   | 参加者の状態を表示する<br/>*アイドル状態、初期メディア、接続中、接続、保留中、ロビー、切断*         | ✔️   | ✔️            | ✔️
|                   | 通話の状態を表示する<br/>*初期メディア、着信、接続中、呼び出し中、接続、保留、切断中、切断* | ✔️   | ✔️            | ✔️
|                   | 参加者がミュートされているかどうかを表示する                                                                                      | ✔️   | ✔️            | ✔️
|                   | 参加者が通話を終了した理由を表示する                                                                       | ✔️   | ✔️            | ✔️
| 画面共有    | アプリケーション内から画面全体を共有する                                                                 | ✔️   | ❌            | ❌
|                   | (実行中のアプリケーションの一覧から) 特定のアプリケーションを共有する                                                | ✔️   | ❌            | ❌
|                   | 開いているタブの一覧から Web ブラウザー タブを共有する                                                                  | ✔️   | ❌            | ❌
|                   | 参加者はリモート スクリーン共有を表示可能                                                                            | ✔️   | ✔️            | ✔️
| 名簿            | 参加者の一覧表示                                                                                                   | ✔️   | ✔️            | ✔️
|                   | 参加者の削除                                                                                                | ✔️   | ✔️            | ✔️
| PSTN              | PSTN 参加者と一対一の通話を行う                                                                     | ✔️   | ✔️            | ✔️
|                   | PSTN 参加者とグループ通話を行う                                                                           | ✔️   | ✔️            | ✔️
|                   | PSTN 参加者との一対一の通話をグループ通話に昇格させる                                                 | ✔️   | ✔️            | ✔️
|                   | グループ通話から PSTN 参加者としてダイヤルアウトする                                                                    | ✔️   | ✔️            | ✔️
| 全般           | オーディオ テスト サービスを使用して、マイク、スピーカー、およびカメラをテストする (8:echo123 に通話することで利用可能)                   | ✔️   | ✔️            | ✔️
| デバイス管理 | オーディオまたはビデオを使用するアクセス許可を求める                                                                       | ✔️   | ✔️            | ✔️
|                   | カメラの一覧を取得する                                                                                                     | ✔️   | ✔️            | ✔️
|                   | カメラを設定する                                                                                                          | ✔️   | ✔️            | ✔️
|                   | 選択したカメラを取得する                                                                                                 | ✔️   | ✔️            | ✔️
|                   | マイクの一覧を取得する                                                                                                 | ✔️   | ❌           |❌  
|                   | マイクを設定する                                                                                                      | ✔️   | ❌           | ❌  
|                   | 選択したマイクを取得する                                                                                             | ✔️   | ❌           | ❌  
|                   | スピーカーの一覧を取得する                                                                                                   | ✔️   | ❌           | ❌  
|                   | スピーカーを設定する                                                                                                         | ✔️   | ❌           | ❌  
|                   | 選択したスピーカーを取得する                                                                                                | ✔️   | ❌           | ❌  
| ビデオのレンダリング   | 複数の場所で 1 つのビデオをレンダリングする (ローカル カメラまたはリモート ストリーム)                                                  | ✔️   | ✔️            | ✔️
|                   | スケーリング モードを設定または更新する                                                                                           | ✔️   | ✔️            | ✔️
|                   | リモート ビデオ ストリームをレンダリングする                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>Calling SDK ストリーミング サポート
Communication Services Calling SDK では、次のストリーミング構成がサポートされています。

| 制限          |Web | Android、iOS|
|-----------|----|------------|
|**同時に送信できる発信ストリームの数** |1 つのビデオまたは 1 つの画面の共有 | 1 つのビデオと 1 つの画面の共有|
|**同時に表示できる着信ストリームの数** |1 つのビデオまたは 1 つの画面の共有| 6 つのビデオと 1 つの画面の共有 |

## <a name="calling-sdk-timeouts"></a>Calling SDK のタイムアウト

Communication Services Calling SDK では、次のタイムアウトが適用されます。

| アクション           | タイムアウトまでの秒数 |
| -------------- | ---------- |
| 参加者の再接続/削除 | 120 |
| 通話に対する新しいモダリティの追加または削除 (ビデオまたはスクリーン共有の開始/停止) | 40 |
| 通話転送操作に関わるタイムアウト | 60 |
| 1:1 通話確立に関わるタイムアウト | 85 |
| グループ通話確立に関わるタイムアウト | 85 |
| PSTN 通話確立に関わるタイムアウト | 115 |
| 1:1 通話のグループ通話への昇格に関わるタイムアウト | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>OS とブラウザーによる JavaScript Calling SDK のサポート

次の表は、現在使用可能な、サポートされているブラウザーのセットを示しています。 特に明記されていない限り、ブラウザーの最新の 3 つのバージョンがサポートされます。

| プラットフォーム                         | Chrome | Safari*  | Edge (Chromium) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS***                         |  ✔️    | ✔️**   | ❌             |
| Windows***                       |  ✔️    | ❌     | ✔️             |
| Ubuntu、Linux                     |  ✔️    | ❌     | ❌             |

*Safari バージョン 13.1 以降がサポートされていますが、Safari では一対一の通話はサポートされていません。

**送信ビデオのサポートには Safari 14 以降または macOS 11 以降が必要です。

***送信画面の共有は、ブラウザーのバージョンに関係なく、デスクトップ プラットフォーム (Windows、macOS、および Linux) でのみサポートされ、モバイル プラットフォーム (Android、iOS、iPad、およびタブレット) ではサポートされません。

****Safari 上の iOS アプリでは、マイクおよびスピーカー デバイス (Bluetooth など) を列挙または選択できません。これは OS の制限であり、常に 1 つのデバイスのみが存在します。


## <a name="calling-client---browser-security-model"></a>通話クライアント - ブラウザーのセキュリティ モデル

### <a name="user-webrtc-over-https"></a>HTTPS 経由のユーザー WebRTC

`getUserMedia` などの WebRTC API では、これらの API を呼び出すアプリが HTTPS 経由で提供される必要があります。

ローカル開発の場合は、`http://localhost` を使用できます。

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Communication Services 通話 SDK を iframe に埋め込む

新しい[アクセス許可ポリシー (機能ポリシーとも呼ばれます)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) は、さまざまなブラウザーで採用されつつあります。 このポリシーは、アプリケーションがクロスオリジン iframe 要素を介してデバイスのカメラとマイクにアクセスする方法を制御することで通話シナリオに影響を与えます。

iframe を使用して別のドメインからアプリの一部をホストする場合は、適切な値を指定した `allow` 属性を iframe に追加する必要があります。

たとえば、次の iframe では、カメラとマイクの両方へのアクセスを許可します。

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [通話を開始する](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

詳細については、次の記事を参照してください。
- 一般的な[通話のフロー](../call-flows.md)を理解する
- [通話の種類](../voice-video-calling/about-call-types.md)について学習する
- [PSTN ソリューションを計画する](../telephony-sms/plan-solution.md)
