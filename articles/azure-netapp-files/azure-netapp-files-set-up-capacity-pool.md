---
title: Azure NetApp Files の容量プールを設定する | Microsoft Docs
description: ボリュームの作成先となる容量プールを設定する方法について説明します。
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
ms.openlocfilehash: 55a1d16ce1617ecf7bc28c7c62de8557ceeea311
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412913"
---
# <a name="set-up-a-capacity-pool"></a>容量プールを設定する
容量プールを設定すると、そこにボリュームを作成できるようになります。  

## <a name="before-you-begin"></a>開始する前に 
あらかじめ NetApp アカウントを作成しておく必要があります。   

[NetApp アカウントを作成する](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>手順 

1. NetApp アカウントの管理ブレードに移動し、ナビゲーション ウィンドウから **[容量プール]** を選択します。

2. **[+ プールの追加]** をクリックして新しい容量プールを作成します。   
    [New Capacity Pool]\(新しい容量プール\) ウィンドウが表示されます。

3. 新しい容量プールに関して、次の情報を入力します。  
  * **名前**  
    容量プールの名前を指定します。  
    容量プールの名前は、NetApp アカウントごとに一意であることが必要です。

  * **サービス レベル**   
    このフィールドには、容量プールのターゲット パフォーマンスが表示されます。  
    現在利用できるのは Premium サービス レベルのみです。 

  *  **サイズ**     
      購入する容量プールのサイズを指定します。        
      容量プールの最小サイズは 4 TiB です。 プールは、4 TiB の倍数のサイズで作成することができます。   
      
      ![新しい容量プール](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Click **OK**.

## <a name="next-steps"></a>次の手順 

[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)


