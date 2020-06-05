---
title: Azure Automation Update Management で動的グループを使用する
description: この記事では、Azure Automation の Update Management で動的グループを使用する方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: c440862f1379983d4644fe99f33207456f747d23
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830941"
---
# <a name="use-dynamic-groups-with-update-management"></a>Update Management を利用して動的グループを使用する

Update Management を使用すると、Azure または Azure 以外の VM の動的グループを更新プログラムの展開の対象にすることができます。 動的グループを使用すると、展開を編集してマシンを更新する必要がなくなります。

> [!NOTE]
> 動的なグループは、クラシック VM では動作しません。

Azure または Azure 以外のマシンの動的グループは、Azure portal の **Update Management** から定義できます。 「[複数の Azure 仮想マシンの更新を管理する](manage-update-multi.md)」を参照してください。

動的グループは、展開時に Azure Automation で評価されるクエリによって定義されます。 動的グループ クエリで多数のマシンを取得しても、Azure Automation では一度に最大 1000 台のマシンしか処理できません。 「[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management)」をご覧ください。 

> [!NOTE]
> 1000 台を超えるマシンを更新することが予想される場合は、更新プログラムを複数の更新スケジュールに分割することをお勧めします。 

## <a name="define-dynamic-groups-for-azure-machines"></a>Azure マシンの動的グループを定義する

Azure マシンの動的グループ クエリを定義する場合は、次の項目を使用して動的グループを設定できます。

* サブスクリプション
* リソース グループ
* 場所
* Tags

![グループを選択する](./media/automation-update-management/select-groups.png)

動的グループ クエリの結果をプレビューするには、 **[プレビュー]** をクリックします。 プレビューには、現時点でのグループ メンバーシップが表示されます。 この例では、グループ **BackendServer** のタグ `Role` を持つマシンを検索しています。 このタグが追加されているマシンがさらにある場合は、それらはそのグループに対する今後の展開に追加されます。

![グループをプレビューする](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Azure 以外のマシンの動的グループを定義する

Azure 以外のマシンの動的グループでは、保存された検索 (コンピューター グループとも呼ばれます) を使用します。 保存された検索の作成方法については、「[コンピューター グループの作成](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)」を参照してください。 保存された検索が作成されると、Azure portal の **Update Management** で保存された検索の一覧から選択できるようになります。 **[プレビュー]** をクリックすると、保存された検索でコンピューターをプレビューできます。

![グループを選択する](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>次のステップ

Update Management を使った作業については、「[Azure VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)」を参照してください。
