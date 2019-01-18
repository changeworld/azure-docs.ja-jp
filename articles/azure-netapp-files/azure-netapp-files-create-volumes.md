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
ms.date: 12/17/2018
ms.author: b-juche
ms.openlocfilehash: 937ff9cf4c099f229df28070be07ba76339704e0
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584005"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Azure NetApp Files のボリュームを作成する

ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。  1 つの容量プールに複数のボリュームを作成できますが、それらのボリュームの総容量消費がプールのサイズを超えないようにする必要があります。 

## <a name="before-you-begin"></a>開始する前に 
あらかじめ容量プールを設定しておく必要があります。   
[容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)   
サブネットが Azure NetApp Files に委任されている必要があります。  
[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)


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
     
        ファイル パス名には、文字、数字、ハイフン ("-") のみを含めることができます。 長さは 16 文字から 40 文字でなければなりません。  

    * **[クォータ]**  
        ボリュームに割り当てられる論理ストレージの量を指定します。  

        **[使用可能なクォータ]** フィールドには、選択した容量プール内の未使用の領域のうち、新しいボリュームの作成に使用できる領域の量が示されます。 新しいボリュームのサイズが、使用可能なクォータを超えてはいけません。  

    * **Virtual Network**  
        ボリュームへのアクセス元となる Azure Virtual Network (Vnet) を指定します。  

        指定する Vnet には、Azure NetApp Files に委任されているサブネットがある必要があります。 Azure NetApp Files サービスにアクセスできるのは、同じ Vnet から、またはボリュームと同じリージョンにある Vnet から Vnet ピアリングを経由する場合のみです。 ボリュームには、オンプレミス ネットワークから Express Route 経由でアクセスすることもできます。   

    * **サブネット**  
        ボリュームで使用するサブネットを指定します。  
        指定するサブネットは Azure NetApp Files に委任されている必要があります。 
        
        サブネットを委任していない場合は、[Create a Volume]\(ボリュームの作成) ページで **[新規作成]** をクリックできます。 次に、[サブネットの作成] ページでサブネットの情報を指定し、**[Microsoft.NetApp/volumes]** を選択してサブネットを Azure NetApp Files に委任します。 各 VNet で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
 
        ![新しいボリューム](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![サブネットの作成](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)


4.  Click **OK**. 
 
ボリュームは、その容量プールから、サブスクリプション、リソース グループ、場所の各属性を継承します。 ボリュームのデプロイ状態を監視するには、[通知] タブを使用してください。

## <a name="next-steps"></a>次の手順  
* [ボリュームのエクスポート ポリシーを構成する (省略可能)](azure-netapp-files-configure-export-policy.md)
* [Azure サービスの仮想ネットワーク統合について理解する](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

