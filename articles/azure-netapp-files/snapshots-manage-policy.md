---
title: Azure NetApp Files のスナップショット ポリシーを管理する | Microsoft Docs
description: Azure NetApp Files を使用して、スナップショット ポリシーを作成、管理、変更、削除する方法について説明します。
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
ms.openlocfilehash: e4ccbd68ead83f682f39359c053e5a608d34903c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699259"
---
# <a name="manage-snapshot-policies-in-azure-netapp-files"></a>Azure NetApp Files でスナップショット ポリシーを管理する

[スナップショット](snapshots-introduction.md)を使用すると、ボリュームの特定の時点への復旧が可能になります。 スナップショット ポリシーを使用して、[ボリュームのスナップショット](snapshots-introduction.md)が自動的に取得されるようにスケジュールすることができます。 必要に応じてスナップショット ポリシーを変更したり、不要になったスナップショット ポリシーを削除したりすることもできます。  

## <a name="create-a-snapshot-policy"></a>スナップショット ポリシーを作成する 

スナップショット ポリシーを使用すると、スナップショットの作成頻度 (時間単位、日単位、週単位、または月単位) を指定できます。 また、ボリュームに対して保持するスナップショットの最大数も指定する必要があります。  

1.  NetApp アカウント ビューで、 **[スナップショット ポリシー]** をクリックします。

    ![スナップショット ポリシーに移動する方法を示すスクリーンショット。](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  [スナップショット ポリシー] ウィンドウで、[ポリシーの状態] を **[有効]** に設定します。 

3.  **[時間単位]** 、 **[日単位]** 、 **[週単位]** 、または **[月単位]** タブをクリックして、時間単位、日単位、週単位、または月単位のスナップショット ポリシーを作成します。 **[保持するスナップショット数]** を指定します。  

    1 つのボリュームで許可されるスナップショットの最大数については、「[Azure NetApp Files のリソースの制限](azure-netapp-files-resource-limits.md)」を参照してください。 

    次の例は、時間単位のスナップショット ポリシーの構成を示しています。 

    ![毎時のスナップショット ポリシーを示すスクリーンショット。](../media/azure-netapp-files/snapshot-policy-hourly.png)

    次の例は、日単位のスナップショット ポリシーの構成を示しています。

    ![毎日のスナップショット ポリシーを示すスクリーンショット。](../media/azure-netapp-files/snapshot-policy-daily.png)

    次の例は、週単位のスナップショット ポリシーの構成を示しています。

    ![毎週のスナップショット ポリシーを示すスクリーンショット。](../media/azure-netapp-files/snapshot-policy-weekly.png)

    次の例は、月単位のスナップショット ポリシーの構成を示しています。

    ![毎月のスナップショット ポリシーを示すスクリーンショット。](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  **[保存]** をクリックします。  

追加のスナップショット ポリシーを作成する必要がある場合は、手順 3 を繰り返します。
作成したポリシーは、[スナップショット ポリシー] ページに表示されます。

ボリュームでスナップショット ポリシーを使用する場合は、[ボリュームにポリシーを適用する](snapshots-manage-policy.md#apply-a-snapshot-policy-to-a-volume)必要があります。 

## <a name="apply-a-snapshot-policy-to-a-volume"></a>ボリュームにスナップショット ポリシーを適用する

作成したスナップショット ポリシーをボリュームで使用する場合は、ボリュームにポリシーを適用する必要があります。 

リージョン間レプリケーションでは、スナップショット ポリシーをターゲット ボリュームに適用することはできません。  

1.  **[ボリューム]** ページに移動し、スナップショット ポリシーを適用するボリュームを右クリックし、 **[編集]** を選択します。

    ![[ボリューム] 右クリック メニュー オプションを示すスクリーンショット。](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  [編集] ウィンドウの **[スナップショット ポリシー]** で、ボリュームに使用するポリシーを選択します。  **[OK]** をクリックしてポリシーを適用します。  

    ![[スナップショット ポリシー] メニューを示すスクリーンショット。](../media/azure-netapp-files/snapshot-policy-edit.png) 

## <a name="modify-a-snapshot-policy"></a>スナップショット ポリシーを変更する 

既存のスナップショット ポリシーを変更して、ポリシーの状態、スナップショットの頻度 (時間単位、日単位、週単位、または月単位)、または保持するスナップショットの数を変更できます。  
 
1.  NetApp アカウント ビューで、 **[スナップショット ポリシー]** をクリックします。

2.  変更するスナップショット ポリシーを右クリックし、 **[編集]** を選択します。

    ![[スナップショット ポリシー] 右クリック メニューを示すスクリーンショット。](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  表示された [スナップショット ポリシー] ウィンドウで変更を行い、 **[保存]** をクリックします。 

## <a name="delete-a-snapshot-policy"></a>スナップショット ポリシーを削除する 

保持する必要がなくなったスナップショット ポリシーを削除できます。   

1.  NetApp アカウント ビューで、 **[スナップショット ポリシー]** をクリックします。

2.  変更するスナップショット ポリシーを右クリックし、 **[削除]** を選択します。

    ![[削除] メニュー項目を示すスクリーンショット。](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  **[はい]** をクリックして、スナップショット ポリシーの削除を確認します。   

    ![スナップショット ポリシーの削除の確認を示すスクリーンショット。](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="next-steps"></a>次のステップ

* [スナップショット ポリシーのトラブルシューティング](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [スナップショットの詳細を表示](snapshots-introduction.md)