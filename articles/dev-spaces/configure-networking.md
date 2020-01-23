---
title: さまざまなネットワーク トポロジで Azure Dev Spaces 用のネットワークを構成する
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: Azure Kubernetes Services で Azure Dev Spaces を実行するためのネットワーク要件について説明します。
keywords: Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、コンテナー、CNI、kubenet、SDN、ネットワーク
ms.openlocfilehash: 9e32e3b65451dceefaeeaf7faed7c8337797e0b8
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044982"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>さまざまなネットワーク トポロジで Azure Dev Spaces 用のネットワークを構成する

Azure Dev Spaces は、既定のネットワーク構成を使用して Azure Kubernetes Service (AKS) クラスター上で実行されます。 AKS クラスターのネットワーク構成を変更する (クラスターをファイアウォールの背後に配置する、ネットワーク セキュリティ グループを使用する、ネットワーク ポリシーを使用するなど) 場合は、Azure Dev Spaces を実行するための追加の考慮事項を組み込む必要があります。

![Virtual Network の構成](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>仮想ネットワークまたはサブネット構成

AKS クラスターには、その AKS クラスターへのイングレスまたはエグレス トラフィックを制限するための別の仮想ネットワークまたはサブネット構成が含まれている可能性があります。 たとえば、クラスターがファイアウォール (Azure Firewall など) の背後に存在したり、ネットワーク トラフィックを制限するためのネットワーク セキュリティ グループまたはカスタム ロールを使用したりすることがあります。

Azure Dev Spaces には、*イングレスおよびエグレス* ネットワーク トラフィックや、*イングレスのみ*のトラフィックに対する特定の要件があります。 AKS クラスターへのトラフィックを制限する仮想ネットワークまたはサブネット構成を含む AKS クラスター上で Azure Dev Spaces を使用している場合、Azure Dev Spaces が正しく機能するには、次のイングレスのみのトラフィックとイングレスおよびエグレス トラフィックの要件に従う必要があります。

### <a name="ingress-and-egress-network-traffic-requirements"></a>イングレスおよびエグレス ネットワーク トラフィックの要件

Azure Dev Spaces では、次の FQDN にはイングレスおよびエグレス トラフィックが必要です。

| FQDN                       | Port       | 用途      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Azure Dev Spaces の Docker イメージをプルするため |
| gcr.io                     | HTTPS: 443 | Azure Dev Spaces の Helm イメージをプルするため |
| storage.googleapis.com     | HTTPS: 443 | Azure Dev Spaces の Helm イメージをプルするため |
| azds-*.azds.io             | HTTPS: 443 | Azure Dev Spaces コントローラーのために Azure Dev Spaces のバックエンド サービスと通信するため。 正確な FQDN は、`USERPROFILE\.azds\settings.json` 内の *dataplaneFqdn* で見つけることができます。 |

上のすべての FQDN との間のネットワーク トラフィックを許可するようにファイアウォールまたはセキュリティ構成を更新してください。 たとえば、ネットワークをセキュリティで保護するためのファイアウォールを使用している場合、これらのドメインとの間のトラフィックを許可するには、そのファイアウォールのアプリケーション ルールに上の FQDN を追加する必要があります。

### <a name="ingress-only-network-traffic-requirements"></a>イングレスのみのネットワーク トラフィックの要件

Azure Dev Spaces は、Kubernetes 名前空間レベルのルーティングや、独自の FQDN を使用したサービスへのパブリック アクセスを提供します。 これらの両方の機能が動作するようにするには、クラスター上の Azure Dev Spaces イングレス コントローラーの外部 IP アドレスへのパブリック イングレスを許可するようにファイアウォールまたはネットワーク構成を更新してください。 あるいは、[内部ロード バランサー][aks-internal-lb]を作成し、ファイアウォールのパブリック IP をその内部ロード バランサーの IP に変換する NAT ルールをファイアウォールに追加することができます。 また、[traefik][traefik-ingress] または [NGINX][nginx-ingress] を使用して、カスタム イングレス コントローラーを作成することもできます。

## <a name="aks-cluster-network-requirements"></a>AKS クラスター ネットワークの要件

AKS では、[ネットワーク ポリシー][aks-network-policies]を使用して、クラスター上のポッド間のイングレスおよびエグレス トラフィックや、ポッドからのエグレス トラフィックを制御できます。 Azure Dev Spaces には、*イングレスおよびエグレス* ネットワーク トラフィックや、*イングレスのみ*のトラフィックに対する特定の要件があります。 AKS ネットワーク ポリシーを含む AKS クラスター上で Azure Dev Spaces を使用している場合、Azure Dev Spaces が正しく機能するには、次のイングレスのみのトラフィックとイングレスおよびエグレス トラフィックの要件に従う必要があります。

### <a name="ingress-and-egress-network-traffic-requirements"></a>イングレスおよびエグレス ネットワーク トラフィックの要件

Azure Dev Spaces では、デバッグのために、クラスター上の開発スペース内のポッドと直接通信できます。 この機能が動作するようにするには、Azure Dev Spaces インフラストラクチャの IP アドレス (これは[リージョンによって異なります][dev-spaces-ip-auth-range-regions]) へのイングレスおよびエグレス通信を許可するネットワーク ポリシーを追加します。

### <a name="ingress-only-network-traffic-requirements"></a>イングレスのみのネットワーク トラフィックの要件

Azure Dev Spaces は、名前空間にまたがるポッド間のルーティングを提供します。 たとえば、Azure Dev Spaces が有効になっている名前空間には、親と子の名前空間にまたがるポッド間でネットワーク トラフィックをルーティングできる親/子の関係を割り当てることができます。 この機能が動作するようにするには、ネットワーク トラフィックがルーティングされる名前空間 (親/子の名前空間など) の間のトラフィックを許可するネットワーク ポリシーを追加します。 また、イングレス コントローラーが *azds* 名前空間にデプロイされている場合、そのイングレス コントローラーは、Azure Dev Spaces によって別の名前空間内にインストルメント化されたポッドと通信することも必要です。 このイングレス コントローラーが正しく機能するには、*azds* 名前空間から、インストルメント化されたポッドが実行されている名前空間へのネットワーク トラフィックを許可する必要があります。

## <a name="using-azure-cni"></a>Azure CNI の使用

既定では、AKS クラスターは、Azure Dev Spaces で動作する [kubenet][aks-kubenet] をネットワークに使用するように構成されます。 また、[Azure Container Networking Interface (CNI)][aks-cni] を使用するように AKS クラスターを構成することもできます。 AKS クラスター上で Azure Dev Spaces と Azure CNI を使用するには、仮想ネットワークとサブネットのアドレス空間に、Azure Dev Spaces によってデプロイされたポッドのための最大 10 個のプライベート IP アドレスを許可します。 プライベート IP アドレスを許可する方法に関する詳細は、[AKS Azure CNI のドキュメント][aks-cni-ip-planning]に記載されています。

## <a name="using-api-server-authorized-ip-ranges"></a>API サーバーの許可された IP 範囲の使用

AKS クラスターでは、クラスターにアクセスする IP アドレスを制限する追加のセキュリティ (カスタム仮想ネットワークの使用や、[許可された IP 範囲を使用する API サーバーへのアクセスのセキュリティ保護][aks-ip-auth-ranges]など) を構成できます。 クラスターの[作成][aks-ip-auth-range-create]中にこの追加のセキュリティを使用する場合に Azure Dev Spaces を使用するには、[リージョンに基づいて追加の範囲を許可する][dev-spaces-ip-auth-range-regions]必要があります。 また、既存のクラスターを[更新][aks-ip-auth-range-update]して、これらの追加の範囲を許可することもできます。 デバッグのために AKS クラスターに接続するすべての開発用コンピューターの IP アドレスが API サーバーに接続できるようにすることも必要です。

## <a name="using-aks-private-clusters"></a>AKS プライベート クラスターの使用

現時点では、[AKS プライベート クラスター][aks-private-clusters]で Azure Dev Spaces はサポートされていません。

## <a name="client-requirements"></a>クライアントの要件

Azure Dev Spaces は、デバッグのための AKS クラスターとの通信にクライアント側ツール (Azure Dev Spaces CLI 拡張機能、Visual Studio Code 拡張機能、Visual Studio 拡張機能など) を使用します。 Azure Dev Spaces のクライアント側ツールを使用するには、開発用コンピューターから *azds-\*.azds.io* ドメインへのトラフィックを許可します。 正確な FQDN については、`USERPROFILE\.azds\settings.json` 内の *dataplaneFqdn* を参照してください。 [API サーバーの許可された IP 範囲][auth-range-section]を使用している場合は、デバッグのために AKS クラスターに接続するすべての開発用コンピューターの IP アドレスが API サーバーに接続できるようにすることも必要です。

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces を使用して複数のコンテナーにまたがるより複雑なアプリケーションを開発する方法と、別の空間で別のバージョンまたは分岐を使用して作業することによって共同開発を簡略化する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces でのチーム開発][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md