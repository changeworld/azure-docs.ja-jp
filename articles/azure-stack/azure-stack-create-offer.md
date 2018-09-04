---
title: Azure Stack でのオファーの作成 | Microsoft Docs
description: クラウド管理者が Azure Stack でユーザーのオファーを作成する方法について説明します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/20/2018
ms.author: brenduns
ms.reviewer: efemmano
ms.openlocfilehash: 66a89c3cb14dd642ae993cbf3c45885635f59759
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "43050426"
---
# <a name="create-an-offer-in-azure-stack"></a>Azure Stack でのオファーの作成

[オファー](azure-stack-key-features.md)は、プロバイダーがユーザーに提示する 1 つまたは複数のプランのグループで、ユーザーはそれを購入またはサブスクライブします。 ここでは、[作成したプラン](azure-stack-create-plan.md)を含むオファーを作成する方法について説明します。 このオファーのサブスクライバーは仮想マシンを設定できます。

1. Azure Stack 管理者ポータル (https://adminportal.local.azurestack.external) にサインインし、**[新規]** > **[テナントのプラン]** > **[オファー]** の順にクリックします。

   ![オファーの作成](media/azure-stack-create-offer/image01.png)
  
2. **[新しいオファー]** で **[表示名]** と **[リソース名]** を入力し、**[リソース グループ]** の下で **[新規作成]** または **[既存のものを使用]** を選択します。 表示名は、オファーのフレンドリ名です。 このフレンドリ名は、ユーザーがオファーをサブスクライブするときに表示されるオファーに関する唯一の情報です。 ユーザーがオファーで提供される内容を理解できるように、わかりやすい名前を付けてください。 リソース名は、管理者のみが表示できます。 この名前を使用して、管理者はオファーを Azure リソース マネージャーのリソースとして操作します。

   ![新しいオファー](media/azure-stack-create-offer/image01a.png)
  
3. **[基本プラン]** を選択して **[プラン]** を開きます。 オファーに含めるプランを選択し、**[選択]** を選択します。 オファーを作成するには、**[作成]** を選択します。

   ![プランの選択](media/azure-stack-create-offer/image02.png)
  
4. オファーの作成後、その状態を変更することができます。 ユーザーがサブスクライブ時にすべての情報を見ることができるように、オファーは "*パブリック*" にする必要があります。 オファーの状態:

   - **パブリック**: ユーザーに表示されます。
   - **プライベート**: クラウド管理者にのみ表示されます。 この設定は、プランまたはオファーの下書き作成や、クラウド管理者が[ユーザーの各サブスクリプションを作成する](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)場合に便利です。
   - **使用停止**: 新しいサブスクライバーに公開されません。 クラウド管理者は [使用停止] を使用して、既存のサブスクライバーに影響を及ぼすことなく、以降のサブスクリプションを回避することができます。

   > [!TIP]  
   > オファーの変更は、ユーザーにすぐに表示されるわけではありません。 変更された新しいオファーをユーザーが見るには、サインアウトしてから、もう一度ユーザー ポータルにサインインする必要がある場合があります。

   オファーの概要で、**[アクセシビリティの状態]** を選択します。 使用する状態 (**[パブリック]** など) を選択し、**[保存]** を選択します。
 
     ![状態の選択](media/azure-stack-create-offer/change-stage-1807.png)

     または、**[状態の変更]** を選択して、状態を選択します。

    ![アクセシビリティの状態を選択する](media/azure-stack-create-offer/change-stage-select-1807.png)

   > [!NOTE]
   > PowerShell を使用して、既定のオファー、プラン、およびクォータを作成することもできます。 詳しくは、「[Azure Stack PowerShell Module 1.4.0](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0)」をご覧ください。

## <a name="next-steps"></a>次の手順

- [サブスクリプションを作成する](azure-stack-subscribe-plan-provision-vm.md)
- [仮想マシンのプロビジョニング](azure-stack-provision-vm.md)
