---
title: Azure Front Door Service - HTTP ヘッダー プロトコルのサポート | Microsoft Docs
description: この記事では、Front Door によってサポートされる HTTP ヘッダー プロトコルについて説明します。
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: efb3ffeb2e5fdde79fe3741377b8a06074671fea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951136"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service - HTTP ヘッダー プロトコルのサポート
このドキュメントでは、次の図で示すような呼び出しパスのさまざまな部分で Azure Front Door Service がサポートするプロトコルの概要を示します。 この後のセクションでは、Front Door がサポートする HTTP ヘッダーについてさらに多くの分析情報を提供しています。

![Azure Front Door Service の HTTP ヘッダー プロトコル][1]

>[!WARNING]
>Azure Front Door Service では、このドキュメントに記載されていない HTTP ヘッダーは保証されていません。

## <a name="1-client-to-front-door"></a>1.クライアントから Front Door

次のヘッダーは、Front Door によって解析されます。 他のヘッダーについては、Front Door はパススルーとして機能し、ヘッダーをアプリケーション バックエンドに送信します。

| ヘッダー  | 例と説明 |
| ------------- | ------------- |
| X-MSEdge-IG  | *X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br> 単一の Web ページをレンダリングするために行われた 1 つまたは複数の HTTP 要求を識別する GUID。 Front Door は、X-FD-ImpressionGuid ヘッダーを使用してバックエンドに GUID を転送します。 </br> クライアントがこのヘッダーを送信しない場合、Front Door は自動的に新しい GUID を生成して、バックエンドに送信します。 |

## <a name="2-front-door-to-backend"></a>2.Front Door からバックエンド

次のヘッダーは、Front Door からアプリケーション バックエンドに送信されます。

| ヘッダー  | 例と説明 |
| ------------- | ------------- |
| X-MS-Ref |  *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Front Door によって提供される要求を識別する ID。 このヘッダー値は、トラブルシューティングのときに最も重要な情報です。 この ID を使用して、アクセス ログを検索できます。 Front Door は、クライアントとバックエンドの両方に同じヘッダーを送信します。 |
| X-FD-ClientHttpVersion | *X-FD-ClientHttpVersion: 1.1* </br>クライアントと Front Door の間の HTTP プロトコルのバージョン。 設定される可能性のある値は、1.1、2.0 などです。 |
| X-FD-ClientIP | *X-FD-ClientIP: 131.107.192.35* </br>処理されている要求に関連付けられている "クライアント" インターネット プロトコル アドレス。 |
| X-FD-EdgeEnvironment | *X-FD-EdgeEnvironment: Edge-Prod-WSTr3* </br>要求を処理した特定の Front Door ノード。 バックエンドでは、デバッグ作業を支援するために、このヘッダーをログに記録できます。 ただし、Front Door の名前付け規則、Front Door ノード間のトラフィックの分布、または特定の個別ノードの可用性には依存しないようにする必要があります。 |
| X-FD-EventID | *X-FD-EventId: FB805145C0124400915BE0E180F3A159* </br>要求処理の開始時に作成された一意識別子 (GUID)。 この要求の処理中に作成されるすべてのアクセス ログは、同じ GUID に関連付けられます。 |
| X-FD-ImpressionGuid | *X-FD-ImpressionGuid: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br>単一の Web ページをレンダリングするために行われた 1 つまたは複数の HTTP 要求を識別する GUID。 クライアント側では、次のようにしてこの GUID を指定できます。 </br>- *X-MSEdge-IG* 要求ヘッダーでの送信。 例: X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F </br>- "ig" クエリ文字列での送信。 例: *?ig=2BC2B67F49ED47DFA47FBE2E962AC81F* クライアントが GUID を送信しない場合、Front Door は新しい GUID を生成します。 |
| X-FD-OriginalURL | *X-FD-OriginalURL: http://www.contoso.com:80/* </br> クライアントによって行われた元の要求の URL。 |
| X-FD-Partner | *X-FD-Partner: ARM-contoso.azurefd.net_Default* </br>要求が処理されている対象の Front Door プロファイルの識別子。 異なる Front Door 用に複数のアプリケーションをホストしているバックエンドでは、現在の要求の提供対象の Front Door 構成を特定する手段として使用できます。 |
| X-FD-RequestHadClientId | 予約済み |
| X-FD-ResponseHasClientId | 予約済み |
| X-FD-SocketIP | *X-FD-SocketIP: 131.107.192.35* </br>上の X-FD-ClientIP と同じです。 |

## <a name="3-front-door-to-client"></a>手順 3.Front Door からクライアント

Front Door からクライアントに送信されるヘッダーを次に示します。 バックエンドから Front Door に送信されるすべてのヘッダーも、クライアントにそのまま渡されます。

| ヘッダー  | 例 |
| ------------- | ------------- |
| X-MS-Ref  | *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br>Front Door によって提供される要求を識別する ID。 このヘッダー値は、トラブルシューティングのときに最も重要な情報です。 この ID を使用して、アクセス ログを検索できます。 Front Door は、クライアントとバックエンドの両方に同じヘッダーを送信します。 |

## <a name="next-steps"></a>次の手順

- [Front Door を作成する](quickstart-create-front-door.md)方法を参照します。
- [Front Door のしくみ](front-door-routing-architecture.md)を参照します。

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png