---
title: リソース、SKU、リージョンの制限
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) の既定のクォータ、制限されたノード VM SKU サイズ、およびリージョンの可用性について説明します。
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 257598f1a1ea8bbc578fe44c40b8e2dafef36837
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130266051"
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

## <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

AKS でサポートされている VM サイズの一覧は、Azure で新しい VM SKU がリリースされることに伴い進化しています。 サポートされている新しい SKU については、[AKS のリリース ノート](https://github.com/Azure/AKS/releases)を参照してください。

## <a name="restricted-vm-sizes"></a>制限されている VM サイズ

CPU が 2 つ未満の VM サイズは、AKS では使用できません。

AKS クラスター内の各ノードには、vCPU やメモリなどの一定量のコンピューティング リソースが含まれています。 AKS ノード内のコンピューティング リソースが不十分な場合、ポッドが正しく実行できないことがあります。 必要な *kube-system* ポッドとアプリケーションを確実にスケジュールできるようにするため、AKS では CPU が 2 つより多い VM サイズをノードで使用する必要があります。

VM の種類とそのコンピューティング リソースの詳細については、[Azure での仮想マシンのサイズ][vm-skus]に関するページを参照してください。

## <a name="region-availability"></a>利用可能なリージョン

クラスターをデプロイおよび実行できる場所の最新のリストについては、[AKS リージョンの可用性][region-availability]に関するページを参照してください。

## <a name="cluster-configuration-presets-in-the-azure-portal"></a>Azure portal のクラスター構成プリセット

Azure portal を使用してクラスターを作成するときは、シナリオに基づいて簡単にカスタマイズするために、プリセットの構成を選択できます。 プリセットの値はいつでも変更できます。

| プリセット           | 説明                                                            |
|------------------|------------------------------------------------------------------------|
| Standard         | 何を選択すればよいかわからない場合に最適です。 ほとんどのアプリケーションで機能します。 |
| 開発/テスト         | AKS で実験したり、テスト アプリケーションをデプロイしたりするのに最適です。 |
| Cost-optimized (コスト最適化)   | 中断を許容できる運用ワークロードのコストを削減するのに最適です。 |
| バッチ処理 | 機械学習、コンピューティング処理やグラフィックス処理の負荷が高いワークロードに最適です。 クラスターの迅速なスケールアップとスケールアウトが必要なアプリケーションに適しています。 |
| Hardened access (強化されたアクセス)  | セキュリティと安定性を完全に制御する必要がある大企業に最適です。 |

## <a name="next-steps"></a>次のステップ

特定のデフォルトの制限とクォータを増やすことができます。 リソースで増加がサポートされている場合は、[Azure サポート リクエスト][azure-support]を使用して増加を要求してください ( **[問題の種類]** で **[クォータ]** を選択してください)。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
