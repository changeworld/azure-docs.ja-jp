---
title: Azure VMware Solution のホスト クォータを要求する
description: Azure VMware Solution のホスト クォータまたは容量を要求する方法について説明します。 また、Azure VMware Solution の既存のプライベート クラウドにさらに多くのホストをリクエストすることもできます。
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 09/27/2021
ms.openlocfilehash: a95f152c5e55b1750aa34c9a4093e311eb27df29
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504495"
---
# <a name="request-host-quota-for-azure-vmware-solution"></a>Azure VMware Solution のホスト クォータを要求する

この記事では、<bpt id="p1">[</bpt>Azure VMware Solution<ept id="p1">](introduction.md)</ept> のホスト クォータまたは容量を要求します。 新しいデプロイの場合も既存のものの場合も、ホストを割り当ててもらうためのサポート チケットを送信します。 

Azure VMware Solution の既存のプライベート クラウドがあり、さらに多くのホストを割り当てる必要がある場合は、同じプロセスに従います。

>[!IMPORTANT]
>要求された数によっては、ホストの割り当てに最長で 5 営業日かかることがあります。  そのため、クォータの引き上げを頻繁に要求しなくて済むように、プロビジョニングに必要な分を要求してください。

## <a name="eligibility-criteria"></a>対象となる条件

次のいずれかの条件に準拠する Azure サブスクリプションの Azure アカウントが必要です。

- Microsoft との <bpt id="p1">[</bpt>Azure Enterprise Agreement (EA)<ept id="p1">](../cost-management-billing/manage/ea-portal-agreements.md)</ept> に従ったサブスクリプション。
- 既存のクラウド ソリューション プロバイダー (CSP) のもとで Azure がコントラクトまたは Azure プランを提供している、CSP によって管理されているサブスクリプション。
- Microsoft との [Microsoft 顧客契約](../cost-management-billing/understand/mca-overview.md)。

## <a name="request-host-quota-for-ea-customers"></a>EA のお客様用のホスト クォータをリクエストする

