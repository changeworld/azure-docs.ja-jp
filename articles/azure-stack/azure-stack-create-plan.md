---
title: Azure Stack でのプランの作成 | Microsoft Docs
description: クラウド管理者として、サブスクライバーが仮想マシンをプロビジョニングできるプランを作成する方法を説明します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: b1bfff16c4f51a9fa53204930df78cbd2cf19b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-plan-in-azure-stack"></a>Azure Stack でのプランの作成

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

[プラン](azure-stack-key-features.md)は、1 つ以上のサービスをグループ化したものです。 プロバイダーは、ユーザーに提供するプランを作成できます。 そして、ユーザーがオファーをサブスクライブして、それに含まれるプランとサービスを使用します。 この例では、コンピューティング、ネットワーク、およびストレージの各リソース プロバイダーを含むプランを作成する方法を示します。 このプランのサブスクライバーは仮想マシンをプロビジョニングすることができます。

1. Azure Stack 管理者ポータル (https://adminportal.local.azurestack.external) にサインインします。

2. ユーザーがサブスクライブできるプランやオファーを作成するには、**[新規]** > **[プラン]** > **[プラン]** の順にクリックします。  
   ![プランを選択する](media/azure-stack-create-plan/select-plan.png)

3. **[新しいプラン]** ウィンドウに **[表示名]** と **[リソース名]** を入力します。 表示名は、ユーザーに表示されるプランのフレンドリ名です。 管理者のみがリソース名を見ることができます。リソース名は、Azure Resource Manager リソースとしてプランを操作するために管理者が使用する名前です。  
   ![詳細を指定する](media/azure-stack-create-plan/plan-name.png)

4. プランのコンテナーとして、新しい**リソース グループ**を作成するか、既存のリソース グループを選択します。  
   ![リソース グループを指定する](media/azure-stack-create-plan/resource-group.png)

5. **[サービス]** を選択し、**[Microsoft.Compute]**、**[Microsoft.Network]**、および **[Microsoft.Storage]** のチェックボックスをオンにします。 次に、**[選択]** を選択して構成を保存します。 マウスで各オプションをポイントすると、チェックボックスが表示されます。  
   ![サービスを選択する](media/azure-stack-create-plan/services.png)

6. **[クォータ]**、**[Microsoft.Storage (ローカル)]** の順に選択し、既定のクォータを選択するか **[新しいクォータの作成]** を選択してクォータをカスタマイズします。  
   ![クォータ](media/azure-stack-create-plan/quotas.png)

7. 新しいクォータを作成する場合は、クォータの **[名前]** を入力し、クォータ値を指定して **[OK]** を選択します。 **[クォータの作成]** ウィンドウが閉じられます。
   ![新しいクォータ](media/azure-stack-create-plan/new-quota.png)

   次に、作成した新しいクォータを選択します。 クォータを選択すると、割り当てられ、選択ウィンドウが閉じられます。  
   ![クォータを割り当てる](media/azure-stack-create-plan/assign-quota.png)

8. 手順 6 と 7 を繰り返して、**Microsoft.Network (ローカル)** と **Microsoft.Compute (ローカル)** のクォータを作成して割り当てます。  3 つのサービスのすべてにクォータが割り当てられている場合は、次の図のように表示されます。  
   ![クォータ割り当ての完了](media/azure-stack-create-plan/all-quotas-assigned.png)

9. **[クォータ]** ウィンドウの **[OK]** を選択し、**[新しいプラン]** ウィンドウの **[作成]** をクリックしてプランを作成します。  
    ![プランを作成する](media/azure-stack-create-plan/create.png)
10. 新しいプランを表示するには、**[すべてのリソース]** を選択してプランを検索し、プラン名を選択します。 リソースの一覧が長い場合は、**[検索]** を使用して名前でプランを探すことができます。  
   ![プランを確認する](media/azure-stack-create-plan/plan-overview.png)

### <a name="next-steps"></a>次の手順
[オファーの作成](azure-stack-create-offer.md)
