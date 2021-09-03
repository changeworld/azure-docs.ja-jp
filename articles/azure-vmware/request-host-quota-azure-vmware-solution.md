---
title: Azure VMware Solution のホスト クォータを要求する
description: Azure VMware Solution のホスト クォータまたは容量を要求する方法について説明します。 また、Azure VMware Solution の既存のプライベート クラウドにさらに多くのホストをリクエストすることもできます。
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 08/06/2021
ms.openlocfilehash: 92ebc79193525802c1e7c7ede9287e3300799a10
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322455"
---
# <a name="request-host-quota-for-azure-vmware-solution"></a>Azure VMware Solution のホスト クォータを要求する

この記事では、[Azure VMware Solution](introduction.md) のホスト クォータまたは容量を要求します。 新しいデプロイの場合も既存のものの場合も、ホストを割り当ててもらうためのサポート チケットを送信します。 

Azure VMware Solution の既存のプライベート クラウドがあり、さらに多くのホストを割り当てる必要がある場合は、同じプロセスに従います。

>[!IMPORTANT]
>要求された数によっては、ホストの割り当てに最長で 5 営業日かかることがあります。  そのため、クォータの引き上げを頻繁に要求しなくて済むように、プロビジョニングに必要な分を要求してください。

## <a name="eligibility-criteria"></a>対象となる条件

次のいずれかの条件に準拠する Azure サブスクリプションの Azure アカウントが必要です。

- Microsoft との [Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) に従ったサブスクリプション。
- 既存のクラウド ソリューション プロバイダー (CSP) のもとで Azure がコントラクトまたは Azure プランを提供している、CSP によって管理されているサブスクリプション。
- Microsoft との [Modern Commerce 契約](../cost-management-billing/understand/mca-overview.md)。

## <a name="request-host-quota-for-ea-customers"></a>EA のお客様用のホスト クォータをリクエストする

1. Azure portal の **[ヘルプとサポート]** で、 **[新しいサポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)** を作成し、次の情報を入力します。
   - **問題の種類:** 技術
   - **サブスクリプション:** サブスクリプションを選択します。
   - **サービス:** [すべてのサービス] > [Azure VMware Solution]
   - **リソース:** 一般的な質問 
   - **概要:** 容量が必要です
   - **問題の種類:** 容量管理に関する問題
   - **問題のサブタイプ:** 追加のホストクォータおよび容量に関する顧客の要求

1. サポート チケットの **[詳細]** タブの **[説明]** に、次の情報を入力します。

   - POC または運用環境 
   - リージョン名
   - ホストの数
   - その他の詳細

   >[!NOTE]
   >Azure VMware Solution には少なくとも 3 つのホストが必要であり、ホストの N+1 冗長が推奨されます。 

1. **[確認および作成]** を選択して、リクエストを送信します。


## <a name="request-host-quota-for-csp-customers"></a>CSP のお客様用のホスト クォータをリクエストする 

CSP は [Microsoft パートナー センター](https://partner.microsoft.com)を使用して、自社の顧客のために Azure VMware Solution を有効にする必要があります。 この記事では、[CSP Azure プラン](/partner-center/azure-plan-lp)を例として使用して、パートナー向けの購入手順を示します。

パートナー センターから **代理管理者** (AOBO) の手順を使用して、Azure portal にアクセスします。

>[!IMPORTANT] 
>Azure VMware Solution サービスでは、必要なマルチテナントに対応していません。 これを必要とするホスティング パートナーはサポートされていません。 

1. CSP Azure プランを構成します。

   1. **パートナー センター** で、 **[CSP]** を選択して、 **[顧客]** 領域にアクセスします。
   
      :::image type="content" source="media/pre-deployment/csp-customers-screen.png" alt-text="Microsoft パートナー センター顧客領域を示すスクリーンショット。" lightbox="media/pre-deployment/csp-customers-screen.png":::
   
   1. 目的の顧客を選択し、 **[製品の追加]** を選択します。
   
      :::image type="content" source="media/pre-deployment/csp-partner-center.png" alt-text="Microsoft パートナー センター で選択された Azure プランを示すスクリーンショット。" lightbox="media/pre-deployment/csp-partner-center.png":::
   
   1. **[Azure プラン]** を選択し、 **[カートに追加]** を選択します。 
   
   1. 顧客の Azure プラン サブスクリプションの全般的な設定を確認し、終了します。 詳細については、[Microsoft パートナー センターに関するドキュメント](/partner-center/azure-plan-manage)を参照してください。

1. Azure プランを構成し、サブスクリプションに必要な [Azure RBAC アクセス許可](/partner-center/azure-plan-manage)を準備したら、Azure プラン サブスクリプションのクォータをリクエストします。 

   1. **代理管理者** (AOBO) の手順を使用して、[Microsoft パートナー センター](https://partner.microsoft.com)から Azure portal にアクセスします。
   
   1. **[CSP]** を選択して、 **[顧客]** 領域にアクセスします。
   
   1. 顧客の詳細を展開し、 **[Microsoft Azure の管理ポータル]** を選択します。
   
   1. Azure portal の **[ヘルプとサポート]** で、 **[新しいサポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)** を作成し、次の情報を入力します。
      - **問題の種類:** 技術
      - **サブスクリプション:** サブスクリプションを選択します。
      - **サービス:** [すべてのサービス] > [Azure VMware Solution]
      - **リソース:** 一般的な質問 
      - **概要:** 容量が必要です
      - **問題の種類:** 容量管理に関する問題
      - **問題のサブタイプ:** 追加のホストクォータおよび容量に関する顧客の要求
   
   1. サポート チケットの **[詳細]** タブの **[説明]** に、次の情報を入力します。
   
      - POC または運用環境 
      - リージョン名
      - ホストの数
      - その他の詳細
      - 複数の顧客をホストすることが想定されているか。
   
      >[!NOTE]
      >Azure VMware Solution には少なくとも 3 つのホストが必要であり、ホストの N+1 冗長が推奨されます。 
   
   1. **[確認および作成]** を選択して、リクエストを送信します。


## <a name="next-steps"></a>次の手順

Azure VMware Solution をデプロイするには、サブスクリプションに[リソース プロバイダーを登録](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)してサービスを有効にしておく必要があります。   
