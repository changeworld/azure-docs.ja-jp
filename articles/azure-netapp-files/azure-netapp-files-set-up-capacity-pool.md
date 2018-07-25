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
ms.openlocfilehash: 0e9203f5b4e2a9043e242b804c82017cf6fc3ee1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010805"
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

1. [Azure NetApp Files のボリュームを作成する](azure-netapp-files-create-volumes.md)
2. [ボリュームのエクスポート ポリシーを構成する (省略可能)](azure-netapp-files-configure-export-policy.md)

