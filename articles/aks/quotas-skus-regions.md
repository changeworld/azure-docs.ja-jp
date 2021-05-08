---
title: リソース、SKU、リージョンの制限
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) の既定のクォータ、制限されたノード VM SKU サイズ、およびリージョンの可用性について説明します。
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 3e1e74834153584525d2093d2a1bb8ba8e991e5a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011466"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のクォータ、仮想マシンのサイズの制限、およびリージョンの可用性

すべての Azure サービスには、特定の仮想マシン (VM) SKU の使用制限を含め、リソースと機能に対してデフォルトの制限とクォータが設定されています。

この記事では、Azure Kubernetes Service (AKS) リソースに対する既定のリソースの制限と、Azure リージョンでの AKS の可用性について詳しく説明します。

## <a name="service-quotas-and-limits"></a>サービスのクォータと制限

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>プロビジョニングされるインフラストラクチャ

その他のすべてのネットワーク、コンピューティング、およびストレージの制限が、プロビジョニングされるインフラストラクチャに適用されます。 関連する制限については、[Azure サブスクリプションとサービスの制限](../azure-resource-manager/management/azure-subscription-service-limits.md)に関するページを参照してください。

> [!IMPORTANT]
> AKS クラスターをアップグレードすると、追加のリソースが一時的に消費されます。 これらのリソースには、仮想ネットワークのサブネットまたは仮想マシンの vCPU クォータで使用できる IP アドレスが含まれます。 
>
> Windows Server コンテナーの場合、アップグレードの操作を実行すると最新のノード更新プログラムを適用できます。 これらの一時リソースの処理に使用できる IP アドレス空間または vCPU クォータがない場合、クラスターのアップグレード プロセスは失敗します。 Windows Server ノードのアップグレード プロセスの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。

## <a name="restricted-vm-sizes"></a>制限されている VM サイズ

AKS クラスター内の各ノードには、vCPU やメモリなどの一定量のコンピューティング リソースが含まれています。 AKS ノード内のコンピューティング リソースが不十分な場合、ポッドが正しく実行できないことがあります。 必要な *kube-system* のポッドとアプリケーションのスケジュールを確実に設定するには、**AKS で次の VM SKU を使用しないでください**。

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

VM の種類とそのコンピューティング リソースの詳細については、[Azure での仮想マシンのサイズ][vm-skus]に関するページを参照してください。

## <a name="region-availability"></a>利用可能なリージョン

クラスターをデプロイおよび実行できる場所の最新のリストについては、[AKS リージョンの可用性][region-availability]に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

特定のデフォルトの制限とクォータを増やすことができます。 リソースで増加がサポートされている場合は、[Azure サポート リクエスト][azure-support]を使用して増加を要求してください ( **[問題の種類]** で **[クォータ]** を選択してください)。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
