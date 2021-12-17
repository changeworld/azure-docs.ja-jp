---
author: bwren
ms.author: bwren
ms.service: azure-monitor
ms.topic: include
ms.date: 09/17/2021
ms.openlocfilehash: 5d31cea42dd51cee65e79c44b2a44615db027aff
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298027"
---
Azure Monitor 内のログ データは、Log Analytics ワークスペースに保持されます。 サブスクリプションに既にワークスペースが作成されている場合は、そのワークスペースを使用できます。 また、各 Azure サブスクリプション内に作成された既定のワークスペースを使用することもできます。 

新しい Log Analytics を作成する場合は、次の手順を使用できます。 既存のものを使用する場合は、次のセクションに進んでください。

Azure portal の **[すべてのサービス]** から、 **[Log Analytics ワークスペース]** を選択します。

:::image type="content" source="media/azure-monitor-tutorial-workspace/azure-portal.png" lightbox="media/azure-monitor-tutorial-workspace/azure-portal.png" alt-text="Azure portal で [Log Analytics ワークスペース] を選択する":::

**[作成]** をクリックして、新しいワークスペースを作成します。

:::image type="content" source="media/azure-monitor-tutorial-workspace/create-workspace.png" lightbox="media/azure-monitor-tutorial-workspace/create-workspace.png" alt-text="ワークスペースの [作成] ボタン":::

**[基本]** タブで、ワークスペースの **[サブスクリプション]** 、 **[リソース グループ]** 、および **[リージョン]** を選択します。 これらは監視対象のリソースと同じである必要はありません。 すべての Azure Monitor サブスクリプションでグローバルに一意である必要がある **[名前]** を指定します。

:::image type="content" source="media/azure-monitor-tutorial-workspace/workspace-basics.png" lightbox="media/azure-monitor-tutorial-workspace/workspace-basics.png" alt-text="ワークスペースの[基本]":::


**[確認および作成]** をクリックしてワークスペースを作成します。