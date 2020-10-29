---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 0c14e5537385b7518fd08d9d3599993bc6d82f88
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535503"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight リリース ノート

この記事では、 **最近** の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープン ソース分析を行うエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

## <a name="release-date-10082020"></a>リリース日: 2020 年 10 月 8 日

このリリースは HDInsight 3.6 と HDInsight 4.0 の両方に適用されます。 HDInsight リリースは、数日以内にすべてのリージョンでご利用になれます。 ここのリリース日は、最初のリージョンのリリース日です。 以下の変更が見られない場合は、お客様のリージョンで数日以内にリリースがライブになるまでお待ちください。

## <a name="new-features"></a>新機能
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>パブリック IP とプライベート リンクを使用しない HDInsight プライベート クラスター (プレビュー)
HDInsight は、プレビュー段階でクラスターへのパブリック IP およびプライベート リンク アクセスを使用しないクラスターの作成をサポートするようになりました。 新しい高度なネットワーク設定を使用して、パブリック IP を使用しない完全に分離されたクラスターを作成し、独自のプライベート エンドポイントを使用してクラスターにアクセスできます。 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットへの移行
HDInsight では、Azure 仮想マシンを使用してクラスターをプロビジョニングするようになりました。 このリリース以降、サービスは徐々に [Azure 仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)に移行されます。 このプロセス全体に数か月かかる可能性があります。 リージョンとサブスクリプションが移行された後は、新しく作成された HDInsight クラスターは、お客様が操作することなく、仮想マシン スケール セット上で動作するようになります。 破壊的変更は想定されていません。

## <a name="deprecation"></a>非推奨
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>HDInsight 3.6 ML Services クラスターの非推奨
HDInsight 3.6 ML Services クラスターの種類は、2020 年 12 月 31 日でサポートの終了となります。 その後は新しい 3.6 ML Services クラスターを作成できなくなります。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 HDInsight のバージョンとクラスターの種類に関するサポートの有効期限については、[こちら](./hdinsight-component-versioning.md#available-versions)で確認してください。

## <a name="behavior-changes"></a>動作の変更
このリリースに動作変更はありません。

## <a name="upcoming-changes"></a>今後の変更
今後のリリースでは、次の変更が行われます。

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Spark、Hadoop、および ML Services 用のさまざまな Zookeeper 仮想マシン サイズを選択する機能
現在、HDInsight では、Spark、Hadoop、および ML サービス クラスターの種類での Zookeeper ノード サイズのカスタマイズはサポートされていません。 既定値は A2_v2 および A2 の仮想マシン サイズであり、無料で提供されます。 今後のリリースでは、お客様のシナリオに最も適した Zookeeper 仮想マシン サイズを選択できます。 A2_v2 および A2 以外の仮想マシン サイズの Zookeeper ノードには課金されます。 A2_v2 および A2 の仮想マシンは引き続き無料で提供されます。

## <a name="bug-fixes"></a>バグの修正
HDInsight は引き続き、クラスターの信頼性とパフォーマンスの向上を実現します。 

## <a name="component-version-change"></a>コンポーネントのバージョンの変更
このリリースでは、コンポーネントのバージョン変更はありません。 HDInsight 4.0 と HDInsight 3.6 の現在のコンポーネント バージョンについては、[こちらのドキュメント](./hdinsight-component-versioning.md)を参照してください。
