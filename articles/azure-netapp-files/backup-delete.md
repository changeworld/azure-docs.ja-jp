---
title: Azure NetApp Files ボリュームのバックアップを削除する |Microsoft Docs
description: ボリュームに対して保持する必要がなくなったバックアップを個別に削除する方法について説明します。
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
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: e1d59d336d331a41bf5f82e650d80d7843ab3977
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250580"
---
# <a name="delete-backups-of-a-volume"></a>ボリュームのバックアップを削除する 

ボリュームに対して保持する必要がなくなったバックアップは個別に削除できます。 バックアップを削除すると、Azure Storage アカウント内の関連付けられているオブジェクトが削除され、省スペースにつながります。  

最新のバックアップは削除できません。  

## <a name="steps"></a>手順

1. **[ボリューム]** を選択します。
2. **[バックアップ]** に移動します。
3. バックアップ リストから、削除するバックアップを選択します。 バックアップの右側にある 3 つのドット (`…` ) をクリックし、[アクション] メニューの **[削除]** をクリックします。

    ![バックアップの [削除] メニューを示すスクリーンショット。](../media/azure-netapp-files/backup-action-menu-delete.png)

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files バックアップについて](backup-introduction.md)
* [Azure NetApp Files バックアップの要件と考慮事項](backup-requirements-considerations.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [ポリシー ベースのバックアップの構成](backup-configure-policy-based.md)
* [手動バックアップの構成](backup-configure-manual.md)
* [バックアップ ポリシーを管理する](backup-manage-policies.md)
* [バックアップを検索する](backup-search.md)
* [バックアップを新しいボリュームに復元する](backup-restore-new-volume.md)
* [ボリュームのバックアップ機能を無効にする](backup-disable.md)
* [ボリュームのバックアップ メトリック](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
