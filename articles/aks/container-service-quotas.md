---
title: Azure Kubernetes Service (AKS) のクォータとリージョンの可用性
description: Azure Kubernetes Service (AKS) の既定のクォータとリージョンの可用性。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 08/01/2018
ms.author: iainfou
ms.openlocfilehash: 9fbc769ef428c41f0e133ec1e139ba9900025dc4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879358"
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のクォータとリージョンの可用性

すべての Azure サービスには、リソースと機能に対する特定の既定の制限とクォータが含まれます。 次のセクションでは、いくつかの Azure Kubernetes Service (AKS) リソースに対する既定のリソースの制限と、Azure リージョンでの AKS サービスの可用性について詳しく説明します。

## <a name="service-quotas-and-limits"></a>サービスのクォータと制限

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>プロビジョニングされるインフラストラクチャ

その他のすべてのネットワーク、コンピューティング、およびストレージの制限が、プロビジョニングされるインフラストラクチャに適用されます。 関連する制限については、[Azure サブスクリプションとサービスの制限](../azure-subscription-service-limits.md)に関するページを参照してください。

## <a name="region-availability"></a>利用可能なリージョン

Azure Kubernetes Service (AKS) は、次のリージョンで利用できます。

- オーストラリア東部
- カナダ中部
- カナダ東部
- 米国中央部
- 東アジア
- 米国東部
- 米国東部 2
- 東日本
- 北ヨーロッパ
- 東南アジア
- インド南部
- 英国南部
- 英国西部
- 西ヨーロッパ
- 米国西部
- 米国西部 2

## <a name="next-steps"></a>次の手順

特定の既定の制限とクォータを増やすことができます。 このような増加に対応する 1 つまたは複数のリソースの増加を要求する場合は、[Azure サポート リクエスト][azure-support]を送信してください (**[問題の種類]** で [クォータ] を選択してください)。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
