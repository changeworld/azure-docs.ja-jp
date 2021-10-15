---
title: Azure NetApp Files の手動 QoS 容量プールを管理する | Microsoft Docs
description: 手動 QoS 容量プールの設定や、手動 QoS を使用するための容量プールの変更など、手動 QoS の種類を使用する容量プールを管理する方法について説明します。
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
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: dd92472ff810a46743ffa08b1ea2fdd8f9e01da7
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407591"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>手動 QoS 容量プールを管理する

この記事では、手動 QoS の種類を使用する容量プールを管理する方法について説明します。  

QoS の種類に関する考慮事項を理解するためには、「[Azure NetApp Files のストレージ階層](azure-netapp-files-understand-storage-hierarchy.md)」と「[Azure NetApp Files のパフォーマンスに関する考慮事項](azure-netapp-files-performance-considerations.md)」を参照してください。  

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>手動 QoS 容量プールを設定する 

手動 QoS の種類を使用する新しい容量プールを作成するには:

1. 「[容量プールを作成する](azure-netapp-files-set-up-capacity-pool.md)」の手順に従います。  

2. [新しい容量プール] ウィンドウで、種類として **[手動 QoS]** を選択します。  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>手動 QoS を使用するように容量プールを変更する

現在は自動 QoS の種類を使用している容量プールを、手動 QoS の種類を使用するように変更できます。  

> [!IMPORTANT]
> 容量の種類を手動 QoS に設定することは、永続的な変更です。 種類が手動 QoS である容量プールを、自動 QoS 容量プール変換することはできません。  
> 変換時には、手動 QoS の種類のボリュームのスループット制限に準拠するために、スループット レベルが制限される可能性があります。 「[Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md#resource-limits)」を参照してください。

1. NetApp アカウントの管理ブレードで、 **[容量プール]** をクリックして既存の容量プールを表示します。   
 
2.  手動 QoS を使用するように変更する容量プールをクリックします。

3.  **[QoS の種類の変更]** をクリックします。 次に、 **[新しい QoS の種類]** を **[手動]** に設定します。 **[OK]** をクリックします。 

![QoS の種類を変更する](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>手動 QoS 容量プールのスループットを監視する  

メトリックは、ボリュームの読み取りと書き込みのスループットの監視に役立てるために使用できます。  「[Azure NetApp Files のメトリック](azure-netapp-files-metrics.md)」を参照してください。  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>手動 QoS ボリュームの割り当てられているスループットを変更する 

ボリュームが手動 QoS 容量プールに含まれている場合は、割り当てられているボリュームのスループットを必要に応じて変更できます。

1. **[ボリューム]** ページで、スループットを変更するボリュームを選択します。   

2. **[スループットの変更]** をクリックします。 必要な **スループット (MiB/S)** を指定します。 **[OK]** をクリックします。 

    ![QoS スループットを変更する](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>次のステップ  

* [容量プールの作成](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files のメトリック](azure-netapp-files-metrics.md)
* [Azure NetApp Files のパフォーマンスに関する考慮事項](azure-netapp-files-performance-considerations.md)
* [容量プールに関する問題のトラブルシューティング](troubleshoot-capacity-pools.md)
* [Azure NetApp Files のストレージ階層](azure-netapp-files-understand-storage-hierarchy.md)
* [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
* [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md)
* [デュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md)
