---
title: Azure NetApp Files を使用して新しいボリュームにスナップショットを復元する | Microsoft Docs
description: Azure NetApp Files を使用してスナップショットから新しいボリュームを作成する方法について説明します。
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
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: 1d6c74acffe68fb75fbad72a4e0ad052f4c4cef0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327426"
---
# <a name="restore-a-snapshot-to-a-new-volume-using-azure-netapp-files"></a>Azure NetApp Files を使用して新しいボリュームにスナップショットを復元する

[スナップショット](snapshots-introduction.md)を使用すると、ボリュームの特定の時点への復旧が可能になります。 現時点では、スナップショットから復元できるのは、新しいボリュームに限られています。 

## <a name="steps"></a>手順

1. [ボリューム] ブレードから **[スナップショット]** を選択して、スナップショットの一覧を表示します。 
2. 復元するスナップショットを右クリックし、メニュー オプションから **[新しいボリュームに復元]** を選択します。  

    ![[新しいボリュームに復元] メニュー示すスクリーンショット。](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. [ボリュームの作成] ウィンドウで、新しいボリュームの情報を指定します。  
    * **[名前]**    
        作成するボリュームの名前を指定します。  
        
        名前はリソース グループ内で一意である必要があります。 3 文字以上になるようにしてください。  任意の英数字を使用できます。

    * **[クォータ]**  
        ボリュームに割り当てる論理ストレージの量を指定します。  

    ![[ボリュームの作成] ウィンドウを示すスナップショット。](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. **[確認および作成]** をクリックします。  **Create** をクリックしてください。   
    新しいボリュームでは、スナップショットで使用されていたものと同じプロトコルが使用されます。   
    スナップショットから復元された新しいボリュームが [ボリューム] ブレードに表示されます。

## <a name="next-steps"></a>次のステップ

* [スナップショットの詳細を確認する](snapshots-introduction.md)
* [ボリュームとスナップショットのメトリックを監視する](azure-netapp-files-metrics.md#volumes)
* [スナップショット ポリシーのトラブルシューティング](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files のスナップショット 101 ビデオ](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files のスナップショットの概要](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)
