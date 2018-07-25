---
title: Azure NetApp Files のボリュームを作成する | Microsoft Docs
description: Azure NetApp Files のボリュームを作成する方法について説明します。
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 42e475f4da2102bb8b010daec6e6451ba7f13848
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011094"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Azure NetApp Files のボリュームを作成する

ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。  1 つの容量プールに複数のボリュームを作成できますが、それらのボリュームの総容量消費がプールのサイズを超えないようにする必要があります。 

## <a name="before-you-begin"></a>開始する前に 
あらかじめ容量プールを設定しておく必要があります。  

[容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)

## <a name="steps"></a>手順 
1.  [Manage Capacity Pools]\(容量プールの管理\) ブレードから **[ボリューム]** ブレードをクリックします。 
2.  **[+ ボリュームの追加]** をクリックして、ボリュームを作成します。  
    [新しいボリューム] ウィンドウが表示されます。

3.  [新しいボリューム] ウィンドウの **[作成]** をクリックして、次のフィールドの情報を指定します。   
    * **[名前]**      
        作成するボリュームの名前を指定します。   
        名前はリソース グループ内で一意である必要があります。 3 文字以上になるようにしてください。  任意の英数字を使用できます。

    * **[ファイル パス]**  
        新しいボリュームのエクスポート パスを作成する際に使用するファイル パスを指定します。 ボリュームのマウントとアクセスには、このエクスポート パスが使用されます。   
        マウント ターゲットは、NFS サービスの IP アドレスのエンドポイントです。 自動的に生成されます。    
        ファイル パス名には、文字、数字、ハイフン ("-") のみを含めることができます。 長さは 16 文字から 40 文字でなければなりません。  

    * **[クォータ]**  
        ボリュームに割り当てられる論理ストレージの量を指定します。  
        **[使用可能なクォータ]** フィールドは、選択した容量プールの領域のうち、新しいボリュームの作成に使用できる未使用領域を示します。 新しいボリュームのサイズが、使用可能なクォータを超えてはいけません。  

    * **Virtual Network**  
        ボリュームへのアクセス元となる Azure Virtual Network (Vnet) を指定します。 指定する Vnet には、Azure NetApp Files が構成されている必要があります。 Azure NetApp Files サービスには、ボリュームと同じ場所にある Vnet からのみアクセスできます。   

    ![新しいボリューム](../media/azure-netapp-files/azure-netapp-files-new-volume.png)

4.  Click **OK**. 
 
ボリュームは、その容量プールから、サブスクリプション、リソース グループ、場所の各属性を継承します。 ボリュームのデプロイ状態を監視するには、[通知] タブを使用してください。

## <a name="next-steps"></a>次の手順  
[ボリュームのエクスポート ポリシーを構成する (省略可能)](azure-netapp-files-configure-export-policy.md)

