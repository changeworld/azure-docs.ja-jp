---
title: このチュートリアルでは、Azure Stack のオファーとプランを更新する方法について説明します | Microsoft Docs
description: この記事では、既存の Azure Stack のオファーとプランを表示および変更する方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3b5d2ab5e924f578f5838d11b0d2058aacf67dec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-update-offers-and-plans"></a>チュートリアル: オファーとプランを更新する
Azure Stack オペレーターとして、ユーザーがサブスクライブする、必要なサービスと適用可能なクォータが含まれているプランを作成します。 これらの "*基本プラン*" には、ユーザーに提供される主要なサービスが含まれています。オファーごとに 1 つの基本プランしか作成できません。 オファーを変更する必要がある場合は、"*アドオン プラン*" を使用して、基本プランで最初に提供されたコンピューター、ストレージ、またはネットワーク クォータを拡張するようにプランを変更できます。 

1 つのプランにすべてをまとめることが最適な場合もありますが、基本プランを用意し、追加のサービスはアドオン プランを使用して提供する方がよい場合もあります。 たとえば、IaaS サービスを基本プランの一部として提供し、すべての PaaS サービスをアドオン プランとして扱うことができます。 また、プランは ASDK 環境で限られたリソースの使用量を制御するために使用することもできます。 たとえば、ユーザーにリソースの使用量を意識してもらいたい場合は、比較的小さな基本プランを作成します (必要なサービスに応じて)。ユーザーが上限に達すると、指定されたプランに基づいて割り当てられたリソースを既に使用したという警告が表示されます。 その後、ユーザーは追加のリソースのために利用可能なアドオン プランを選択することができます。 

> [!NOTE]
> ユーザーが既存のオファーのサブスクリプションにアドオン プランを追加すると、追加のリソースが表示されるまでに最大で 1 時間かかることがあります。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * アドオン プランを作成する 
> * アドオン プランにサブスクライブする

## <a name="create-an-add-on-plan"></a>アドオン プランを作成する
**アドオン プラン**は、既存のオファーを変更して作成します。

1. [Azure Stack ポータル](https://adminportal.local.azurestack.external)にクラウド管理者としてサインインします。
2. 前に[基本プランを作成](asdk-offer-services.md)したときと同じ手順で、これまでは提供されていなかったサービスを提供する新しいプランを作成します。 この例では、Key Vault (Microsoft.KeyVault) サービスがプランに含められます。
3. 管理者ポータルで **[オファー]** をクリックし、アドオン プランで更新するオファーを選択します。

   ![](media/asdk-update-offers/1.PNG)

4.  オファーのプロパティの一番下までスクロールし、**[アドオン プラン]** を選択します。 **[追加]**をクリックします。
   
    ![](media/asdk-update-offers/2.PNG)

5. 追加するプランを選択します。 この例では、プランの名前は **[Key vault plan]** です。その後、**[選択]** をクリックして、オファーにプランを追加します。 プランがオファーに正常に追加されたという通知が届きます。
   
    ![](media/asdk-update-offers/3.PNG)

6. オファーに含まれているアドオン プランの一覧を見て、新しいアドオン プランが一覧に表示されていることを確認します。
   
    ![](media/asdk-update-offers/4.PNG)

## <a name="subscribe-to-the-add-on-plan"></a>アドオン プランにサブスクライブする
既存の Azure Stack サブスクリプションをアドオン プランで拡張するには、Azure Stack ユーザー ポータルにログインする必要があります。

次の手順を使用して、Azure Stack オペレーターによって提供されている追加リソースを見つけ、以前にサブスクライブしたオファーにアドオン プランを追加します。

1. [ユーザー ポータル](https://portal.local.azurestack.external)にログインします。
2. アドオン プランで拡張するサブスクリプションを検索するには、**[その他のサービス]** をクリックし、**[サブスクリプション]** をクリックして、サブスクリプションを選択します。
   
    ![](media/asdk-update-offers/5.PNG)

3.  サブスクリプションの概要で、**[プランの追加]** をクリックします。
   
    ![](media/asdk-update-offers/6.PNG)

4. [プランの追加] ブレードで、サブスクリプションに追加するアドオン プランを選択します。 この例では、**[Key vault plan]** が選択されています。 その後、アドオン プランが正常に取得されたことと、更新されたサブスクリプションにアクセスするにはポータルを更新する必要があることを示す通知を受け取ります。
   
    ![](media/asdk-update-offers/7.PNG)

5. 最後に、サブスクリプションに関連付けられているアドオン プランを調べて、アドオン プランが正常に追加されたことを確認します。
   
    ![](media/asdk-update-offers/8.PNG)


## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * アドオン プランを作成する 
> * アドオン プランにサブスクライブする

> [!div class="nextstepaction"]
> [テンプレートから仮想マシンを作成する](asdk-create-vm-template.md)

