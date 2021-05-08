---
title: プライベート リンクを使用して Azure Front Door Premium を配信元に接続する
titleSuffix: Azure Private Link
description: Azure Front Door Premium を内部ロード バランサーに接続する方法について説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: tyao
ms.openlocfilehash: 6876692bcf752570ecdc5d42b65da81992ad3743
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803187"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>プライベート リンクを使用して Azure Front Door Premium を配信元に接続する

この記事では、Azure Private Link サービスを使用して内部ロード バランサーの配信元に接続するように Azure Front Door Premium SKU を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

[Private Link サービス](../../private-link/create-private-link-service-portal.md)を作成します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-private-link-to-an-internal-load-balancer"></a>内部ロード バランサーのプライベート リンクを有効にする
 
このセクションでは、Private Link サービスを、Azure Front Door のプライベート ネットワークに作成されたプライベート エンドポイントにマップします。 

1. Azure Front Door Premium プロファイル内の *[設定]* で、 **[配信元グループ]** を選択します。

1. 内部ロード バランサーのプライベート リンクを有効にする配信元グループを選択します。

1. **[+ 配信元の追加]** を選択して、内部ロード バランサーの配信元を追加します。

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="内部ロード バランサーのプライベート リンクを有効にするスクリーンショット。":::

1. **[Azure リソースの選択]** で、 **[In my directory]\(マイ ディレクトリ内\)** を選択します。 次の設定を選択または入力して、Azure Front Door Premium でプライベートに接続するサイトを構成します。

    | 設定 | 値 |
    | ------- | ----- |
    | リージョン | 配信元と同じか最も近いリージョンを選択します。 |
    | リソースの種類 | **Microsoft.Network/privateLinkServices** を選択します。 |
    | リソース | 内部ロード バランサーに関連付けられているプライベート リンクを選択します。 |
    | ターゲット サブリソース | 空白のままにします。 |
    | 要求メッセージ | メッセージをカスタマイズするか、既定値を選択します。 |

1. 次に、 **[追加]** 、 **[更新]** の順に選択して、構成を保存します。

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>ストレージ アカウントからプライベート エンドポイント接続を承認する

1. Private Link センターにアクセスし、 **[プライベート リンク サービス]** を選択します。 次に、プライベート リンク名を選択します。

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="プライベート リンクの一覧のスクリーンショット。":::

1. *[設定]* で、 **[プライベート エンドポイント接続]** を選択します。

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="プライベート リンクの概要ページのスクリーンショット。":::

1. Azure Front Door Premium から "*保留中*" のプライベート エンドポイント要求を選択し、 **[承認]** を選択します。

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="プライベート リンクの保留中の承認のスクリーンショット。":::

1. 承認されると、次のスクリーンショットのようになります。 接続が完全に確立されるまで数分かかります。 これで、Azure Front Door Premium から内部ロード バランサーにアクセスできるようになりました。

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="承認されたプライベート リンク要求のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

[Private Link サービスについて学習します](../../private-link/private-link-service-overview.md)。
