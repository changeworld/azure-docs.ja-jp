---
title: Azure Dev Spaces についてよく寄せられる質問
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Azure Dev Spaces についての一般的ないくつかの質問にお答えします
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: 7439af9c5f936d309df655ca6fa301c39fa3f9ec
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117806"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Azure Dev Spaces についてよく寄せられる質問

Azure Dev Spaces についてよく寄せられる質問に回答します。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>現在はどの Azure リージョンで Azure Dev Spaces が提供されていますか。

利用可能なリージョンの完全な一覧については、[サポートされているリージョン][supported-regions]を参照してください。

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Azure Dev Spaces が含まれる AKS クラスターを別のリージョンに移行できますか。

はい。Azure Dev Spaces が含まれる AKS クラスターを、別の[サポートされているリージョン][supported-regions]に移動する場合は、他のリージョンに新しいクラスターを作成してから、Azure Dev Spaces をインストールして構成し、リソースとアプリケーションを新しいクラスターにデプロイすることをお勧めします。 AKS の移行について詳しくは、「[Azure Kubernetes Service (AKS) に移行する][aks-migration]」をご覧ください。

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>既存の Dockerfiles または Helm Ｃhart で Azure Dev Spaces を使用できますか。

はい。プロジェクトに既に Dockerfile または Helm Ｃhart が存在する場合は、これらのファイルを Azure Dev Spaces で使用できます。 `azds prep` を実行する場合は、`--chart` パラメーターを使用してチャートの場所を指定します。 Azure Dev Spaces でも *azds.yaml* ファイルと *Dockerfile.develop* ファイルが生成されますが、既存の Dockerfile や Helm Ｃｈａｒｔ が置換または変更されることはありません。 `azds up` の実行時に既存のアプリケーションですべてを正常に機能させるために、*azds.yaml* ファイルと *Dockerfile.develop* ファイルの変更が必要になる場合があります。

独自の Dockerfile または Helm Ｃhart を使用する場合、次の制限があります。
* Dockerfile を 1 つだけ使用する場合は、ランタイムだけでなく言語 SDK など、開発シナリオを有効にするために必要なすべてを含める必要があります。 Azure Dev Spaces 用の独立した Dockerfile (Dockerfile.develop など) を使用する場合は、開発シナリオを有効にするために必要なすべてのものを Dockerfile に含める必要があります。
* Helm Ｃhart では、*values.yaml* からの値として、イメージ タグの一部または全体を渡すことがサポートされている必要があります。
* イングレスで何かを変更する場合は、Azure Dev Spaces によって提供されるイングレス ソリューションを使用するように Helm Ｃhart を更新することもできます。
* [Azure Dev Spaces によって提供されるルーティング機能][dev-spaces-routing]を使用する場合は、個々のプロジェクトのすべてのサービスが単一の Kubernetes 名前空間に収まる必要があり、単純な名前 (たとえば *service-a*) でデプロイする必要があります。 標準の Helm Ｃhart では、*fullnameOverride* プロパティの値を指定することでこの名前を更新できます。

独自の Dockerfile または Helm Ｃhart を Azure Dev Spaces で動作する既存のバージョンと比較するには、[quickstart][quickstart-cli] で生成されたファイルを確認します。


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Azure Dev Spaces によって生成されたファイルを変更できますか。

はい。[プロジェクトを準備するときに Azure Dev Spaces によって生成される][dev-spaces-prep] *azds.yaml* ファイル、Dockerfile、および Helm Ｃhart を変更できます。 これらのファイルを変更すると、プロジェクトのビルド方法と実行方法が変更されます。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>パブリック IP アドレスなしで Azure Dev Spaces を使用できますか。

いいえ。パブリック IP を持たない AKS クラスター上に Azure Dev Spaces をプロビジョニングすることはできません。 パブリック IP は、[ルーティングのために Azure Dev Spaces に必要][dev-spaces-routing]です。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Azure Dev Spaces で独自のイングレスを使用できますか。

はい。Azure Dev Spaces によって作成されるイングレスと共に、独自のイングレスを構成できます。 たとえば、[traefik][ingress-traefik] または [NGINX][ingress-nginx] を使用できます。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Azure Dev Spaces で HTTPS を使用できますか。

はい。[traefik][ingress-https-traefik] または [NGINX][ingress-https-nginx] を使用して、HTTPS で独自のイングレスを構成できます。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>kubenet ではなく CNI を使用するクラスター上で Azure Dev Spaces を使用できますか。 

はい。ネットワークに CNI を使用する AKS クラスター上で、Azure Dev Spaces を使用できます。 たとえば、ネットワークに CNI を使用する[既存の Windows コンテナー][windows-containers]がある AKS クラスター上で、Azure Dev Spaces を使用できます。 Azure Dev Spaces を使用したネットワークでの CNI の使用の詳細については、[こちら](configure-networking.md#using-azure-cni)を参照してください。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Azure Dev Spaces で Windows コンテナーを使用できますか。

現時点では、Azure Dev Spaces は Linux ポッドおよびノード上でのみ実行することが想定されていますが、[既存の Windows コンテナー][windows-containers]を持つ AKS クラスター上で Azure Dev Spaces を実行できます。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>API サーバーの承認済み IP アドレス範囲が有効になっている AKS クラスターで Azure Dev Spaces を使用できますか。

はい。[API サーバーの承認済み IP アドレス範囲][aks-auth-range]が有効になっている AKS クラスターで Azure Dev Spaces を使用できます。 Azure Dev Spaces で有効な API サーバー承認済み IP アドレスの範囲の AKS クラスターを使用する方法の詳細については、[こちら](configure-networking.md#using-api-server-authorized-ip-ranges)を参照してください。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>クラスター ノードのエグレス トラフィックが制限されている AKS クラスターで Azure Dev Spaces を使用できますか?

はい。正しい FQDN が許可されていると、[クラスター ノードに対する制限されたエグレス トラフィック][aks-restrict-egress-traffic]が有効になっている AKS クラスターで Azure Dev Spaces を使用できます。 Azure Dev Spaces での「クラスターノードに対する制限されたエグレストラフィックが有効な AKS クラスター」の使用の詳細については、[こちら](configure-networking.md#ingress-and-egress-network-traffic-requirements)を参照してください。

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>RBAC が有効化された AKS クラスター上で Azure Dev Spaces を使用することはできますか。

はい。RBAC が有効化されているかに関わらず、AKS クラスター上で Azure Dev Spaces を使用することができます。

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Visual Studio でプロジェクトのイングレスを有効にすると、どうなりますか。

Visual Studio を使用してプロジェクトを準備する場合は、サービスのイングレスを有効にすることを選択できます。 イングレスを有効にすると、AKS クラスター上で実行されるときにサービスにアクセスするためのパブリック エンドポイントが作成されます。これは、オプションです。 イングレスを有効にしなかった場合は、AKS クラスター内からのみサービスにアクセスできます。

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Azure Dev Spaces でポッドのマネージド ID を使用できますか。

現在、Azure Dev Spaces では、Azure Dev Spaces が有効になっている AKS クラスターで[ポッドのマネージド ID][aks-pod-managed-id] を使用することはサポートされていません。 ポッドのマネージド ID がインストールされていて、それをアンインストールしたい場合は、[アンインストールに関する注意事項][aks-pod-managed-id-uninstall]のページで詳細を確認できます。

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md