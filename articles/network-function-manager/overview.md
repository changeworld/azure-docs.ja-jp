---
title: Azure Network Function Manager について
description: フル マネージドのクラウドネイティブ オーケストレーション サービスである Azure Network Function Manager について説明します。このサービスを使用すると、Azure portal を使用して一貫したハイブリッド体験を実現するために、GPU を搭載した Azure Stack Edge Pro 上にネットワーク機能をデプロイ、プロビジョニングすることができます。
author: prmitt
ms.service: network-function-manager
ms.topic: overview
ms.date: 06/16/2021
ms.author: prmitt
ms.custom: references_regions
ms.openlocfilehash: c64dd8ca25ee4b39533022e83e572442cc7529c5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263346"
---
# <a name="what-is-azure-network-function-manager-preview"></a>Azure Network Function Manager とは (プレビュー)


Azure Network Function Manager は、モバイル パケット コア、SD-WAN エッジ、VPN サービスなどのネットワーク機能をオンプレミスの環境で実行されている [Azure Stack Edge デバイス](https://azure.microsoft.com/products/azure-stack/edge/) にデプロイするための [Azure Marketplace](https://azure.microsoft.com/marketplace/) エクスペリエンスを提供します。 プライベート モバイル ネットワーク サービスや SD-WAN ソリューションを、Azure 管理ポータルから直接エッジ デバイスに迅速にデプロイできるようになりました。 Network Function Manager を利用することで、拡大を続けている[パートナー](#partners)のエコシステムが提供するネットワーク機能を利用できます。 このプレビューは、[GPU を搭載した Azure Stack Edge Pro](../databox-online/azure-stack-edge-gpu-overview.md) でサポートされています。 

## <a name="preview-features"></a><a name="preview"></a>プレビュー機能

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

## <a name="prerequisites"></a><a name="prereq"></a>前提条件

### <a name="azure-stack-edge-pro-with-gpu-installed-and-activated"></a><a name="edge-pro"></a>GPU がインストールおよびアクティブ化された Azure Stack Edge Pro

Azure Network Function Manager サービスは Azure Stack Edge Pro デバイスで有効になっています。 ネットワーク機能をデプロイする前に、Azure Stack Edge Pro がインストールされ、アクティブ化されていることを確認します。 Azure Stack Edge リソースは、Network Function Manager のリソースでサポートされているリージョンにデプロイする必要があります。 詳細については、[[利用可能なリージョン]](#regions) をご覧ください。 Azure Stack Edge Pro の[クイックスタート](../databox-online/azure-stack-edge-gpu-quickstart.md)と[チュートリアル](../databox-online/azure-stack-edge-gpu-deploy-checklist.md)のすべての手順を実行してください。

また、Azure 管理ポータルの Azure Stack Edge リソースのプロパティ セクションにあるデバイスの **[状態]** が **オンライン** であることも確認するようにしてください。

:::image type="content" source="./media/overview/properties.png" alt-text="プロパティのスクリーンショット。" lightbox="./media/overview/properties.png":::

### <a name="partner-prerequisites"></a><a name="partner-prereq"></a>パートナーの前提条件 

お客様は、1 つまたは複数の Network Function Manager [パートナー](#partners)から選んで、ネットワーク機能を Azure Stack Edge デバイスにデプロイすることができます。 各パートナーには、ネットワーク機能を Azure Stack Edge デバイスにデプロイするためのネットワーク要件があります。 次の構成タスクを完了するには、ネットワーク機能パートナーの製品ドキュメントを参照してください。

* [異なるポートでネットワークを構成する](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。
* [Azure Stack Edge デバイスでコンピューティング ネットワークを有効にする](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)。 


### <a name="azure-account"></a><a name="account"></a>Azure アカウント

Azure Network Function Manager サービスは、Network Function Manager **デバイス** と Network Function Manager の **ネットワーク機能** リソースで構成されています。 デバイスとネットワークの機能のリソースは、Azure サブスクリプションに含まれています。 Azure Stack Edge Pro デバイスと Network Function Manager のリソースをアクティブ化するために使用する Azure サブスクリプション ID は同じである必要があります。 

[Azure Function Manager プレビュー フォーム](https://go.microsoft.com/fwlink/?linkid=2163583)に記入して、Network Function Manager プレビュー用の Azure サブスクリプション ID をオンボードしてください。 プレビューの場合、Azure Network Function Manager パートナーが Azure Marketplace からネットワーク機能をデプロイするには、同じ Azure サブスクリプション ID を有効にする必要があります。 両方の場所で、Azure サブスクリプション ID がオンボードされていることを確認します。 

## <a name="resource-provider-registration-and-permissions"></a><a name="permissions"></a>リソース プロバイダーの登録とアクセス許可

Azure Network Function Manager のリソースは、Microsoft.HybridNetwork リソース プロバイダー内にあります。 Azure サブスクリプション ID を Network Function Manager サービスのプレビュー用にオンボードしたら、サブスクリプション ID を Microsoft.HybridNetwork リソース プロバイダーに登録します。 登録方法の詳細については、[[Azure リソース プロバイダーと種類]](../azure-resource-manager/management/resource-providers-and-types.md) を参照してください。

Network Function Manager デバイス リソースの作成に使用するアカウントは、次の表に示す必要なアクションが割り当てられたカスタム ロールに割り当てる必要があります。 詳細については、[カスタム ロール](../role-based-access-control/custom-roles.md)に関する記事を参照してください。

| 名前 | アクション|
|---|---|
| Microsoft.DataBoxEdge/dataBoxEdgeDevices/read|ネットワーク機能がデプロイされる Azure Stack Edge リソースを読み取るために必要です。 |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action |Azure Stack Edge リソースのプロパティ セクションを読み取るために必要です。 |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write |Azure Stack Edge リソース上に Network Function Manager デバイス リソースを作成するために必要です。|
| Microsoft.HybridNetwork/devices/* | Network Function Manager デバイス リソースを作成、更新、削除するために必要です。 |

Azure Managed Applications リソースの作成に使用するアカウントは、次の表に示す必要なアクションが割り当てられた[カスタム ロール](../role-based-access-control/custom-roles.md)に割り当てる必要があります。 

|名前 |操作 |
|---|---|
|[Managed Application Contributor Role](../role-based-access-control/built-in-roles.md#managed-application-contributor-role)|マネージド アプリ リソースを作成するために必要です。|

## <a name="managed-identity"></a><a name="managed-identity"></a>マネージド ID 

Network Function Manager を使用して Azure マネージド アプリケーションを提供するネットワーク機能パートナーは、既存の Network Function Manager デバイス リソースにアタッチされたマネージド アプリケーションをデプロイすることができます。 パートナー マネージド アプリケーションを Azure portal にデプロイする際には、Network Function Manager デバイス リソースへのアクセス許可を持つ Azure ユーザー割り当てマネージド ID リソースを指定する必要があります。 ユーザー割り当てマネージド ID は、マネージド アプリケーション リソース プロバイダーとネットワーク機能のパブリッシャーに、管理対象リソース グループの外にデプロイされている Network Function Manager デバイス リソースに対する適切な許可を与えます。 詳細については、[Azure portal でユーザー割り当てマネージド ID を管理する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)方法に関する記事を参照してください。

ネットワーク機能をデプロイするためのユーザー割り当てマネージド ID を作成するには、次の手順に従います。

1. ユーザー割り当てマネージド ID を作成し、Microsoft.HybridNetwork/devices/join/action のアクセス許可を持つカスタム ロールに割り当てます。 詳細については、[Azure portal でユーザー割り当てマネージド ID を管理する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)方法に関する記事を参照してください。

1. Azure portal でパートナーのマネージド アプリケーションを作成するときに、このマネージド ID を指定します。 詳細については、[[Azure portal を使用してリソースにマネージド ID アクセスを割り当てる]](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md) を参照してください。


## <a name="port-requirements-and-firewall-rules"></a><a name="port-firewall"></a>ポートの要件とファイアウォール規則

Azure Stack Edge 上で動作する Network Function Manager (NFM) サービスは、ネットワーク機能をデプロイするための管理トラフィックとして NFM クラウド サービスへのアウトバウンド接続が必要です。 NFM は、Azure Stack Edge サービスと完全に統合されています。 [Azure Stack Edge](../databox-online/azure-stack-edge-gpu-system-requirements.md#networking-port-requirements) デバイスのネットワーク ポートの要件とファイアウォール規則を確認してください。  

ネットワーク機能パートナーでは、パートナー管理ポータルへのトラフィックを管理するためのファイアウォールとポートの構成規則の要件が異なります。 具体的な要件については、担当のネットワーク機能パートナーに確認してください。  

## <a name="region-availability"></a><a name="regions"></a>利用可能なリージョン

Azure Stack Edge リソース、Azure Network Function Manager デバイス、およびネットワーク機能のための Azure マネージド アプリケーションは、同じ Azure リージョン内にある必要があります。 Azure Stack Edge Pro GPU の物理デバイスについては、同じリージョンにある必要はありません。 

* **リソースの可用性 -** プレビューの場合、Network Function Manager リソースは次のリージョンで使用できます。

   [!INCLUDE [Preview- available regions](../../includes/network-function-manager-regions-include.md)]

* **デバイスの可用性 -** Azure Stack Edge Pro GPU デバイスが使用可能なすべての国またはリージョンの一覧については、[Azure Stack Edge Pro GPU の価格](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro)に関するページにアクセスしてください。 **[Azure Stack Edge Pro]** タブの  **[可用性]**   セクションに記載されています。

現在のリリースでは、Network Function Manager はリージョン サービスです。 リージョン全体で障害が発生した場合、Network Function Manager リソースの管理操作には影響がありますが、Azure Stack Edge デバイス上で実行されているネットワーク機能は、このリージョン全体で発生した障害の影響を受けません。 

## <a name="partner-solutions"></a><a name="partners"></a>パートナー ソリューション

プライベート モバイル ネットワーク、SD-WAN、および VPN ソリューション用の Marketplace マネージド アプリケーションを提供して拡大を続けているパートナーのエコシステムについては、Network Function Manager の[パートナー ページ](partners.md)をご覧ください。

## <a name="faq"></a><a name="faq"></a>FAQ

FAQ については、[Network Function Manager に関する FAQ](faq.md) を参照してください。

## <a name="next-steps"></a>次のステップ

[デバイス リソースを作成する](create-device.md)。
