---
title: Azure Network Function Manager について
description: フル マネージドのクラウドネイティブ オーケストレーション サービスである Azure Network Function Manager について説明します。このサービスを使用すると、Azure portal を使用して一貫したハイブリッド体験を実現するために、GPU を搭載した Azure Stack Edge Pro 上にネットワーク機能をデプロイ、プロビジョニングすることができます。
author: prmitt
ms.service: network-function-manager
ms.topic: overview
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 55ea5ebe1dbeeb0d3e1d17c2085c19d471396b21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024109"
---
# <a name="what-is-azure-network-function-manager"></a>Azure Network Function Manager とは

Azure Network Function Manager は、モバイル パケット コア、SD-WAN エッジ、VPN サービスなどのネットワーク機能をオンプレミスの環境で実行されている [Azure Stack Edge デバイス](https://azure.microsoft.com/products/azure-stack/edge/) にデプロイするための [Azure Marketplace](https://azure.microsoft.com/marketplace/) エクスペリエンスを提供します。 プライベート モバイル ネットワーク サービスや SD-WAN ソリューションを、Azure 管理ポータルから直接エッジ デバイスに迅速にデプロイできるようになりました。 Network Function Manager を利用することで、拡大を続けている[パートナー](#partners)のエコシステムが提供するネットワーク機能を利用できます。 Network Function Manager は、[GPU を搭載した Azure Stack Edge Pro](../databox-online/azure-stack-edge-gpu-overview.md) でサポートされています。

## <a name="features"></a><a name="features"></a> の機能

* **一貫した管理エクスペリエンス –** Network Function Manager は、エンタープライズ エッジにデプロイされたさまざまなパートナーが提供する、ネットワーク機能の一貫した Azure 管理エクスペリエンスを提供します。 これにより、ガバナンスと管理を簡素化することができます。 宣言型テンプレートを使用することで、使い慣れた Azure ツールや SDK を使用してネットワーク機能のデプロイを自動化できます。 また、Azure ロールベースのアクセス制御 [Azure RBAC](../role-based-access-control/overview.md) を適用して、Azure Stack Edge デバイスにネットワーク機能をグローバルにデプロイすることができます。

* **5G ネットワーク機能のための Azure Marketplace エクスペリエンス –** 任意の LTE および 5G モバイル パケット コア ネットワーク機能を Azure Marketplace から直接選択して、Azure Stack Edge デバイスへのプライベート モバイル ネットワーク ソリューションのデプロイを加速させます。

* **SD-WAN と VPN ソリューションのシームレスなクラウドツーエッジ エクスペリエンス –** Network Function Manager を使用することで、パブリック クラウドで慣れ親しんでいる Marketplace ネットワーク機能の Azure 管理エクスペリエンスをエッジ デバイスに拡張します。 これにより、Azure パブリック クラウドや Azure Stack Edge デバイスに展開した任意の SD-WAN や VPN パートナーのネットワーク機能で、一貫したデプロイ エクスペリエンスを利用することができます。

* **エンタープライズ エッジのネットワーク機能における Azure Managed Applications エクスペリエンス –** Network Function Manager を使用すると、モバイル パケット コアなどの特殊なネットワーク機能を Azure Stack Edge デバイスに簡単にデプロイできます。 Microsoft では、パートナーから承認されたネットワーク機能イメージのライフサイクル管理の再検証を行いました。 ユーザーは、ネットワーク機能のリソースが一貫した状態でフリート全体にデプロイされていることを確認できます。 詳細については、[Azure Managed Applications](../azure-resource-manager/managed-applications/overview.md) に関するページを参照してください。

* **ネットワークの高速化とネットワーク機能への動的または静的 IP 割り当ての選択 –** Network Function Manager と [Azure Stack Edge Pro](../databox-online/azure-stack-edge-gpu-overview.md) では、ネットワーク機能ワークロードのネットワーク パフォーマンスが向上しています。 モバイル パケット コアなどの特殊なネットワーク機能を Azure Stack Edge デバイス上にデプロイし、アクセス ポイント ネットワークやユーザー プレーン ネットワークのデータ パス パフォーマンスを高速化できるようになりました。 また、ネットワーク機能をデプロイする際に、異なる仮想インターフェイスに対して、静的または動的な IP 割り当てを選択することもできます。 これらのネットワーク機能のサポートについては、ネットワーク機能パートナーに確認してください。  

## <a name="azure-managed-applications-for-network-functions"></a><a name="managed"></a>ネットワーク機能の Azure Managed Applications

Network Fuction Manager を使用してデプロイできるネットワーク機能は、Azure Stack Edge デバイス上で実行するように特別に設計されています。 ネットワーク機能のオファーは、[Azure マネージド アプリケーション](../azure-resource-manager/managed-applications/overview.md)として Azure Marketplace に公開されています。 お客様は、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) から直接、または Azure portal 経由で Network Function Manager デバイス リソースからオファーをデプロイできます。 

