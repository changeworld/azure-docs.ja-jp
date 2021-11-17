---
author: bwren
ms.author: bwren
ms.service: azure-monitor
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: a1df3925bc4e574beb04af2df92a97a964291ace
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349868"
---
[アクション グループ](../articles/azure-monitor/alerts/action-groups.md)は、携帯ショートメール (SMS) メッセージやメールの送信など、アラートが発生したときに実行される一連のアクションを定義するものです。

**[Add action groups]\(アクション グループの追加\)** をクリックして、アラート ルールに追加します。

:::image type="content" source="media/azure-monitor-tutorial-action-group/add-action-group.png" lightbox="./media/azure-monitor-tutorial-action-group/add-action-group.png" alt-text="[Add action groups]\(アクション グループの追加\)":::


選択すべきアクション グループがまだサブスクリプションにない場合は、 **[アクション グループの作成]** をクリックして新たに作成します。

:::image type="content" source="media/azure-monitor-tutorial-action-group/create-action-group.png" lightbox="./media/azure-monitor-tutorial-action-group/create-action-group.png" alt-text="アクション グループの作成":::

アクション グループの **[サブスクリプション]** と **[リソース グループ]** を選択し、ポータルに表示される **[アクション グループ名]** と、メールや SMS 通知に記載される **[表示名]** を指定します。

:::image type="content" source="media/azure-monitor-tutorial-action-group/action-group-basics.png" lightbox="./media/azure-monitor-tutorial-action-group/action-group-basics.png" alt-text="アクション グループの [基本]":::

**[通知]** タブを選択し、アラートが発生したときに適切な人員に通知する方法を追加します。複数の方法を追加することもできます。

:::image type="content" source="media/azure-monitor-tutorial-action-group/action-group-notifications.png" lightbox="./media/azure-monitor-tutorial-action-group/action-group-notifications.png" alt-text="アクション グループの [通知]":::
