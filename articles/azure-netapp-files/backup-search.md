---
title: Azure NetApp Files ボリュームのバックアップを検索する | Microsoft Docs
description: Azure NetApp Files ボリュームのバックアップを、ボリューム レベルと NetApp アカウント レベルで表示して検索する方法について説明します。
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
ms.openlocfilehash: 0ed10fbe492a49f61a989e9159bed1cf4b60e513
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256307"
---
# <a name="search-backups-of-azure-netapp-files-volumes"></a>Azure NetApp Files ボリュームのバックアップを検索する

特定のバックアップをボリューム レベルまたは NetApp アカウント レベルで表示し、検索することができます。

スナップショットに使用される名前は、スナップショットがバックアップされるときに保存されます。 スナップショット名には、プレフィックス `daily`、`weekly`、`monthly` のいずれかが含まれます。 また、スナップショットが作成されたときの timestamp も含まれます。 バックアップ機能が有効になっているときの最初のスナップショットは、"*ベースライン スナップショット*" と呼ばれ、その名前にはプレフィックス `snapmirror` が含まれています。

ボリュームが削除されても、そのバックアップは引き続き保持されます。 バックアップは、関連付けられている NetApp アカウントの [バックアップ] セクションに一覧表示されます。 このリストには、そのリージョンにおけるサブスクリプション内のバックアップが、(NetApp アカウントの枠を越えて) すべて含まれています。 これを使用することで、同じサブスクリプションに属する別の NetApp アカウント内のボリュームに、バックアップを復元することができます。

## <a name="search-backups-at-volume-level"></a>ボリューム レベルでバックアップを検索する    

ボリューム レベルでバックアップを表示して検索できます。

1. **[ボリューム]** を選択します。

2. **[バックアップ]** に移動して、ボリュームのバックアップを表示します。   
    **[種類 ]** 列には、バックアップが、"*スケジュール*" (ポリシーベース) に基づいて生成されたか、"*手動*" で生成されたかが示されます。

3. **[バックアップの検索]** ボックスに、検索するバックアップの名前を入力します。  

    部分検索がサポートされています。必ずしもバックアップ名全体を指定する必要はありません。 検索を実行すると、検索文字列に基づいてバックアップがフィルター処理されます。

    ![ボリュームのバックアップ リストを示すスクリーンショット。](../media/azure-netapp-files/backup-search-volume-level.png)

## <a name="search-backups-at-netapp-account-level"></a>NetApp アカウント レベルでバックアップを検索する 

サブスクリプションのリージョン内ですべての NetApp アカウントを対象に、サブスクリプションに関連付けられているバックアップを表示し、検索することができます。   

1. **[NetApp アカウント]** を選択します。

2. **[バックアップ]** に移動します。
    **[種類 ]** 列には、バックアップが、"*スケジュール*" (ポリシーベース) に基づいて生成されたか、"*手動*" で生成されたかが示されます。 

3. **[バックアップの検索]** ボックスに、検索するバックアップの名前を入力します。

    * バックアップ リストには、サブスクリプションのすべての NetApp アカウントに属する、すべてのボリュームのすべてのバックアップが (アクティブか削除済みかを問わず) 含まれています。

    * バックアップ リストは、バックアップを新しいボリュームに復元するだけでなく、バックアップを削除するときにも使用できます。 バックアップを復元するためのワークフローは、[ボリュームレベルのバックアップ復元](backup-restore-new-volume.md)と同じです。 バックアップを削除するためのワークフローは、[ボリュームレベルのバックアップ削除](backup-delete.md)と同じです。

    * [手動バックアップ](backup-configure-manual.md)の名前付け規則に関するベスト プラクティスの提案は、バックアップがどのボリュームに属しているかを特定するうえで役立ちます。 この方法は、(バックアップを保持したまま) 削除されたボリュームにバックアップが属している場合に特に便利です。

    ![NetApp アカウントのバックアップ リストを示すスクリーンショット。](../media/azure-netapp-files/backup-search-account-level.png)

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files バックアップについて](backup-introduction.md)
* [Azure NetApp Files バックアップの要件と考慮事項](backup-requirements-considerations.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [ポリシー ベースのバックアップの構成](backup-configure-policy-based.md)
* [手動バックアップの構成](backup-configure-manual.md)
* [バックアップ ポリシーを管理する](backup-manage-policies.md)
* [バックアップを新しいボリュームに復元する](backup-restore-new-volume.md)
* [ボリュームのバックアップ機能を無効にする](backup-disable.md)
* [ボリュームのバックアップを削除する](backup-delete.md)
* [ボリュームのバックアップ メトリック](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
