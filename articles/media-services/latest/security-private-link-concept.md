---
title: Azure Media Services でのプライベート リンクの使用の概要
description: この記事では、Azure Media Services でのプライベート リンクの使用の概要について説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 10d27003cfa5300e097c2328deb14c0d9c1f7e93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091251"
---
# <a name="overview-of-using-azure-private-link-with-azure-media-services"></a>Azure Media Services での Azure Private Link の使用の概要

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、Azure Media Services でのプライベート リンクの使用の概要について説明します。

## <a name="when-to-use-private-link-with-media-services"></a>Media Services で Private Link を使用するタイミング

Private Link を使用すると、プライベート ネットワークから Media Services にアクセスすることができます。 Media Services が提供するネットワーク アクセス制御と併用することで、プライベート リンクを使用する際にパブリック インターネットにエンドポイントを公開することなく Media Services を使用できます。

## <a name="azure-private-endpoint-and-azure-private-link"></a>Azure プライベート エンドポイントと Azure Private Link

[Azure プライベート エンドポイント](/private-link/private-endpoint-overview)は、仮想ネットワークのプライベート IP アドレスを使用するネットワーク インターフェイスです。  このネットワーク インターフェイスを使用することで、ユーザーは Azure Private Link 経由でサービスにプライベートかつ安全に接続できます。

Media Services エンドポイントには、プライベート エンドポイントを使用して仮想ネットワークからアクセスすることができます。 プライベート エンドポイントには、ピアリングされた仮想ネットワークや、Express Route または VPN を使用して仮想ネットワークに接続されている他のネットワークからアクセスすることもできます。

[Azure Private Link](/private-link/) を使用すると、仮想ネットワーク内の Media Services のプライベート エンドポイントをパブリック インターネットに公開することなく、アクセスすることができます。 トラフィックは、Microsoft のバックボーン ネットワーク経由でルーティングされます。

## <a name="restricting-access"></a>アクセスの制限

> [!Important]
> プライベート エンドポイントを作成しても、そのエンドポイントへのインターネットアクセスが暗黙的に無効になるわけでは **ありません**。

Media Services アカウントのエンドポイントへのインターネット アクセスは、次の 2 つの方法で制限できます。

- Media Services アカウント内のすべてのリソースへのアクセスを制限する。
- IP 許可リストを使用して、リソースごとに個別にアクセスを制限する。

## <a name="media-services-endpoints"></a>Media Services エンドポイント

| エンドポイント                    | 説明                                                               | プライベート リンクをサポート | インターネット アクセスの制御 |
| --------------------------- | ------------------------------------------------------------------------- | --------------------- | ----------------------- |
| ストリーミング エンドポイント          | 動画をストリーミングし、メディアを HLS および DASH にフォーマットするための配信元サーバー | はい                   | IP 許可リスト            |
| CDN によるストリーミング エンドポイント | 多くの視聴者にメディアをストリーミングする                                              | No                    | CDN による管理          |
| キー配信                | メディア視聴者にメディアのコンテンツ キーと DRM ライセンスを提供する             | はい                   | IP 許可リスト            |
| ライブ イベント                  | ライブ ストリーミング用にメディア コンテンツを取り込む                                  | はい                   | IP 許可リスト            |

> [!NOTE]
> 2020 年 5 月 1 日より前の API バージョンで作成された Media Services アカウントには、レガシ RESTv2 API エンドポイントのエンドポイントもあります (非推奨を保留中)。  このエンドポイントは、プライベート リンクをサポートしていません。

## <a name="other-private-link-enabled-azure-services"></a>Private Link が有効になっているその他の Azure サービス

| サービス                | Media Services 統合                      | Private Link のドキュメント |
| ---------------------- | ----------------------------------------------- | -------------------------- |
| Azure Storage          | メディアの格納に使用                             | [Azure Storage のプライベート エンドポイントを使用する](/storage/common/storage-private-endpoints) |
| Azure Key Vault        | [カスタマー マネージド キー](security-customer-managed-keys-portal-tutorial.md)の格納に使用             | [Azure Key Vault のネットワーク設定を構成する](/key-vault/general/how-to-azure-key-vault-network-security) |
| Azure Resource Manager | Media Services API へのアクセスを提供          | [REST API を使用して Azure リソースを管理するためのプライベート リンクを作成する](/azure-resource-manager/management/create-private-link-access-rest) |
| Event Grid             | [Media Services イベントの通知](./monitoring/job-state-events-cli-how-to.md)を提供 | [Azure Event Grid のトピックまたはドメインに対してプライベート エンドポイントを構成する](/event-grid/configure-private-endpoints)  |

## <a name="private-endpoints-are-created-on-the-media-services-account"></a>Media Services アカウントにプライベート エンドポイントが作成される

キー配信、ストリーミング エンドポイント、およびライブ イベントのプライベート エンドポイントは、個別に作成するのではなく、Media Services アカウントに作成されます。

プライベート IP アドレスは、Media Services プライベート エンドポイント リソースが作成されるときに、Media Services アカウントのストリーミング エンドポイントまたはライブ イベントごとに作成されます。 たとえば、2 つのストリーミング エンドポイントが開始された場合、両方のストリーミング エンドポイントを仮想ネットワークに接続するために 1 つのプライベート エンドポイントを作成する必要があります。 リソースは、同時に複数の仮想ネットワークに接続できます。

Media Services アカウントへのインターネット アクセスは、アカウント内のすべてのリソースに対して、または各リソースに対して個別に制限する必要があります。

## <a name="private-link-pricing"></a>Private Link の価格
料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください

## <a name="private-link-how-tos-and-faqs"></a>Private Link の操作方法と FAQ

- [Azure Resource Management テンプレートによりプライベート リンクを使用して Media Services とストレージ アカウントを作成する](security-private-link-arm-how-to.md)
- [ストリーミング エンドポイントのプライベート リンクを作成する](security-private-link-streaming-endpoint-how-to.md)