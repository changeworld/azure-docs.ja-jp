---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435465"
---
# <a name="release-notes"></a>リリース ノート

この記事では、**最近**の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープンソースの Apache Hadoop および Apache Spark の分析を行う、エンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

## <a name="release-date-12172019"></a>リリース日: 12/17/2019

このリリースは、HDInsight 3.6 と4.0 の両方に適用されます。

> [!IMPORTANT]  
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight のバージョン管理に関する記事](hdinsight-component-versioning.md)に関するページを参照してください。


## <a name="new-features"></a>新機能

### <a name="service-tags"></a>サービス タグ
サービス タグを利用することで、ネットワーク アクセスを Azure サービスに簡単に制限できるため、Azure 仮想マシンと Azure 仮想ネットワークのセキュリティが簡単になります。 ネットワーク セキュリティ グループ (NSG) ルールでサービス タグを使用し、グローバルで、または Azure リージョンごとに特定の Azure サービスへのトラフィックを許可または拒否できます。 Azure では、各タグの基になる IP アドレスが保守管理されます。 ネットワーク セキュリティ グループ (NSG) 用の HDInsight サービス タグは、正常性および管理サービスのための IP アドレスのグループです。 これらのグループを使用すると、セキュリティ規則の作成の複雑さを最小限に抑えることができます。 HDInsight をご利用のお客様は Azure portal、PowerShell、REST API からサービス タグを有効にできます。 詳細については、「[Azure HDInsight のネットワーク セキュリティ グループ (NSG) サービス タグ](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags)」を参照してください。

### <a name="custom-ambari-db"></a>カスタム Ambari DB
HDInsight では、自分の SQL DB for Apache Ambari を使用できるようになりました。 このカスタム Ambari DB は、Azure portal または Resource Manager テンプレートから構成できます。  この機能により、自分に必要な処理と容量に最適な SQL DB を選択できます。 また、事業の成長に合わせて簡単にアップグレードできます。 詳細については、「[カスタム Ambari DB を使用して HDInsight クラスターを設定する](hdinsight-custom-ambari-db.md)」を参照してください。

![カスタム Ambari DB](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>非推奨
このリリースに非推奨はありません。 今後の非推奨に備えるには、「[今後の変更](#upcoming-changes)」を参照してください。

## <a name="behavior-changes"></a>動作の変更
このリリースに動作変更はありません。 今後の動作変更に備えるには、「[今後の変更](#upcoming-changes)」を参照してください。

## <a name="upcoming-changes"></a>今後の変更
今後のリリースでは、次の変更が行われます。 

### <a name="transport-layer-security-tls-12-enforcement"></a>トランスポート層セキュリティ (TLS) 1.2 施行
TLS (トランスポート層セキュリティ) と SSL (Secure Sockets Layer) は、コンピューター ネットワーク上の通信にセキュリティを確保する暗号プロトコルです。 詳細については、「[トランスポート層セキュリティ](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)」を参照してください。 Azure HDInsight クラスターのパブリック HTTPS エンドポイントは TLS 1.2 に対応していますが、旧クライアントとの下位互換性のため、TLS 1.1 も引き続きサポートされます。

次のリリースから、新しい HDInsight クラスターで TLS 1.2 接続のみを許可するように選択し、構成できます。 

2020 年 6 月 30 日以降、Azure HDInsight ではすべての HTTPS 接続で TLS 1.2 以降のバージョンが強制されます。 すべてのクライアントで確実に TLS 1.2 以降のバージョンに対応できるようにすることをお勧めします。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットへの移行
HDInsight では、Azure 仮想マシンを使用してクラスターをプロビジョニングするようになりました。 2020 年 2 月より (正式な日付は今後通達します)、HDInsight では Azure 仮想マシン スケール セットが代わりに使用されます。 [Azure 仮想マシン スケール セット](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)についてご確認ください。

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark クラスター ノード サイズの変更 
次期リリースで:
- ESP Spark クラスターに許容される最小ノード サイズが Standard_D13_V2 に変更されます。 
- CPU とメモリの容量が比較的低いことに起因し、A シリーズの VM は ESP クラスターに問題を引き起こす可能性があるため、新しい ESP クラスターの作成するとき、A シリーズ VM は非推奨となります。

### <a name="hbase-20-to-21"></a>HBase 2.0 から 2.1
今後の HDInsight 4.0 リリースでは、HBase のバージョンがバージョン 2.0 から 2.1 にアップグレードされます。

## <a name="bug-fixes"></a>バグの修正
HDInsight は引き続き、クラスターの信頼性とパフォーマンスの向上を実現します。 

## <a name="component-version-change"></a>コンポーネントのバージョンの変更
HDInsight 3.6 のサポートが 2020 年 12 月 31 日に延長されます。 詳細は「[サポートされている HDInsight バージョン](hdinsight-component-versioning.md#supported-hdinsight-versions)」にあります。

HDInsight 4.0 のコンポーネント バージョン変更はありません。

HDInsight 3.6 の Apache Zeppelin:0.7.0 --> 0.7.3 

最新のコンポーネント バージョンについては[こちらのドキュメント](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)でご確認いただけます。

## <a name="new-regions"></a>新しいリージョン

### <a name="uae-north"></a>アラブ首長国連邦北部
アラブ首長国連邦北部の管理 IP: `65.52.252.96` と `65.52.252.97`
