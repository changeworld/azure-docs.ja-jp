---
title: Azure Automation Update Management で動的グループを使用する
description: この記事では、動的グループが Azure Automation Update Management でどのように機能するかについて説明します。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: bf4c156d2bf9c205bd7545a96b5314dd43b2d02c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690771"
---
# <a name="use-dynamic-groups-with-update-management"></a>Update Management を利用して動的グループを使用する

Update Management を使用すると、Azure または Azure 以外の VM の動的グループを更新プログラムの展開の対象にすることができます。 Using a dynamic group keeps you from having to edit your deployment to update machines.

> [!NOTE]
> 動的なグループは、クラシック VM では動作しません。

You can define dynamic groups for Azure or non-Azure machines from **Update management** in the Azure portal. 複数の Azure 仮想マシンの更新を管理する | Microsoft Docs

A dynamic group is defined by a query that Azure Automation evaluates at deployment time. Even if a dynamic group query retrieves a large number of machines, Azure Automation can process only a maximum of 1000 machines at a time. 「[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management)」をご覧ください。 

> [!NOTE]
> If you expect to update more than 1000 machines, we recommend that you split up the updates among multiple update schedules. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Define dynamic groups for Azure machines

When defining a dynamic group query for Azure machines, you can use the following items to populate the dynamic group:

* サブスクリプション
* リソース グループ
* 場所
* Tags

![グループを選択する](./media/automation-update-management/select-groups.png)

動的グループの結果をプレビューするには、 **[プレビュー]** をクリックします。 プレビューには、現時点でのグループ メンバーシップが表示されます。 この例では、グループ **BackendServer** のタグ `Role` を持つマシンを検索しています。 このタグが追加されているマシンがさらにある場合は、それらはそのグループに対する今後の展開に追加されます。

![グループをプレビューする](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Define dynamic groups for non-Azure machines

A dynamic group for non-Azure machines uses saved searches, also called computer groups. 保存された検索の作成方法については、「[コンピューター グループの作成](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)」を参照してください。 Once your saved search is created, you can select it from the list of saved searches in **Update management** in the Azure portal. **[プレビュー]** をクリックすると、保存された検索でのその時点でのコンピューターをプレビューできます。

![グループを選択する](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>次のステップ

動的グループを作成したら、[更新プログラムの展開を作成](automation-tutorial-update-management.md)できます。
