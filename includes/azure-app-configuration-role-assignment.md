---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 05/03/2021
ms.openlocfilehash: 7f5f062e35aad6b7623f1a2f97ab3b9133266ac6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108748284"
---
タスクで App Configuration ストアにアクセスできるように、タスク内で使用する資格情報に、適切な App Configuration のロールの割り当てを割り当てます。

1. ターゲットの App Configuration ストアに移動します。 
1. 左側のメニューで **[アクセス制御 (IAM)]** を選択します。
1. 右側のペインで、 **[ロールの割り当てを追加する]** を選択します。
    
    :::image type="content" source="./media/azure-app-configuration-role-assignment/add-role-assignment-button.png" alt-text="[ロールの割り当ての追加] ボタンを示すスクリーンショット。":::

1. **[ロール]** で、 **[App Configuration データ所有者]** を選択します。 このロールを使用すると、タスクで App Configuration ストアに対して読み取りや書き込みを行うことができます。 
1. 前のセクションで作成したサービス接続に関連付けるサービス プリンシパルを選択します。

    :::image type="content" source="./media/azure-app-configuration-role-assignment/add-role-assignment.png" alt-text="[ロールの割り当ての追加] ダイアログを示すスクリーンショット。":::
