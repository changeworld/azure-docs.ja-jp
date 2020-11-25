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
ms.date: 11/17/2020
ms.author: b-juche
ms.openlocfilehash: e08b69271ba9d115c26418bc5e421ee6c94b031d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695709"
---
# <a name="delete-replications"></a>レプリケーションの削除

ボリュームのレプリケーションを削除することで、ソース ボリュームとコピー先のボリューム間のレプリケーション接続を終了できます。 ソースまたはコピー先のボリュームから削除操作を実行できます。 削除操作では、レプリケーションの承認のみが削除されます。ソース ボリュームまたはコピー先のボリュームは削除されません。 

## <a name="steps"></a>手順

1. ボリューム レプリケーションを削除する前に、レプリケーション ピアリングが中断されていることを確認してください。    
    「[レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)」と「[レプリケーション ピアリングの中断](cross-region-replication-manage-disaster-recovery.md#break-replication-peering-to-activate-the-destination-volume)」を参照してください。  

1. ボリュームのレプリケーションを削除するには、ソースまたはコピー先のボリュームから **[レプリケーション]** を選択します。  

2. **[削除]** をクリックします。    

3. 「**はい**」と入力して削除を確認し、 **[削除]** をクリックします。   

    ![レプリケーションの削除](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>次のステップ  

* [リージョン間レプリケーション](cross-region-replication-introduction.md)
* [リージョン間レプリケーションを使用するための要件と考慮事項](cross-region-replication-requirements-considerations.md)
* [レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)
* [リージョン間レプリケーションのトラブルシューティング](troubleshoot-cross-region-replication.md)

