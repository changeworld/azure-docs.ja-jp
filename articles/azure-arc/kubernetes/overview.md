---
title: Azure Arc 対応 Kubernetes の概要
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: この記事では、Azure Arc 対応 Kubernetes の概要を示します。
keywords: Kubernetes, Arc, Azure, コンテナー
ms.custom: references_regions
ms.openlocfilehash: 3960a0f0b6866a0a5d3476e841d29c6e5a89a238
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309936"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Azure Arc 対応 Kubernetes プレビューとは

Azure Arc 対応 Kubernetes プレビューを使用して、Azure の内部または外部で Kubernetes クラスターを接続して構成することができます。 Kubernetes クラスターが Azure Arc に接続されると、Azure portal に表示されます。 また、Azure Resource Manager ID とマネージド ID が割り当てられます。 クラスターは、標準の Azure サブスクリプションに接続され、リソース グループ内に存在し、他の Azure リソースと同様にタグを受け取ることができます。 

Kubernetes クラスターを Azure に接続するには、クラスター管理者がエージェントをデプロイする必要があります。 これらのエージェントは、`azure-arc` という名前の Kubernetes 名前空間で実行され、標準の Kubernetes デプロイです。 エージェントは、Azure への接続、Azure Arc ログおよびメトリックの収集、構成要求の監視を担当します。 

Azure Arc 対応 Kubernetes では、転送中のデータをセキュリティで保護する業界標準の SSL がサポートされます。 また、保存中は Azure Cosmos DB データベースに暗号化された状態で格納されるので、データの機密性が確保されます。
 
 > [!NOTE]
> Azure Arc 対応 Kubernetes はプレビュー段階にあります。 運用環境のワークロード用にはお勧めしません。 


## <a name="supported-scenarios"></a>サポートされるシナリオ 

Azure Arc 対応 Kubernetes では、以下のシナリオがサポートされます。 

* インベントリ、グループ化、タグ付けのために Azure 外部で実行されている Kubernetes を接続する。

* アプリケーションをデプロイし、GitOps ベースの構成管理を使用して構成を適用する。 

* コンテナーに対して Azure Monitor を使用して、クラスターを表示および監視する。 

* Kubernetes 用の Azure Policy を使用してポリシーを適用する。 

 
## <a name="supported-regions"></a>サポートされているリージョン 

Azure Arc 対応 Kubernetes は、現在、以下のリージョンでサポートされています。 

* 米国東部 
* 西ヨーロッパ 

## <a name="next-steps"></a>次のステップ

* [クラスターを接続する](./connect-cluster.md)
