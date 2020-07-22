---
title: 概念 - ハブ アンド スポークのアーキテクチャで Azure VMware Solution (AVS) のデプロイを統合する
description: Azure の既存または新規のハブ アンド スポークのアーキテクチャで Azure VMware Solution (AVS) のデプロイを統合するための推奨事項について説明します。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 82937e04fc0a5101c353702b92b6b068d027d7ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85374975"
---
# <a name="integrate-azure-vmware-solution-avs-in-a-hub-and-spoke-architecture"></a>ハブ アンド スポークのアーキテクチャで Azure VMware Solution (AVS) を統合する

この記事では、Azure の既存または新規の[ハブ アンド スポークのアーキテクチャ](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/shared-services)で Azure VMware Solution (AVS) のデプロイを統合するための推奨事項について説明します。 

ハブ アンド スポークのシナリオでは、次のワークロードを含むハイブリッド クラウド環境が想定されています。

* IaaS サービスまたは PaaS サービスを使用したネイティブ Azure
* AVS 
* オンプレミスの vSphere

## <a name="architecture"></a>アーキテクチャ

"*ハブ*" は、オンプレミスおよび AVS のプライベート クラウドへの接続の中心点として機能する Azure Virtual Network です。 "*スポーク*" は、仮想ネットワーク間通信を有効にするためにハブとピアリングされた仮想ネットワークです。

