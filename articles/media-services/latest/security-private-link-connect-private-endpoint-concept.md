---
title: プライベート エンドポイント接続の概要
description: この記事では、Media Services でのプライベート エンドポイント接続の概要を取り上げます。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 346630425cd6a77c7ebf18b6f16a1bbd1bcab705
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091250"
---
# <a name="private-endpoint-connections-overview"></a>プライベート エンドポイント接続の概要

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、Media Services でのプライベート エンドポイント接続の概要を取り上げます。

## <a name="clients-using-vnet"></a>VNet を使用しているクライアント

プライベート エンドポイントを使用する VNet 上のクライアントは、パブリック Media Services エンドポイントに接続しているクライアントと同じ DNS 名を使用して Media Services に接続する必要があります。 Media Services では、VNet からの接続をプライベート リンク上で Media Services エンドポイントへと自動的にルーティングするために、DNS 解決が利用されています。

> [!IMPORTANT]
> プライベート エンドポイントを使用する場合、Media Services エンドポイントに対する DNS 名には、プライベート以外のエンドポイントを使用するときと同じ DNS 名を使用してください。 Media Services エンドポイントへの接続に、その privatelink サブドメインの URL を使用することは避けてください。

既定では、VNet に接続されている[プライベート DNS ゾーン](/dns/private-dns-overview)が作成され、プライベート エンドポイントに必要な更新も行われます。 ただし、独自の DNS サーバーを使用している場合は、DNS 構成に追加の変更が必要になることがあります。 以下の DNS の変更に関するセクションで、プライベート エンドポイントに必要な更新について説明しています。

## <a name="dns-changes-for-private-endpoints"></a>プライベート エンドポイントの DNS の変更

プライベート エンドポイントを作成すると、Media Services の各エンドポイントの **DNS CNAME** リソース レコードは、プレフィックス `privatelink` を含むサブドメイン内のエイリアスに更新されます。 既定で、`privatelink` サブドメインに対応するプライベート DNS ゾーンも作成されます。これには、プライベート エンドポイントの DNS A リソース レコードが含まれます。

プライベート エンドポイントを備える VNet の外部から Media Services の DNS 名を解決すると、Media Services エンドポイントのパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている VNet から解決されると、Media Services の URL はプライベート エンドポイントの IP アドレスに解決されます。

たとえば、`MediaAccountA` という Media Services のストリーミング エンドポイントに使用される DNS リソース レコードは、プライベート エンドポイントのホストとなる VNet の外部から解決されると、次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | CNAME | `<Streaming Endpoint public endpoint>` |
| `<Streaming Endpoint public endpoint>` | CNAME | `<Streaming Endpoint internal endpoint>` |
| `<Streaming Endpoint internal endpoint>` | A | `<Streaming Endpoint public IP address>` |

Media Services エンドポイントに対するパブリック インターネット アクセスの拒否または制限は、IP 許可リストを使用するか、またはアカウント内のすべてのリソースについてパブリック ネットワーク アクセスを無効にすることで行えます。

"MediaAccountA" のサンプル ストリーミング エンドポイントに対する DNS リソース レコードは、プライベート エンドポイントをホストしている VNet 内のクライアントによって解決されると、次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | A | `<Streaming Endpoint public endpoint>` (例: 10.0.0.9) |

この方法を使用すると、プライベート エンドポイントをホストしている VNet 内のクライアントから同じ DNS 名を使用して Media Services エンドポイントにアクセスできます。 VNet の外部のクライアントについても同様です。

ネットワーク上でカスタム DNS サーバーを使用している場合、クライアントで、Media Services エンドポイントの FQDN をプライベート エンドポイントの IP アドレスに解決する必要があります。 プライベート リンク サブドメインを VNet のプライベート DNS ゾーンに委任するように DNS サーバーを構成するか、プライベート エンドポイントの IP アドレスを使用して  `mediaaccounta-usw22.streaming.privatelink.media.azure.net` の A レコードを構成します。

> [!TIP]
> カスタムまたはオンプレミスの DNS サーバーを使用している場合は、"privatelink" サブドメインの Media Services エンドポイント名をプライベート エンドポイントの IP アドレスに解決するように DNS サーバーを構成する必要があります。 これを行うには、VNet のプライベート DNS ゾーンに privatelink サブドメインを委任するか、DNS サーバーで DNS ゾーンを構成し、DNS A レコードを追加します。

ストレージ サービスのプライベート エンドポイントに推奨される DNS ゾーン名、および関連付けられたエンドポイント ターゲット サブリソースは次のとおりです。

| Media Services エンドポイント | プライベート リンク グループ ID | DNS ゾーン名 |
| ----------------------- | --------------------- | ------------- |
| ストリーミング エンドポイント | streamingendpoint | privatelink.media.azure.net |
| キー配信 | streamingendpoint | privatelink.media.azure.net |
| ライブ イベント | liveevent | privatelink.media.azure.net |

プライベート エンドポイントをサポートするように独自の DNS サーバーを構成する方法の詳細については、次の記事を参照してください。

- [Azure 仮想ネットワーク内のリソースの名前解決](/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [プライベート エンドポイントの DNS 構成](/private-link/private-endpoint-overview#dns-configuration)

## <a name="public-network-access-flag"></a>パブリック ネットワーク アクセスのフラグ

Media Services アカウントの `publicNetworkAccess` フラグを使用すると、パブリック インターネットからの Media Services エンドポイントへのアクセスを許可または禁止できます。 `publicNetworkAccess` が無効になっている場合、パブリック インターネットからすべての Media Services エンドポイントへの要求がブロックされます。プライベート エンドポイントへの要求は引き続き許可されます。  

## <a name="service-level-ip-allowlists"></a>サービス レベルの IP 許可リスト

`publicNetworkAccess` が有効になっている場合、サービス レベルの IP 許可リストに基づいてパブリック インターネットからの要求が許可されます。 `publicNetworkAccess` が無効になっている場合、IP 許可リストの設定にかかわらず、パブリック インターネットからの要求はブロックされます。 IP 許可リストが適用されるのは、パブリック インターネットからの要求だけです。プライベート エンドポイントへの要求は、IP 許可リストではフィルタリングされません。
