---
title: バックアップを新しい Azure NetApp Files ボリュームに復元する |Microsoft Docs
description: バックアップを新しいボリュームに復元する方法について説明します。
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
ms.openlocfilehash: 5e6302893884674708f584b0261204b2cc891c6c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256383"
---
# <a name="restore-a-backup-to-a-new-volume"></a>バックアップを新しいボリュームに復元する

バックアップを復元すると、同じプロトコルの種類のボリュームが新しく作成されます。 この記事では、復元操作について説明します。 

## <a name="considerations"></a>考慮事項

* 復元操作によって作成された新しいボリュームは、復元が完了するまでマウントできません。 

* ベースライン バックアップがない場合は、復元操作をトリガーする必要があります。 そうしないと、復元によって、ご自身のデータがバックアップされている Azure BLOB アカウントの負荷が増大する可能性があります。 

## <a name="steps"></a>手順

1. **[ボリューム]** を選択します。 **[バックアップ]** に移動します。

    > [!NOTE]
    > ボリュームが削除されても、ボリュームの削除前にバックアップ ポリシーが無効になっていなかった場合、そのボリュームに関するバックアップはすべて Azure ストレージ内に保持されるため、関連付けられている NetApp アカウントの下で見つけることができます。  「[NetApp アカウント レベルでバックアップを検索する](backup-search.md#search-backups-at-netapp-account-level)」を参照してください。


2. バックアップ リストから、復元するバックアップを選択します。 バックアップの右側にある 3 つのドット (`…` ) をクリックし、[アクション] メニューの **[新しいボリュームに復元]** をクリックします。   

    ![バックアップを新しいボリュームに復元するオプションを示すスクリーンショット。](../media/azure-netapp-files/backup-restore-new-volume.png)

3. 表示される [ボリュームの作成] ページで、ページ内のフィールドの情報を適宜指定し、 **[確認と作成]** をクリックして、新しいボリュームへのバックアップの復元を開始します。   

    * **[プロトコル]** フィールドは、元のボリュームに基づいて事前に入力されており、変更できません。    
        ただし、NetApp アカウント レベルでバックアップ リストからボリュームを復元する場合は、[プロトコル] フィールドを指定する必要があります。 [プロトコル] フィールドは、元のボリュームのプロトコルと一致させる必要があります。 そうしないと、復元操作は次のエラーにより失敗します。  
        `Protocol Type value mismatch between input and source volume of backupId <backup-id of the selected backup>. Supported protocol type : <Protocol Type of the source volume>`

    * **クォータ** 値は、復元のトリガーとなるバックアップ サイズ以上にする必要があります (最小 100 GiB)。

    * バックアップの復元先となる **容量プール** には、新しい復元ボリュームをホストするのに十分な未使用容量が必要です。 そうしないと、復元操作は失敗します。   

    ![[ボリュームの作成] ページを示すスナップショット。](../media/azure-netapp-files/backup-restore-create-volume.png)

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files バックアップについて](backup-introduction.md)
* [Azure NetApp Files バックアップの要件と考慮事項](backup-requirements-considerations.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [ポリシー ベースのバックアップの構成](backup-configure-policy-based.md)
* [手動バックアップの構成](backup-configure-manual.md)
* [バックアップ ポリシーを管理する](backup-manage-policies.md)
* [バックアップを検索する](backup-search.md)
* [ボリュームのバックアップ機能を無効にする](backup-disable.md)
* [ボリュームのバックアップを削除する](backup-delete.md)
* [ボリュームのバックアップ メトリック](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
