---
title: "オファーをサブスクラブする | Microsoft Docs"
description: "テナントとしてオファーをサブスクライブする方法について説明します。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/03/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3cd87ebe9827249d32f15b5de0ad8521d0282c47
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---
# <a name="subscribe-to-an-offer"></a>オファーをサブスクライブする
[オファーの作成](azure-stack-create-offer.md)が終わったら、テナントがサブスクリプションを作成できるかどうかをテストします。

1. テナントとして Azure Stack のテナント ポータル (https://portal.local.azurestack.external) に[サインイン](azure-stack-connect-azure-stack.md)し、**[サブスクリプションの取得]** をクリックします。

   ![](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. **[表示名]** フィールドにサブスクリプションの名前を入力し、**[オファー]** をクリックします。**[Choose an offer]\(オファーの選択\)** ブレードでいずれかのオファーをクリックし、**[作成]** をクリックします。

   ![](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. 作成したサブスクリプションを表示するには、**[その他のサービス]**、**[サブスクリプション]** の順にクリックし、作成した新しいサブスクリプションをクリックします。  

オファーをサブスクライブしたら、ポータルを更新して、どのサービスが新しいサブスクリプションの一部であるかを確認します。

## <a name="subscribe-to-an-add-on-plan"></a>アドオン プランをサブスクライブする
オファーにアドオン プランがある場合、テナントは、それらをいつでも自分のサブスクリプションに追加できます。  

1. テナントポータルで、**[その他のサービス]** > **[サブスクリプション]** を選択します。

2. サブスクリプションをクリックし、**[プランの追加]** ボタンをクリックし、アドオン プランを選択します。



## <a name="next-steps"></a>次のステップ
[仮想マシンのプロビジョニング](azure-stack-provision-vm.md)

