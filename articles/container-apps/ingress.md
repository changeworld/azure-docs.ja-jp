---
title: Azure Container Apps プレビューでの HTTPS イングレスの設定
description: Azure Container Apps を使用してアプリでパブリックとプライベートのエンドポイントを有効にする
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5979588ec977ef7655fab21ef30e4fd233c12b43
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026427"
---
# <a name="set-up-https-ingress-in-azure-container-apps-preview"></a>Azure Container Apps プレビューでの HTTPS イングレスの設定

Azure Container Apps を使用すると、イングレスを有効にすることで、コンテナー アプリをパブリックの Web に公開できます。 イングレスを有効にすると、Azure Load Balancer、パブリック IP アドレス、またはその他の Azure リソースを作成しなくても、受信 HTTPS 要求を有効にすることができます。

イングレスが有効になっているコンテナー アプリには、次のような特徴があります。

- TLS 終端をサポートする
- HTTP/1.1 および HTTP/2 をサポートする
- エンドポイントは常に TLS 1.2 を使用し、イングレス ポイントで終了する
- エンドポイントは、常にポート 80 (HTTP 用) と 443 (HTTPS 用) を公開する。
  - 既定では、ポート 80 への HTTP 要求は 443 の HTTPS に自動的にリダイレクトされます。

## <a name="configuration"></a>構成

イングレスは、アプリケーション全体に適用される設定です。 イングレス設定の変更はすべてのリビジョンに同時に適用され、新しいリビジョンは生成されません。

イングレス構成セクションは、次のような形式になります。

```json
{
  ...
  "configuration": {
      "ingress": {
          "external": true,
          "targetPort": 80,
          "transport": auto
      }
  }
}
```

イングレスを構成するときには、次の設定を使用できます。

| プロパティ | 説明 | 値 | 必須 |
|---|---|---|---|
| `external` | イングレス IP と完全修飾ドメイン名は、インターネットの外部に表示することも、VNET 内で内部的に表示することもできます。 |`true` が外部表示用で、`false` が内部表示用 (既定値) | はい |
| `targetPort` | コンテナーが受信要求をリッスンするポート。 | この値には、コンテナーで使用するポート番号を設定します。 アプリケーションのイングレス エンドポイントは、常にポート `443` で公開されています。 | はい |
| `transport` | HTTP/1.1 または HTTP/2 を使用することも、トランスポートの種類を自動的に検出するように設定することもできます。 | `http` が HTTP/1 の場合、`http2` が HTTP/2 の場合、`auto` がトランスポートの種類を自動的に検出する場合 (既定値) | いいえ |
| `allowInsecure` | コンテナー アプリへの、セキュリティで保護されていないトラフィックを許可します。 | `false` (既定値)、`true`<br><br>`true` に設定した場合、ポート 80 への HTTP 要求は HTTPS を使用してポート 443 に自動的にリダイレクトされず、安全でない接続が許可されます。 | いいえ |

> [!NOTE]
> アプリケーションのイングレスを無効にするには、`ingress` 構成プロパティ全体を省略します。

## <a name="ip-addresses-and-domain-names"></a>IP アドレスとドメイン名

イングレスが有効になっている場合、アプリケーションには完全修飾ドメイン名 (FQDN) が割り当てられます。 ドメイン名の形式は次のとおりです。

|イングレスの可視性の設定 | 完全修飾ドメイン名 |
|---|---|
| 外部 | `<APP_NAME>.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io`|
| 内部 | `<APP_NAME>.internal.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io` |

コンテナー アプリ環境には、`external` イングレスの可視性を持つアプリケーション用に 1 つのパブリック IP アドレスがあり、`internal` イングレスの可視性を持つアプリケーション用に 1 つの内部 ID アドレスがあります。 そのため、コンテナー アプリ環境内の、`external` イングレスの可視性を持つすべてのアプリケーションは、単一のパブリック IP アドレスを共有します。 同様に、コンテナー アプリ環境内の、`internal` イングレスの可視性を持つすべてのアプリケーションは、単一の内部 IP アドレスを共有します。 HTTP トラフィックは、ホスト ヘッダー内の FQDN に基づいて個々のアプリケーションにルーティングされます。

環境の設定に対してクエリを実行することで、環境の一意の識別子を取得できます。

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

> [!div class="nextstepaction"]
> [スケーリングの管理](scale-app.md)
