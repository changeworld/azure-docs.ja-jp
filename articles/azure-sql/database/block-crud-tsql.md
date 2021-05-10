---
title: Azure SQL リソースを作成または変更する T-SQL コマンドをブロックする
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: この記事では、Azure SQL リソースを作成または変更する T-SQL コマンド をAzure 管理者がブロックする機能について詳しく説明します。
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 4ecaa8a3ee1d11ea13563ae5c74835b8d62fd960
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555883"
---
# <a name="what-is-block-t-sql-crud-feature"></a>ブロック T-SQL CRUD 機能とは
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


この機能により、Azure 管理者は T-SQL を介して Azure SQL リソースの作成または変更をブロックできます。 これは、T-SQL コマンドが Azure SQL データベースまたはマネージド インスタンスの SQL リソースに影響を与えないようにするために、サブスクリプション レベルで適用されます。

## <a name="overview"></a>概要

T-SQL を介してリソースの作成または変更をブロックし、特定のサブスクリプションの Azure Resource Manager テンプレート (ARM テンプレート) を使用してリソース管理を強制するには、Azure portal のサブスクリプション レベルのプレビュー機能を使用できます。 これは、ARM テンプレートを使用して組織の標準を強制するために [Azure ポリシー](/azure/governance/policy/overview) を使用する場合に特に便利です。 T-SQL は Azure ポリシーに準拠していないため、T-SQL の作成または変更の操作にブロックを適用できます。 ブロックされる構文には、Azure SQL のデータベースの CRUD (create、update、delete) ステートメント、つまり `CREATE DATABASE`、`ALTER DATABASE`、`DROP DATABASE` ステートメントが含まれます。 

T-SQL CRUD 操作は、Azure portal、[PowerShell](/powershell/module/az.resources/register-azproviderfeature)、または [Azure CLI](/cli/azure/feature#az_feature_register) を使用してブロックできます。

## <a name="permissions"></a>アクセス許可

この機能を登録または削除するには、Azure ユーザーがサブスクリプションの所有者ロールまたは共同作成者ロールのメンバーである必要があります。

## <a name="examples"></a>例

次のセクションでは、Azure portal で、Microsoft.Sql リソース プロバイダーを使用してプレビュー機能を登録または登録解除する方法について説明します。 

### <a name="register-block-t-sql-crud"></a>ブロック T-SQL CRUD を登録する

1. Azure portal でサブスクリプションに移動します。
2. **[プレビュー機能]** タブを選択します。 
3. **[ブロック T-SQL CRUD]** を選択します。
4. **[ブロック T-SQL CRUD]** を選択した後、新しいウィンドウが開きます。 **[登録]** を選択して、このブロックを Microsoft.Sql リソース プロバイダーに登録します。

![プレビュー機能の一覧で [ブロック T-SQL CRUD] を選択します。](./media/block-tsql-crud/block-tsql-crud.png)

![[ブロック T-SQL CRUD] チェックボックスをオンにして、[登録] を選択します。](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>Microsoft.sql リソース プロバイダーを再登録します。 
Microsoft.Sql リソース プロバイダーに T-SQL CRUD のブロックを登録した後、変更を有効にするには、Microsoft.Sql リソース プロバイダーを再登録する必要があります。 Microsoft.Sql リソース プロバイダーを再登録するには、次の手順に従います。

1. Azure portal でサブスクリプションに移動します。
2. **[リソース プロバイダー]** タブを選択します。
3. **[Microsoft.Sql]** リソース プロバイダー を検索して選択します。
4. **[再登録]** を選択します。 

> [!NOTE]
> T-SQL ブロックをサブスクリプションに適用するには、再登録の手順が必須です。 

![Microsoft.Sql リソース プロバイダーを再登録する](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>ブロック T-SQL CRUD の削除
T-SQL 作成操作または変更操作へのブロックをサブスクリプションから削除するには、まず、以前に登録した T-SQLブロックの登録を解除します。 次に、T-SQL ブロックの削除を有効にするために、上記のように Microsoft.Sql リソース プロバイダーを再登録します。 


## <a name="next-steps"></a>次の手順

- [Azure SQL Database のセキュリティ機能の概要](security-overview.md)
- [Azure SQL Database のセキュリティに関するベスト プラクティス](security-best-practice.md)
