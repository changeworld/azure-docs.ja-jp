---
title: Azure SignalR におけるメッセージと接続
description: Azure SignalR Service におけるメッセージと接続に関する主要な概念の概要。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: c2348df7a1a55584807a03216e294486ddadfc52
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352599"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Azure SignalR Service におけるメッセージと接続

Azure SignalR Service の課金モデルは接続の数とメッセージの数に基づいています。 以下では、メッセージと接続の定義のほか、課金を目的としたそれらのカウント方法について説明します。

## <a name="message-formats-supported"></a>サポートされるメッセージ形式

Azure SignalR Service は、ASP.NET Core SignalR がサポートするのと同じ形式をサポートします。[JSON](https://www.json.org/) と [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>メッセージ サイズ

Azure SignalR Service には、メッセージ サイズに制限はありません。

実際には、大きいメッセージは各 2 KB 以下の小さなメッセージに分割され、個別のメッセージとして送信されます。 メッセージの分割と結合は SDK が処理します。 開発者が対処する必要はありません。

ただし、大きいメッセージはメッセージング パフォーマンスに悪影響を与えます。 可能な限り小さいメッセージ サイズを使用し、各ユース ケース シナリオに最適なメッセージ サイズをテストして選択してください。

## <a name="how-to-count-messages-for-billing-purpose"></a>課金を目的としたメッセージのカウント方法

SignalR Service からの送信メッセージのみがカウントされます。クライアントとサーバー間の ping メッセージは無視されます。

2 KB を超えるメッセージは各 2 KB の複数のメッセージとしてカウントされます。 Azure portal のメッセージ カウント グラフは、ハブあたり 100 件のメッセージごとに更新されます。

たとえば、3 つのクライアントと 1 つのアプリケーション サーバーがあるとします。 1 つのクライアントが 4 KB のメッセージを 1 件送信し、サーバーがすべてのクライアントにブロードキャストしました。 メッセージ数は 8 件になります。サービスからアプリケーション サーバーへのメッセージが 1 件、サービスからクライアントへのメッセージが 3 件、さらに、各メッセージは 2 KB のメッセージが 2 件としてカウントされます。

Azure portal に表示されるメッセージ数は、累計 100 件を超えるまでは 0 件のままです。

## <a name="how-to-count-connections"></a>接続のカウント方法

サーバー接続とクライアント接続があります。 既定では、各アプリケーション サーバーと SignalR Service との接続はハブあたり 5 個で、各クライアントと SignalR Service とのクライアント接続は 1 個です。

Azure portal に表示される接続数には、サーバー接続とクライアント接続の両方が含まれます。

たとえば、2 つのアプリケーション サーバーがあり、コードを使って 5 つのハブを定義しているとします。 この場合のサーバー接続数は、50 (2 アプリ サーバー * 5 ハブ * 5 接続/ハブ) になります。

ASP.NET SignalR では、サーバー接続の計算が異なります。 顧客が定義したハブに加えて、既定のハブが 1 つあります。 各アプリケーション サーバーで、既定でさらに 5 つのサーバー接続が必要になります。 既定のハブの接続数は、他のハブと一貫しています。

## <a name="how-to-count-inbound-traffic--outbound-traffic"></a>受信トラフィック/送信トラフィックのカウント方法

受信/送信は、SignalR Service からの観点になります。 トラフィックは、バイト単位で計算されます。 メッセージ数と同様に、トラフィックにもサンプリング レートがあります。 Azure portal の受信/送信グラフは、ハブあたり 100 KB ごとに更新されます。

## <a name="related-resources"></a>関連リソース

- [Azure Monitor での集計の種類](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR の構成](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)