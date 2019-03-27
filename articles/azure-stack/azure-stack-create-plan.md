---
title: Azure Stack でのプランの作成 | Microsoft Docs
description: クラウド管理者として、サブスクライバーが仮想マシンをプロビジョニングできるプランを作成する方法を説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: a2ac138228b7b54f486acb0f42975748136a8da7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759471"
---
# <a name="create-a-plan-in-azure-stack"></a>Azure Stack でのプランの作成

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

[プラン](azure-stack-key-features.md)は、1 つまたは複数のサービスとそのクォータをグループ化したものです。 プロバイダーは、ユーザーに提供するプランを作成できます。 そして、ユーザーがオファーをサブスクライブして、それに含まれるプラン、サービス、クォータを使用します。 この例では、コンピューティング、ネットワーク、およびストレージの各リソース プロバイダーを含むプランを作成する方法を示します。 このプランのサブスクライバーは仮想マシンをプロビジョニングすることができます。

## <a name="create-a-plan-1902-and-later"></a>プランを作成する (1902 以降)

1. [Azure Stack 管理者ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. ユーザーがサブスクライブできるプランやオファーを作成するには、**[+ リソースの作成]**、**[オファー + プラン]**、**[プラン]** の順に選択します。
  
   ![プランを選択する](media/azure-stack-create-plan/select-plan.png)

3. タブ付きのユーザー インターフェイスが表示され、プラン名の指定、サービスの追加、選択した各サービスのクォータの定義を行うことができます。 最も重要なのは、作成を決定する前に、作成するオファーの詳細を確認できることです。

   **[新しいプラン]** ウィンドウの **[基本]** タブで、**[表示名]** と **[リソース名]** を入力します。 表示名は、オペレーターに表示されるプランのフレンドリ名です。 管理者ポータル内でオペレーターに表示されるのはプランの詳細のみであることに注意してください。

   ![詳細を指定する](media/azure-stack-create-plan/plan-name.png)

4. プランのコンテナーとして、新しい**リソース グループ**を作成するか、既存のリソース グループを選択します。

   ![リソース グループを指定する](media/azure-stack-create-plan/resource-group.png)

5. **[サービス]** タブを選択し、**[Microsoft.Compute]**、**[Microsoft.Network]**、および **[Microsoft.Storage]** のチェック ボックスをオンにします。
  
   ![サービスを選択する](media/azure-stack-create-plan/services.png)

6. **[クォータ]** タブを選択します。**[Microsoft.Storage]** の横にあるドロップダウン ボックスから既定のクォータを選択するか、**[新規作成]** を選択してカスタマイズされたクォータを作成します。
  
   ![Quotas (クォータ)](media/azure-stack-create-plan/quotas.png)

7. 新しいクォータを作成する場合は、クォータの **[名前]** を入力し、クォータ値を指定します。 **[OK]** を選択してクォータを作成します。

   ![新しいクォータ](media/azure-stack-create-plan/new-quota.png)

8. 手順 6 と 7 を繰り返して、**Microsoft.Network** と **Microsoft.Compute** のクォータを作成して割り当てます。 3 つのサービスすべてにクォータが割り当てられると、次の例のようになります。

   ![クォータの割り当てを完了する](media/azure-stack-create-plan/all-quotas-assigned.png)

9. **[確認および作成]** を選択して、プランを確認します。 すべての値とクォータに目を通し、正しいことを確認します。 各サービス/クォータのペアの左側にある展開矢印に注意してください。 新機能により、選択したプランのクォータを一度に 1 つずつ展開して、プランの各クォータの詳細を表示し、戻って必要な編集を加えることができます。

   ![プランを作成する](media/azure-stack-create-plan/create.png)

10. 準備ができたら、**[作成]** を選択してプランを作成します。

11. 新しいプランを表示するには、**[プラン]** を選択してプランを検索し、プラン名を選択します。 リソースの一覧が長い場合は、**[検索]** を使用して名前でプランを探すことができます。

## <a name="create-a-plan-1901-and-earlier"></a>プランを作成する (1901 以前)

1. [Azure Stack 管理者ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. ユーザーがサブスクライブできるプランやオファーを作成するには、**[+ リソースの作成]**、**[オファー + プラン]**、**[プラン]** の順に選択します。
  
   ![プランを選択する](media/azure-stack-create-plan/select-plan1901.png)

3. **[新しいプラン]** で、**[表示名]** と **[リソース名]** を入力します。 表示名は、ユーザーに表示されるプランのフレンドリ名です。 リソース名は管理者にのみ表示されます。リソース名は、Azure Resource Manager リソースとしてプランを操作するために管理者が使用します。

   ![詳細を指定する](media/azure-stack-create-plan/plan-name1901.png)

4. プランのコンテナーとして、新しい**リソース グループ**を作成するか、既存のリソース グループを選択します。

   ![リソース グループを指定する](media/azure-stack-create-plan/resource-group1901.png)

5. **[サービス]** を選択し、**[Microsoft.Compute]**、**[Microsoft.Network]**、および **[Microsoft.Storage]** のチェックボックスをオンにします。 次に、**[選択]** を選択して構成を保存します。 マウスで各オプションをポイントすると、チェックボックスが表示されます。
  
   ![サービスを選択する](media/azure-stack-create-plan/services1901.png)

6. **[クォータ]**、**[Microsoft.Storage (ローカル)]** の順に選択し、既定のクォータを選択するか **[新しいクォータの作成]** を選択してカスタマイズされたクォータを作成します。
  
   ![Quotas (クォータ)](media/azure-stack-create-plan/quotas1901.png)

7. 新しいクォータを作成する場合は、クォータの **[名前]** を入力し、クォータ値を指定して **[OK]** を選択します。 **[クォータの作成]** ダイアログが閉じられます。

   ![新しいクォータ](media/azure-stack-create-plan/new-quota1901.png)

   次に、作成した新しいクォータを選択します。 クォータを選択するとそれが割り当てられ、選択ダイアログが閉じられます。
  
   ![クォータを割り当てる](media/azure-stack-create-plan/assign-quota1901.png)

8. 手順 6 と 7 を繰り返して、**Microsoft.Network (ローカル)** と **Microsoft.Compute (ローカル)** のクォータを作成して割り当てます。 3 つのサービスすべてにクォータが割り当てられると、次の例のようになります。

   ![クォータの割り当てを完了する](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. **[クォータ]** で **[OK]** を選択し、**[新しいプラン]** で **[作成]** を選択してプランを作成します。

    ![プランを作成する](media/azure-stack-create-plan/create1901.png)

10. 新しいプランを表示するには、**[すべてのリソース]** を選択してプランを検索し、プラン名を選択します。 リソースの一覧が長い場合は、**[検索]** を使用して名前でプランを探すことができます。

    ![プランを確認する](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>次の手順

* [オファーの作成](azure-stack-create-offer.md)
