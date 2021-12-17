---
title: Azure Managed Instance for Apache Cassandra の紹介
description: Azure Managed Instance for Apache Cassandra について説明します。 このサービスでは、Azure で Apache Cassandra のネイティブ オープンソース インスタンスのデプロイとスケーリングが管理されます。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 91af4ca87c2363220081fe5e4912a26a764e85d1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844393"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra"></a>Azure Managed Instance for Apache Cassandra とは

Azure Managed Instance for Apache Cassandra サービスでは、マネージド オープンソースの Apache Cassandra データセンターに対して、デプロイとスケーリングの自動化された操作を提供します。 このサービスは、ハイブリッド シナリオの推進と継続的なメンテナンスの削減に役立ちます。

:::image type="content" source="./media/introduction/icon.gif" alt-text="Azure Managed Instance for Apache Cassandra は Apache Cassandra 向けの管理サービスです。" border="false":::

## <a name="key-benefits"></a>主な利点

### <a name="hybrid-deployments"></a>ハイブリッド デプロイ

仮想マシン スケール セットとして自動的にデプロイされる Apache Cassandra データセンターのマネージド インスタンスを新規または既存の Azure Virtual Network にこのサービスを利用して簡単に配置できます。 これらのデータセンターは、Azure または別のクラウド環境にある [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) 経由で、オンプレミスで実行されている既存の Apache Cassandra リングに追加できます。 これは、[ハイブリッド構成](configure-hybrid-cluster.md)によって実現されます。

- **簡略化デプロイ:** ハイブリッド接続の確立後、ゴシップ プロトコルによりデプロイが簡単になります。
- **ホストされたメトリック:** クラスター全体のアクティビティを監視するために、[Prometheus](https://prometheus.io/docs/introduction/overview/) でメトリックがホストされます。 サービスは、[メトリックおよび診断ログの Azure Monitor](monitor-clusters.md) とも統合されています。

### <a name="simplified-scaling"></a>簡素化スケーリング

マネージド インスタンスでは、データセンターでのノードのスケールアップとスケールダウンが完全に管理されます。 必要なノードの数を入力すると、スケーリング オーケストレーターによって Cassandra リング内でその操作が確立されます。

### <a name="managed-and-cost-effective"></a>マネージドかつ高いコスト効果

このサービスでは、次の一般的な Apache Cassandra タスクの管理操作が提供されます。

- クラスターのプロビジョニング
- データセンターのプロビジョニング
- データセンターのスケーリング
- データセンターの削除
- データセンターの構成の変更
- バックアップの設定

価格モデルは柔軟であり、オンデマンドであり、インスタンスベースです。ライセンス料金はありません。 この価格モデルでは、特定のワークロード ニーズに合わせて調整できます。 コアの数、VM SKU、メモリ サイズ、ノードあたりの P30 ディスクの数を選択します。

## <a name="next-steps"></a>次のステップ

次のいずれかのクイックスタートに取り組みます。

* [Azure portal からマネージド インスタンス クラスターを作成する](create-cluster-portal.md)
* [Azure Databricks でマネージド Apache Spark クラスターをデプロイする](deploy-cluster-databricks.md)
* [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)
