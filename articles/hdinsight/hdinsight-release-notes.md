---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/02/2021
ms.openlocfilehash: 61b32852de0cb15db3860b72561f87e56db8f40d
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111903853"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight リリース ノート

この記事では、**最近** の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープン ソース分析を行うエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

リリース ノートをサブスクライブする場合は、[この GitHub リポジトリ](https://github.com/hdinsight/release-notes/releases)のリリースをご覧ください。


## <a name="release-date-06022021"></a>リリース日: 2021 年 6 月 2 日

このリリースは HDInsight 3.6 と HDInsight 4.0 の両方に適用されます。 HDInsight リリースは、数日以内にすべてのリージョンでご利用になれます。 ここのリリース日は、最初のリージョンのリリース日です。 以下の変更が見られない場合は、お客様のリージョンで数日以内にリリースがライブになるまでお待ちください。

このリリースの OS バージョンは次のとおりです。
- HDInsight 3.6: Ubuntu 16.04.7 LTS
- HDInsight 4.0: Ubuntu 18.04.5 LTS

## <a name="new-features"></a>新機能
### <a name="os-version-upgrade"></a>OS バージョンのアップグレード
[Ubuntu のリリース サイクル](https://ubuntu.com/about/release-cycle)に関する記事で言及されているように、Ubuntu 16.04 カーネルは、2021 年 4 月にサポート終了 (EOL) となります。 このリリースでは、Ubuntu 18.04 で実行される新しい HDInsight 4.0 クラスター イメージのロールアウトが開始されました。 新しく作成された HDInsight 4.0 クラスターは、使用可能になった後、既定では Ubuntu 18.04 で実行されます。 Ubuntu 16.04 上の既存のクラスターは、完全サポートでそのまま実行されます。

HDInsight 3.6 は、Ubuntu 16.04 で引き続き実行されます。 2021 年 7 月 1 日からは、Basic サポートに (Standard サポートから) 変更されます。 日付とサポート オプションの詳細については、「[Azure HDInsight のバージョン](./hdinsight-component-versioning.md#supported-hdinsight-versions)」を参照してください。 Ubuntu 18.04 は、HDInsight 3.6 ではサポートされません。 Ubuntu 18.04 を使用する場合は、クラスターを HDInsight 4.0 に移行する必要があります。 

既存の HDInsight 4.0 クラスターを Ubuntu 18.04 に移動する場合は、クラスターを削除して再作成する必要があります。 Ubuntu 18.04 のサポートが利用可能になった後、クラスターの作成または再作成を計画してください。

新しいクラスターを作成した後は、クラスターに SSH で接続し、`sudo lsb_release -a` を実行して Ubuntu 18.04 で実行されることを確認できます。 運用環境に移行する前に、まずテスト サブスクリプションでアプリケーションをテストすることをお勧めします。 [HDInsight Ubuntu 18.04 での更新の詳細を確認してください](./hdinsight-ubuntu-1804-qa.md)。

### <a name="scaling-optimizations-on-hbase-accelerated-writes-clusters"></a>HBase 高速書き込みクラスターでのスケーリングの最適化
HDInsight では、HBase 高速書き込み対応クラスターのスケーリングに関して、いくつかの機能強化と最適化が行われました。 [HBase 高速書き込みの詳細を確認してください](./hbase/apache-hbase-accelerated-writes.md)。

## <a name="deprecation"></a>非推奨
このリリースに非推奨はありません。

## <a name="behavior-changes"></a>動作の変更
### <a name="disable-stardard_a5-vm-size-as-head-node-for-hdinsight-40"></a>HDInsight 4.0 用のヘッド ノードとして Stardard_A5 VM サイズが無効になった
HDInsight クラスターのヘッド ノードは、クラスターの初期化と管理を担当します。 Standard_A5 VM サイズには、HDInsight 4.0 のヘッド ノードとして信頼性の問題があります。 このリリース以降、お客様は、ヘッド ノードとして Standard_A5 VM サイズを使用して、新しいクラスターを作成できなくなります。 E2_v3 や E2s_v3 など、他の 2 コア VM は使用できます。 既存のクラスターはそのまま実行されます。 運用 HDInsight クラスターの高可用性と信頼性を確保するために、ヘッド ノードには 4 コアの VM を強くお勧めします。

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットで実行されているクラスターでのネットワーク インターフェイス リソースの非表示
HDInsight は Azure 仮想マシン スケール セットに段階的に移行しています。 仮想マシンのネットワーク インターフェイスは、Azure 仮想マシン スケール セットを使用するクラスターの顧客に対しては表示されなくなりました。

## <a name="upcoming-changes"></a>今後の変更
今後のリリースでは、次の変更が行われます。

### <a name="hdinsight-interactive-query-only-supports-schedule-based-autoscale"></a>HDInsight Interactive Query がスケジュールベースの自動スケーリングのみをサポート

お客様のシナリオがますます成熟し、多様化する中、Interactive Query (LLAP) の負荷ベースの自動スケーリングに制限がいくつかあることがわかりました。 これらの制限は、LLAP クエリ ダイナミクスの性質、将来の負荷予測の精度の問題、LLAP スケジューラのタスク再配布の問題によるものです。 こうした制限により、自動スケーリングが有効になっていると、LLAP クラスター上でのクエリの実行が遅くなる可能性があります。 パフォーマンスへの影響は、自動スケーリングのコスト面での利点を上回る可能性があります。

2021 年 7 月以降、HDInsight 内の Interactive Query ワークロードでは、スケジュールベースの自動スケーリングのみがサポートされます。 新しい Interactive Query クラスター上では自動スケーリングを有効にできません。 実行中の既存のクラスターについては、前述の既知の制限付きで引き続き実行できます。 

Microsoft では、LLAP のスケジュールベースの自動スケーリングに移行することを推奨しています。  Grafana Hive ダッシュボードを使用すると、クラスターの現在の使用パターンを分析できます。 詳細については、「[Azure HDInsight クラスターを自動的にスケール調整する](hdinsight-autoscale-clusters.md)」を参照してください。 

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>HDInsight 3.6 の Basic サポート (2021 年 7 月 1 日以降)
2021 年 7 月 1 日以降、Microsoft では特定の種類の HDInsight 3.6 クラスターに対する [Basic サポート](hdinsight-component-versioning.md#support-options-for-hdinsight-versions)を提供します。 Basic サポート プランは 2022 年 4 月 3 日まで利用できます。 Basic サポートには 2021 年 7 月 1 日から自動的に登録されます。 オプトインするために必要な操作はありません。 Basic サポートに含まれるクラスターの種類については、[こちらのドキュメント](hdinsight-36-component-versioning.md)を参照してください。 

HDInsight 3.6 で新しいソリューションを構築することはお勧めしません。既存の 3.6 の環境に対する変更を凍結してください。 [クラスターを HDInsight 4.0 に移行](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40)することをお勧めします。 HDInsight 4.0 の新機能については、[こちら](hdinsight-version-release.md#whats-new-in-hdinsight-40)を参照してください。

### <a name="vm-host-naming-will-be-changed-on-july-1-2021"></a>VM ホストの名前付けが、2021 年 7 月 1 日に変更されます
HDInsight では、Azure 仮想マシンを使用してクラスターをプロビジョニングするようになりました。 このサービスは [Azure 仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)に段階的に移行しています。 この移行により、クラスター ホスト名の FQDN 名の形式が変更され、ホスト名に含まれる番号の順番は保証されなくなります。 各ノードの FQDN 名を取得する場合は、「[クラスター ノードのホスト名を検索する](./find-host-name.md)」を参照してください。

### <a name="move-to-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットに移行する
HDInsight では、Azure 仮想マシンを使用してクラスターをプロビジョニングするようになりました。 このサービスは、[Azure 仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)に段階的に移行します。 このプロセス全体に数か月かかる可能性があります。 リージョンとサブスクリプションが移行された後は、新しく作成された HDInsight クラスターは、お客様が操作することなく、仮想マシン スケール セット上で動作するようになります。 破壊的変更は想定されていません。

## <a name="bug-fixes"></a>バグの修正
HDInsight は引き続き、クラスターの信頼性とパフォーマンスの向上を実現します。 

## <a name="component-version-change"></a>コンポーネントのバージョンの変更
HDInsight 4.0 と HDInsight 3.6 の現在のコンポーネント バージョンについては、[こちらのドキュメント](./hdinsight-component-versioning.md)を参照してください。
