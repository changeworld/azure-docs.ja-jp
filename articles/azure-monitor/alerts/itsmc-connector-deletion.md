---
title: 使用されていない ITSM Connector を削除する
description: この記事では、ITSM Connector と、それに関連付けられているアクション グループを削除する方法について説明します。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387940"
---
# <a name="delete-unused-itsm-connectors"></a>使用されていない ITSM Connector を削除する

使用されていない IT Service Management (ITSM) Connector の削除プロセスには、2 つのフェーズがあります。 ITSM Connector に関連付けられているすべてのアクションを削除してから、コネクタ自体を削除します。 まず、アクションを削除します。これは、コネクタのないアクションが原因で、サブスクリプションでエラーが発生する可能性があるからです。

## <a name="delete-associated-actions"></a>関連付けられたアクションを削除する

1. Azure portal で、 **[モニター]** を選択します。
  
    ![[モニター] の選択のスクリーンショット。](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. **[Alerts]** (アラート) を選択します。
   
    ![[アラート] の選択のスクリーンショット。](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. **[アクションの管理]** を選択します。
   
    ![[アクションの管理] の選択のスクリーンショット。](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. 削除する ITSM Connector に関連付けられているアクション グループを選択します。 この記事では、Cherwell コネクタの例を使用します。
   
    ![Cherwell コネクタに関連付けられているアクションのスクリーンショット。](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. 情報を確認し、 **[アクション グループの削除]** を選択します。

    ![アクション グループ情報と、グループを削除するためのボタンのスクリーンショット。](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>コネクタを削除する

1. 検索バーで、「**servicedesk**」を検索します。 次に、リソース一覧で **[ServiceDesk]** を選択します。

    ![ServiceDesk の検索と選択のスクリーンショット。](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. **[ITSM Connections]\(ITSM 接続\)** を選択し、次に Cherwell コネクタを選択します。

    ![Cherwell ITSM Connector のスクリーンショット。](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. **[削除]** を選択します。

    ![ITSM Connector の [削除] ボタンのスクリーンショット。](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>次のステップ

* [ITSM Connector での問題のトラブルシューティング](./itsmc-resync-servicenow.md)
