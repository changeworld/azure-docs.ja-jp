---
title: Azure NetApp Files でスナップショットの復元を使用してボリュームを元に戻す | Microsoft Docs
description: Azure NetApp Files を使用してボリュームを以前の状態に戻す方法について説明します。
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
ms.openlocfilehash: 7bf50ee981052424cf0a57f366b3e24b483eb5ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699707"
---
# <a name="revert-a-volume-using-snapshot-revert-with-azure-netapp-files"></a>Azure NetApp Files でスナップショットの復元を使用してボリュームを元に戻す

[スナップショット](snapshots-introduction.md)の復元機能を使用すると、特定のスナップショットが作成されたときの状態にボリュームをすばやく戻すことができます。 ほとんどの場合、ボリュームを元に戻す方が、個々のファイルをスナップショットからアクティブなファイル システムに復元するよりもはるかに高速です。 また、スナップショットを新しいボリュームに復元する場合と比べて、スペース効率が高くなります。 

[ボリュームを元に戻す] オプションは、ボリュームの [スナップショット] メニューにあります。 復元するスナップショットを選択すると、Azure NetApp Files によって、ボリュームが選択したスナップショットの作成時に含まれていたデータとタイムスタンプに戻されます。 

> [!IMPORTANT]
> 選択したスナップショットよりも後に作成されたアクティブなファイル システム データとスナップショットは失われます。 スナップショットの復元操作によって、ターゲット ボリューム内の "*すべて*" のデータが、選択したスナップショットのデータに置き換えられます。 スナップショットを選択するときは、スナップショットの内容と作成日に注意する必要があります。 スナップショットの復元操作を元に戻すことはできません。

## <a name="steps"></a>手順

1. ボリュームの **[スナップショット]** メニューにアクセスします。  復元操作に使用するスナップショットを右クリックします。 **[ボリュームを元に戻す]** を選択します。 

    ![スナップショットの右クリック メニューについて説明したスクリーンショット。](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. [ボリュームをスナップショットに戻す] ウィンドウで、ボリュームの名前を入力し、 **[元に戻す]** をクリックします。   

    これで、選択したスナップショットの特定の時点にボリュームが復元されます。

![[ボリュームをスナップショットに戻す] ウィンドウのスクリーンショット。](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="next-steps"></a>次のステップ

* [スナップショットの詳細を表示](snapshots-introduction.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files のスナップショット 101 ビデオ](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files のスナップショットの概要](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)