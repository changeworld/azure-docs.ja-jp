---
title: Azure NetApp Files リージョン間レプリケーションを使用したディザスター リカバリーの管理 | Microsoft Docs
description: Azure NetApp Files リージョン間レプリケーションを使用してディザスター リカバリーを管理する方法について説明します。
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
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 5b1c1a5216b7a1ad5b23167e776f2b0bbb0a578f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590995"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>リージョン間レプリケーションを使用したディザスター リカバリーの管理 

ソース ボリュームとコピー先のボリューム間で継続的にレプリケーション ([ボリューム レプリケーションの作成](cross-region-replication-create-peering.md)に関するページを参照) することで、ディザスター リカバリー イベントに備えることができます。 

このようなイベントが発生した場合は、[コピー先のボリュームにフェールオーバーする](#fail-over-to-destination-volume)ことができます。これにより、クライアントからコピー先のボリュームへの読み取りおよび書き込みが可能になります。 

ディザスター リカバリー後、[再同期](#resync-replication)操作を実行して、ソース ボリュームにフェールバックすることができます。 次に、[ソースからコピー先へのレプリケーションを再確立](#reestablish-source-to-destination-replication)して、クライアントがアクセスできるようにソース ボリュームを再マウントします。 

詳細については、以下を参照してください。 

## <a name="fail-over-to-destination-volume"></a>宛先ボリュームにフェールオーバーする

コピー先のボリュームをアクティブ化する必要がある場合 (たとえば、ターゲット リージョンにフェールオーバーする場合) は、レプリケーションのピアリングを中断してから、コピー先のボリュームをマウントする必要があります。  

1. レプリケーションのピアリングを中断するには、コピー先のボリュームを選択します。 [ストレージ サービス] の **[レプリケーション]** をクリックします。  

2.  続行する前に、次のフィールドを確認してください。  
    * ミラーの状態に、***[Mirrored]\(ミラー化\)*** と表示されていることを確認します。   
        ミラーの状態が *[Uninitialized]\(未初期化\)* と表示されている場合は、レプリケーションのピアリングを中断しないようにしてください。
    * リレーションシップの状態に ***[Idle]\(アイドル状態\)*** と表示されていることを確認します。   
        リレーションシップの状態が *[Transferring]\(転送中\)* と表示されている場合は、レプリケーションのピアリングを中断しないようにしてください。   

    「[レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)」を参照してください。 

3.  **[Break Peering]\(ピアリングの中断\)** をクリックします。  

4.  確認を求められたら「**yes**」と入力し、 **[Break]\(中断\)** ボタンをクリックします。 

    ![レプリケーション ピアリングの中断](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  [「Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する」](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)の手順に従って、コピー先のボリュームをマウントします。   
    このステップにより、クライアントからコピー先のボリュームにアクセスできるようになります。

## <a name="resync-volumes-after-disaster-recovery"></a><a name="resync-replication"></a>ディザスター リカバリー後にボリュームを再同期する

ディザスター リカバリー後、再同期操作を実行して、ソース ボリュームを再アクティブ化できます。  再同期操作によってレプリケーション プロセスが反転され、コピー先のボリュームからソース ボリュームにデータが同期されます。  

> [!IMPORTANT] 
> 再同期操作によって、ソース ボリュームのデータがコピー先のボリュームのデータで上書きされます。  UI に、データ損失の可能性がある旨の警告が表示されます。 操作が開始される前に、この再同期アクションを実行するかどうかを確認するメッセージが表示されます。

1. レプリケーションを再同期するには、 *[ソース]* ボリュームを選択します。 [ストレージ サービス] の **[レプリケーション]** をクリックします。 次に、 **[Resync]\(再同期\)** をクリックします。  

2. 確認を求められたら「**Yes**」と入力し、 **[Resync]\(再同期\)** をクリックします。 
 
    ![レプリケーションの再同期](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. 「[レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)」の手順に従って、ソース ボリュームの正常性状態を監視します。   
    ソース ボリュームの正常性状態に次の値が表示された場合、再同期操作は完了しており、コピー先のボリュームで行われた変更はソース ボリュームでキャプチャされています。   

    * ミラー化の状態:*ミラー化*  
    * 転送状態:*Idle*  

## <a name="reestablish-source-to-destination-replication"></a>ソースからコピー先へのレプリケーションを再確立する

コピー先からソースへの再同期操作が完了したら、レプリケーション ピアリングをもう一度中断して、ソースからコピー先へのレプリケーションを再確立する必要があります。 また、クライアントからアクセスできるようにするため、ソース ボリュームを再マウントする必要があります。  

1. レプリケーション ピアリングを中断します。  
    a. *コピー先* のボリュームを選択します。 [ストレージ サービス] の **[レプリケーション]** をクリックします。  
    b. 続行する前に、次のフィールドを確認してください。   
    * ミラーの状態に、***[Mirrored]\(ミラー化\)*** と表示されていることを確認します。   
    ミラーの状態が *[uninitialized]\(未初期化\)* と表示されている場合は、レプリケーションのピアリングを中断しないようにしてください。  
    * リレーションシップの状態に ***[Idle]\(アイドル状態\)*** と表示されていることを確認します。   
    リレーションシップの状態が *[Transferring]\(転送中\)* と表示されている場合は、レプリケーションのピアリングを中断しないようにしてください。    

        「[レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)」を参照してください。 

    c. **[Break Peering]\(ピアリングの中断\)** をクリックします。   
    d. 確認を求められたら「**yes**」と入力し、 **[Break]\(中断\)** ボタンをクリックします。  

2. コピー先のボリュームとソース ボリュームを再同期します。  
    a. *コピー先* のボリュームを選択します。 [ストレージ サービス] の **[レプリケーション]** をクリックします。 次に、 **[Resync]\(再同期\)** をクリックします。   
    b. 確認を求められたら「**yes**」と入力し、 **[Resync]\(再同期\)** ボタンをクリックします。

3. [「Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する」](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)の手順に従って、ソース ボリュームを再マウントします。  
    このステップにより、クライアントからソース ボリュームにアクセスできるようになります。

## <a name="next-steps"></a>次のステップ  

* [リージョン間レプリケーション](cross-region-replication-introduction.md)
* [リージョン間レプリケーションを使用するための要件と考慮事項](cross-region-replication-requirements-considerations.md)
* [レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)
* [リージョン間レプリケーションの宛先ボリュームのサイズを変更する](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [ボリューム レプリケーション メトリック](azure-netapp-files-metrics.md#replication)
* [ボリューム レプリケーションまたはボリュームを削除する](cross-region-replication-delete.md)
* [リージョン間レプリケーションのトラブルシューティング](troubleshoot-cross-region-replication.md)

