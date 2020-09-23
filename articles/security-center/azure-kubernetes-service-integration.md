---
title: Azure Security Center と Azure Kubernetes Service
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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: f1444ea31fe693f1c912a3ecb785bbb1e3aa6333
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977273"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure Kubernetes Service と Security Center の統合

Azure Kubernetes Service (AKS) は、コンテナー化されたアプリケーションを開発、デプロイ、管理するための Microsoft のマネージド サービスです。 

Azure **Defender for Kubernetes** を有効にすると、お使いの AKS ノード、クラウド トラフィック、およびセキュリティ制御に対する可視性が高まります。

Security Center と AKS を組み合わせることで、最高レベルのクラウドネイティブな Kubernetes セキュリティ オファリングが形成されます。

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Security Center の Kubernetes 保護のコンポーネントは何ですか。

Kubernetes に対する Security Center の保護は、次の 2 つの要素の組み合わせによって提供されます。

- **仮想マシンに対する Azure Security Center の脅威保護** - Security Center が他の VM で使用しているものと同じ Log Analytics エージェントを使用して、Security Center は、AKS ノードで発生しているセキュリティの問題を示すことができます。 エージェントは、コンテナー固有の分析についても監視します。

- **Azure Security Center のオプションの Azure Defender for Kubernetes プラン** - Kubernetes プランは、AKS サービスを介して Kubernetes サブシステムからログと情報を受け取ります。 これらのログは、AKS サービスを介して Azure で既に利用できます。 Azure Defender for Kubernetes を有効にすると、Security Center にログへのアクセスが付与されます。 そのため、Security Center は、AKS マスター ノードによって既に収集されたデータを使用して、AKS クラスターにセキュリティ上の利点をもたらします。 Kubernetes 環境から Azure Security Center によってスキャンされたデータには、機密情報が含まれている場合があります。


## <a name="next-steps"></a>次のステップ

Security Center のコンテナーのセキュリティ機能の詳細については、以下をご覧ください。

* [Azure Security Center とコンテナーのセキュリティ](container-security.md)

* [Azure Container Registry との統合](azure-container-registry-integration.md)

* [Microsoft でのデータ管理](https://www.microsoft.com/trust-center/privacy/data-management) - Microsoft サービス (Azure、Intune、Microsoft 365 など) のデータ ポリシー、Microsoft のデータ管理の詳細、お客様のデータに影響する保持ポリシーについて説明します。
