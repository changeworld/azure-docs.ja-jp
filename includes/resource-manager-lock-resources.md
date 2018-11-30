---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: 03e4053b65cf39101e8cb5d35ce439a759ec11d6
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440284"
---
1. ロックを適用するリソース、リソース グループ、またはサブスクリプションの [設定] ブレードで、 **[ロック]** を選択します。
   
      ![ロックを選択する](./media/resource-manager-lock-resources/select-lock.png)
2. ロックを追加するには、 **[追加]** を選択します。 親レベルでロックを作成する場合は、親を選択します。 現在選択されているリソースは、ロックを親から継承します。 たとえば、リソース グループをロックすることで、グループのリソースすべてにロックを適用することができます。
   
      ![ロックを追加する](./media/resource-manager-lock-resources/add-lock.png) 
3. ロックに名前とロック レベルを指定します。 必要に応じて、ロックについて説明したメモを追加することができます。
   
      ![ロックを設定する](./media/resource-manager-lock-resources/set-lock.png) 
4. ロックを削除するには、省略記号 (...) を選択し、表示されるオプションから **[削除]** を選択します。
   
      ![ロックを削除する](./media/resource-manager-lock-resources/delete-lock.png) 

