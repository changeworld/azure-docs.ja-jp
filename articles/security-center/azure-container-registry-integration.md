---
title: Azure Security Center と Azure Container Registry
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
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 1c1b48d3715d838827f88f99fc0849d25677fdcc
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585739"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure Container Registry と Security Center の統合

Azure Container Registry (ACR) は、Azure デプロイ用のコンテナー イメージを中央のレジストリに格納して管理する、プライベートなマネージド Docker レジストリ サービスです。 これは、オープンソースの Docker Registry 2.0 を基にしています。

Azure Security Center の 標準レベルを使用している場合には、Container Registries のバンドルを追加できます。 このオプションの機能を使用すると、ARM ベースのレジストリ内のイメージの脆弱性をより詳しく把握できます。 サブスクリプション レベルでバンドルを有効または無効にし、サブスクリプション内のすべてのレジストリをカバーします。 この機能は、[価格のページ](security-center-pricing.md)に示されているように、イメージごとに課金されます。 Container Registries のバンドルを有効にすると、Security Center は、レジストリにプッシュされたイメージをスキャンできるようになります。 

イメージがレジストリにプッシュされるたびに、Security Center はそのイメージを自動的にスキャンします。 イメージのスキャンをトリガーするには、イメージをリポジトリにプッシュしてください。

スキャンが完了すると (通常は約 10 分後)、次のような Security Center の推奨事項で結果が得られます。

[![Azure Container Registry (ACR) でホストされるイメージで検出された脆弱性に関する Azure Security Center の推奨事項のサンプル](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>統合の利点

Security Center では、サブスクリプション内の ARM ベースの ACR レジストリが識別されて、次のものがシームレスに提供されます。

* プッシュされたすべての Linux イメージに対する **Azure ネイティブの脆弱性スキャン**。 Security Center では、業界をリードする脆弱性スキャン ベンダーである Qualys のスキャナーを使ってイメージのスキャンを行います。 このネイティブ ソリューションは、既定でシームレスに統合されています。

* 既知の脆弱性がある Linux イメージの**セキュリティに関する推奨事項**。 Security Center には、報告された各脆弱性の詳細と、重要度の分類が表示されます。 また、レジストリにプッシュされた各イメージで検出された特定の脆弱性を修復する方法についてのガイダンスも提供されます。

![Azure Security Center と Azure Container Registry (ACR) の概要](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>次のステップ

Security Center のコンテナーのセキュリティ機能の詳細については、以下をご覧ください。

* [Azure Security Center とコンテナーのセキュリティ](container-security.md)

* [Azure Kubernetes Service との統合](azure-kubernetes-service-integration.md)

* [仮想マシンの保護](security-center-virtual-machine-protection.md) - Security Center の推奨事項について説明します。