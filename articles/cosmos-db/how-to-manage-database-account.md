---
title: Azure Cosmos DB でデータベース アカウントを管理する方法について
description: Azure portal、PowerShell、CLI、Azure Resource Manager テンプレートを使用して、Azure Cosmos DB リソースを管理する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: 6046468281878f8ae6b9ac9f802bb16fd378c616
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434144"
---
# <a name="manage-an-azure-cosmos-account-using-the-azure-portal"></a>Azure portal を使用して Azure Cosmos アカウントを管理する

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

この記事では、Azure portal を使用して、Azure Cosmos アカウントでさまざまなタスクを管理する方法を紹介します。 

> [!TIP]
> Azure Cosmos DB は、[Azure PowerShell](manage-with-powershell.md)、[Azure CLI](sql/manage-with-cli.md)、[Azure Resource Manager テンプレート](./manage-with-templates.md)、[Bicep](sql/manage-with-bicep.md) などの他の Azure 管理クライアントでも管理できます。

## <a name="create-an-account"></a>アカウントを作成する

[!INCLUDE [cosmos-db-create-dbaccount](includes/cosmos-db-create-dbaccount.md)]

## <a name="addremove-regions-from-your-database-account"></a>データベース アカウントのリージョンの追加/削除

> [!TIP]
> 新しいリージョンが追加されるとき、すべてのデータが新しいリージョンに完全にレプリケートおよびコミットされるまで、リージョンには使用可能のマークが付きません。 この操作にかかる時間は、アカウント内に保存されているデータの量によって変動します。 [非同期スループット スケーリング操作](scaling-provisioned-throughput-best-practices.md#background-on-scaling-rus)が進行中の場合、スループットのスケールアップ操作は一時停止され、リージョンの追加または削除操作が完了すると自動的に再開されます。 

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. お使いの Azure Cosmos アカウントに移動して、 **[データをグローバルにレプリケートする]** メニューを開きます。

1. リージョンを追加するには、目的のリージョンに対応する六角形 ( **+** ラベルが付いたもの) をマップ上で選択します。 別の方法でリージョンを追加するには、 **[+ リージョンの追加]** オプションを選択し、ドロップダウン メニューからリージョンを選択します。

1. リージョンを削除するには、チェック マークの付いた青い六角形をマップ上で選択して、1 つ以上のリージョンを消去します。 または、右側で、リージョンの横にある "ごみ箱" (🗑) アイコンを選択します。

1. 変更を保存するには、 **[OK]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="リージョンの追加や削除を行うメニュー":::

単一リージョン書き込みモードでは、書き込みリージョンを削除できません。 その現在の書き込みリージョンを削除する前に、別のリージョンへのフェールオーバーを行う必要があります。

複数リージョン書き込みモードでは、少なくとも 1 つのリージョンがあれば、どのリージョンも追加または削除できます。

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>複数の書き込みリージョンの構成

**[データをグローバルにレプリケートする]** タブを開き、 **[有効]** を選択して複数リージョンの書き込みを有効にします。 複数リージョンの書き込みを有効にすると、アカウントで現在用意されているすべての読み取りリージョンが読み書きリージョンになります。

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Azure Cosmos アカウントの複数リージョン書き込み構成のスクリーンショット":::

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Azure Cosmos アカウントでの自動フェールオーバーの有効化

自動フェールオーバーを選択すると、あるリージョンが利用できなくなったとき、フェールオーバーの優先順位が最も高いリージョンに Azure Cosmos DB がフェールオーバーされます。ユーザー側の操作は必要ありません。 自動フェールオーバーが有効になっているとき、リージョン優先順位を変更できます。 自動フェールオーバーを有効にするには、アカウントに 2 つ以上のリージョンを用意する必要があります。

1. お使いの Azure Cosmos アカウントで、 **[データをグローバルにレプリケートする]** ウィンドウを開きます。

2. ウィンドウの上部で、 **[自動フェールオーバー]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[データをグローバルにレプリケートする] メニュー":::

3. **[自動フェールオーバー]** ウィンドウで、 **[自動フェールオーバーの有効化]** を **[ON]** に設定してください。 

4. **[保存]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="自動フェールオーバーのポータル メニュー":::

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Azure Cosmos アカウントでのフェールオーバーの優先度の設定

Cosmos アカウントに自動フェールオーバーを構成した後、リージョンのフェールオーバーの優先順位を変更できます。

> [!IMPORTANT]
> アカウントに自動フェールオーバーを構成しているとき、書き込みリージョン (フェールオーバーの優先順位がゼロ) は変更できません。 書き込みリージョンを変更するには、自動フェールオーバーを無効にし、手動フェールオーバーを実行する必要があります。

1. お使いの Azure Cosmos アカウントで、 **[データをグローバルにレプリケートする]** ウィンドウを開きます。

2. ウィンドウの上部で、 **[自動フェールオーバー]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[データをグローバルにレプリケートする] メニュー":::

3. **[自動フェールオーバー]** ウィンドウで、 **[自動フェールオーバーの有効化]** を **[ON]** に設定してください。

4. フェールオーバーの優先度を変更するには、カーソルを置くと行の左側に表示される 3 つのドットを使い、読み込みリージョンをドラッグします。

5. **[保存]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="自動フェールオーバーのポータル メニュー":::

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Azure Cosmos アカウントで手動フェールオーバーを実行する

> [!IMPORTANT]
> この操作を成功させるには、Azure Cosmos アカウントに手動フェールオーバーを構成する必要があります。

> [!NOTE]
> [非同期スループット スケーリング操作](scaling-provisioned-throughput-best-practices.md#background-on-scaling-rus)の進行中に手動フェールオーバー操作を実行すると、スループットのスケールアップ操作は一時停止されます。 フェールオーバー操作が完了すると、自動的に再開されます。

1. お使いの Azure Cosmos アカウントに移動して、 **[データをグローバルにレプリケートする]** メニューを開きます。

2. メニューの上部で、 **[手動フェールオーバー]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[データをグローバルにレプリケートする] メニュー":::

3. **[手動フェールオーバー]** メニューで、新しい書き込みリージョンを選択します。 チェック ボックスをオンにして、このオプションによって書き込みリージョンが変更されることを理解していることを示します。

4. フェールオーバーをトリガーするには、 **[OK]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="手動フェールオーバーのポータル メニュー":::

## <a name="next-steps"></a>次のステップ

Azure Cosmos アカウント、データベース、コンテナーの管理方法に関する詳細と例については、次の記事をお読みください。

* [Azure PowerShell を使用した Azure Cosmos DB の管理](manage-with-powershell.md)
* [Azure CLI を使用した Azure Cosmos DB の管理](sql/manage-with-cli.md)
* [Azure Resource Manager テンプレートを使用して Azure Cosmos DB を管理する](./manage-with-templates.md)
* [Bicep を使用して Azure Cosmos DB を管理する](sql/manage-with-bicep.md)
