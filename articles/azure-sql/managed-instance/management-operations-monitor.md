---
title: 管理操作を監視する
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance の管理操作を監視するさまざまな方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 6cdd3137798e221974dadda78dd55b6ae944bc78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589340"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Azure SQL Managed Instance の管理操作の監視
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance には、新しいマネージド インスタンスのデプロイ、インスタンスのプロパティの更新、不要になったインスタンスの削除の際に使用できる[管理操作](management-operations-overview.md)の監視が用意されています。 

## <a name="overview"></a>概要

すべての管理操作は次のように分類できます。

- インスタンスのデプロイ (新しいインスタンスの作成)。
- インスタンスの更新 (仮想コアや予約済み記憶域などの、インスタンスのプロパティの変更)。
- インスタンスの削除。

ほとんどの管理操作は[長期の操作](management-operations-overview.md#duration)です。 そのため、状態を監視するか、操作手順の進行状況を追跡する必要があります。 

マネージド インスタンスの管理操作を監視するには、いくつかの方法があります。

- [リソース グループのデプロイ](../../azure-resource-manager/templates/deployment-history.md)
- [アクティビティ ログ](../../azure-monitor/essentials/activity-log.md)
- [マネージド インスタンス操作 API](#managed-instance-operations-api)


次の表は、管理操作の監視オプションを比較したものです。 

| オプション | 保持 | キャンセルのサポート | 作成 | 更新 | 削除 | キャンセル | 手順 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| リソース グループのデプロイ | 無限<sup>1</sup> | いいえ<sup>2</sup> | Visible | Visible | 表示されません | Visible | 表示されません |
| アクティビティ ログ | 90 日間 | いいえ | Visible | Visible | Visible | Visible |  表示されません |
| マネージド インスタンス操作 API | 24 時間 | [はい](management-operations-cancel.md) | Visible | Visible | Visible | Visible | Visible |
|  |  |  |  |  |  |  | |

<sup>1</sup> リソース グループのデプロイ履歴は、800 件のデプロイに制限されます。

<sup>2</sup> リソース グループのデプロイは、キャンセル操作をサポートしています。 ただし、キャンセル ロジックにより、キャンセル アクションの実行後にデプロイがスケジュールされた操作のみがキャンセルされます。 リソース グループのデプロイがキャンセルされても、進行中のデプロイはキャンセルされません。 (Azure Resource Manger の観点から) マネージド インスタンスのデプロイは 1 つの長期の手順で構成されているため、リソース グループのデプロイをキャンセルしても、マネージド インスタンスのデプロイはキャンセルされず、操作は完了します。 

## <a name="managed-instance-operations-api"></a>マネージド インスタンス操作 API

管理操作 API は、操作を監視するために特別に設計されています。 マネージド インスタンス操作を監視することで、操作パラメーターと操作手順に関する分析情報を得られるだけでなく、[特定の操作をキャンセルする](management-operations-cancel.md)ことができます。 操作の詳細とキャンセル コマンドに加えて、この API は、マルチリソースのデプロイを含む自動化スクリプトで使用できます。進行状況の手順に基づいて、いくつかの依存するリソースのデプロイを開始することができます。

API は次のとおりです。 

| command | 説明 |
| --- | --- |
|[Managed Instance の操作 - 取得](/rest/api/sql/managedinstanceoperations/get)|マネージド インスタンスに対する管理操作を取得します。|
|[Managed Instance の操作 - キャンセル](/rest/api/sql/managedinstanceoperations/cancel)|マネージド インスタンスに対する非同期操作をキャンセルします。|
|[Managed Instance の操作 - Managed Instance 別に一覧表示](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|マネージド インスタンス上で実行された操作の一覧を取得します。|

> [!NOTE]
> API バージョン 2020-02-02 を使用して、操作の一覧でマネージド インスタンスの作成操作を確認します。 これは、Azure portal と最新の PowerShell および Azure CLI パッケージで使用される既定のバージョンです。

## <a name="monitor-operations"></a>操作を監視する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で、マネージド インスタンスの **[概要]** ページを使用してマネージド インスタンスの操作を監視します。 

たとえば、**作成操作** は、作成プロセスの開始時に **[概要]** ページで確認できます。 

![マネージド インスタンスの作成の進行状況](./media/management-operations-monitor/monitoring-create-operation.png)

**[実行中の操作]** を選択すると、 **[実行中の操作]** ページが開き、 **[作成]** または **[更新]** の操作を確認できます。 また、このページから操作を [[キャンセル]](management-operations-cancel.md) することもできます。  

![マネージド インスタンス操作の詳細](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> Azure portal、PowerShell、Azure CLI、または REST API バージョン 2020-02-02 を使用するその他のツールを介して送信された作成操作は[キャンセルできます](management-operations-cancel.md)。 2020-02-02 より前の REST API バージョンが作成操作の送信に使用された場合、インスタンスのデプロイは開始されますが、このデプロイは操作 API で一覧表示されず、キャンセルできません。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Get-AzSqlInstanceOperation コマンドレットを使用すると、マネージド インスタンスに対する操作の情報を取得できます。 マネージド インスタンスに対するすべての操作を確認することや、操作名を指定して特定の操作を確認することができます。

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

コマンドの詳細な説明については、「[Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)」を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

az sql mi op list を使用すると、マネージド インスタンスに対して実行された操作の一覧を取得できます。 Azure CLI をまだインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

コマンドの詳細については、[az sql mi op](/cli/azure/sql/mi/op) を参照してください。

---

## <a name="next-steps"></a>次のステップ

- 最初のマネージド インスタンスを作成する方法については、[クイック スタート ガイド](instance-create-quickstart.md)を参照してください。
- 機能と比較の一覧については、[共通の SQL 機能](../database/features-comparison.md)に関するページを参照してください。
- VNet の構成の詳細については、[SQL Managed Instance VNet の構成](connectivity-architecture-overview.md)に関するページを参照してください。
- マネージド インスタンスを作成し、バックアップ ファイルからデータベースを復元するためのクイック スタートについては、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
- Azure Database Migration Service を使用して移行する方法のチュートリアルについては、[Database Migration Service を使用した SQL Managed Instance の移行](../../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。