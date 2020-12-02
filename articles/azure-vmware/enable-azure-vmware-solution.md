---
title: Azure VMware Solution リソースを有効にする方法
description: サポート リクエストを送信して Azure VMware Solution リソースを有効にする方法について説明します。 また、Azure VMware Solution のプライベート クラウドにさらに多くのホストを要求することもできます。
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 526d6b38f4b4e3f6c4806b71b4728dee90cf558a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325079"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Azure VMware Solution リソースを有効にする方法
サポート リクエストを送信して [Azure VMware Solution](introduction.md) リソースを有効にする方法について説明します。 また、Azure VMware Solution のプライベート クラウドにさらに多くのホストを要求することもできます。

## <a name="eligibility-criteria"></a>対象となる条件

Azure サブスクリプションの Azure アカウントが必要です。 Azure サブスクリプションは、次のいずれかの条件に準拠している必要があります。

* Microsoft との [Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) に従ったサブスクリプション。
* Azure プランのもとでクラウド ソリューション プロバイダー (CSP) が管理するサブスクリプション。


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>EA のお客様のために Azure VMware Solution を有効にする
Azure VMware Solution リソースを作成する前に、サポート チケットを提出して、ホストが割り当てられるようにする必要があります。 サポート チームがリクエストを受け取った後、リクエストが確認され、ホストが割り当てられるまでに、最大 5 営業日かかります。 Azure VMware Solution の既存のプライベート クラウドがあり、さらに多くのホストを割り当てる必要がある場合は、同じプロセスを実行します。


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
   - ホストの数
   - その他の詳細

   >[!NOTE]
   >Azure VMware Solution では、プライベート クラウドを稼働させ、冗長 N + 1 ホストを提供するために、3 つ以上のホストを推奨しています。 

1. **[確認および作成]** を選択して、リクエストを送信します。

   サポート担当者がリクエストを確認するまで、最大 5 営業日かかります。

   >[!IMPORTANT] 
   >既存の Azure VMware Solution が既にあり、追加のホストを要求する場合は、ホストが割り当てられるまでに 5 営業日が必要であることに注意してください。 

1. ホストをプロビジョニングする前に、Azure portal で **Microsoft.AVS** リソース プロバイダーを必ず登録してください。  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   リソース プロバイダーを登録するその他の方法については、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>CSP のお客様のために Azure VMware Solution を有効にする 

CSP は [Microsoft パートナー センター](https://partner.microsoft.com)を使用して、自社の顧客のために Azure VMware Solution を有効にする必要があります。 

   >[!IMPORTANT] 
   >Azure VMware Solution サービスでは、必要なマルチテナントに対応していません。 これを必要とするホスティング パートナーはサポートされていません。 

1. **パートナー センター** で、 **[CSP]** を選択して、 **[顧客]** 領域にアクセスします。

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft パートナー センターの [顧客] 領域" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. 目的の顧客を選択し、 **[製品の追加]** を選択します。

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft パートナー センター" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. **[Azure プラン]** を選択し、 **[カートに追加]** を選択します。 

1. 顧客の Azure プラン サブスクリプションの全般的な設定を確認し、終了します。 詳細については、[Microsoft パートナー センターに関するドキュメント](/partner-center/azure-plan-manage)を参照してください。

Azure プランを構成し、必要とされる vSphere RBAC アクセス許可を CSP として指定したら、Azure プラン サブスクリプションのクォータを有効にするよう Microsoft に依頼します。 **代理管理者** (AOBO) の手順を使用して、パートナー センターから Azure portal にアクセスします。

1. [パートナー センター](https://partner.microsoft.com)にサインインします。

1. **[CSP]** を選択して、 **[顧客]** 領域にアクセスします。

1. 顧客の詳細を展開し、 **[Microsoft Azure の管理ポータル]** を選択します。

1. Azure portal の **[ヘルプとサポート]** で、 **[新しいサポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)** を作成し、チケットに関する次の情報を入力します。
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
   - ホストの数
   - その他の詳細
   - 複数の顧客をホストすることが想定されているか。

   >[!NOTE]
   >Azure VMware Solution では、プライベート クラウドを稼働させ、冗長 N + 1 ホストを提供するために、3 つ以上のホストを推奨しています。 

1. **[確認および作成]** を選択して、リクエストを送信します。

   サポート担当者がリクエストを確認するまで、最大 5 営業日かかります。

   >[!IMPORTANT] 
   >既存の Azure VMware Solution が既にあり、追加のホストを要求する場合は、ホストが割り当てられるまでに 5 営業日が必要であることに注意してください。 

1. サブスクリプションがサービス プロバイダーによって管理されている場合は、管理チームが、**Admin On Behalf Of (AOBO)** の手順をもう一度使用してパートナー センターから Azure portal にアクセスする必要があります。 Azure portal では、[Cloud Shell](../cloud-shell/overview.md) インスタンスを起動し、**Microsoft AVS** のリソース プロバイダーを登録して、Azure VMware Solution プライベート クラウドのデプロイを続行します。  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   リソース プロバイダーを登録するその他の方法については、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

1. サブスクリプションが顧客によって直接管理されている場合は、サブスクリプションに必要な権限を持つユーザーが **Microsoft.AVS** リソース プロバイダーを登録する必要があります。詳しい情報と、リソース プロバイダーの登録方法については、「[Azure リソースプロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。 


## <a name="next-steps"></a>次のステップ

Azure VMware Solution リソースを有効にして、適切なネットワークを準備したら、[プライベート クラウドを作成](tutorial-create-private-cloud.md)できます。