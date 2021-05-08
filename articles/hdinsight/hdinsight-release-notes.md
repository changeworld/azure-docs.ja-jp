---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: a648ff3aa0c042aaefe16eaae0f9d73953241b3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065499"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight リリース ノート

この記事では、**最近** の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープン ソース分析を行うエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

リリース ノートをサブスクライブする場合は、[この GitHub リポジトリ](https://github.com/hdinsight/release-notes/releases)のリリースをご覧ください。

## <a name="release-date-03242021"></a>リリース日: 2021 年 3 月 24 日

このリリースは HDInsight 3.6 と HDInsight 4.0 の両方に適用されます。 HDInsight リリースは、数日以内にすべてのリージョンでご利用になれます。 ここのリリース日は、最初のリージョンのリリース日です。 以下の変更が見られない場合は、お客様のリージョンで数日以内にリリースがライブになるまでお待ちください。

## <a name="new-features"></a>新機能
### <a name="spark-30-preview"></a>Spark 3.0 プレビュー
HDInsight では、プレビュー機能として HDInsight 4.0 に [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) のサポートを追加しました。 

### <a name="kafka-24-preview"></a>Kafka 2.4 プレビュー
HDInsight では、プレビュー機能として HDInsight 4.0 に [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) のサポートを追加しました。

### <a name="eav4-series-support"></a>Eav4 シリーズのサポート
HDInsight のこのリリースでは、Eav4 シリーズのサポートが追加されました。 [Dav4 シリーズの詳細についてはこちら](../virtual-machines/eav4-easv4-series.md)を参照してください。 このシリーズは、次のリージョンで提供されています。 

* オーストラリア東部
* ブラジル南部
* 米国中部
* 東アジア
* 米国東部
* 東日本
* 東南アジア
* 英国南部
* 西ヨーロッパ
* 米国西部 2

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットへの移行
HDInsight では、Azure 仮想マシンを使用してクラスターをプロビジョニングするようになりました。 このサービスは [Azure 仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)に段階的に移行しています。 このプロセス全体に数か月かかる可能性があります。 リージョンとサブスクリプションが移行された後は、新しく作成された HDInsight クラスターは、お客様が操作することなく、仮想マシン スケール セット上で動作するようになります。 破壊的変更は想定されていません。

## <a name="deprecation"></a>非推奨
このリリースに非推奨はありません。

## <a name="behavior-changes"></a>動作の変更
### <a name="default-cluster-version-is-changed-to-40"></a>既定のクラスター バージョンは 4.0 に変更されています
HDInsight クラスターの既定のバージョンは 3.6 から 4.0 に変更されています。 使用可能なバージョンの詳細については、「[使用可能なバージョン](./hdinsight-component-versioning.md)」を参照してください。 [HDInsight 4.0](./hdinsight-version-release.md) の新機能については、こちらを参照してください。

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>既定のクラスター VM サイズが Ev3 シリーズに変更 
既定のクラスター VM のサイズは、D シリーズから Ev3 シリーズに変更されています。 この変更は、ヘッド ノードとワーカー ノードに適用されます。 この変更がテスト済みのワークフローに影響を与えることを回避するには、使用する VM サイズを ARM テンプレートで指定します。

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットで実行されているクラスターでのネットワーク インターフェイス リソースの非表示
HDInsight は Azure 仮想マシン スケール セットに段階的に移行しています。 仮想マシンのネットワーク インターフェイスは、Azure 仮想マシン スケール セットを使用するクラスターの顧客に対しては表示されなくなりました。

## <a name="upcoming-changes"></a>今後の変更
今後のリリースでは、次の変更が行われます。

### <a name="os-version-upgrade"></a>OS バージョンのアップグレード
HDInsight では、OS バージョンが Ubuntu 16.04 から 18.04 にアップグレードされます。 アップグレードは、2021 年 4 月までに完了する予定です。

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>HDInsight 3.6 の Basic サポート (2021 年 7 月 1 日以降)
2021 年 7 月 1 日以降、Microsoft では特定の種類の HDInsight 3.6 クラスターに対する [Basic サポート](hdinsight-component-versioning.md#support-options-for-hdinsight-versions)を提供します。 Basic サポート プランは 2022 年 4 月 3 日まで利用できます。 Basic サポートには 2021 年 7 月 1 日から自動的に登録されます。 オプトインするために必要な操作はありません。 Basic サポートに含まれるクラスターの種類については、[こちらのドキュメント](hdinsight-36-component-versioning.md)を参照してください。 

HDInsight 3.6 で新しいソリューションを構築することはお勧めしません。既存の 3.6 の環境に対する変更を凍結してください。 [クラスターを HDInsight 4.0 に移行](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40)することをお勧めします。 HDInsight 4.0 の新機能については、[こちら](hdinsight-version-release.md#whats-new-in-hdinsight-40)を参照してください。

## <a name="bug-fixes"></a>バグの修正
HDInsight は引き続き、クラスターの信頼性とパフォーマンスの向上を実現します。 

## <a name="component-version-change"></a>コンポーネントのバージョンの変更
Spark 3.0.0 と Kafka 2.4.1 のプレビューとしてのサポートが追加されました。 HDInsight 4.0 と HDInsight 3.6 の現在のコンポーネント バージョンについては、[こちらのドキュメント](./hdinsight-component-versioning.md)を参照してください。

## <a name="recommanded-features"></a>推奨される機能
### <a name="service-tags"></a>サービス タグ
サービス タグを使用すると、Azure 仮想マシンと Azure 仮想ネットワークに対する Azure サービスへのネットワーク アクセスの制限が簡素化されます。 ネットワーク セキュリティ グループ (NSG) ルールのサービス タグによって、特定の Azure サービスへのトラフィックを許可または拒否できます。 ルールはグローバルに設定することも、Azure リージョンごとに設定することもできます。 Azure では、各タグの基になる IP アドレスが保守管理されます。 ネットワーク セキュリティ グループ (NSG) 用の HDInsight サービス タグは、正常性および管理サービスのための IP アドレスのグループです。 これらのグループを使用すると、セキュリティ規則の作成の複雑さを最小限に抑えることができます。 HDInsight をご利用のお客様は Azure portal、PowerShell、REST API からサービス タグを有効にできます。 詳細については、「[Azure HDInsight のネットワーク セキュリティ グループ (NSG) サービス タグ](./hdinsight-service-tags.md)」を参照してください。
