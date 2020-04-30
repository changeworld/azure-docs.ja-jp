---
title: Azure Automation Update Management で動的グループを使用する
description: この記事では、動的グループが Azure Automation Update Management でどのように機能するかについて説明します。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617438"
---
# <a name="use-dynamic-groups-with-update-management"></a>Update Management を利用して動的グループを使用する

Update Management を使用すると、Azure または Azure 以外の VM の動的グループを更新プログラムの展開の対象にすることができます。 クエリで定義されたこれらのグループは展開時に評価されるため、マシンを追加するために展開を編集する必要はありません。

## <a name="azure-machines"></a>Azure マシン

動的なグループは、クラシック VM では動作しません。 クエリを定義するときに、次の項目をまとめて使用して動的グループを設定できます。

* サブスクリプション
* リソース グループ
* 場所
* Tags

![グループを選択する](./media/automation-update-management/select-groups.png)

動的グループの結果をプレビューするには、 **[プレビュー]** をクリックします。 プレビューには、現時点でのグループ メンバーシップが表示されます。 この例では、グループ **BackendServer** のタグ `Role` を持つマシンを検索しています。 このタグが追加されているマシンがさらにある場合は、それらはそのグループに対する今後の展開に追加されます。

![グループをプレビューする](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Azure 以外のマシン

Azure 以外のマシンの場合、保存された検索 (コンピューター グループとも呼ばれます) が動的グループの作成に使用されます。 保存された検索の作成方法については、「[コンピューター グループの作成](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)」を参照してください。 グループを作成したら、保存された検索の一覧からそれを選択できます。 **[プレビュー]** をクリックすると、保存された検索でのその時点でのコンピューターをプレビューできます。

![グループを選択する](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>次のステップ

動的グループを作成したら、[更新プログラムの展開を作成](automation-tutorial-update-management.md)できます。
