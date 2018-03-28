---
title: このチュートリアルでは、Azure Stack オファーにサブスクライブする方法について説明します | Microsoft Docs
description: このチュートリアルでは、Azure Stack サービスの新しいサブスクリプションを作成し、テスト仮想マシンを作成してオファーをテストする方法について説明します。
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
ms.openlocfilehash: d2adda5613b9a10bc3314045b9d68b0f3196f19a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-and-test-a-subscription"></a>チュートリアル: サブスクリプションの作成とテスト
このチュートリアルでは、オファーが含まれているサブスクリプションを作成してテストする方法を示します。 テストの場合は、クラウド管理者として[ユーザー ポータル](https://portal.local.azurestack.external)にログインし、オファーにサブスクライブしてから、仮想マシンを作成します。

> [!TIP]
> より高度な評価エクスペリエンスを使用するには、[特定のユーザーのサブスクリプションを作成](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator)し、ユーザー ポータルでそのユーザーとしてログインします。 

このチュートリアルでは、[前のチュートリアル](asdk-offer-services.md)で作成したオファーにサブスクライブする方法について説明します。

学習内容

> [!div class="checklist"]
> * プランへのサブスクライブ 
> * オファーのテスト

## <a name="subscribe-to-an-offer"></a>プランへのサブスクライブ
ユーザーとしてオファーにサブスクライブするには、Azure Stack ユーザー ポータルにログインして、Azure Stack オペレーターによって提供されているサービスを見つける必要があります。

1. [ユーザー ポータル](https://portal.local.azurestack.external)にログインし、**[Get a Subscription]\(サブスクリプションの取得\)** をクリックします。

   ![サブスクリプションの取得](media/asdk-subscribe-services/get-subscription.png)

2. **[表示名]** フィールドに、お客様のサブスクリプションの名前を入力します。 次に、**[Offer]\(オファー\)** をクリックし、**[Choose an offer]\(オファーの選択\)** セクションで利用可能なオファーの 1 つを選択し、**[作成]** をクリックします。

   ![オファーの作成](media/asdk-subscribe-services/create-subscription.png)

   > [!TIP]
   > サブスクリプションの使用を開始するには、ここでユーザー ポータルを更新する必要があります。

3. 作成したサブスクリプションを表示するには、**[その他のサービス]**、**[サブスクリプション]** の順にクリックし、作成した新しいサブスクリプションをクリックします。 オファーにサブスクライブしたら、ポータルを更新して、サービスが新しいサブスクリプションの一部として含まれているかどうかを確認します。 この例では、**仮想マシン**が追加されています。

   ![サブスクリプションの表示](media/asdk-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>オファーのテスト
[ユーザー ポータル](https://portal.local.azurestack.external)にログインしている間に、新しいサブスクリプション機能を使用して仮想マシンをプロビジョニングすることで、オファーをテストすることができます。 

> [!NOTE]
> このテストでは、[前のチュートリアル](asdk-marketplace-item.md)で Azure Stack マーケットプレースに追加された Windows Server 2016 Datacenter VM イメージが必要です。 

1. [ユーザー ポータル](https://portal.local.azurestack.external)にログインします。

2. ユーザー ポータルで **[仮想マシン]** > **[追加]** > **[Windows Server 2016 Datacenter]** の順にクリックし、**[作成]** をクリックします。

3. **[基本]** セクションで、**[名前]**、**[ユーザー名]**、**[パスワード]** を入力し、**[サブスクリプション]** を選択し、**[リソース グループ]** を作成し (または既存のものを選択し)、**[OK]** をクリックします。

4. **[サイズの選択]** セクションで、**[A1 標準]** をクリックしてから **[選択]** をクリックします。  

5. [設定] ブレードで、既定値のまま **[OK]** をクリックします。

6. **[概要]** セクションで、**[OK]** をクリックして仮想マシンを作成します。  

7. 新しい仮想マシンを表示するには、**[仮想マシン]** をクリックし、新しい仮想マシンを検索して、その名前をクリックします。

    ![すべてのリソース](media/asdk-subscribe-services/view-vm.png)

> [!NOTE]
> 仮想マシンのデプロイが完了するまでに数分かかる場合があります。


## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容:

> [!div class="checklist"]
> * プランへのサブスクライブ 
> * オファーのテスト

次のチュートリアルに進み、テンプレートを使用して VM を作成する方法を学習してください。

> [!div class="nextstepaction"]
> [テンプレートから仮想マシンを作成する](asdk-create-vm-template.md)