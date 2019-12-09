---
title: Azure Update Management を利用して動的グループを使用する
description: この記事では、動的グループが Azure Automation Update Management でどのように機能するかについて説明します。
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 11/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 022c2061febb54666acee1cfed4ec595421660a3
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278705"
---
# <a name="use-dynamic-groups-with-update-management"></a>Update Management を利用して動的グループを使用する

Update Management では、Azure または Azure 以外の VM の動的グループを更新プログラムのデプロイの対象にする機能が提供されています。 これらのグループはデプロイ時に評価されるため、マシンを追加するためにデプロイを編集する必要はありません。

## <a name="azure-machines"></a>Azure マシン

これらのグループはクエリによって定義され、更新プログラムのデプロイが開始するときに、そのグループのメンバーが評価されます。 動的なグループは、クラシック VM では動作しません。 クエリを定義するときに、次の項目をまとめて使用して動的グループを設定できます。

* Subscription
* リソース グループ
* 場所
* Tags

![グループを選択する](./media/automation-update-management/select-groups.png)

動的グループの結果をプレビューするには、 **[プレビュー]** ボタンをクリックします。 このプレビューでは、その時点でのグループのメンバーシップが表示されます。この例では、タグ **Role** が **BackendServer** に等しいマシンを検索しています。 このタグを持つマシンがさらに追加された場合、そのグループに対する将来のデプロイに追加されます。

![グループをプレビューする](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Azure 以外のマシン

Azure 以外のマシンの場合、動的グループの作成にコンピューター グループが使用されるため、保存された検索も参照されます。 保存された検索の作成方法については、「[コンピューター グループの作成](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)」を参照してください。 グループを作成した後は、保存された検索の一覧からそのグループを選択できます。 **[プレビュー]** をクリックすると、保存された検索でのその時点でのコンピューターをプレビューできます。

![グループを選択する](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>次の手順

動的グループを作成した後、[更新プログラムのデプロイを作成](automation-tutorial-update-management.md)できます