オンプレミスのデータセンター、AVS プライベート クラウド、およびハブの間のトラフィックは、ExpressRoute 接続を経由します。 スポーク仮想ネットワークには、通常、IaaS ベースのワークロードが含まれていますが、Virtual Network に直接統合されている PaaS サービス ([App Service Environment](../app-service/environment/intro.md) など) や、[Azure Private Link](https://docs.microsoft.com/azure/private-link/) が有効になっているその他の PaaS サービスを含めることができます。 

この図は、ExpressRoute 経由でオンプレミスおよび AVS に接続されている Azure のハブ アンド スポークのデプロイの例を示しています。

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="AVS のハブ アンド スポークの統合デプロイ":::




このアーキテクチャには、次の主要なコンポーネントがあります。

-   **オンプレミス サイト:** Express Route 接続を介して Azure に接続されている顧客のオンプレミスのデータセンター。

-   **AVS プライベート クラウド:** 1 つ以上の vSphere クラスターによって形成された AVS SDDC。それぞれに最大 16 個のノードがあります。

-   **ExpressRoute ゲートウェイ:** AVS プライベート クラウド、オンプレミスのネットワーク、ハブ仮想ネットワーク上の共有サービス、およびスポーク仮想ネットワークで実行されているワークロードの間の通信を有効にします。

    > [!NOTE]
    > **S2S VPN に関する考慮事項:** AVS 運用環境のデプロイでは、HCX のネットワーク要件により、Azure S2S はサポートされていません。 ただし、HCX を必要としない PoC または非運用環境のデプロイでは、これを使用できます。

-   **ハブ仮想ネットワーク:** オンプレミスのネットワークおよび AVS プライベート クラウドへの中心となる接続ポイントとして機能します。

-   **スポーク仮想ネットワーク**

    -   **IaaS スポーク:** IaaS スポークでは、VM 可用性セットと仮想マシン スケール セットを含む Azure IaaS ベースのワークロード、および対応するネットワーク コンポーネントがホストされます。

    -   **PaaS スポーク:** PaaS スポークでは、[プライベート エンドポイント](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)と[プライベート リンク](https://docs.microsoft.com/azure/private-link/private-link-overview)により、プライベート アドレス指定を使用して Azure PaaS サービスがホストされます。

-   **Azure Firewall:** スポーク、オンプレミス、AVS の間でトラフィックをセグメント化するための中心的な要素として機能します。

-   **Application Gateway**Azure IaaS/PaaS または AVS 仮想マシン (VM) 上で実行される Web アプリを公開し、保護します。 これは、API Management などの他のサービスと統合されています。

## <a name="network-and-security-considerations"></a>ネットワークとセキュリティに関する考慮事項

ExpressRoute 接続を使用すると、オンプレミス、AVS、Azure ネットワーク ファブリックの間でトラフィックをフローさせることができます。 AVS では、[ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) を使用してこの接続が実装されます。

オンプレミスの接続では、ExpressRoute Global Reach も使用できますが、必須ではありません。

* **オンプレミスから AVS へのトラフィック フロー**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="オンプレミスから AVS へのトラフィック フロー":::


* **AVS からハブ VNET へのトラフィック フロー**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="AVS からハブへの仮想ネットワーク トラフィック フロー":::


AVS ネットワークと相互接続の概念の詳細については、[AVS 製品ドキュメント](https://docs.microsoft.com/azure/azure-vmware/concepts-networking)を参照してください。

### <a name="traffic-segmentation"></a>トラフィックのセグメント化

[Azure Firewall](https://docs.microsoft.com/azure/firewall/) はハブ アンド スポークのトポロジの中心的な要素であり、ハブ仮想ネットワークにデプロイされています。 Azure Firewall、またはその他の Azure でサポートされているネットワーク仮想アプライアンスを使用して、トラフィック規則を確立し、さまざまなスポーク、オンプレミス、AVS のワークロード間の通信をセグメント化します。

トラフィックを Azure Firewall に送信するためのルート テーブルを作成します。  スポーク仮想ネットワークの場合は、Azure Firewall の内部インターフェイスに既定のルートを設定するルートを作成します。これにより、Virtual Network 内のワークロードが AVS アドレス空間に到達する必要がある場合に、ファイアウォールによってこのワークロードを評価し、対応するトラフィック規則を適用して許可または拒否することができます。  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="トラフィックを Azure Firewall に送信するためのルート テーブルを作成する":::


> [!IMPORTANT]
> **GatewaySubnet** 設定のアドレス プレフィックス 0.0.0.0/0 のルートはサポートされていません。

対応するルート テーブルで特定のネットワークのルートを設定します。 たとえば、Azure Firewall を介してオンプレミスから AVS プライベート クラウドにすべてのトラフィックをルーティングする、AVS 管理およびワークロードの IP プレフィックスとオンプレミスとの間のルートなどです。

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="対応するルート テーブルで特定のネットワークのルートを設定する":::

スポークおよびハブ内のネットワーク セキュリティ グループを使用して、より詳細なトラフィック ポリシーを作成する、2 番目のレベルのトラフィック セグメント。 


### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway V1 および V2 は、AVS VM 上でバックエンド プールとして実行される Web アプリでテストされています。 現在、Application Gateway は、AVS VM で実行されている Web アプリをインターネットに公開する、唯一のサポートされている方法です。 また、アプリを内部ユーザーに安全に公開することもできます。

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="ネットワーク セキュリティ グループを使用する 2 番目のレベルのトラフィック セグメント":::


### <a name="jumpbox-and-azure-bastion"></a>Jumpbox と Azure Bastion

ハブ仮想ネットワーク内の共有サービス サブネットにデプロイされている Windows 10 または Windows Server の VM である Jumpbox を使用して、AVS 環境にアクセスします。

セキュリティのベスト プラクティスとして、ハブ仮想ネットワーク内に [Microsoft Azure Bastion](https://docs.microsoft.com/azure/bastion/) サービスをデプロイすることをお勧めします。 Azure Bastion では、Azure にデプロイされている VM へのシームレスな RDP アクセスと SSH アクセスが提供されます。これらのリソースにパブリック IP アドレスをプロビジョニングする必要はありません。 Azure Bastion サービスをプロビジョニングすると、選択した VM に Azure portal からアクセスできるようになります。 接続を確立すると、新しいタブが開いて Jumpbox デスクトップが表示され、そのデスクトップから AVS プライベート クラウド管理プレーンにアクセスできるようになります。

> [!IMPORTANT]
> Jumpbox VM にパブリック IP アドレスを指定したり、パブリック インターネットに 3389/TCP ポートを公開したりしないでください。 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure Bastion ハブ仮想ネットワーク":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS の解決に関する考慮事項

Azure DNS の解決には、次の 2 つのオプションを使用できます。

-   ハブにデプロイされている Azure Active Directory (Azure AD) ドメイン コントローラーをネーム サーバーとして使用します (「[ID に関する考慮事項](#identity-considerations)」を参照)。

-   Azure DNS プライベート ゾーンをデプロイして構成します。

最善の方法は、両方を組み合わせて、AVS、オンプレミス、Azure に対して信頼性の高い名前解決を提供することです。

一般的な設計の推奨事項として、2 つ以上の Azure VM (ハブ仮想ネットワークにデプロイされており、DNS 設定で Azure DNS サーバーを使用するようにスポーク仮想ネットワークで構成されている) にデプロイされている既存の Azure DNS インフラストラクチャ (この場合は Active Directory 統合 DNS) を使用します。

Azure DNS プライベート ゾーンが仮想ネットワークにリンクされている場合も使用することができ、DNS サーバーは顧客の Azure プライベート DNS インフラストラクチャを活用して DNS 名を実行しているオンプレミスまたは AVS への条件付き転送にハイブリッド リゾルバーとして使用されます。

Azure DNS プライベート ゾーンについていくつかの考慮事項があります。

* スポーク仮想ネットワーク内にデプロイされている VM の DNS レコードのライフサイクルを自動的に管理するには、Azure DNS に対する自動登録を有効にする必要があります。
* 自動登録が有効な状態で仮想ネットワークをリンクできるプライベート DNS ゾーンの最大数は、1 つだけです。
* 自動登録が有効になっていない状態で仮想ネットワークをリンクできるプライベート DNS ゾーンの最大数は、1,000 です。

オンプレミスおよび AVS のサーバーでは、Azure プライベート DNS ゾーンに対する Azure 内のリゾルバー VM への条件付きフォワーダーを構成できます。

## <a name="identity-considerations"></a>ID に関する考慮事項

ID に関する理由により、共有サービス サブネットを使用して、ハブに少なくとも 1 つの AD ドメイン コントローラー (理想的には、ゾーン分散形式の 2 つまたは VM 可用性セット) をデプロイすることをお勧めします。 オンプレミスの AD ドメインを Azure に拡張する方法については、[Azure アーキテクチャ センター](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)に関する記事を参照してください。

さらに、vSphere 環境内で ID および DNS ソースとして機能するように、別のドメイン コントローラーを AVS 側にデプロイします。

vCenter および SSO の場合は、Azure portal の **[管理]\>[ID]\>[ID ソース]** で ID ソースを設定します。

推奨されるベスト プラクティスとして、[AD ドメインと Azure Active Directory](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/azure-ad) を統合することをお勧めします。

<!-- LINKS - external -->
[Azure Architecture Center]: https://docs.microsoft.com/azure/architecture/

[Hub & Spoke topology]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: https://docs.microsoft.com/azure/networking/

<!-- LINKS - internal -->


