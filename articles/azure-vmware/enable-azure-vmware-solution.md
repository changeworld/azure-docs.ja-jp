---
title: Azure VMware Solution リソースを有効にする方法
description: サポート リクエストを送信して Azure VMware Solution リソースを有効にする方法について説明します。 また、Azure VMware Solution のプライベート クラウドにさらに多くのノードを要求することもできます。
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 72a7aca97067ce4e9ed0e901e4016c82b3549eb1
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817883"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Azure VMware Solution リソースを有効にする方法
サポート リクエストを送信して Azure VMware Solution リソースを有効にする方法について説明します。 また、Azure VMware Solution のプライベート クラウドにさらに多くのノードを要求することもできます。

## <a name="eligibility-criteria"></a>対象となる条件

* Microsoft との [Azure Enterprise Agreement (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) が必要です。
* Azure サブスクリプションの Azure アカウントが必要です。


## <a name="enable-azure-vmware-solution-resource"></a>Azure VMware Solution リソースを有効にする
Azure VMware Solution リソースを作成する前に、サポート チケットを提出して、ノードが割り当てられるようにする必要があります。 サポート チームがリクエストを受け取ると、リクエストが確認されてノードが割り当てられるまでに、最大 5 営業日かかります。 Azure VMware Solution の既存のプライベート クラウドがあり、さらに多くのノードを割り当てる必要がある場合は、同じプロセスを実行します。


1. Azure portal の **[ヘルプとサポート]** で、 **[新しいサポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)** を作成し、チケットに関する次の情報を提供します。
   - **問題の種類:** 技術
   - **サブスクリプション:** サブスクリプションを選択します。
   - **サービス:** [すべてのサービス] > [Azure VMware Solution]
   - **リソース:** 一般的な質問 
   - **概要:** 容量が必要です
   - **問題の種類:** 容量管理に関する問題
   - **問題のサブタイプ:** 追加のホストクォータおよび容量に関する顧客の要求

1. サポート チケットの **[詳細]** タブの **[説明]** で、次を指定します。

   - POC または運用環境 
   - リージョン名
   - ノードの数
   - その他の詳細

   >[!NOTE]
   >Azure VMware Solution では、プライベート クラウドを稼働させ、冗長 N + 1 ノードを提供するために、3 つ以上のノードを推奨しています。 

1. **[確認および作成]** を選択して、リクエストを送信します。

   サポート担当者がリクエストを確認するまで、最大 5 営業日かかります。

   >[!IMPORTANT] 
   >既存の Azure VMware Solution が既にあり、追加のノードをリクエストする場合は、ノードが割り当てられるまでに 5 営業日が必要であることに注意してください。 

1. ノードをプロビジョニングする前に、Azure portal で **Microsoft.AVS** リソース プロバイダーを必ず登録してください。  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   リソース プロバイダーを登録するその他の方法については、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。