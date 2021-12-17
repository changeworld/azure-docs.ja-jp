---
title: Azure Arc を使用してハイブリッド インフラストラクチャを大規模に管理する
description: Azure Lighthouse は、Azure の外にある顧客のマシンと Kubernetes クラスターを効果的に管理するのに役立ちます。
ms.date: 09/07/2021
ms.topic: how-to
ms.openlocfilehash: 5acb9cae08795a9c87db0d6353f4870e92849f24
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293063"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Azure Arc を使用してハイブリッド インフラストラクチャを大規模に管理する

[Azure Lighthouse](../overview.md) によって、サービス プロバイダーは Azure Arc を使用して顧客のハイブリッド環境を管理し、管理しているすべての Azure Active Directory (Azure AD) テナントを可視化できます。

[Azure Arc](../../azure-arc/overview.md) を使用すると、オンプレミス、エッジ、マルチクラウドにまたがる複雑な分散環境を簡素化し、任意の場所に Azure サービスをデプロイし、Azure の管理を任意のインフラストラクチャに拡張することができます。

[Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)を使用すると、顧客は、ネイティブの Azure 仮想マシンを管理する場合と同じ方法で、企業ネットワーク上の Azure の外部でホストされている Windows および Linux マシンを管理できます。 ハイブリッド マシンを Azure にリンクすると、接続され、Azure でリソースとして扱われるようになります。 サービス プロバイダーでは、このような非 Azure マシンを顧客の Azure リソースと共に管理できます。

[Azure Arc 対応 Kubernetes](../../azure-arc/kubernetes/overview.md) を使用すると、顧客は、Azure の内部または外部で Kubernetes クラスターを接続して構成できるようになります。 Kubernetes クラスターを Azure Arc に接続すると、Azure portal に表示され、Azure Resource Manager ID とマネージド ID が付与されます。 クラスターは、標準の Azure サブスクリプションに接続され、リソース グループ内に存在し、他の Azure リソースと同様にタグを受け取ることができます。

このトピックでは、管理する顧客テナント全体でスケーラブルな方法で Azure Arc 対応サーバーと Azure Arc 対応 Kubernetes を使用する方法の概要について説明します。

> [!TIP]
> このトピックではサービスのプロバイダーと顧客について触れますが、このガイドラインは、[Azure Lighthouse を使用して複数のテナントを管理する企業](../concepts/enterprise.md)にも当てはまります。

## <a name="manage-hybrid-servers-at-scale-with-azure-arcenabled-servers"></a>Azure Arc 対応サーバーでハイブリッド サーバーを大規模に管理する

サービス プロバイダーの場合、顧客が [Azure Connected Machine エージェント](../../azure-arc/servers/agent-overview.md)を使用してサブスクリプションに接続する、Azure の外部のオンプレミスの Windows Server または Linux マシンを管理できます。 Azure portal で委任されたサブスクリプションのリソースを表示すると、これらの接続されたマシンに **Azure Arc** というラベルが付いていることがわかります。

これらの接続されたマシンは、顧客の Azure リソースを管理するのと同じ方法で、Azure Policy やタグ付けなどの Azure の構成概念を使用して管理できます。 また、顧客のテナント全体について作業し、接続されているすべてのハイブリッド マシンをまとめて管理することもできます。

たとえば、[顧客のハイブリッド マシン全体に同じポリシー セットを適用することができます](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md)。 また、Microsoft Defender for Cloud を使用して、すべての顧客のハイブリッド環境全体のコンプライアンスを監視したり、[Azure Monitor を使用してハイブリッド マシンから直接データを収集し](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md)、Log Analytics ワークスペースに取り込んだりすることができます。 [仮想マシン拡張機能](../../azure-arc/servers/manage-vm-extensions.md)は、Azure 以外の Windows および Linux VM にデプロイできるため、顧客のハイブリッド マシンの管理が簡素化されます。

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes を使用してハイブリッド Kubernetes クラスターを大規模に管理する

[Azure Arc を使用して顧客のサブスクリプションに接続されている](../../azure-arc/kubernetes/quickstart-connect-cluster.md) Kubernetes クラスターは、Azure で実行されている場合と同様に管理できます。

顧客が [Kubernetes クラスターを Azure Arc にオンボードするためにサービス プリンシパル アカウント](../../azure-arc/kubernetes/create-onboarding-service-principal.md)を作成している場合は、このアカウントにアクセスしてクラスターをオンボードして管理できます。 これを行うには、サービス プリンシパル アカウントを含むサブスクリプションが [Azure Lighthouse にオンボードされた](onboard-customer.md)ときに、管理テナントのユーザーに Azure 組み込みロールの "Kubernetes クラスター - Azure Arc のオンボード" が付与されている必要があります。

接続されたクラスターの GitOps を使用して、[構成](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md)と [Helm Chart](../../azure-arc/kubernetes/use-gitops-with-helm.md) をデプロイできます。

接続されたクラスターを Azure Monitor を使用して監視し、[Azure Policy を使用してクラスター構成を大規模に適用することもできます](../../azure-arc/kubernetes/use-azure-policy.md)。

## <a name="next-steps"></a>次の手順

- [Azure Arc GitHub リポジトリ](https://github.com/microsoft/azure_arc)ですぐに使える項目とサンプルを確認します。
- [Azure Arc 対応サーバーのサポートされるシナリオ](../../azure-arc/servers/overview.md#supported-cloud-operations)を確認します。
- [Azure Arc でサポートされている Kubernetes ディストリビューション](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions)を確認します。
