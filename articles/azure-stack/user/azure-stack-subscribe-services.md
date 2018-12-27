---
title: このチュートリアルでは、Azure Stack オファーにサブスクライブする方法について説明します | Microsoft Docs
description: このチュートリアルでは、Azure Stack サービスの新しいサブスクリプションを作成し、テスト仮想マシンを作成してオファーをテストする方法について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 35b3e6d59cdb1edc59aa211954b8a69a3ce51bc5
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632207"
---
# <a name="tutorial-create-and-test-a-subscription"></a>チュートリアル: サブスクリプションの作成とテスト

このチュートリアルでは、オファーが含まれているサブスクリプションを作成してテストする方法を示します。 テストの場合は、クラウド管理者として Azure Stack ユーザー ポータルにサインインし、オファーにサブスクライブしてから、仮想マシンを作成します。

> [!TIP]
> より高度な評価エクスペリエンスを使用するには、[特定のユーザーのサブスクリプションを作成](../azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)し、ユーザー ポータルでそのユーザーとしてサインインします。 

このチュートリアルでは、Azure Stack のオファーにサブスクライブする方法について説明します。

学習内容

> [!div class="checklist"]
> * プランへのサブスクライブ 
> * オファーのテスト

## <a name="subscribe-to-an-offer"></a>プランへのサブスクライブ

ユーザーとしてオファーにサブスクライブするには、Azure Stack ユーザー ポータルにサインインして、Azure Stack オペレーターによって提供されているサービスを見つけます。

1. ユーザー ポータルにサインインし、**[サブスクリプションの取得]** を選択します。

   ![サブスクリプションの取得](media/azure-stack-subscribe-services/get-subscription.png)

2. **[表示名]** フィールドに、お客様のサブスクリプションの名前を入力します。 次に、**[Offer]\(オファー\)** を選択し、**[Choose an offer]\(オファーの選択\)** セクションで利用可能なオファーの 1 つを選択します。 **[作成]** を選択します。

   ![オファーの作成](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > サブスクリプションの使用を開始するには、ここでユーザー ポータルを更新する必要があります。

3. 作成したサブスクリプションを表示するには、**[すべてのサービス]** を選択します。 その後、**[一般]** カテゴリで **[サブスクリプション]** を選択し、新しいサブスクリプションを選択します。 オファーにサブスクライブしたら、ポータルを更新して、サービスが新しいサブスクリプションの一部として含まれているかどうかを確認します。 この例では、**仮想マシン**が追加されています。

   ![サブスクリプションの表示](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>オファーのテスト

ユーザー ポータルにサインインしている間に、新しいサブスクリプション機能を使用して仮想マシンをプロビジョニングすることで、オファーをテストできます。 

> [!NOTE]
> このテストでは、まず Windows Server 2016 Datacenter VM が Azure Stack マーケットプレースに追加されている必要があります。 

1. ユーザー ポータルにサインインします。

2. ユーザー ポータルで、**[仮想マシン]**、**[追加]**、**[Windows Server 2016 Datacenter]** の順に選択し、**[作成]** をクリックします。

3. **[基本]** セクションで、**[名前]**、**[ユーザー名]**、**[パスワード]** を入力し、**[サブスクリプション]** を選択します。次に、**[リソース グループ]** を作成し (または既存のものを選択し)、**[OK]** を選択します。

4. **[サイズの選択]** セクションで、**[Standard A1]** を選択してから **[選択]** をクリックします。  

5. **[設定]** ブレードで、既定値のまま **[OK]** を選択します。

6. **[概要]** セクションで、**[OK]** をクリックして仮想マシンを作成します。  

7. 新しい仮想マシンを表示するには、**[仮想マシン]** を選択し、新しい仮想マシンを検索して、その名前をクリックします。

    ![すべてのリソース](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> 仮想マシンのデプロイが完了するまでに数分かかる場合があります。


## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容:

> [!div class="checklist"]
> * プランへのサブスクライブ 
> * オファーのテスト


> [!div class="nextstepaction"]
> [コミュニティ テンプレートからの VM の作成](azure-stack-create-vm-template.md)