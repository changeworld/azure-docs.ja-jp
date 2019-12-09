---
title: 低優先度のクォータ | Microsoft Docs
description: 低優先度のクォータ要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 49ac478330cf73dff050a3edcc15933692fa6448
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534727"
---
# <a name="low-priority-quota-limit-increase-for-all-vm-series"></a>低優先度のクォータ: すべての VM シリーズでの制限の引き上げ

低優先度 VM では、Azure 使用の異なるモデルが提供されます。従量課金制または予約 VM インスタンスのデプロイでは必要に応じて Azure で VM を削除でき、低優先度の VM ではそれと引き換えにコストを削減できます。 低優先度の VM の詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority)をご覧ください。

Resource Manager では、仮想マシンに対して 2 種類の vCPU クォータがサポートされています。 **従量課金制の VM と予約 VM インスタンス**では、標準のクォータが使用されます。 **低優先度 VM** では、低優先度のクォータが使用されます。 

**低優先度のクォータ**の種類の場合、Resource Manager の vCPU クォータは、使用可能なすべての VM シリーズにおいて単一のリージョン制限として適用されます。

新しい低優先度 VM をデプロイする場合は、すべての低優先度 VM インスタンスに対する新規および既存の vCPU 使用量の合計が、承認されている低優先度の vCPU のクォータ制限を超えてはなりません。 低優先度のクォータを超過すると、低優先度 VM のデプロイは許可されません。 Azure portal から低優先度の vCPU クォータ制限の引き上げを要求できます。 

標準の vCPU クォータの詳細については、「仮想マシンの vCPU クォータ」ページおよび Azure サブスクリプションとサービスの制限に関するページを参照してください。 標準のクォータに対するリージョンの vCPU の上限引き上げの詳細については、[このページ](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)をご覧ください。

ポータルの **[ヘルプとサポート]** ブレードまたは **[Usages + Quota]\(使用量とクォータ\)** ブレードから、**すべての VM シリーズの低優先度のクォータ制限**の引き上げを要求できます。

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>[ヘルプとサポート] ブレードを使用して、サブスクリプションごとにすべての VM シリーズの低優先度のクォータ制限の引き上げを要求する

Azure Portal で使用可能な Azure の [ヘルプとサポート] ブレード経由でサポート要求を作成するには、次の手順に従います。

1 つのサポート ケースを使用して、**複数のリージョンのクォータを要求**することもできます。 詳細については、以下の手順 10 を参照してください。 


1. [https://portal.azure.com](https://portal.azure.com ) から、 **[ヘルプとサポート]** を選択します。

   ![[ヘルプとサポート]](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **[新しいサポート リクエスト]** を選択します。 

     ![新しいサポート要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. [問題の種類] ドロップダウンで、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

   ![[問題の種類] ドロップダウン](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. クォータの追加が必要なサブスクリプションを選択します。

   ![サブスクリプション newSR を選択する](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. **[クォータの種類]** ドロップダウンで **[Compute -VM (cores-vCPUs) subscription limit increases]/(コンピューティング-VM (コア-vCPU) サブスクリプション制限の増加/)** を選択します。 

   ![[クォータの種類] を選択する](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. **[Problem Details] (問題の詳細)** で、 **[詳細の指定]** をクリックして、要求の処理に役立つ追加情報を指定します。

   ![[詳細の指定]](./media/resource-manager-core-quotas-request/provide-details.png)
   
7.  **[クォータの詳細]** * パネルで、**デプロイ モデル**と**場所**を選択します。

![[詳細の指定]](./media/resource-manager-core-quotas-request/3-7.png)

8. 選択した場所で、 **[種類]** の値に **[Low priority]\(低優先度\)** を選択します。 **[種類]** フィールドで複数選択サポートを使用して、単一のサポート ケースから標準と低優先度の両方のクォータ タイプを要求できます。 **VM シリーズごとの標準クォータの引き上げ**については、 [このページ](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)を参照してください。

![[詳細の指定]](./media/resource-manager-core-quotas-request/3-8.png)

9.  サブスクリプションに対して必要な新しい制限を入力します。 
 
 ![[詳細の指定]](./media/resource-manager-core-quotas-request/3-9.png)

10. 複数の場所に対してクォータを要求するには、ドロップダウンで別の場所をチェックし、該当する VM の種類を選択します。 その後で、必要な新しい制限を入力できます。

![[詳細の指定]](./media/resource-manager-core-quotas-request/3-10.png)

11. 目的のクォータを入力したら、[クォータの詳細] パネルの **[保存して続行]** をクリックしてサポート要求の作成を続行します。

## <a name="request-low-priority-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>[Usages + Quota]\(使用量とクォータ\) ブレードを使用して、サブスクリプションごとにすべての VM シリーズの低優先度のクォータ制限の引き上げを要求する

Azure Portal で使用可能な Azure の [Usages + Quota] (使用量とクォータ) ブレード経由でサポート要求を作成するには、次の手順に従います。

1 つのサポート ケースを使用して、**複数のリージョンのクォータを要求**することもできます。 詳細については、以下の手順 9 を参照してください。 

1.  [https://portal.azure.com](https://portal.azure.com ) から **[サブスクリプション]** を選択します。

 ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  クォータの追加が必要なサブスクリプションを選択します。

 ![サブスクリプションを選択します。](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  **[使用量 + クォータ]** を選択します。

 ![使用量とクォータを選択します。](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  右上隅の **[引き上げを依頼する]** を選択します。

   ![引き上げを依頼する](./media/resource-manager-core-quotas-request/request-increase.png)

5.  クォータの種類として **[Compute-VM (cores-vCPUs) subscription limit increases] (Compute-VM (cores-vCPU) サブスクリプションの制限の増加)** を選択します。

  ![フォームに入力する](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  **[クォータの詳細]** パネルで、デプロイ モデルと場所を選択します。

  ![フォームに入力する](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  選択した場所で、 **[種類]** の値に **[Low priority]\(低優先度\)** を選択します。 **[種類]** フィールドで複数選択サポートを使用して、単一のサポート ケースから標準と低優先度の両方のクォータ タイプを要求できます。 **VM シリーズごとの標準クォータの引き上げ**については、[このページ](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)を参照してください。

  ![フォームに入力する](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  サブスクリプションに対して必要な新しい制限を入力します。

  ![フォームに入力する](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  複数の場所に対してクォータを要求するには、ドロップダウンで別の**場所**をチェックし、該当する VM の種類を選択します。 その後で、必要な新しい制限を入力できます。

  ![フォームに入力する](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. 目的のクォータを入力したら、[クォータの詳細] パネルの **[保存して続行]** をクリックしてサポート要求の作成を続行します。

