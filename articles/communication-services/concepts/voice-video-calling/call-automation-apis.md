---
title: Azure Communication Services の Call Automation API の概要
titleSuffix: An Azure Communication Services concept document
description: 通話自動化機能および API の概要について説明します。
author: GrantMeStrength
manager: anvalent
services: azure-communication-services
ms.author: jken
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 63f9182f1ebd888a69eade3d851b888645dd128d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609614"
---
# <a name="call-automation-overview"></a>通話の自動化の概要

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

Call Automation API を使用すると、**サービス** から音声およびビデオによる通話機能にアクセスできます。 これらの API を使用して、予定に関する自動発信リマインダー通話を行ったり、停電や山火事などの事象を先を見越して通知したりするサービス アプリケーションを作成できます。 通話に参加するサービス アプリケーションによって、参加者の参加や離脱などの更新を監視できるため、充実したレポートおよびログ機能を実装できます。

![通話内および通話外のアプリ](../media/call-automation-apps.png)

Call Automation API は、通話内 (アプリケーション参加者またはアプリ参加者) アクションと通話外アクションの両方に対して提供されます。 これらの API セットの主な違いは次の 2 つです。
- 通話内 API では、アプリケーションが参加者として通話に参加する必要があります。 アプリ参加者は、[標準の PSTN および VoIP 料金](https://azure.microsoft.com/pricing/details/communication-services/)で課金されます。
- 通話内 API は、アプリ参加者に関連付けられた `callConnectionId` を使用し、通話外 API では通話インスタンスに関連付けられている `serverCallId` を使用します。 

## <a name="use-cases"></a>ユース ケース
| ユース ケース                                                       | 通話内 (アプリ参加者) | 通話外   |
| ---------------------------------------------------------------| ------------------------- | ------------- |
| ボットと人間の参加者の間で 1:1 の通話をかける、または受ける  | X                         |               |
| オーディオ プロンプトを再生し、応答をリッスンする                    | X                         |               |
| 通話内イベントを監視する                                         | X                         |               |
| 複数の参加者による通話を作成する                        | X                         |               |
| 通話参加者と参加者の詳細を取得する                  | X                         |               |
| 通話参加者を追加または削除する                                | X                         | X             |
| ピア ツー ピア通話でのサーバー側のアクション (記録など)     |                           | X             |
| すべての参加者にオーディオ アナウンスを再生する                   |                           | X             |
| 通話の記録を開始および管理する                                |                           | X             |

## <a name="in-call-app-participant-apis"></a>通話内 (アプリ参加者) API

通話内 API を使用すると、アプリケーションはアプリ参加者として通話内でアクションを実行できます。 アプリケーションが通話に応答または参加すると、`callConnectionId` が割り当てられます。これは、次のような通話内アクションに使用されます。
- 通話参加者を追加または削除する
- オーディオ プロンプトを再生し、DTMF 応答をリッスンする
- 通話名簿の更新情報とイベントをリッスンする
- 通話を切る

![通話内アプリケーション](../media/call-automation-in-call.png)

### <a name="in-call-events"></a>通話内イベント
イベント通知は、通話への参加時に設定される `callbackUri` を介して、呼び出し元アプリケーションに JSON ペイロードとして送信されます。 通話内アプリ参加者に送信されるイベントは次のとおりです。
- CallState イベント (確立中、確立済み、終了中、終了済み)
- DTMF の受信
- オーディオ再生の結果
- メディア処理を取り消す
- 参加者招待の結果
- 参加者の更新

## <a name="out-of-call-apis"></a>Out-of-Call API
通話外 API を使用すると、アプリ参加者の出席なしで、通話または会議に対してアクションを実行できます。 通話外 API は、Calling Client SDK によって提供されるか、Call Automation API を介して通話が作成されたときに生成される `serverCallId` を使用します。 通話外 API はアプリ参加者を必要としないので、ピア ツー ピア通話にサーバー側ビジネス ロジックを実装する場合に便利です。 たとえば、ピア ツー ピア通話として開始され、支援のために領域の専門家を参加させることをエージェント (参加者 B) が望んでいるサポート コール シナリオについて考えます。 参加者 B は、サーバー アプリケーションのクライアント インターフェイスでイベントをトリガーして、参加できる領域の専門家を特定し、通話に招待します。 以下に示すフローの終了状態は、3 名の人の参加者によるグループ通話です。

![通話外アプリケーション](../media/call-automation-out-of-call.png)

通話外 API は、次のようなアクションで使用できます。
- 通話参加者を追加または削除する
- 通話の記録を開始、停止、一時停止、または再開する
                                                       
### <a name="out-of-call-events"></a>通話外イベント
イベント通知は、元の API 呼び出し内に指定されている `callbackUri` を介して、呼び出し元アプリケーションに JSON ペイロードとして送信されます。 対応する通話外イベントを含むアクションは次のとおりです。
- 通話の記録 (開始、停止、一時停止、再開)
- 参加者招待の結果

## <a name="next-steps"></a>次のステップ
詳細については、[通話の自動化のクイック スタート](../../quickstarts/voice-video-calling/call-automation-api-sample.md)に関する記事を参照してください。
