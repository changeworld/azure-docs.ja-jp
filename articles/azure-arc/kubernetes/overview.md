---
title: Azure Arc 対応 Kubernetes の概要
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, Arc, Azure, コンテナー
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665277"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Azure Arc 対応 Kubernetes (プレビュー) とは

Azure Arc 対応 Kubernetes (プレビュー) を使用して、Azure の内部または外部で Kubernetes クラスターを接続して構成することができます。 Kubernetes クラスターを Azure Arc に接続すると、Azure portal に表示され、Azure Resource Manager ID とマネージド ID が付与されます。 クラスターは、標準の Azure サブスクリプションに接続され、リソース グループに存在し、他の Azure リソースと同様にタグを受け取ることができます。 


Kubernetes クラスターを Azure に接続するには、クラスター管理者がエージェントをデプロイする必要があります。 これらのエージェントは、`azure-arc` という名前の Kubernetes 名前空間で実行され、標準の Kubernetes デプロイです。 エージェントは、Azure への接続、Azure Arc ログおよびメトリックの収集、構成要求の監視を担当します。  
 
 > [!NOTE]
> Azure Arc 対応 Kubernetes は、プレビュー段階にあり、運用ワークロードには推奨されません。 


## <a name="supported-scenarios"></a>サポートされるシナリオ 

Azure Arc 対応 Kubernetes は、次のシナリオをサポートします。 

* インベントリ、グループ化、タグ付けのために Azure 外部で実行されている Kubernetes を接続する 

* アプリケーションをデプロイし、GitOps ベースの構成管理を使用して構成を適用する 

* コンテナーに Azure Monitor を使用して、クラスターを表示および管理する 

* Kubernetes 用の Azure Policy を使用してポリシーを適用する 

 
## <a name="supported-regions"></a>サポートされているリージョン 

Azure Arc 対応 Kubernetes は、現在次のリージョンでサポートされています。 

* 米国東部 
* 西ヨーロッパ 


## <a name="next-steps"></a>次のステップ

* [クラスターの接続](./connect-cluster.md)
