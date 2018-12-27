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
ms.openlocfilehash: 0dcb769627714be9da55faf2a8e82c8750789498
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038852"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service - HTTP ヘッダー プロトコルのサポート
このドキュメントでは、次の図で示すような呼び出しパスのさまざまな部分で Azure Front Door Service がサポートするプロトコルの概要を示します。 この後のセクションでは、Front Door がサポートする HTTP ヘッダーについてさらに多くの分析情報を提供しています。

![Azure Front Door Service の HTTP ヘッダー プロトコル][1]

>[!WARNING]
>Azure Front Door Service では、このドキュメントに記載されていない HTTP ヘッダーは保証されていません。

## <a name="1-client-to-front-door"></a>1.クライアントから Front Door
Front Door では受信した要求のほとんどのヘッダーが (変更なしで) 受け入れられますが、一部の予約済みヘッダーは、送信された場合、受信した要求から削除されます。 これには、次のプレフィックスを持つヘッダーが含まれます。
 - X-FD*
 - X-MS*

## <a name="2-front-door-to-backend"></a>2.Front Door からバックエンド

Front Door では、上で説明した制限により削除された場合を除き、受信した要求からのヘッダーが含められます。 Front Door では、次のヘッダーの追加も行われます。

| ヘッダー  | 例と説明 |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> これは、Front Door によって提供される要求を示す一意の参照文字列です。 アクセス ログの検索に使用されるため、トラブルシューティングのために重要です。|
| X-MS-RequestChain |  *X-MS-RequestChain: hops=1* </br> これは Front Door が要求ループの検出に使用するヘッダーであり、ユーザーはそれに対する依存関係を取得することはできません。 |
| X-MS-Via |  *X-MS-Via: Azure* </br> これは、Azure/Front Door がクライアントとバックエンドの間で要求に対する中間受信者であることを示すために、Front Door によって追加されます。 |

## <a name="3-front-door-to-client"></a>手順 3.Front Door からクライアント

Front Door からクライアントに送信されるヘッダーを次に示します。 バックエンドから Front Door に送信されるすべてのヘッダーも、クライアントにそのまま渡されます。

| ヘッダー  | 例 |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> これは、Front Door によって提供される要求を示す一意の参照文字列です。 アクセス ログの検索に使用されるため、トラブルシューティングのために重要です。|

## <a name="next-steps"></a>次の手順

- [Front Door の作成](quickstart-create-front-door.md)方法について説明します。
- [Front Door の機能](front-door-routing-architecture.md)について説明します。

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png