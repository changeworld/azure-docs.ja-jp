---
title: Azure NetApp Files にアクセスするための NetApp アカウントを作成する | Microsoft Docs
description: 容量プールを設定してボリュームを作成できるよう Azure NetApp Files にアクセスして NetApp アカウントを作成する方法について説明します。
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
ms.openlocfilehash: ad8cc550ce69e4dc4c19a569718fa873a65b3620
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010346"
---
# <a name="create-a-netapp-account"></a>NetApp アカウントを作成する
NetApp アカウントを作成することによって、容量プールを設定し、その後、ボリュームを作成することができます。 新しい NetApp アカウントの作成は、[Azure NetApp Files] ブレードを使用して行います。

## <a name="before-you-begin"></a>開始する前に
Microsoft.NetApp Azure リソース プロバイダーにアクセスするためのホワイトリストに登録されていること、また、Azure NetApp Files サービスを使用するための構成が済んでいることが必要です。  

[Azure NetApp Files パブリック プレビュー サインアップ ページ](https://aka.ms/nfspublicpreview) 

## <a name="steps"></a>手順 

1. プレビューの招待状からその Azure portal URL を特定し、ポータルにログインします。 
2.  次のいずれかの方法で [Azure NetApp Files] ブレードにアクセスします。  
  * Azure portal の検索ボックスで「**Azure NetApp Files**」を検索します。  
  * ナビゲーションの **[すべてのサービス]** をクリックし、フィルターで Azure NetApp Files を特定します。  

  [Azure NetApp Files] ブレードは、その横に表示される星のアイコンをクリックすることで、"お気に入り" に登録することができます。 

3. **[+ 追加]** をクリックして新しい NetApp アカウントを作成します。  
  [New NetApp account]\(新しい NetApp アカウント\) ウィンドウが表示されます。  

4. NetApp アカウントについて次の情報を入力します。 
  * **アカウント名**  
    サブスクリプションの一意の名前を指定します。
  *  **サブスクリプション**  
    既存のサブスクリプションからいずれかのサブスクリプションを選択します。
  * **リソース グループ**   
    既存のリソース グループを使用するか、新しいリソース グループを作成します。
  * **場所**  
    アカウントとその子リソースを置くリージョンを選択します。  
    現在、Azure NetApp Files サービスは、米国東部リージョンでのみサポートされます。  

    ![新しい NetApp アカウント](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. **Create** をクリックしてください。     
  作成した NetApp アカウントが [Azure NetApp Files] ブレードに表示されます。 

## <a name="next-steps"></a>次の手順  

1. [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
2. [Azure NetApp Files のボリュームを作成する](azure-netapp-files-create-volumes.md)
3. [ボリュームのエクスポート ポリシーを構成する (省略可能)](azure-netapp-files-configure-export-policy.md)
