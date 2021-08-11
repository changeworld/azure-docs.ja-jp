---
title: Premium Azure Event Hubs 名前空間の処理ユニットを構成する
description: Premium レベル Event Hubs 名前空間の処理ユニットを構成する手順について説明します。
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: 443832c9fcd4ee8ce8e314a80251f2575aed003d
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110631692"
---
# <a name="configure-processing-units-for-a-premium-tier-azure-event-hubs-namespace"></a>Premium レベル Azure Event Hubs 名前空間の処理ユニットを構成する
このアーティクルでは、Premium レベル Event Hubs 名前空間の処理ユニット (PU) を構成する手順について説明します。 **Premium** レベルの詳細については、「[Event Hubs Premium](event-hubs-premium-overview.md)」を参照してください。

## <a name="configure-processing-units-when-creating-a-namespace"></a>名前空間の作成時に処理ユニットを構成する
Azure portal で **premium** レベルの名前空間を作成すると、自動展開機能が自動的に有効になります。 名前空間の処理ユニット (PU) の数 (1、2、4、8、または 16) を構成できます。これは、名前空間を作成した後で更新できます。 

:::image type="content" source="./media/configure-processing-units-premium-namespace/event-hubs-auto-inflate-prem.png" alt-text="Premium 名前空間を作成するときに有効にするスクリーンショット":::

## <a name="configure-processing-units-for-an-existing-namespace"></a>既存名前空間の処理ユニットを構成する
既存の premium 名前空間の PUs の数を更新するには、次の手順を実行します。 

1. **[Event Hubs Namespace (Event Hubs 名前空間)]** ページの左側にあるメニューの **[Settings (設定)]** の下にある **[スケール]** を選択します。 
1. **[processing units (処理ユニット)]** の値を更新し、 **[Save (保存)]** を選択します。 

    :::image type="content" source="./media/configure-processing-units-premium-namespace/scale-settings-prem.png" alt-text="既存の premium 名前空間の自動インフレを有効にするスクリーンショット":::

## <a name="next-steps"></a>次の手順
処理ユニットと Event Hubs premium レベルの詳細については、次の記事を参照してください。

- [Event Hubs Premium](event-hubs-premium-overview.md)
- [Event Hubs のスケーラビリティ](event-hubs-scalability.md)
