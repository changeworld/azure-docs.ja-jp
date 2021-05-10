---
title: Azure NetApp Files レプリケーション関係の正常性状態を表示する | Microsoft Docs
description: Azure NetApp Files のソース ボリュームまたはレプリケーション先ボリュームのレプリケーションの状態を表示する方法について説明します。
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
ms.date: 03/11/2021
ms.author: b-juche
ms.openlocfilehash: 2819ee3bc76c0b9ff0f35d442e52149096ddc9f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590978"
---
# <a name="display-health-status-of-replication-relationship"></a>レプリケーション関係の正常性状態を表示する 

ソース ボリュームまたはレプリケーション先ボリュームのレプリケーションの状態を表示できます。  

## <a name="display-replication-status"></a>レプリケーションの状態を表示する

1. ソース ボリュームまたはレプリケーション先ボリュームのいずれかで、[ストレージ サービス] の **[レプリケーション]** をクリックします。

    次のレプリケーションの状態と正常性の情報が表示されます。  
    * **エンドポイントの種類** – ボリュームがレプリケーションのソースまたはレプリケーション先のいずれであるかを示します。
    * **正常性** – レプリケーション関係の正常性状態が表示されます。
    * **ミラー状態** – 次のいずれかの値が表示されます。
        * *初期化されていません*:  
            これは、ピアリング関係が作成されるときの初期状態と既定の状態です。 初期化が正常に完了するまで、状態は [初期化されていません] のままになります。
        * *ミラー化*:   
            レプリケーション先ボリュームは初期化されており、ミラーリングの更新を受信する準備ができています。
        * *中断*:   
            これはピアリング関係を解除した後の状態です。 レプリケーション先ボリュームが `‘RW’` で、スナップショットが存在します。
    * **関係の状態** – 次のいずれかの値が表示されます。 
        * *アイドル状態*:  
            転送操作が進行中ではなく、今後の転送は無効になっていません。
        * *転送中*:  
            転送操作が進行中で、今後の転送は無効になっていません。
    * **レプリケーション スケジュール** – 初期化 (ベースライン コピー) の完了後に、ミラーリングの増分更新が実行される頻度を示します。

    * **進行状態** – 現在の転送操作で転送されたデータの合計サイズ (バイト単位) を表示します。 このサイズは実際に転送されたビットであり、ソースとレプリケーション先のボリュームで報告される論理領域とは異なる場合があります。  

    ![レプリケーションの正常性状態](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> 以前のレプリケーション ジョブが完了していない場合、レプリケーション関係には正常性状態が *異常* として表示されます。 この状態は、短い転送期間で大量のデータが転送された結果です (たとえば、大量のデータに 10 分間の転送時間)。 この場合、関係の状態には *転送中* が表示され、正常性状態には *異常* が表示されます。

## <a name="next-steps"></a>次のステップ  

* [リージョン間レプリケーション](cross-region-replication-introduction.md)
* [ディザスター リカバリーの管理](cross-region-replication-manage-disaster-recovery.md)
* [リージョン間レプリケーションの宛先ボリュームのサイズを変更する](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [ボリューム レプリケーション メトリック](azure-netapp-files-metrics.md#replication)
* [ボリューム レプリケーションまたはボリュームを削除する](cross-region-replication-delete.md)
* [リージョン間レプリケーションのトラブルシューティング](troubleshoot-cross-region-replication.md)

