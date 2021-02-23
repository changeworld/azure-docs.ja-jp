---
title: ITSM コネクタとそれに関連付けられているアクションの削除
description: この記事では、ITSM コネクタと、それに関連付けられているアクション グループを削除する方法について説明します。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 1fbaa48d104642984e604bc66a3aa914fbfed44c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530883"
---
# <a name="deletion-of-unused-itsm-connectors"></a>未使用の ITSM コネクタの削除

未使用のコネクタを削除するプロセスには、次の 2 つのフェーズがあります。

1. 関連付けられたアクションの削除: ITSM コネクタに関連付けられているすべてのアクションを削除する必要があります。 コネクタのないアクションがあるとサブスクリプションでエラーが発生する可能性があり、これは、それを避けるために行う必要があります。

2. 未使用の ITSM コネクタの削除。

## <a name="deletion-of-the-associated-actions"></a>関連付けられたアクションの削除

1. アクション グループを見つけるには、[モニター] に進む必要があります。![モニターの選択のスクリーンショット。](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. [アラート] を選択します。![アラートの選択のスクリーンショット。](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. [アクションの管理] を選択します。![アクションの管理の選択のスクリーンショット。](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. Cherwell に接続されているすべての ITSM コネクタを選択します。![Cherwell に接続されている ITSM コネクタのスクリーンショット。](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. アクション グループを削除します。![アクション グループの削除のスクリーンショット。](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>未使用の ITSM コネクタの削除

1. 上部の検索バーで "ServiceDesk" LA を検索して選択します。!["ServiceDesk" LA を検索して選択するスクリーンショット。](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. "ITSM Connections" を選択し、Cherwell コネクタを選択します。![Cherwell ITSM コネクタのスクリーンショット。](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. [削除] を選択します。![ITSM コネクタの削除のスクリーンショット。](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>次のステップ

* [ITSM Connector での問題のトラブルシューティング](./itsmc-resync-servicenow.md)
