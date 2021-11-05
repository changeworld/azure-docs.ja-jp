---
title: サーバー信頼グループ
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance でサーバー信頼グループを使用してインスタンス間の信頼を管理する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 857af87fac233ff15bad924746c87e0856f90b1f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131431049"
---
# <a name="set-up-trust-between-instances-with-server-trust-group-azure-sql-managed-instance"></a>サーバー信頼グループを持つインスタンス間の信頼を設定する (Azure SQL Managed Instance)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

サーバー信頼グループ (SQL 信頼グループとも呼ばれる) は、Azure SQL Managed Instance のインスタンス間に信頼を管理するために使用される概念です。 グループを作成すると、そのメンバー間に証明書ベースの信頼が確立されます。 この信頼は、さまざまなクロスインスタンス シナリオに使用できます。 グループからサーバーを削除するか、グループを削除すると、サーバー間の信頼が解除されます。 サーバー信頼グループを作成ないし削除するには、マネージド インスタンスに対する書き込みアクセス許可が必要です。
[サーバー信頼グループ](/azure/templates/microsoft.sql/allversions)は、Azure Resource Managerとしてラベル付けされた **SQL 信頼グループ** Azure portal。


## <a name="set-up-group"></a>グループを設定する

サーバー信頼グループは、 または を使用[Azure PowerShell](/powershell/module/az.sql/new-azsqlservertrustgroup)セットアップ[Azure CLI](/cli/azure/sql/stg)。 

Azure portal を使用してカスタム ドメイン名を作成するには、次のステップのようにする： 

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。

2. サーバー信頼グループSQL Managed Instance追加する予定の Azure アカウントに移動します。

3. **[セキュリティ]** の設定で、 **[SQL trust groups]\(SQL 信頼グループ\)** タブを選択します。

   :::image type="content" source="./media/server-trust-group-overview/sql-trust-groups.png" alt-text="SQL信頼グループ":::

4. **SQL信頼グループ** 構成ページで、**新しいグループ** アイコンを選択します。

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group-button.png" alt-text="新しいグループ":::

5. **[SQL trust groups]\(SQL 信頼グループ\)** の作成ブレードで、 **[グループ名]** を設定します。 これは、グループのサブスクリプション、リソース グループ、およびリージョンで一意である必要があります。 **信頼スコープ** は、サーバー信頼グループで有効になっているインスタンス間シナリオの種類を定義します。 信頼スコープは固定です。使用可能なすべての機能が事前に選択され、変更することはできません。 **サブスクリプション**  と **リソース グループ** を選択して、グループのメンバーになるマネージド インスタンスを選択します。

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group.png" alt-text="SQLグループの作成ブレード":::

6. すべての必須フィールドに値が入力された後、**保存** を 選択します。

## <a name="edit-group"></a>グループの編集 

サーバー信頼グループを編集するには、次の手順に従います。 

1. Azure portal にアクセスします。
1. 信頼グループに属するマネージド インスタンスに移動します。
1. **[セキュリティ]** の設定で、 **[SQL trust groups]\(SQL 信頼グループ\)** タブを選択します。
1. 編集する信頼グループを選択します。
1. **[グループ の構成]** をクリックします。

   :::image type="content" source="./media/server-trust-group-overview/configure-sql-trust-group.png" alt-text="信頼SQLを構成する":::

1. グループに対してマネージド インスタンスを追加または削除します。
1. **保存**  を クリックして選択を確定するか、 **キャンセル** をクリック して変更を破棄します。

## <a name="delete-group"></a>グループの削除

サーバー信頼グループを削除するには、次の手順に従います。 

1. Azure Portal にアクセスします。
1. 信頼グループに属するマネージド SQL移動します。
1. **[セキュリティ]** の設定で、 **[SQL trust groups]\(SQL 信頼グループ\)** タブを選択します。
1. 削除する信頼グループを選択します。

   :::image type="content" source="./media/server-trust-group-overview/select-delete-sql-trust-group.png" alt-text="信頼グループSQL選択する":::

1. **グループ の削除** を選択します。

   :::image type="content" source="./media/server-trust-group-overview/delete-sql-trust-group.png" alt-text="信頼SQLを削除する"::: 

1. 信頼グループ名SQL入力して削除を確定し、 **削除** を選択します。

   :::image type="content" source="./media/server-trust-group-overview/confirm-delete-sql-trust-group-2.png" alt-text="信頼SQL削除を確認する":::

> [!NOTE]
> 信頼グループSQL削除すると、2 つのマネージド インスタンス間の信頼が直ちに削除されない場合があります。 信頼の削除は、マネージド インスタンスの [フェールオーバー](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover)を 呼び出して適用できます。 この最新の更新プログラムに関する「[既知の問題](../managed-instance/doc-changes-updates-known-issues.md)」を確認してください。

## <a name="limitations"></a>制限事項

サーバー信頼グループには、次の制限事項が適用されます。 

 * グループには、Azure のインスタンスのみを含SQL Managed Instance。
 * グループの作成または変更時に信頼スコープを変更することはできません。
 * サーバー信頼グループの名前は、そのサブスクリプション、リソース グループ、およびリージョンに対して一意である必要があります。

## <a name="next-steps"></a>次のステップ

* Azure SQL Managed Instance での分散トランザクションの詳細については、[分散トランザクション](../database/elastic-transactions-overview.md)に関するページを参照してください。
* リリースの更新情報や既知の問題の状態については、「[最新情報](doc-changes-updates-release-notes-whats-new.md)」を参照してください。
* 機能に関するご要望がある場合は、[Managed Instance フォーラム](https://feedback.azure.com/d365community/forum/a99f7006-3425-ec11-b6e6-000d3a4f0f84)に追加してください。
