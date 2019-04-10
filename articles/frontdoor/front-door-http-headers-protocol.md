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
ms.openlocfilehash: b34ab417ab1d9ef77c3141d5aa130c338fb89188
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726330"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service - HTTP ヘッダー プロトコルのサポート
このドキュメントでは、次の図で示すような呼び出しパスのさまざまな部分で Azure Front Door Service がサポートするプロトコルの概要を示します。 この後のセクションでは、Front Door がサポートする HTTP ヘッダーについてさらに多くの分析情報を提供しています。

![Azure Front Door Service の HTTP ヘッダー プロトコル][1]

>[!WARNING]
>Azure Front Door Service では、このドキュメントに記載されていない HTTP ヘッダーは保証されていません。

## <a name="1-client-to-front-door"></a>1.クライアントから Front Door
Front Door では受信した要求のほとんどのヘッダーが (変更なしで) 受け入れられますが、一部の予約済みヘッダーは、送信された場合、受信した要求から削除されます。 これには、次のプレフィックスを持つヘッダーが含まれます。
 - X-FD-*

## <a name="2-front-door-to-backend"></a>2.Front Door からバックエンド

Front Door では、上で説明した制限により削除された場合を除き、受信した要求からのヘッダーが含められます。 Front Door では、次のヘッダーの追加も行われます。

| ヘッダー  | 例と説明 |
| ------------- | ------------- |
| Via |  *Via:1.1 Azure* </br> Front Door は、クライアントの HTTP バージョンを加算し、その後ろに、Via ヘッダーの値として "Azure" を付けます。 これは、クライアントの HTTP バージョンを示すと共に、Azure Front Door がクライアントとバックエンドの間での要求の中間受信者となったことを示すために追加されます。  |
| X-Azure-ClientIP | *X-Azure-ClientIP:127.0.0.1* </br> 処理されている要求に関連付けられた "クライアント" インターネット プロトコル アドレスを表します。 たとえば、プロキシから送信される要求では、最初の呼び出し元の IP アドレスを示すために、X-Forwarded-For ヘッダーが追加されることがあります。 |
| X-Azure-SocketIP |  *X-Azure-SocketIP:127.0.0.1* </br> TCP 接続に関連付けられた、ソケットのインターネット プロトコル アドレス (現在の要求の送信元) を表します。 要求のクライアント IP アドレスは、エンドユーザーが任意に上書きできるので、ソケットの IP アドレスと同じではない場合もあります。|
| X-Azure-Ref |  *X-Azure-Ref:0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> これは、Front Door によって提供される要求を示す一意の参照文字列です。 アクセス ログの検索に使用されるため、トラブルシューティングのために重要です。|
| X-Azure-RequestChain |  *X-Azure-RequestChain: hops=1* </br> これは Front Door が要求ループの検出に使用するヘッダーであり、ユーザーはそれに対する依存関係を取得することはできません。 |
| X-Forwarded-For | *X-Forwarded-For:127.0.0.1* </br> X-Forwarded-For (XFF) HTTP ヘッダー フィールドは、HTTP プロキシまたはロード バランサーを経由して Web サーバーに接続しているクライアントの発信元 IP アドレスを識別するための一般的な方法です。 既存の XFF ヘッダーがあった場合、Front Door はそのヘッダーにクライアント ソケット IP を追加します。その他の場合は、クライアント ソケット IP を使用した XFF ヘッダーを追加します。 |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> X-Forwarded-Host HTTP ヘッダー フィールドは、Host HTTP 要求でクライアントによって要求された元のホストを識別するための一般的な方法です (Front Door からのホスト名は要求を処理するバックエンド サーバーが異なる場合があるため)。 |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> X-Forwarded-Proto HTTP ヘッダー フィールドは、HTTP 要求の発信元のプロトコルを識別するための一般的な方法です (構成によっては、リバース プロキシへの要求が HTTP であっても、Front Door が HTTPS を使用してバックエンドと通信する場合があるため)。 |

## <a name="3-front-door-to-client"></a>手順 3.Front Door からクライアント

Front Door からクライアントに送信されるヘッダーを次に示します。 バックエンドから Front Door に送信されるすべてのヘッダーも、クライアントにそのまま渡されます。

| ヘッダー  | 例 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref:0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> これは、Front Door によって提供される要求を示す一意の参照文字列です。 アクセス ログの検索に使用されるため、トラブルシューティングのために重要です。|

## <a name="next-steps"></a>次の手順

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png