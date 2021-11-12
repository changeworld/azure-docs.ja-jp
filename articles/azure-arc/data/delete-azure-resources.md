---
title: Azure からのリソースの削除
description: Azure からのリソースの削除
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: ad92c16b70fd2d9f2e137558db1e70c387815a8f
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564140"
---
# <a name="delete-resources-from-azure"></a>Azure からのリソースの削除

この記事では、Azure から Azure Arc 対応データ サービス リソースを削除する方法について説明します。

> [!WARNING]
> この記事で説明されているように、リソースを削除すると、これらのアクションを元に戻すことはできません。

## <a name="before"></a>以前

Azure Arc SQL マネージド インスタンスや Azure Arc データ コントローラーなどのリソースを削除する前に、[Azure への課金データのアップロード (間接接続モード)](view-billing-data-in-azure.md#upload-billing-data-to-azure---indirectly-connected-mode) に関するページに記載されている手順に従って、使用状況情報をエクスポートし、Azure にアップロードして正確な課金計算が行われるようにする必要があります。

## <a name="direct-connectivity-mode"></a>直接接続モード

クラスターが直接接続モードで Azure に接続されている場合は、Microsoft Azure portalを使用してリソースを管理します。 データ コントローラー、マネージド インスタンス、PostgreSQL グループのすべての作成、読み取り、更新、削除 (CRUD) 操作にポータルを使用します。

Azure portal から次を行います。
1. リソース グループを参照し、Azure Arc データ コントローラーを削除します
2. Azure Arc 対応 Kubernetes クラスターを選択し、[概要] ページに進みます
    - [設定] で **[拡張機能]** を選択します
    - [拡張機能] ページで、Azure Arc データ サービス拡張機能 (microsoft.arcdataservice 型) を選択し、 **[アンインストール]** をクリックします
3. 必要に応じて、Azure Arc データ コントローラーが配置されているカスタムの場所を削除します。
4. 必要に応じて、名前空間にほかのリソースが作成されていない場合は、Kubernetes クラスターの名前空間を削除することもできます。



「[Manage Azure resources by using the Azure portal (Azure portal を使用した Azure リソースの管理)](../../azure-resource-manager/management/manage-resources-portal.md)」を参照してください。

## <a name="indirect-connectivity-mode"></a>間接接続モード

間接接続モードでは、Kubernetes からインスタンスを削除しても Azure から削除されることはなく、Azure からインスタンスを削除しても Kubernetes から削除されることはありません。 間接接続モードの場合、リソースの削除は 2 段階のプロセスであり、今後改善される予定です。 Kubernetes が信頼できるソースとなり、Azure はそれを反映するように更新されます。

場合によっては、Azure で Azure Arc 対応データ サービスのリソースを手動で削除する必要があります。  これらのリソースは、次のいずれかのオプションを使用して削除できます。

- [Azure からのリソースの削除](#delete-resources-from-azure)
  - [リソース グループ全体の削除](#delete-an-entire-resource-group)
  - [リソース グループ内の特定のリソースの削除](#delete-specific-resources-in-the-resource-group)
  - [Azure CLI を使用したリソースの削除](#delete-resources-using-the-azure-cli)
    - [Azure CLI を使用した SQL マネージド インスタンス リソースの削除](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Azure CLI を使用した PostgreSQL Hyperscale サーバー グループ リソースの削除](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Azure CLI を使用した Azure Arc データ コントローラー リソースの削除](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Azure CLI を使用したリソース グループの削除](#delete-a-resource-group-using-the-azure-cli)


## <a name="delete-an-entire-resource-group"></a>リソース グループ全体の削除

Azure Arc 対応データ サービスに特定の専用リソース グループを使用していて、そのリソース グループ内の *すべてのもの* を削除したい場合、リソース グループを削除すれば、その中のすべてのものが削除されます。  

Azure portal でリソース グループを削除するには、次の操作を行います。

- Azure portal で Azure Arc 対応データ サービスのリソースが作成されているリソース グループを参照します。
- **[リソース グループの削除]** ボタンをクリックします。
- リソース グループ名を入力して削除を確認し、 **[削除]** をクリックします。

## <a name="delete-specific-resources-in-the-resource-group"></a>リソース グループ内の特定のリソースの削除

Azure portal でリソース グループ内の特定の Azure Arc 対応データ サービスのリソースを削除するには、次の操作を行います。

- Azure portal で Azure Arc 対応データ サービスのリソースが作成されているリソース グループを参照します。
- 削除するリソースをすべて選択します。
- [削除] ボタンをクリックします。
- 「はい」と入力して削除を確認し、 **[削除]** をクリックします。

## <a name="delete-resources-using-the-azure-cli"></a>Azure CLI を使用したリソースの削除

Azure CLI を使用して、特定の Azure Arc 対応データ サービスのリソースを削除できます。

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Azure CLI を使用した SQL マネージド インスタンス リソースの削除

Azure CLI を使用して Azure から SQL マネージド インスタンスのリソースを削除するには、以下のコマンドのプレースホルダー値を置き換えて、そのコマンドを実行します。

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Azure CLI を使用した PostgreSQL Hyperscale サーバー グループ リソースの削除

Azure CLI を使用して Azure から PostgreSQL Hyperscale サーバー グループのリソースを削除するには、以下のコマンドのプレースホルダー値を置き換えて、そのコマンドを実行します。

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Azure CLI を使用した Azure Arc データ コントローラー リソースの削除

> [!NOTE]
> Azure Arc データ コントローラーを削除する前に、それによって管理されているデータベース インスタンスのリソースをすべて削除する必要があります。

Azure CLI を使用して Azure から Azure Arc データ コントローラーを削除するには、以下のコマンドのプレースホルダー値を置き換えて、そのコマンドを実行します。

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Azure CLI を使用したリソース グループの削除

Azure CLI を使用して、[リソース グループを削除する](../../azure-resource-manager/management/delete-resource-group.md)こともできます。
