---
title: Azure NetApp Files ボリュームのバックアップ機能を無効にする | Microsoft Docs
description: バックアップ保護が不要になったボリュームのバックアップ機能を無効にする方法について説明します。
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
ms.openlocfilehash: 1555a1c42f6365202787750f149b345ccd0cd55c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224145"
---
# <a name="disable-backup-functionality-for-a-volume"></a>ボリュームのバックアップ機能を無効にする 

ボリュームのバックアップ保護が不要になった場合、そのバックアップ機能は無効にすることができます。 

> [!IMPORTANT]
> ボリュームのバックアップを無効にすると、そのボリュームの Azure ストレージ内に格納されているすべてのバックアップが削除されます。

ボリュームが削除されても、ボリュームの削除前にバックアップ ポリシーが無効になっていなかった場合は、そのボリュームに関連するバックアップすべてが Azure ストレージ内に保持され、関連付けられている NetApp アカウントの下に一覧表示されます。 

## <a name="steps"></a>手順

1. **[ボリューム]** を選択します。
2. 無効にするバックアップ機能の特定のボリュームを選択します。
3. **[構成]** をクリックします。
4. [バックアップの構成] ページで、 **[有効]** の設定を **[オフ]** に切り替えます。 確認のためにボリューム名を入力し、 **[OK]** をクリックします。

    ![バックアップが無効になっている [バックアップの構成] ウィンドウで復元先を示すスクリーンショット。](../media/azure-netapp-files/backup-configure-backups-disable.png)

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files バックアップについて](backup-introduction.md)
* [Azure NetApp Files バックアップの要件と考慮事項](backup-requirements-considerations.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [ポリシー ベースのバックアップの構成](backup-configure-policy-based.md)
* [手動バックアップの構成](backup-configure-manual.md)
* [バックアップ ポリシーを管理する](backup-manage-policies.md)
* [バックアップを検索する](backup-search.md)
* [バックアップを新しいボリュームに復元する](backup-restore-new-volume.md)
* [ボリュームのバックアップを削除する](backup-delete.md)
* [ボリュームのバックアップ メトリック](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)

