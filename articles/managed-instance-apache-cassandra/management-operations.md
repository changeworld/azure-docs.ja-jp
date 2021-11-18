---
title: Azure Managed Instance for Apache Cassandra での管理操作
description: Azure Managed Instance for Apache Cassandra でサポートされている管理操作について説明します。 また、スタンドアロンおよびハイブリッド クラスターを管理する際の Azure サポート チームとお客様の責任の分離についても説明します。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 53e454ba84747c0e5fdeb009bc9f53e51480be9d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715266"
---
# <a name="management-operations-in-azure-managed-instance-for-apache-cassandra"></a>Azure Managed Instance for Apache Cassandra での管理操作

Azure Managed Instance for Apache Cassandra では、マネージド オープンソースの Apache Cassandra データセンターに対して、自動化されたデプロイおよびスケーリング操作を提供します。 この記事では、このサービスで提供される管理操作と機能について説明します。 また、スタンドアロンおよび[ハイブリッド](configure-hybrid-cluster.md) クラスターを管理する際の Azure サポート チームとお客様の責任の分離についても説明します。

## <a name="patching"></a>Patching

* オペレーティング システムレベルのパッチは、約 2 週間の頻度で自動的に適用されます。

* Apache Cassandra のソフトウェアレベルのパッチは、セキュリティの脆弱性が特定されたときに適用されます。 パッチ適用の頻度は変わる場合があります。

* パッチの適用中、マシンは 1 ラックずつ再起動されます。 **クォーラムの ALL 設定を使用しておらず**、レプリケーション係数が **3 以上** であれば、アプリケーション側でパフォーマンスが低下することはありません。

* Apache Cassandra のバージョンは、`X.Y.Z` の形式になります。 サービス ツールを使用して、メジャー (X) バージョンとマイナー (Y) バージョンのデプロイを手動で制御できます。 一方、メジャー バージョンとマイナー バージョンの組み合わせに必要となる場合がある Cassandra のパッチ (Z) は自動的に適用されます。  

## <a name="maintenance"></a>メンテナンス

* [Nodetool の修復](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/tools/toolsRepair.html)は、[reaper](http://cassandra-reaper.io/) を使用してサービスによって自動的に実行されます。 このツールは毎週 1 回実行されます。 [ハイブリッド デプロイ](configure-hybrid-cluster.md)に独自のサービスを使用する場合は、これを無効にすることもできます。

* ノードの稼働状況の監視は以下で構成されます。

  * Cassandra リング内の各ノードのメンバーシップをアクティブに監視する。
  * Azure、Virtual Machines、ストレージ、Linux、サポート ソフトウェアの問題を特定し、修正するために仮想マシンをアクティブに監視する。

* Cassandra の使用によって発生する可能性がある、CPU、ディスク、ネットワークの使用率の問題についてはお客様が責任を負います。 このような問題の例を次に示します。

  * 非効率なクエリ操作。
  * 容量を超えるスループット。
  * ストレージ容量を超えるデータの取り込み。
  * キースペースの誤った構成設定。
  * 不十分なデータ モデル戦略またはパーティション キー戦略。

## <a name="security"></a>セキュリティ

Azure Managed Instance for Apache Cassandra には、明示的なセキュリティ制御および機能が多数組み込まれています。

* サプライ チェーンの制御によってセキュリティが強化された Linux 仮想マシン イメージ。
* オペレーティング システム レベルでの Common Vulnerability & Exposure (CVE) 監視。
* マネージド仮想マシンでホストされている Apache Cassandra と Prometheus の両方のソフトウェアの証明書ローテーション。
* アクティブな脆弱性スキャン
* アクティブなウイルス スキャン。
* 安全なコーディング方法。

## <a name="hybrid-support"></a>ハイブリッド サポート

[ハイブリッド](configure-hybrid-cluster.md) クラスターを構成すると、サービスで実行される自動 reaper 操作によって、クラスター全体にメリットがもたらされます。 これには、サービスによってプロビジョニングされていないデータセンターが含まれます。 それ以外では、オンプレミスまたは外部でホストされているデータセンターの管理はお客様が行う必要があります。

## <a name="next-steps"></a>次のステップ

次のいずれかのクイックスタートに取り組みます。
* [Azure portal からマネージド インスタンス クラスターを作成する](create-cluster-portal.md)
* [Azure Databricks でマネージド Apache Spark クラスターをデプロイする](deploy-cluster-databricks.md)
* [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)
