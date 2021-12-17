---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8864e8025c77fb0b4b6efc694d9f4938e9ed8ae7
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108737539"
---
1. ロックを適用するリソース、リソース グループ、またはサブスクリプションの [設定] ブレードで、 **[ロック]** を選択します。

    :::image type="content" source="media/resource-manager-lock-resources/select-lock.png" alt-text="ロックを選択します。":::

1. ロックを追加するには、**[追加]** を選択します。 親レベルでロックを作成する場合は、親を選択します。 現在選択されているリソースは、ロックを親から継承します。 たとえば、リソース グループをロックすることで、グループのリソースすべてにロックを適用することができます。

    :::image type="content" source="media/resource-manager-lock-resources/add-lock.png" alt-text="ロックを追加します。":::

1. ロックに名前とロック レベルを指定します。 必要に応じて、ロックについて説明したメモを追加することができます。

    :::image type="content" source="media/resource-manager-lock-resources/set-lock.png" alt-text="ロックを設定します。":::

1. ロックを削除するには、 **[削除]** ボタンを選択します。

    :::image type="content" source="media/resource-manager-lock-resources/delete-lock.png" alt-text="ロックを削除します。":::
