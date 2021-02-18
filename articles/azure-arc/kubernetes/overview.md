---
title: Azure Arc 対応 Kubernetes の概要
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: この記事では、Azure Arc 対応 Kubernetes の概要を示します。
keywords: Kubernetes, Arc, Azure, コンテナー
ms.custom: references_regions
ms.openlocfilehash: 54b439d73ed5dfb0709d33dea1f588b8bdf4489f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560264"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Azure Arc 対応 Kubernetes プレビューとは

Azure Arc 対応 Kubernetes プレビューを使用して、Azure の内部または外部で Kubernetes クラスターを接続して構成することができます。 Kubernetes クラスターが Azure Arc に接続されると、Azure portal に表示されます。 また、Azure Resource Manager ID とマネージド ID が割り当てられます。 クラスターは、標準の Azure サブスクリプションに接続され、リソース グループ内に存在し、他の Azure リソースと同様にタグを受け取ることができます。 

Kubernetes クラスターを Azure に接続するには、クラスター管理者がエージェントをデプロイする必要があります。 これらのエージェントは、`azure-arc` という名前の Kubernetes 名前空間で実行され、標準の Kubernetes デプロイです。 エージェントは、Azure への接続、Azure Arc ログおよびメトリックの収集、構成要求の監視を担当します。 

Azure Arc 対応 Kubernetes では、転送中のデータをセキュリティで保護する業界標準の SSL がサポートされます。 また、保存中は Azure Cosmos DB データベースに暗号化された状態で格納されるので、データの機密性が確保されます。
 
> [!NOTE]
> Azure Arc 対応 Kubernetes はプレビュー段階です。 運用環境のワークロード用にはお勧めしません。

## <a name="supported-kubernetes-distributions"></a>サポートされている Kubernetes ディストリビューション

Azure Arc 対応 Kubernetes は、Cloud Native Computing Foundation (CNCF) で認定されたすべての Kubernetes クラスター (Azure 上の AKS エンジン、Azure Stack Hub 上の AKS エンジン、GKE、EKS、VMware vSphere クラスターなど) で動作します。

次のディストリビューションについて、Azure Arc 対応 Kubernetes の機能が Arc チームによってテストされています。
* Red Hat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* AKS Engine
* Azure Stack Hub 上の AKS エンジン
* Azure Stack HCI 上の AKS
* クラスター API プロバイダー Azure

## <a name="supported-scenarios"></a>サポートされるシナリオ 

Azure Arc 対応 Kubernetes では、以下のシナリオがサポートされます。 

* インベントリ、グループ化、タグ付けのために Azure 外部で実行されている Kubernetes を接続する。

* アプリケーションをデプロイし、GitOps ベースの構成管理を使用して構成を適用する。 

* コンテナーに対して Azure Monitor を使用して、クラスターを表示および監視する。 

* Kubernetes 用の Azure Policy を使用してポリシーを適用する。 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>サポートされているリージョン 

Azure Arc 対応 Kubernetes は、現在、以下のリージョンでサポートされています。 

* 米国東部 
* 西ヨーロッパ

## <a name="next-steps"></a>次のステップ

* [クラスターを接続する](./connect-cluster.md)
