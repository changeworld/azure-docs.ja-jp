---
title: "Azure Stack でのオファーの作成 | Microsoft Docs"
description: "クラウド管理者が Azure Stack でユーザーのオファーを作成する方法について説明します。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 2666aacbbaf44ae9b3bcf2df480e835457e6f449
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-offer-in-azure-stack"></a>Azure Stack でのオファーの作成

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

[オファー](azure-stack-key-features.md)は、プロバイダーがユーザーに提示する 1 つまたは複数のプランのグループで、ユーザーはそれを購入またはサブスクライブします。 ここでは、最後の手順で[作成したプラン](azure-stack-create-plan.md)を含むオファーを作成する方法について説明します。 このオファーのサブスクライバーは仮想マシンをプロビジョニングすることができます。

1. Azure Stack 管理者ポータルに (https://adminportal.local.azurestack.external) にサインインし、**[新規]** > **[テナントのプラン]** > **[オファー]** をクリックします。

   ![](media/azure-stack-create-offer/image01.png)
2. **[新しいプラン]** ページの **[表示名]** と **[リソース名]** に入力し、新規または既存の **[リソース グループ]** を選択します。 表示名は、オファーのフレンドリ名です。 このフレンドリ名は、ユーザーがサブスクライブするときに表示されるオファーに関する唯一の情報です。 そのため、ユーザーがオファーで提供される内容を理解できるように、わかりやすい名前を付けてください。 リソース名は、管理者のみが表示できます。 この名前を使用して、管理者はオファーを Azure リソース マネージャーのリソースとして操作します。

   ![](media/azure-stack-create-offer/image01a.png)
3. **[基本プラン]** をクリックして **[プラン]** ウィンドウを開き、オファーに含めるプランを選択して、**[選択]** をクリックします。 **[作成]** をクリックしてオファーを作成します。

   ![](media/azure-stack-create-offer/image02.png)
4. **[すべてのリソース]** をクリックし、新しいオファーを検索して新しいオファーをクリックします。**[状態の変更]** をクリックし、**[パブリック]** をクリックします。

   ![](media/azure-stack-create-offer/image03.png)

サブスクライブ時にユーザーが完全なビューを取得するために、オファーはパブリックにする必要があります。 オファーの状態:

* **パブリック**: ユーザーに表示されます。
* **プライベート**: クラウド管理者にのみ表示されます。 プランまたはオファーの下書き作成や、クラウド管理者が[ユーザーの各サブスクリプションを作成](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)する必要がある場合に便利です。
* **使用停止**: 新しいサブスクライバーに公開されません。 クラウド管理者は [使用停止] を使って、既存のサブスクライバーを変更することなく、以降のサブスクリプションを回避することができます。

オファーの変更は、ユーザーにすぐに表示されるわけではありません。 変更を表示するには、新しいリソース/リソース グループを作成するときにログアウト/ログインして、"サブスクリプションの選択" に新しいサブスクリプションを表示する必要があります。

> [!NOTE]
>また、[Azure Stack Service Administrator の readme](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin) の説明に従って、PowerShell を使用して既定のオファー、プラン、クォータを作成することもできます。
>


### <a name="next-steps"></a>次の手順
[サブスクリプションを作成する](azure-stack-subscribe-plan-provision-vm.md)      
[仮想マシンのプロビジョニング](azure-stack-provision-vm.md)
