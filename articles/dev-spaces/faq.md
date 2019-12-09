---
title: Azure Dev Spaces についてよく寄せられる質問
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Azure Dev Spaces についての一般的ないくつかの質問にお答えします
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: 2baab0812061bec7dcf08d35056804313d873889
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482302"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Azure Dev Spaces についてよく寄せられる質問

Azure Dev Spaces についてよく寄せられる質問に回答します。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>現在はどの Azure リージョンで Azure Dev Spaces が提供されていますか。

利用可能なリージョンの完全な一覧については、[サポートされているリージョンと構成][supported-regions]に関するページを参照してください。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>パブリック IP アドレスなしで Azure Dev Spaces を使用できますか。

いいえ。パブリック IP を持たない AKS クラスター上に Azure Dev Spaces をプロビジョニングすることはできません。 パブリック IP は、[ルーティングのために Azure Dev Spaces に必要][dev-spaces-routing]です。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Azure Dev Spaces で独自のイングレスを使用できますか。

はい。Azure Dev Spaces によって作成されるイングレスと共に、独自のイングレスを構成できます。 たとえば、[traefik][ingress-traefik] を使用できます。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Azure Dev Spaces で HTTPS を使用できますか。

はい。[traefik][ingress-https-traefik] を使用して、HTTPS で独自のイングレスを構成できます。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>kubenet ではなく CNI を使用するクラスター上で Azure Dev Spaces を使用できますか。 

はい。ネットワークに CNI を使用する AKS クラスター上で、Azure Dev Spaces を使用できます。 たとえば、ネットワークに CNI を使用する[既存の Windows コンテナー][windows-containers]がある AKS クラスター上で、Azure Dev Spaces を使用できます。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Azure Dev Spaces で Windows コンテナーを使用できますか。

現時点では、Azure Dev Spaces は Linux ポッドおよびノード上でのみ実行することが想定されていますが、[既存の Windows コンテナー][windows-containers]を持つ AKS クラスター上で Azure Dev Spaces を実行できます。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>API サーバーの承認済み IP アドレス範囲が有効になっている AKS クラスターで Azure Dev Spaces を使用できますか。

はい。[API サーバーの承認済み IP アドレス範囲][aks-auth-range]が有効になっている AKS クラスターで Azure Dev Spaces を使用できます。 クラスターを[作成][aks-auth-range-create]する場合は、[リージョンに基づいて追加の範囲を許可][aks-auth-range-ranges]する必要があります。 また、既存のクラスターを[更新][aks-auth-range-update]して、これらの追加の範囲を許可することもできます。

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>クラスター ノードのエグレス トラフィックが制限されている AKS クラスターで Azure Dev Spaces を使用できますか?

はい。次の FQDN が許可されていると、[クラスター ノードに対するエグレス トラフィックの制限][aks-restrict-egress-traffic]が有効にされた AKS クラスターで Azure Dev Spaces を使用できます。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS: 443 | linux alpine やその他の Azure Dev Spaces イメージをプルする |
| gcr.io | HTTP:443 | helm/tiller イメージをプルする |
| storage.googleapis.com | HTTP:443 | helm/tiller イメージをプルする |
| azds-<guid>.<location>.azds.io | HTTPS: 443 | コントローラーの Azure Dev Spaces バックエンド サービスと通信する 正確な FQDN は、%USERPROFILE%\.azds\settings.json の "dataplaneFqdn" で確認できます |


[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md