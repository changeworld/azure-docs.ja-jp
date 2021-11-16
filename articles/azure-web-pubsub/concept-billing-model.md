---
title: Azure Web PubSub サービスの課金モデル
description: Azure Web PubSub サービスの課金モデルに関する主要概念の概要。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: 7f937e833a909f15cdc86af35c27bf5240abcfd8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426831"
---
# <a name="billing-model-of-azure-web-pubsub-service"></a>Azure Web PubSub サービスの課金モデル

Azure Web PubSub サービスの課金モデルは、ユニットの数と送信トラフィックのサイズ (メッセージ数) に基づいています。 この記事では、ユニットと送信トラフィック (メッセージ数) がどのように定義され、課金対象としてカウントされるかについて説明します。

## <a name="how-units-are-counted-with-billing-model"></a>課金モデルを使用してユニットをカウントする方法

### <a name="what-is-the-connection"></a>接続とは

クライアントとサーバーとの間の接続を表します。 Azure portal で接続数を監視できます。 [Connection (Max)]\(接続 (最大)\) のビューには、特定の期間における接続の最大数が表示されます。 

### <a name="what-is-the-unit"></a>ユニットとは

ユニットは、Azure Web PubSub サービスの機能の抽象概念です。 各ユニットで最大 1,000 のコンカレント接続がサポートされます。 各 Azure Web PubSub サービス インスタンスは、1、2、5、10、20、50、または 100 ユニットとなります。 したがって、ユニット数は、Web PubSub サービス インスタンスで受け入れることができる接続の数を示します。

###  <a name="how-units-are-counted-with-billing-model"></a>課金モデルを使用してユニットをカウントする方法

ユニットは、ユニットの数と使用時間 (秒) に基づいてカウントされ、日単位で課金されます。 

たとえば、5 ユニットの Azure Web PubSub サービス インスタンスが 1 つあり、午前 10 時から午後 4 時までは 10 ユニットにスケールアップし、午後 4 時以降は 5 ユニットにスケールバックするとします。 この場合、特定の日の 18 時間は 5 ユニットで、6 時間は 10 ユニットであることがわかります。

> 課金対象のユニットの使用量 = (5 ユニット * 18 時間 + 10 ユニット * 6 時間) / 24 時間 = 6.25 ユニット/日

## <a name="how-outbound-traffic-is-counted-with-billing-model"></a>課金モデルでの送信トラフィックのカウント方法

### <a name="what-is-inboundoutbound-traffic"></a>受信/送信トラフィックとは 

送信トラフィックは、Azure Web PubSub サービスから送信されたメッセージです。 Azure portal で送信トラフィックを監視することができます。 [Outbound Traffic (total)]\(送信トラフィック (合計)\) のビューには、特定の期間に集計された送信メッセージのサイズ (バイト数) が表示されます。

- サービスから受信者にブロードキャストされたメッセージ。
- サービスからアップストリーム Webhook に送信されたメッセージ。
- [ライブ トレース ツール](./howto-troubleshoot-resource-logs.md#capture-resource-logs-with-live-trace-tool)を使用したリソース ログ。 

受信トラフィックは、Azure Web PubSub サービスに送信されたメッセージです。 

- クライアントからサービスに送信されたメッセージ。
- サーバーまたは Functions からサービスに送信されたメッセージ。

### <a name="what-is-message-count"></a>メッセージ数とは

課金目的のメッセージ数は抽象概念であり、2 KB に分割された送信トラフィックのサイズ (バイト数) として定義されています。 たとえば、100 KB のトラフィックは 50 メッセージとしてカウントされます。  

### <a name="how-traffic-is-counted-with-billing-model"></a>課金モデルを使用したトラフィックのカウント方法

課金対象として、送信トラフィックのみがカウントされます。 

たとえば、Azure Web PubSub サービスと Azure Functions を使用するアプリケーションがあるとします。 1 人のユーザーが 4 KB のデータをグループ内の 10 個の接続にブロードキャストします。 この場合、サービスから Functions へのアップストリームは 4 KB で、サービスから 10 個の接続へのブロードキャストは 40 KB であることがわかります。

> 課金の送信トラフィック = 4 KB (アップストリーム トラフィック) + 4 KB * 10 (クライアント トラフィックへのサービス ブロードキャスト) = 44 KB

> 相当するメッセージ数 = 44 KB / 2 KB = 22

Azure Web PubSub サービスでは、ユニットの使用量に基づいて、送信トラフィック (メッセージ数) の 1 日の空きクォータも提供します。 空きクォータを超える送信トラフィック (メッセージ数) は、追加の送信トラフィック (追加メッセージ) となります。 たとえば、Standard レベルの場合、空きクォータはユニット/日あたり 200 万 KB の送信トラフィック (100 万メッセージ) となります。

前のユニット使用量の例を使用した場合、アプリケーションで 1 日あたり 6.25 ユニットが使用されることになります。これにより、1 日の空きクォータが 1,250 万 KB の送信トラフィック (625 万メッセージ) として確保されます。 たとえば、1 日の送信トラフィックが 3,000 万 KB (1,500 万メッセージ) である場合、追加メッセージは 1,750 万 KB の送信トラフィック (875 万メッセージ) となります。 そのため、その日の 6.25 の Standard ユニットと 8.75 の追加メッセージ ユニットで課金されることになります。

## <a name="pricing"></a>価格 

Azure Web PubSub サービスでは、価格が異なる複数のレベルが提供されます。 課金モデルでユニットの数と送信トラフィックのサイズ (メッセージ数) がどのようにカウントされるかを理解したら、[Azure Web PubSub サービスの価格](https://azure.microsoft.com/pricing/details/web-pubsub)に関するページで価格についてさらに詳しく学習できます。





