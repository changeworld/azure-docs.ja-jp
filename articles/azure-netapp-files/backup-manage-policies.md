---
title: Azure NetApp Files のバックアップ ポリシーを管理する | Microsoft Docs
description: Azure NetApp Files ボリュームのバックアップ ポリシーを変更または一時停止する方法について説明します。
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
ms.openlocfilehash: 71f5c09fed1a733b4e04664aade5a69c3a99aae0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229977"
---
# <a name="manage-backup-policies-for-azure-netapp-files"></a>Azure NetApp Files のバックアップ ポリシーを管理する 

Azure NetApp Files の[ポリシーベースのバックアップ](backup-configure-policy-based.md)でボリュームを自動的にバックアップするには、スナップショット ポリシーの構成が必要です。 バックアップ ポリシーを使用して Azure NetApp Files のバックアップを設定したら、必要に応じてバックアップ ポリシーを変更または一時停止できます。  

手動バックアップは、バックアップ ポリシーの変更に影響されません。

## <a name="modify-a-backup-policy"></a>バックアップ ポリシーを変更する   

Azure NetApp Files ボリュームに対して適切なバックアップ適用範囲を確保するために、既存の Azure NetApp Files バックアップ ポリシーを必要に応じて変更することができます。  たとえば、サービスによって保護されているバックアップの保持数を変更する必要がある場合は、ボリュームの Azure NetApp Files バックアップ ポリシーを変更することで、保持する復元数を変更できます。 

バックアップ ポリシー設定を変更するには:   

1. **[バックアップ]** に移動します。  

2. **[バックアップ ポリシー]** を選択し、バックアップ ポリシーの右側にある 3 つのドット (`…`) をクリックして、 **[編集]** を クリックします。

    ![バックアップ ポリシーの状況依存のメニューを示すスクリーンショット。](../media/azure-netapp-files/backup-policies-edit.png)

3. [バックアップ ポリシーの変更] ウィンドウで、日単位、週単位、月単位のバックアップに対して保持する復元の数を更新します。 バックアップ ポリシー名を入力して、操作の確認を行います。 **[保存]** をクリックします。  

    ![[バックアップ ポリシーの変更] ウィンドウを示すスクリーンショット。](../media/azure-netapp-files/backup-modify-policy.png)

    > [!NOTE] 
    > バックアップを有効にして、スケジュールされた頻度に対して適用したら、バックアップ保持数を `0` に変更することはできません。 バックアップ ポリシーには、`1` 以上の保持数が必要です。 詳細については、「[Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)」を参照してください。  

## <a name="suspend-a-backup-policy"></a>バックアップ ポリシーを一時停止する  

関連付けられているボリュームに対して新しいバックアップ操作が実行されないように、バックアップ ポリシーを一時停止することができます。 既存のバックアップを維持する必要があるが、バージョン管理のために削除したくない場合に、この操作によってバックアップを一時的に停止できます。   

### <a name="suspend-a-backup-policy-for-all-volumes-associated-with-the-policy"></a>ポリシーに関連付けられているすべてのボリュームのバックアップ ポリシーを一時停止する

1. **[バックアップ]** に移動します。

2. **[バックアップ ポリシー]** を選択し、バックアップ ポリシーの右側にある 3 つのドット (`…`) をクリックして、 **[編集]** を クリックします。 

3. **[ポリシーの状態]** を **[無効]** に切り替えて、確認のためにポリシー名を入力し、 **[保存]** をクリックします。 

    ![[ポリシーの状態] が無効になっている [バックアップ ポリシーの変更] ウィンドウを示すスクリーンショット。](../media/azure-netapp-files/backup-modify-policy-disabled.png)

### <a name="suspend-a-backup-policy-for-a-specific-volume"></a>特定のボリュームのバックアップ ポリシーを一時停止する 

1. **[ボリューム]** ブレードに移動します。 
2. 一時停止するバックアップの特定のボリュームを選択します。
3. **[構成]** をクリックします。
4. [バックアップの構成] ページで、 **[ポリシーの状態]** を **[一時停止]** に切り替えて、確認のためにボリューム名を入力し、 **[OK]** をクリックします。   

    ![ポリシーの状態が一時停止になっている [バックアップの構成] ウィンドウを示すスクリーンショット。](../media/azure-netapp-files/backup-modify-policy-suspend.png)

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files バックアップについて](backup-introduction.md)
* [Azure NetApp Files バックアップの要件と考慮事項](backup-requirements-considerations.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [ポリシー ベースのバックアップの構成](backup-configure-policy-based.md)
* [手動バックアップの構成](backup-configure-manual.md)
* [バックアップを検索する](backup-search.md)
* [バックアップを新しいボリュームに復元する](backup-restore-new-volume.md)
* [ボリュームのバックアップ機能を無効にする](backup-disable.md)
* [ボリュームのバックアップを削除する](backup-delete.md)
* [ボリュームのバックアップ メトリック](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)



