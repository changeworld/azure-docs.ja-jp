---
title: Azure Front Door Standard/Premium (プレビュー) で Private Link を使用して配信元をセキュリティで保護する
description: このページでは、Private Link を使用して配信元への接続をセキュリティで保護する方法について説明します。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: 6a1ec6e0b8862c6ad2b884b019e908e7d2a59a1e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715515"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (プレビュー) で Private Link を使用して配信元をセキュリティで保護する

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [Azure Front Door ドキュメント](../front-door-overview.md)を参照してください。

## <a name="overview"></a>概要

[Azure Private Link](../../private-link/private-link-overview.md) を使用すると、お使いの仮想ネットワーク内のプライベート エンドポイント経由で、Azure PaaS サービスと Azure でホストされているサービスにアクセスできます。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Front Door Premium SKU では、Private Link サービスを使用して、Web アプリとストレージ アカウントの背後にある配信元に接続できます。これにより、配信元をパブリックにアクセス可能にする必要がなくなります。

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Front Door プライベート エンドポイントのアーキテクチャ":::

Azure Front Door Premium の構成で配信元に対して Private Link を有効にすると、ユーザーの代わりに Front Door によって Front Door のリージョン プライベート ネットワークからプライベート エンドポイントが作成されます。 このエンドポイントは、Azure Front Door によって管理されます。 配信元で、承認メッセージの Azure Front Door プライベート エンドポイント要求を受け取ります。 要求を承認すると、Front Door の仮想ネットワークからプライベート IP アドレスが割り当てられ、Azure Front Door と配信元の間のトラフィックによって、Azure ネットワーク バックボーンを使用して確立されたプライベート リンクが走査されます。 配信元への着信トラフィックは、Azure Front Door からの受信時にセキュリティで保護されるようになりました。

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="プライベート エンドポイントの有効化":::

> [!NOTE]
> Private Link の配信元を有効にしてプライベート エンドポイント接続を承認すると、接続が確立されるまでに数分かかります。 この間に、配信元への要求を行うと Front Door エラー メッセージが表示されます。 接続が確立されると、エラー メッセージは表示されなくなります。

## <a name="limitations"></a>制限事項

Azure Front Door のプライベート エンドポイントは、パブリック プレビューの間は、米国東部、米国西部 2、米国中南部の各リージョンで利用できます。

待機時間を最短にするには、Front Door プライベート リンク エンドポイントを有効にすることを選択したときに、配信元に最も近い Azure リージョンを選択する必要があります。

Azure Front Door プライベート エンドポイントは、プラットフォーム、および Azure Front Door のサブスクリプションで管理されます。 Azure Front Door を使用すると、Front Door プロファイルの作成に使用したのと同じ顧客サブスクリプションにプライベート リンク接続できます。

## <a name="next-steps"></a>次のステップ

* Private Link サービスを介して Azure Front Door Premium を Web アプリに接続するには、「[プライベート エンドポイントを使用して Web アプリに接続する](../../private-link/tutorial-private-endpoint-webapp-portal.md)」を参照してください。
* Private Link サービスを介して Azure Front Door Premium をストレージ アカウントに接続する方法については、[プライベート エンドポイントを使用したストレージ アカウントへの接続](../../private-link/tutorial-private-endpoint-storage-portal.md)に関する記事を参照してください。