:::image type="content" source="./media/overview/managed-app-workflow.png" alt-text="マネージド アプリケーション ワークフローの図。":::

Network Function Manager を使用してデプロイできるすべてのネットワーク機能のオファーには、Azure Marketplace で利用可能なマネージド アプリケーションがあります。 マネージド アプリケーションを使用すると、パートナーは次のことを行うことができます。

* Azure portal を使用して、ネットワーク機能のカスタム デプロイ エクスペリエンスを構築。 

* Azure Stack Edge デバイスに直接ネットワーク機能を作成するための専用の Resource Manager テンプレートを提供。

* ソフトウェア ライセンスのコストを直接、または Azure Marketplace を通じて請求。 

* カスタム プロパティとリソース メーターを公開。

ネットワーク機能パートナーは、アプライアンスのデプロイ、構成のライセンス、および管理のニーズに応じて、異なるリソースを作成する場合があります。 すべての Azure Managed Applications の場合と同様に、お客様が Azure Stack Edge デバイスでネットワーク機能を作成すると、サブスクリプションに以下の 2 つのリソース グループが作成されます。

* **カスタマー リソース グループ –** このリソース グループには、マネージド アプリケーションのアプリケーション プレースホルダーが含まれます。 パートナーはこれを使用して、ここで選択したカスタマー プロパティを公開できます。 

* **管理対象リソース グループ –** このリソース グループ内のリソースは、マネージド アプリケーションの公開元によって制御されるため、ユーザーが構成したり変更したりすることはできません。 このリソース グループには、Network Function Manager の **ネットワーク機能** のリソースが含まれます。

:::image type="content" source="./media/overview/managed-app-resource-groups.png" alt-text="マネージド アプリケーション リソース グループの図。":::

## <a name="network-function-configuration-process"></a><a name="configuration"></a>ネットワーク機能の構成プロセス 

Network Function Manager を使用して Azure マネージド アプリケーションを提供するネットワーク機能パートナーは、デプロイ プロセスの一部としてネットワーク機能を自動的に構成するエクスペリエンスを提供します。 マネージド アプリケーションのデプロイが成功し、ネットワーク機能インスタンスが Azure Stack Edge にプロビジョニングされたら、その後に追加するネットワーク機能に必要な設定は、ネットワーク機能パートナーの管理ポータルを介して行う必要があります。 Azure Stack Edge デバイスにデプロイされているネットワーク機能のエンドツーエンドの管理エクスペリエンスについては、担当のネットワーク機能パートナーにお問い合わせください。

## <a name="region-availability"></a><a name="regions"></a>利用可能なリージョン

Azure Stack Edge リソース、Azure Network Function Manager デバイス、およびネットワーク機能のための Azure マネージド アプリケーションは、同じ Azure リージョン内にある必要があります。 Azure Stack Edge Pro GPU の物理デバイスについては、同じリージョンにある必要はありません。

* **リソースの使用可能性 -** Network Function Manager リソースは次のリージョンで使用できます。

   [!INCLUDE [Available regions](../../includes/network-function-manager-regions-include.md)]

* **デバイスの可用性 -** Azure Stack Edge Pro GPU デバイスが使用可能なすべての国またはリージョンの一覧については、[Azure Stack Edge Pro GPU の価格](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro)に関するページにアクセスしてください。 **[Azure Stack Edge Pro]** タブの  **[可用性]**   セクションに記載されています。

現在のリリースでは、Network Function Manager はリージョン サービスです。 リージョン全体で障害が発生した場合、Network Function Manager リソースの管理操作には影響がありますが、Azure Stack Edge デバイス上で実行されているネットワーク機能は、このリージョン全体で発生した障害の影響を受けません。

## <a name="partner-solutions"></a><a name="partners"></a>パートナー ソリューション

プライベート モバイル ネットワーク、SD-WAN、および VPN ソリューション用の Marketplace マネージド アプリケーションを提供して拡大を続けているパートナーのエコシステムについては、Network Function Manager の[パートナー ページ](partners.md)をご覧ください。

## <a name="faq"></a><a name="faq"></a>FAQ

FAQ については、[Network Function Manager に関する FAQ](faq.md) を参照してください。

## <a name="next-steps"></a>次のステップ

[デバイス リソースを作成する](create-device.md)。
