---
title: Azure Resource Manager の vCPU クォータを増やす要求 | Microsoft Docs
description: Azure Resource Manager の vCPU クォータを増やす要求
author: ganganarayanan
ms.author: gangan
ms.date: 3/15/2018
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a42fa8e4e8dae140db4fcc8977bda335455b97a1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Resource Manager の vCPU クォータを増やす要求

Resource Manager の vCPU クォータは、リージョン レベルおよび SKU のファミリ レベルで適用されます。
クォータを適用する方法について詳しくは、[Azure のサブスクリプションとサービスの制限](http://aka.ms/quotalimits)に関するページをご覧ください。
SKU のファミリについては、[Virtual Machines の料金](http://aka.ms/pricingcompute)ページでコストとパフォーマンスを比較できます。

vCPU 数の増加を要求するには、Azure Portal ([https://portal.azure.com](https://portal.azure.com)) で vCPU についてクォータのサポート ケースを作成します。

> [!NOTE]
> Azure Portal で[サポート要求を作成](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)する方法

1. **[サブスクリプション]** を選択します。

   ![サブスクリプション](./media/resource-manager-core-quotas-request/subscriptions.png)

2. クォータの追加が必要なサブスクリプションを選択します。

   ![サブスクリプションを選択します。](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **[使用量 + クォータ]** を選択します。

   ![使用量とクォータを選択します。](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 右上の **[引き上げを依頼する]** を選択します。

   ![引き上げを依頼する](./media/resource-manager-core-quotas-request/request-increase.png)

5. クォータの種類として、**[コア]** を選択します。 

   ![フォームに入力する](./media/resource-manager-core-quotas-request/forms.png)