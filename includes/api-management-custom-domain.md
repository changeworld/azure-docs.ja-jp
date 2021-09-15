---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 08/23/2021
ms.author: vlvinogr
ms.openlocfilehash: 8a213834ede720a3a7f9df39d6030bd48d7d7b09
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779802"
---
## <a name="how-api-management-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>API Management プロキシ サーバーが SSL 証明書を使用して TLS ハンドシェイクで応答する方法

### <a name="clients-calling-with-server-name-indication-sni-header"></a>Server Name Indication (SNI) ヘッダーを使用して呼び出すクライアント
プロキシ (ゲートウェイ) 用に 1 つ以上のカスタム ドメインが構成されている場合、API Management は次の両方からの HTTPS 要求に応答できます。
* カスタム ドメイン (`contoso.com` など)
* デフォルト ドメイン (`apim-service-name.azure-api.net` など) 

SNI ヘッダーの情報に基づいて、API Management は、サーバー証明書を使用して応答します。

### <a name="clients-calling-without-sni-header"></a>SNI ヘッダーを使用せずに呼び出すクライアント
[SNI](https://tools.ietf.org/html/rfc6066#section-3) ヘッダーを送信しないクライアントを使用している場合、API Management は次のロジックに基づいて応答を作成します。

* **このサービスにプロキシ用のカスタム ドメインが 1 つだけ設定されている場合**、そのプロキシ カスタム ドメインに発行された証明書が既定の証明書になります。
* **このサービスにプロキシ用のカスタム ドメインが複数設定されている場合 (**Developer**  および **Premium** サービス レベルでサポート)** 、[defaultSslBinding](/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) プロパティを true ("defaultSslBinding":"true") に設定することで既定の証明書を指定できます。 このプロパティを設定していない場合、既定の証明書は、`*.azure-api.net` でホストされる既定のプロキシ ドメインに発行された証明書になります。

## <a name="support-for-putpost-request-with-large-payload"></a>ペイロードの大きい PUT/POST 要求のサポート

HTTPS でクライアント側の証明書を使用する場合、API Management プロキシ サーバーで (40 KB を超える) 大きなペイロードの要求がサポートされます。 サーバーの要求がフリーズしないように、プロキシ ホスト名に ["negotiateClientCertificate": "true"](/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) プロパティを設定できます。 

このプロパティが true に設定されている場合、HTTP 要求の交換前の SSL/TLS 接続時に、クライアント証明書が要求されます。 この設定は **プロキシ ホスト名** レベルで適用されるため、すべての接続要求でクライアント証明書が求められます。 この制限を回避し、プロキシ用のカスタム ドメインを最大 20 個設定できます (**Premium** サービス レベルでのみサポート)。