1. Azure portal の <bpt id="p1">**</bpt>[ヘルプとサポート]<ept id="p1">**</ept> で、 <bpt id="p2">**</bpt><bpt id="p3">[</bpt>新しいサポート リクエスト<ept id="p3">](https://rc.portal.azure.com/#create/Microsoft.Support)</ept><ept id="p2">**</ept> を作成し、次の情報を入力します。
   - <bpt id="p1">**</bpt>問題の種類:<ept id="p1">**</ept> 技術
   - <bpt id="p1">**</bpt>サブスクリプション:<ept id="p1">**</ept> サブスクリプションを選択します。
   - <bpt id="p1">**</bpt>サービス:<ept id="p1">**</ept> [すべてのサービス] > [Azure VMware Solution]
   - <bpt id="p1">**</bpt>リソース:<ept id="p1">**</ept> 一般的な質問 
   - <bpt id="p1">**</bpt>概要:<ept id="p1">**</ept> 容量が必要です
   - <bpt id="p1">**</bpt>問題の種類:<ept id="p1">**</ept> 容量管理に関する問題
   - <bpt id="p1">**</bpt>問題のサブタイプ:<ept id="p1">**</ept> 追加のホストクォータおよび容量に関する顧客の要求

1. サポート チケットの <bpt id="p1">**</bpt>[詳細]<ept id="p1">**</ept> タブの <bpt id="p2">**</bpt>[説明]<ept id="p2">**</ept> に、次の情報を入力します。
 
   - リージョン名
   - ホストの数
   - その他の詳細

   >[!NOTE]
   >Azure VMware Solution には少なくとも 3 つのホストが必要であり、ホストの N+1 冗長が推奨されます。 

1. <bpt id="p1">**</bpt>[確認および作成]<ept id="p1">**</ept> を選択して、リクエストを送信します。


## <a name="request-host-quota-for-csp-customers"></a>CSP のお客様用のホスト クォータをリクエストする 

CSP は <bpt id="p1">[</bpt>Microsoft パートナー センター<ept id="p1">](https://partner.microsoft.com)</ept>を使用して、自社の顧客のために Azure VMware Solution を有効にする必要があります。 この記事では、<bpt id="p1">[</bpt>CSP Azure プラン<ept id="p1">](/partner-center/azure-plan-lp)</ept>を例として使用して、パートナー向けの購入手順を示します。

パートナー センターから<bpt id="p1">**</bpt>代理管理者<ept id="p1">**</ept> (AOBO) の手順を使用して、Azure portal にアクセスします。

>[!IMPORTANT] 
>Azure VMware Solution サービスでは、必要なマルチテナントに対応していません。 これを必要とするホスティング パートナーはサポートされていません。 

1. CSP Azure プランを構成します。

   1. <bpt id="p1">**</bpt>パートナー センター<ept id="p1">**</ept>で、 <bpt id="p2">**</bpt>[CSP]<ept id="p2">**</ept> を選択して、 <bpt id="p3">**</bpt>[顧客]<ept id="p3">**</ept> 領域にアクセスします。
   
      <bpt id="p1">:::image type="content" source="media/pre-deployment/csp-customers-screen.png" alt-text="</bpt>Microsoft パートナー センター顧客領域を示すスクリーンショット。<ept id=&quot;p1&quot;>" lightbox="media/pre-deployment/csp-customers-screen.png":::</ept>
   
   1. 目的の顧客を選択し、 <bpt id="p1">**</bpt>[製品の追加]<ept id="p1">**</ept> を選択します。
   
      <bpt id="p1">:::image type="content" source="media/pre-deployment/csp-partner-center.png" alt-text="</bpt>Microsoft パートナー センター で選択された Azure プランを示すスクリーンショット。<ept id=&quot;p1&quot;>" lightbox="media/pre-deployment/csp-partner-center.png":::</ept>
   
   1. <bpt id="p1">**</bpt>[Azure プラン]<ept id="p1">**</ept> を選択し、 <bpt id="p2">**</bpt>[カートに追加]<ept id="p2">**</ept> を選択します。 
   
   1. 顧客の Azure プラン サブスクリプションの全般的な設定を確認し、終了します。 詳細については、<bpt id="p1">[</bpt>Microsoft パートナー センターに関するドキュメント<ept id="p1">](/partner-center/azure-plan-manage)</ept>を参照してください。

1. Azure プランを構成し、サブスクリプションに必要な <bpt id="p1">[</bpt>Azure RBAC アクセス許可<ept id="p1">](/partner-center/azure-plan-manage)</ept>を準備したら、Azure プラン サブスクリプションのクォータをリクエストします。 

   1. <bpt id="p2">**</bpt>代理管理者<ept id="p2">**</ept> (AOBO) の手順を使用して、<bpt id="p1">[</bpt>Microsoft パートナー センター<ept id="p1">](https://partner.microsoft.com)</ept>から Azure portal にアクセスします。
   
   1. **[CSP]** を選択して、 **[顧客]** 領域にアクセスします。
   
   1. 顧客の詳細を展開し、 **[Microsoft Azure の管理ポータル]** を選択します。
   
   1. Azure portal の <bpt id="p1">**</bpt>[ヘルプとサポート]<ept id="p1">**</ept> で、 <bpt id="p2">**</bpt><bpt id="p3">[</bpt>新しいサポート リクエスト<ept id="p3">](https://rc.portal.azure.com/#create/Microsoft.Support)</ept><ept id="p2">**</ept> を作成し、次の情報を入力します。
      - <bpt id="p1">**</bpt>問題の種類:<ept id="p1">**</ept> 技術
      - <bpt id="p1">**</bpt>サブスクリプション:<ept id="p1">**</ept> サブスクリプションを選択します。
      - <bpt id="p1">**</bpt>サービス:<ept id="p1">**</ept> [すべてのサービス] > [Azure VMware Solution]
      - <bpt id="p1">**</bpt>リソース:<ept id="p1">**</ept> 一般的な質問 
      - <bpt id="p1">**</bpt>概要:<ept id="p1">**</ept> 容量が必要です
      - <bpt id="p1">**</bpt>問題の種類:<ept id="p1">**</ept> 容量管理に関する問題
      - <bpt id="p1">**</bpt>問題のサブタイプ:<ept id="p1">**</ept> 追加のホストクォータおよび容量に関する顧客の要求
   
   1. サポート チケットの <bpt id="p1">**</bpt>[詳細]<ept id="p1">**</ept> タブの <bpt id="p2">**</bpt>[説明]<ept id="p2">**</ept> に、次の情報を入力します。
   
      - リージョン名
      - ホストの数
      - その他の詳細
      - 複数の顧客をホストすることが想定されているか。
   
      >[!NOTE]
      >Azure VMware Solution には少なくとも 3 つのホストが必要であり、ホストの N+1 冗長が推奨されます。 
   
   1. <bpt id="p1">**</bpt>[確認および作成]<ept id="p1">**</ept> を選択して、リクエストを送信します。


## <a name="next-steps"></a>次の手順

Azure VMware Solution をデプロイするには、サブスクリプションに<bpt id="p1">[</bpt>リソース プロバイダーを登録<ept id="p1">](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)</ept>してサービスを有効にしておく必要があります。   
