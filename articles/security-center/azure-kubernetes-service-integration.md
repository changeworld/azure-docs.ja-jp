---
title: Azure Security Center と Azure Kubernetes Service | Microsoft Docs
description: Azure Security Center と Azure Kubernetes Service の統合について説明します。
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
ms.openlocfilehash: 2a5d01978d8a4883d760e7ecf84afa381dfd563d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868560"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Azure Kubernetes Service と Security Center の統合 (プレビュー)
Azure Kubernetes Service (AKS) は、コンテナー化されたアプリケーションを開発、デプロイ、管理するためのMicrosoft のマネージド サービスです。 

Azure Security Center の Standard レベル ([価格](security-center-pricing.md)に関するページを参照) と AKS を組み合わせて使用すると、AKS ノード、クラウド トラフィック、セキュリティ制御を詳細に可視化できます。

Security Center は、AKS マスター ノードで既に収集済みのデータを使用して、AKS クラスターにセキュリティ上の利点をもたらします。 

![Azure Security Center と Azure Kubernetes Service (AKS) の概要](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

この 2 つのツールを組み合わせることで、最高レベルのクラウドネイティブな Kubernetes セキュリティ オファリングが形成されます。 

## <a name="benefits-of-integration"></a>統合の利点

2 つのサービスを組み合わせて使用すると、次の利点が得られます。

* **セキュリティに関する推奨事項** - Security Center では、クラスターから個々の仮想マシンまで、AKS リソースが識別され、分類されます。 これにより、セキュリティに関する推奨事項をリソースごとに表示できます。 詳細については、[セキュリティに関する推奨事項を実装する方法](security-center-recommendations.md)に関する記事をご覧ください。 

    > [!NOTE]
    > Security Center の推奨事項の名前が "(Preview)" タグで終わっている場合、機能ではなく、その推奨事項のプレビューの性質を示しています。

* **環境の強化機能** - Security Center では、Kubernetes クラスターの構成を常に監視し、業界標準を反映したセキュリティの推奨事項を生成します。

* **実行時の保護** - Security Center では、次の AKS ソースの継続的な分析によって、ホスト "*および*" AKS クラスター レベルで検出された脅威や悪意のあるアクティビティについてアラートを通知します (詳細については、[Azure Container に対する脅威の検出](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)に関する記事をご覧ください)。
    * 未加工のセキュリティ イベント (ネットワーク データやプロセスの作成など)
    * Kubernetes 監査ログ

![Azure Security Center と Azure Kubernetes Service (AKS) の詳細](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Kubernetes 環境から Azure Security Center によってスキャンされたデータには、機密情報が含まれている場合があります。

## <a name="next-steps"></a>次の手順

Security Center のコンテナーのセキュリティ機能の詳細については、以下をご覧ください。

* [Azure Security Center とコンテナーのセキュリティ](container-security.md)

* [Azure Container Registry との統合](azure-container-registry-integration.md)

* [仮想マシンの保護](security-center-virtual-machine-protection.md) - Security Center の推奨事項について説明します。

* [Microsoft でのデータ管理](https://www.microsoft.com/trust-center/privacy/data-management) - Microsoft サービス (Azure、Intune、Office 365 など) のデータ ポリシー、Microsoft のデータ管理の詳細、お客様のデータに影響する保持ポリシーについて説明します。