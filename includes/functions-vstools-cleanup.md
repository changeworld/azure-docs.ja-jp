---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/28/2021
ms.author: glenga
ms.openlocfilehash: ba8915bd7b45e38b54d392a2294dba31f3ed8e32
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "110785225"
---
追加コストの発生を避けるために、次の手順に従って関数アプリとその関連リソースを削除してください。

1. Cloud Explorer で、自分のサブスクリプション > **[App Services]** の順に展開し、関数アプリを右クリックして、 **[ポータルで開く]** を選択します。 

1. 関数アプリのページで、 **[概要]** タブを選択してから、 **[リソース グループ]** の下にあるリンクを選択します。

   :::image type="content" source="media/functions-vstools-cleanup/functions-app-delete-resource-group.png" alt-text="関数アプリのページで削除するリソース グループを選択する":::

2. **[リソース グループ]** ページで、含まれているリソースの一覧を確認し、削除するものであることを確認します。
 
3. **[リソース グループの削除]** を選択し、指示に従います。

   削除には数分かかることがあります。 実行されると、通知が数秒間表示されます。 ページの上部にあるベルのアイコンを選択して、通知を表示することもできます。