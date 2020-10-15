---
title: Azure NetApp Files リージョン間レプリケーションのレプリケーションを削除する | Microsoft Docs
description: ソース ボリュームとコピー先のボリュームの間で不要になったレプリケーション接続を削除する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708382"
---
# <a name="delete-replications"></a>レプリケーションの削除

ボリュームのレプリケーションを削除することで、ソース ボリュームとコピー先のボリューム間のレプリケーション接続を終了できます。 ソースまたはコピー先のボリュームから削除操作を実行できます。 削除操作では、レプリケーションの承認のみが削除されます。ソース ボリュームまたはコピー先のボリュームは削除されません。 

## <a name="steps"></a>手順

1. ボリュームのレプリケーションを削除するには、ソースまたはコピー先のボリュームから **[レプリケーション]** を選択します。  

2. **[削除]** をクリックします。    

3. 「**はい**」と入力して削除を確認し、 **[削除]** をクリックします。   

    ![レプリケーションの削除](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>次のステップ  

* [リージョン間レプリケーション](cross-region-replication-introduction.md)
* [リージョン間レプリケーションを使用するための要件と考慮事項](cross-region-replication-requirements-considerations.md)
* [レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)
* [リージョン間レプリケーションのトラブルシューティング](troubleshoot-cross-region-replication.md)

