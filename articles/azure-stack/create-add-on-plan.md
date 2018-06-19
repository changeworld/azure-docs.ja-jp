---
title: この記事では、Azure Stack のオファーとプランを更新する方法について説明します | Microsoft Docs
description: この記事では、既存の Azure Stack のオファーとプランを表示および変更する方法について説明します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a84148a3ac31d51ff30cebffab00e5fec8fdaa87
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2018
ms.locfileid: "35237005"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack のアドオン プラン
Azure Stack オペレーターとして、ユーザーがサブスクライブする、必要なサービスと適用可能なクォータが含まれているプランを作成します。 これらの "[*基本プラン*](azure-stack-create-plan.md)" には、ユーザーに提供される主要なサービスが含まれています。オファーごとに 1 つの基本プランのみ作成できます。 オファーを変更する必要がある場合は、"*アドオン プラン*" を使用して、基本プランで最初に提供されたコンピューター、ストレージ、またはネットワーク クォータを拡張するようにプランを変更できます。 

1 つのプランにすべてをまとめることが最適な場合もありますが、基本プランを用意し、追加のサービスはアドオン プランを使用して提供する方がよい場合もあります。 たとえば、IaaS サービスを基本プランの一部として提供し、すべての PaaS サービスをアドオン プランとして扱うことができます。 また、プランは Azure Stack 環境内のリソースの使用量を制御するために使用することもできます。 たとえば、ユーザーにリソースの使用量を意識してもらいたい場合は、比較的小さな基本プランを作成します (必要なサービスに応じて)。ユーザーが上限に達すると、指定されたプランに基づいて割り当てられたリソースを既に使用したという警告が表示されます。 その後、ユーザーは追加のリソースのために利用可能なアドオン プランを選択することができます。 

> [!NOTE]
> ユーザーが既存のオファーのサブスクリプションにアドオン プランを追加すると、追加のリソースが表示されるまでに最大で 1 時間かかることがあります。 

## <a name="create-an-add-on-plan"></a>アドオン プランを作成する
アドオン プランは、既存のオファーを変更して作成します。

1. Azure Stack 管理者ポータルにクラウド管理者としてサインインします。
2. [新しい基本プランを作成](azure-stack-create-plan.md)したときと同じ手順で、これまでは提供されていなかったサービスを提供する新しいプランを作成します。 この例では、Key Vault (Microsoft.KeyVault) サービスが新しいプランに含められます。
3. 管理者ポータルで **[オファー]** をクリックし、アドオン プランで更新するオファーを選択します。

   ![](media/create-add-on-plan/1.PNG)

4.  オファーのプロパティの一番下までスクロールし、**[アドオン プラン]** を選択します。 **[追加]** をクリックします。
   
    ![](media/create-add-on-plan/2.PNG)

5. 追加するプランを選択します。 この例では、プランの名前は **[Key vault plan]** です。その後、**[選択]** をクリックして、オファーにプランを追加します。 プランがオファーに正常に追加されたという通知が届きます。
   
    ![](media/create-add-on-plan/3.PNG)

6. オファーに含まれているアドオン プランの一覧を見て、新しいアドオン プランが一覧に表示されていることを確認します。
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>次の手順
[オファーの作成](azure-stack-create-offer.md)