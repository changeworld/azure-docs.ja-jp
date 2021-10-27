---
title: リソースを管理するためのプライベート リンクを作成する - Azure portal
description: Azure portal を使用して、リソースを管理するためのプライベート リンクを作成します。
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 4d829dbc75aa384e57f706f3e689e8b36365a9c1
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137761"
---
# <a name="use-portal-to-create-private-link-for-managing-azure-resources"></a>ポータルを使用して、Azure リソースを管理するためのプライベート リンクを作成します

この記事では、[Azure Private Link](../../private-link/index.yml) を使用して、サブスクリプション内のリソースを管理するためのアクセスを制限する方法について説明します。 Azure portal を使用して、プライベート アクセスを介したリソース管理の設定について説明しています。

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>リソース管理のプライベート リンクを作成する

リソース管理のプライベート リンクを作成すると、プライベート リンクの関連付けが自動的に作成されます。

1. [ポータル](https://portal.azure.com)で、「**リソース管理のプライベート リンク**」を検索し、使用可能なオプションから選択します。

   :::image type="content" source="./media/create-private-link-access-portal/search.png" alt-text="リソース管理のプライベート リンクの検索":::

1. サブスクリプションにリソース管理のプライベート リンクがまだない場合は、空のページが表示されます。 **[リソース管理のプライベート リンクを作成する]** を選択します。

   :::image type="content" source="./media/create-private-link-access-portal/start-create.png" alt-text="リソース管理のプライベート リンクの作成を選択します":::

1. 新しいリソース管理のプライベート リンクの値を指定します。 新しいリソースには、選択したディレクトリのルート管理グループが使用されます。 **[Review + create]\(レビュー + 作成\)** を選択します。

   :::image type="content" source="./media/create-private-link-access-portal/provide-values.png" alt-text="リソース管理のプライベート リンクの値を指定します":::

1. 検証に合格した後、 **[作成]** を選択します。

## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

次に、このリソース管理のプライベート リンクを参照するプライベート エンドポイントを作成します。

1. **[Private Link センター]** に移動します。 **[プライベート エンドポイントの作成]** を選択します。

   :::image type="content" source="./media/create-private-link-access-portal/private-link-center.png" alt-text="プライベート リンク センターを選択します":::

1. **[基本]** タブで、プライベート エンドポイントの値を指定します。

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-basics.png" alt-text="基本の値を指定します":::

1. **[リソース]** タブで **[Connect to an Azure resource in my directory]\(マイ ディレクトリ内の Azure リソースに接続する\)** を選択します。 リソースの種類として、**Microsoft.Authorization/resourceManagementPrivateLinks** を選択します。 ターゲット サブリソースには、**ResourceManagement** を選択します。

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-resource.png" alt-text="リソースの値を指定します":::

1. **[構成]** タブで、仮想ネットワークを選択します。 プライベート DNS ゾーンと統合することをお勧めします。 **[Review + create]\(レビュー + 作成\)** を選択します。

1. 検証に合格した後、 **[作成]** を選択します。

## <a name="verify-private-dns-zone"></a>プライベート DNS ゾーンを確認する

環境が正しく構成されていることを確認するには、DNS ゾーンのローカル IP アドレスを確認します。

1. プライベート エンドポイントをデプロイしたリソース グループで、**privatelink.azure.com** という名前のプライベート DNS ゾーン リソースを選択します。

1. **management** という名前のレコード セットに有効なローカル IP アドレスが設定されていることを確認します。

   :::image type="content" source="./media/create-private-link-access-portal/verify.png" alt-text="ローカル IP アドレスを確認します":::

## <a name="next-steps"></a>次のステップ

プライベート リンクの詳細については、[Azure Private Link](../../private-link/index.yml) に関するページを参照してください。
