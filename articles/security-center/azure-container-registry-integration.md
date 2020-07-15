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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: f3ef633ff0271d74eea7320faadf17685976d3b6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970469"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure Container Registry と Security Center の統合

Azure Container Registry (ACR) は、Azure デプロイ用のコンテナー イメージを中央のレジストリに格納して管理する、プライベートなマネージド Docker レジストリ サービスです。 これは、オープンソースの Docker Registry 2.0 を基にしています。

Azure Security Center の 標準レベルを使用している場合には、Container Registries のバンドルを追加できます。 このオプションの機能を使用すると、ARM ベースのレジストリ内のイメージの脆弱性をより詳しく把握できます。 サブスクリプション レベルでバンドルを有効または無効にし、サブスクリプション内のすべてのレジストリをカバーします。 この機能は、[価格のページ](security-center-pricing.md)に示されているように、イメージごとに課金されます。 Container Registries のバンドルを有効にすると、Security Center は、レジストリにプッシュされたイメージをスキャンできるようになります。 


## <a name="availability"></a>可用性

- リリース状態: **一般提供**
- 必要なロール: **セキュリティ閲覧者**と [Azure Container Registry 閲覧者ロール](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- クラウド: 
    - ✔ 商用クラウド
    - ✘ 米国政府のクラウド
    - ✘中国政府のクラウド、その他の政府のクラウド


## <a name="when-are-images-scanned"></a>イメージはどのような場合にスキャンされますか。

イメージがレジストリにプッシュされるたびに、Security Center はそのイメージを自動的にスキャンします。 イメージのスキャンをトリガーするには、イメージをリポジトリにプッシュしてください。

スキャンが完了すると (通常は約 10 分後ですが、最大で 40 分かかることもあります)、結果が次のような Security Center の推奨事項で表示されます。

[![Azure Container Registry (ACR) でホストされるイメージで検出された脆弱性に関する Azure Security Center の推奨事項のサンプル](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>統合の利点

Security Center では、サブスクリプション内の ARM ベースの ACR レジストリが識別されて、次のものがシームレスに提供されます。

* プッシュされたすべての Linux イメージに対する **Azure ネイティブの脆弱性スキャン**。 Security Center では、業界をリードする脆弱性スキャン ベンダーである Qualys のスキャナーを使ってイメージのスキャンを行います。 このネイティブ ソリューションは、既定でシームレスに統合されています。

* 既知の脆弱性がある Linux イメージの**セキュリティに関する推奨事項**。 Security Center には、報告された各脆弱性の詳細と、重要度の分類が表示されます。 また、レジストリにプッシュされた各イメージで検出された特定の脆弱性を修復する方法についてのガイダンスも提供されます。

![Azure Security Center と Azure Container Registry (ACR) の概要](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="acr-with-security-center-faq"></a>Security Center と ACR のよく寄せられる質問

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Azure Security Center ではどのような種類のイメージをスキャンできますか。
Security Center では、シェル アクセスを与える Linux OS ベースのイメージがスキャンされます。 

Qualys スキャナーでは、[Docker スクラッチ](https://hub.docker.com/_/scratch/)のようなスーパー ミニマリスト イメージ、またはアプリケーションとそのランタイム依存関係のみが含まれ、パッケージ マネージャー、シェル、OS は含まれない、"ディストリビューションレス" イメージは、サポートされていません。

### <a name="how-does-azure-security-center-scan-an-image"></a>Azure Security Center ではどのような方法でイメージがスキャンされますか。
イメージがレジストリから抽出されます。 その後、隔離されたサンドボックスの中で、既知の脆弱性の一覧を抽出する Qualys スキャナーによって実行されます。

Security Center では、スキャナーによる検出結果がフィルター処理および分類されます。 イメージが正常な場合、Security Center ではそのように示されます。 Security Center では、解決の必要な問題があるイメージに対してのみ、セキュリティに関する推奨事項が生成されます。 問題があるときにだけ通知することにより、Security Center では不要な情報アラートの可能性が減ります。

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Azure Security Center ではどのくらいの頻度でイメージがスキャンされますか。
イメージ スキャンはプッシュのたびにトリガーされます。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>REST API 経由でスキャン結果を取得できますか。
はい。 結果は [Sub-Assessments Rest API](/rest/api/securitycenter/subassessments/list/) の下にあります。 また、Azure Resource Graph (ARG) を利用できます。これはすべてのリソースを対象とする Kusto のような API であり、1 つのクエリで特定のスキャンをフェッチできます。
 



## <a name="next-steps"></a>次のステップ

Security Center のコンテナーのセキュリティ機能の詳細については、以下をご覧ください。

* [Azure Security Center とコンテナーのセキュリティ](container-security.md)

* [Azure Kubernetes Service との統合](azure-kubernetes-service-integration.md)

* [仮想マシンの保護](security-center-virtual-machine-protection.md) - Security Center の推奨事項について説明します。
