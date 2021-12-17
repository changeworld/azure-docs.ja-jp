---
title: Azure NetApp Files の [スナップショット パスを非表示にする] オプションを編集する | Microsoft Docs
description: Azure NetApp Files を使用して、スナップショット ボリュームの可視性をコントロールする方法について説明します。
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
ms.openlocfilehash: 5b165bd2e4ca3ee6ac2d16bac7c4d5ff1117fdc9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699262"
---
# <a name="edit-the-hide-snapshot-path-option-of-azure-netapp-files"></a>Azure NetApp Files の [スナップショット パスを非表示にする] オプションを編集する
[スナップショット パスを非表示にする] オプションを使用して、ボリュームのスナップショット パスを表示するかどうかを制御します。 [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) または [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) のボリュームの作成中に、スナップショット パスを非表示にするかどうかを指定することができます。 その後、必要に応じて、[スナップショット パスを非表示にする] オプションを編集できます。  

> [!NOTE]
> リージョン間レプリケーションの[ターゲット ボリューム](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume)に対しては、[スナップショット パスを非表示にする] オプションが既定で有効になっており、この設定を変更することはできません。 

## <a name="steps"></a>手順

1. ボリュームの [スナップショット パスを非表示にする] オプションの設定を表示するには、ボリュームを選択します。 **[スナップショット パスを非表示にする]** フィールドに、オプションが有効になっているかどうかが表示されます。   
    ![[スナップショット パスを非表示にする] フィールドを示すスクリーンショット。](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. [スナップショット パスを非表示にする] オプションを編集するには、ボリュームのページで **[編集]** をクリックし、必要に応じて **[スナップショット パスを非表示にする]** オプションを変更します。   
    ![ボリューム スナップショットの [編集] オプションを示しているスクリーンショット。](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="next-steps"></a>次のステップ

* [スナップショットの詳細を表示](snapshots-introduction.md)