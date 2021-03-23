---
title: Private Link を使用して Azure Front Door Premium を配信元に接続する
description: Azure portal を使用して、Private Link サービスによって Azure Front Door Premium を配信元に接続する方法について説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 3015a0560171b61aaab05e27b369d9ca531e81c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097872"
---
# <a name="connect-azure-front-door-premium-to-your-origin-with-private-link"></a>Private Link を使用して Azure Front Door Premium を配信元に接続する

この記事では、Azure Private Link サービスを使用して仮想ネットワークでホストされているアプリケーションに接続するように Azure Front Door Premium SKU を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 配信元の Web サーバー用に [Private Link](../../private-link/create-private-link-service-portal.md) サービスを作成します。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-private-endpoint-in-azure-front-door-service"></a>Azure Front Door サービスでプライベート エンドポイントを有効にする

このセクションでは、Azure Private Link サービスを、Azure Front Door Premium SKU のプライベート ネットワークに作成されたプライベート エンドポイントにマップします。 

1. Azure Front Door Premium プロファイル内の *[設定]* で、 **[配信元グループ]** を選択します。

1. Private Link を有効にする配信元が含まれている配信元グループを選択します。

1. **[+ Add an origin]\(+ 配信元の追加\)** を選択して新しい配信元を追加するか、以前に作成した配信元を一覧から選択します。 次に、**Private Link サービスを有効にする** チェックボックスをオンにします。

    :::image type="content" source="../media/how-to-enable-private-link/front-door-private-endpoint-private-link.png" alt-text="[Add an origin]\(配信元の追加\) ページで Private Link を有効にするスクリーンショット。":::

1. **[Azure リソースの選択]** で、 **[In my directory]\(マイ ディレクトリ内\)** を選択します。 次の設定を選択または入力して、Azure Front Door Premium でプライベートに接続するリソースを構成します。
    
    | 設定 | 値 |
    | ------- | ----- |
    | リージョン | 配信元と同じまたは最も近いリージョンを選択します。 |
    | リソースの種類 | **Microsoft.Network/privateLinkServices** を選択します。 |
    | リソース | **myPrivateLinkService** を選択します。 |
    | [Target sub resources]\(ターゲット サブリソース\) | このフィールドは空のままにしておきます。 |
    | 要求メッセージ | メッセージをカスタマイズするか、既定のメッセージを選択します。 |

## <a name="next-steps"></a>次の手順

[Azure Front Door Premium Private Link](concept-private-link.md) について学びます。
