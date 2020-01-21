---
title: Azure Dev Spaces についてよく寄せられる質問
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Azure Dev Spaces についての一般的ないくつかの質問にお答えします
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: d5ab56edfe4799d51fb7f08642aad9e2ee01db05
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044968"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Azure Dev Spaces についてよく寄せられる質問

Azure Dev Spaces についてよく寄せられる質問に回答します。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>現在はどの Azure リージョンで Azure Dev Spaces が提供されていますか。

利用可能なリージョンの完全な一覧については、[サポートされているリージョン][supported-regions]を参照してください。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>パブリック IP アドレスなしで Azure Dev Spaces を使用できますか。

いいえ。パブリック IP を持たない AKS クラスター上に Azure Dev Spaces をプロビジョニングすることはできません。 パブリック IP は、[ルーティングのために Azure Dev Spaces に必要][dev-spaces-routing]です。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Azure Dev Spaces で独自のイングレスを使用できますか。

はい。Azure Dev Spaces によって作成されるイングレスと共に、独自のイングレスを構成できます。 たとえば、[traefik][ingress-traefik] を使用できます。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Azure Dev Spaces で HTTPS を使用できますか。

はい。[traefik][ingress-https-traefik] を使用して、HTTPS で独自のイングレスを構成できます。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>kubenet ではなく CNI を使用するクラスター上で Azure Dev Spaces を使用できますか。 

はい。ネットワークに CNI を使用する AKS クラスター上で、Azure Dev Spaces を使用できます。 たとえば、ネットワークに CNI を使用する[既存の Windows コンテナー][windows-containers]がある AKS クラスター上で、Azure Dev Spaces を使用できます。 Azure Dev Spaces を使用したネットワークでの CNI の使用の詳細については、[こちら](configure-networking.md#using-azure-cni)を参照してください。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Azure Dev Spaces で Windows コンテナーを使用できますか。

現時点では、Azure Dev Spaces は Linux ポッドおよびノード上でのみ実行することが想定されていますが、[既存の Windows コンテナー][windows-containers]を持つ AKS クラスター上で Azure Dev Spaces を実行できます。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>API サーバーの承認済み IP アドレス範囲が有効になっている AKS クラスターで Azure Dev Spaces を使用できますか。

はい。[API サーバーの承認済み IP アドレス範囲][aks-auth-range]が有効になっている AKS クラスターで Azure Dev Spaces を使用できます。 Azure Dev Spaces で有効な API サーバー承認済み IP アドレスの範囲の AKS クラスターを使用する方法の詳細については、[こちら](configure-networking.md#using-api-server-authorized-ip-ranges)を参照してください。

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>クラスター ノードのエグレス トラフィックが制限されている AKS クラスターで Azure Dev Spaces を使用できますか?

はい。正しい FQDN が許可されていると、[クラスター ノードに対する制限されたエグレス トラフィック][aks-restrict-egress-traffic]が有効になっている AKS クラスターで Azure Dev Spaces を使用できます。 Azure Dev Spaces での「クラスターノードに対する制限されたエグレストラフィックが有効な AKS クラスター」の使用の詳細については、[こちら](configure-networking.md#ingress-and-egress-network-traffic-requirements)を参照してください。

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md