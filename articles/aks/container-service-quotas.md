---
title: Azure Kubernetes Service (AKS) のクォータとリージョンの可用性
description: Azure Kubernetes Service (AKS) の既定のクォータとリージョンの可用性。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: c8a2c0cac963fcc0622cff547e85593a13aa076a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243841"
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
- オーストラリア南東部
- カナダ中部
- カナダ東部
- 米国中央部
- 東アジア
- 米国東部
- 米国東部 2
- フランス中部
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
