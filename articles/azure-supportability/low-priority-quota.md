---
title: スポット クォータ | Microsoft Docs
description: スポット クォータ要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850569"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>スポット クォータ: すべての VM シリーズでの制限の引き上げ

スポット VM では、Azure 使用の異なるモデルが提供されます。従量課金制または予約 VM インスタンスのデプロイでは必要に応じて Azure で VM を削除でき、スポット VM ではそれと引き換えにコストを削減できます。 スポット VM について詳しくは、[こちら](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)を参照してください。

Resource Manager では、仮想マシンに対して 2 種類の vCPU クォータがサポートされています。 **従量課金制の VM と予約 VM インスタンス**では、標準のクォータが使用されます。 **スポット VM** は、スポットクォータを使用します。 

**スポット クォータ**の種類の場合、Resource Manager の vCPU クォータは、使用可能なすべての VM シリーズにおいて単一のリージョン制限として適用されます。

新しいスポット VM をデプロイする場合は、すべてのスポット VM インスタンスに対する新規および既存の vCPU 使用量の合計が、承認されているスポット vCPU のクォータ制限を超えてはなりません。 スポット クォータを超過すると、スポット VM のデプロイは許可されません。 Azure portal からスポット vCPU クォータ制限の引き上げを要求できます。 

標準の vCPU クォータの詳細については、「仮想マシンの vCPU クォータ」ページおよび Azure サブスクリプションとサービスの制限に関するページを参照してください。 標準のクォータに対するリージョンの vCPU の上限引き上げの詳細については、[このページ](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)をご覧ください。

ポータルの **[ヘルプとサポート]** ブレードまたは **[Usages + Quota]\(使用量とクォータ\)** ブレードから、**すべての VM シリーズのスポット クォータ制限**の引き上げを要求できます。

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>[ヘルプとサポート] ブレードを使用して、サブスクリプションごとにすべての VM シリーズのスポット クォータ制限の引き上げを要求する

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

8. 選択した場所で、 **[タイプ]** の値に **[スポット]** を選択します。 **[タイプ]** フィールドで複数選択サポートを使用して、単一のサポート ケースから Standard とスポットの両方のクォータ タイプを要求できます。 **VM シリーズごとの標準クォータの引き上げ**については、 [このページ](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)を参照してください。

![[詳細の指定]](./media/resource-manager-core-quotas-request/3-8.png)

9.  サブスクリプションに対して必要な新しい制限を入力します。 
 
 ![[詳細の指定]](./media/resource-manager-core-quotas-request/3-9.png)

10. 複数の場所に対してクォータを要求するには、ドロップダウンで別の場所をチェックし、該当する VM の種類を選択します。 その後で、必要な新しい制限を入力できます。

![[詳細の指定]](./media/resource-manager-core-quotas-request/3-10.png)

11. 目的のクォータを入力したら、[クォータの詳細] パネルの **[保存して続行]** をクリックしてサポート要求の作成を続行します。

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>[Usages + Quota]\(使用量とクォータ\) ブレードを使用して、サブスクリプションごとにすべての VM シリーズのスポット クォータ制限の引き上げを要求する

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
 
7.  選択した場所で、 **[タイプ]** の値に **[スポット]** を選択します。 **[タイプ]** フィールドで複数選択サポートを使用して、単一のサポート ケースから Standard とスポットの両方のクォータ タイプを要求できます。 **VM シリーズごとの標準クォータの引き上げ**については、[このページ](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)を参照してください。

  ![フォームに入力する](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  サブスクリプションに対して必要な新しい制限を入力します。

  ![フォームに入力する](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  複数の場所に対してクォータを要求するには、ドロップダウンで別の**場所**をチェックし、該当する VM の種類を選択します。 その後で、必要な新しい制限を入力できます。

  ![フォームに入力する](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. 目的のクォータを入力したら、[クォータの詳細] パネルの **[保存して続行]** をクリックしてサポート要求の作成を続行します。


