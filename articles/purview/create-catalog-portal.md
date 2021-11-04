---
title: 'クイックスタート: Azure portal で Purview アカウントを作成する'
description: このクイックスタートでは、Azure Purview アカウントを作成し、アクセス許可を構成して、その使用を開始する方法について説明します。
author: nayenama
ms.author: nayenama
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: purview
ms.custom: mode-portal
ms.openlocfilehash: a84af112016ea4775f19234e89551f5f69b059bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037855"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>クイックスタート: Azure portal で Azure Purview アカウントを作成する

このクイックスタートでは、Azure portal で Azure Purview アカウントを作成し、Purview でデータを分類、保護、検出するプロセスを開始する手順について説明します。

Azure Purview は、データ環境の管理と制御に役立つデータ ガバナンス サービスです。 Purview では、オンプレミス、マルチクラウド、サービスとしてのソフトウェア (SaaS) のソース全体のデータに接続することで、情報の最新のマップが作成されます。 これにより、機密データが識別されて分類され、エンドツーエンドの系列が提供されます。 データ コンシューマーは組織全体のデータを検出でき、データ管理者はデータを監査し、セキュリティで保護し、データの適切な使用を確保することができます。

Purview の詳細については、[概要ページを参照してください](overview.md)。 組織全体に Purview をデプロイする方法の詳細については、[デプロイのベスト プラクティスを参照してください](deployment-best-practices.md)。

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

## <a name="create-an-azure-purview-account"></a>Azure Purview アカウントを作成する

1. [Azure portal](https://portal.azure.com) の **[Purview アカウント]** ページに移動します。

    :::image type="content" source="media/create-catalog-portal/purview-accounts-page.png" alt-text="Azure portal の [Purview アカウント] ページを示すスクリーンショット":::

1. **[作成]** を選択して、新しい Azure Purview アカウントを作成します。

   :::image type="content" source="media/create-catalog-portal/select-create.png" alt-text="Azure portal の Azure Purview を示すスクリーンショット。[作成] ボタンが強調表示されています。":::
  
      または、マーケットプレースに移動し、**Azure Purview** を検索し、 **[作成]** を選択することもできます。

     :::image type="content" source="media/create-catalog-portal/search-marketplace.png" alt-text="Azure Marketplace の Azure Purview を示すスクリーンショット。[作成] ボタンが強調表示されています。":::

1. 新しい [Create Purview account]\(Purview アカウントの作成\) ページの **[基本]** タブで、Purview アカウントを作成する Azure サブスクリプションを選択します。

1. Purview アカウントを保持するために、既存の **リソース グループ** を選択するか、新しく作成します。

    リソース グループについて詳しくは、[リソース グループを使用した Azure リソースの管理](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)に関する記事を参照してください。

1. **Purview アカウント名** を入力します。 スペースと記号は使用できません。
    Purview アカウントの名前は、グローバルに一意である必要があります。 次のエラーが表示される場合は、Purview アカウントの名前を変更し、再び作成してみてください。

    :::image type="content" source="media/create-catalog-portal/name-error.png" alt-text="[Create Purview account]\(Purview アカウントの作成\) 画面を示すスクリーンショット。アカウント名が既に使用されているため、エラー メッセージが表示されています (強調表示されている部分)。":::

1. **場所** を選択します。
    一覧には、Purview をサポートする場所のみ表示されます。 選択する場所は、Purview アカウントとメタデータを保存するリージョンです。 ソースは、他のリージョンに配置できます。

      > [!Note]
      > Azure Purview は、リージョン間でのアカウントの移動をサポートしていないため、正しいリージョンにデプロイする必要があります。 この詳細については、「[リソースの移動操作のサポート](../azure-resource-manager/management/move-support-resources.md)」を参照してください。

1. **[確認および作成]** を選択し、次に **[作成]** を選択します。 作成が完了するまでに数分かかります。 **[Purview アカウント]** ページの一覧に、新しく作成した Azure Purview アカウント インスタンスが表示されます。

    :::image type="content" source="media/create-catalog-portal/create-resource.png" alt-text="[Create Purview account]\(Purview アカウントの作成\) 画面を示すスクリーンショット。[確認および作成] ボタンが強調表示されています":::

## <a name="open-purview-studio"></a>Purview Studio を開く

Azure Purview アカウントの作成後、Purview Studio を使用してそれにアクセスし、管理します。 Purview Studio を開く方法は 2 つあります。

* [Azure portal](https://portal.azure.com) で Purview アカウントを開きます。 [概要] ページで [Purview Studio を開く] タイルを選択します。
    :::image type="content" source="media/create-catalog-portal/open-purview-studio.png" alt-text="Purview アカウントの [概要] ページを示すスクリーンショット。[Purview Studio を開く] タイルが強調表示されています。":::

* または、[https://web.purview.azure.com](https://web.purview.azure.com) を参照し、自分の Purview アカウントを選択し、ワークスペースにサインインできます。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Purview アカウントを作成する方法、および Purview Studio でそのアカウントにアクセスする方法について説明しました。

次の記事に従い、Purview Studio を操作する方法、コレクションを作成する方法、Purview にアクセス権を付与する方法について学習してください。

* [Purview Studio の使用](use-purview-studio.md)
* [コレクションの作成](quickstart-create-collection.md)
* [Azure Purview アカウントにユーザーを追加する](catalog-permissions.md)
