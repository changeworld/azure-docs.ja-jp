---
title: "Azure Stack でのプランの作成 | Microsoft Docs"
description: "クラウド管理者として、サブスクライバーが仮想マシンをプロビジョニングできるプランを作成する方法を説明します。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 5eefca3541ae9f73514f80b0f8df9e5027600f87
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-plan-in-azure-stack"></a>Azure Stack でのプランの作成

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

[プラン](azure-stack-key-features.md)は、1 つ以上のサービスをグループ化したものです。 プロバイダーは、ユーザーに提供するプランを作成できます。 そして、ユーザーがオファーをサブスクライブして、それに含まれるプランとサービスを使用します。 この例では、コンピューティング、ネットワーク、およびストレージの各リソース プロバイダーを含むプランを作成する方法を示します。 このプランのサブスクライバーは仮想マシンをプロビジョニングすることができます。

1. Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサインインします。 [PowerShell スクリプトの実行](azure-stack-run-powershell-script.md)に関するセクションの手順 5 で作成したアカウントの資格情報を入力します。

2. ユーザーがサブスクライブできるプランやオファーを作成するには、**[新規]** > **[テナントのプラン]** > **[プラン]** をクリックします。

   ![](media/azure-stack-create-plan/image01.png)
3. **[新しいプラン]** ブレードに **[表示名]** と **[リソース名]** を入力します。 表示名は、ユーザーに表示されるプランのフレンドリ名です。 リソース名は、管理者のみが表示できます。 この名前を使用して、管理者はプランを Azure リソース マネージャーのリソースとして操作します。

   ![](media/azure-stack-create-plan/image02.png)
4. プランのコンテナーとして、新しい**リソース グループ**を作成するか、既存のリソース グループを選択します。

   ![](media/azure-stack-create-plan/image02a.png)
5. **[サービス]** をクリックし、**[Microsoft.Compute]**、**[Microsoft.Network]**、および **[Microsoft.Storage]** を選択し、**[選択]** をクリックします。

   ![](media/azure-stack-create-plan/image03.png)
6. **[クォータ]** をクリックし、**[Microsoft.Storage (ローカル)]** をクリックし、既定のクォータを選択するか **[新しいクォータの作成]** をクリックしてクォータをカスタマイズします。

   ![](media/azure-stack-create-plan/image04.png)
7. 新しいクォータを作成する場合は、クォータの名前を入力し、クォータ値を設定し、**[OK]** をクリックし、新しいクォータ名をクリックします。

   ![](media/azure-stack-create-plan/image06.png)
8. **[Microsoft.Network (ローカル)]** をクリックし、既定のクォータを選択するか **[新しいクォータの作成]** をクリックしてクォータをカスタマイズします。

    ![](media/azure-stack-create-plan/image07.png)
9. 新しいクォータを作成する場合は、クォータの名前を入力し、クォータ値を設定し、**[OK]** をクリックし、新しいクォータ名をクリックします。

    ![](media/azure-stack-create-plan/image08.png)
10. **[Microsoft.Compute (ローカル)]** をクリックし、既定のクォータを選択するか **[新しいクォータの作成]** をクリックしてクォータをカスタマイズします。

    ![](media/azure-stack-create-plan/image09.png)
11. 新しいクォータを作成する場合は、クォータの名前を入力し、クォータ値を設定し、**[OK]** をクリックし、新しいクォータ名をクリックします。

    ![](media/azure-stack-create-plan/image10.png)
12. **[クォータ]** ブレードの **[OK]** をクリックし、**[新しいプラン]** ブレードの **[作成]** をクリックしてプランを作成します。

    ![](media/azure-stack-create-plan/image11.png)
13. 新しいプランを表示するには、**[すべてのリソース]** をクリックしてプランを検索し、プラン名をクリックします。

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>次の手順
[オファーの作成](azure-stack-create-offer.md)
