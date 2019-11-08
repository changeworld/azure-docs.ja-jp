---
title: Azure Security Center と Azure Container Registry | Microsoft Docs
description: Azure Security Center と Azure Container Registry の統合について説明します
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f0de56f968488f0e5d551ad705cc6f8ca6e7bc47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521453"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Azure Container Registry と Security Center の統合 (プレビュー)

Azure Container Registry (ACR) は、Azure デプロイ用のコンテナー イメージを中央のレジストリに格納して管理する、プライベートなマネージド Docker レジストリ サービスです。 これは、オープンソースの Docker Registry 2.0 を基にしています。

ACR を Azure Security Center の Standard レベル ([価格](security-center-pricing.md)を参照) と共に使用すると、レジストリとイメージの脆弱性をより詳しく把握できます。

[![Azure Security Center 内での Azure Container Registry (ACR) の推奨事項](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>統合の利点

Security Center では、サブスクリプション内の ACR レジストリが識別されて、次のものがシームレスに提供されます。

* プッシュされたすべての Linux イメージに対する **Azure ネイティブの脆弱性スキャン**。 Security Center では、業界をリードする脆弱性スキャン ベンダーである Qualys のスキャナーを使ってイメージのスキャンを行います。 このネイティブ ソリューションは、既定でシームレスに統合されています。

* 既知の脆弱性がある Linux イメージの**セキュリティに関する推奨事項**。 Security Center には、報告された各脆弱性の詳細と、重要度の分類が表示されます。 また、レジストリにプッシュされた各イメージで検出された特定の脆弱性を修復する方法についてのガイダンスも提供されます。

![Azure Security Center と Azure Container Registry (ACR) の概要](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>次の手順

Security Center のコンテナーのセキュリティ機能の詳細については、以下を参照してください。

* [Azure Security Center とコンテナーのセキュリティ](container-security.md)

* [Azure Kubernetes Service との統合](azure-kubernetes-service-integration.md)

* [仮想マシンの保護](security-center-virtual-machine-protection.md) - Security Center の推奨事項についての説明