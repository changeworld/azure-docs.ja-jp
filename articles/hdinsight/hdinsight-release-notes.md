---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: 399ba119e7a58a04bd623d8b08c28dd860304b99
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2021
ms.locfileid: "109632225"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight リリース ノート

この記事では、**最近** の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープン ソース分析を行うエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

リリース ノートをサブスクライブする場合は、[この GitHub リポジトリ](https://github.com/hdinsight/release-notes/releases)のリリースをご覧ください。

## <a name="price-correction-for-hdinsight-dv2-virtual-machines"></a>HDInsight Dv2 Virtual Machines の価格修正

2021 年 4 月 25 日、HDInsight 上の Dv2 VM シリーズの価格の誤りが修正されました。 この価格の誤りにより、4 月 25 日より前の一部のお客様の請求書に減額された料金が示されていましたが、これが HDInsight 価格ページと HDInsight 料金計算ツールで公開されていたものと一致するように修正されました。 この価格の誤りは、Dv2 VM を使用している以下のリージョンのお客様に影響を与えました。

- カナダ中部
- カナダ東部
- 東アジア
- 南アフリカ北部
- 東南アジア
- アラブ首長国連邦中部

2021 年 4 月 25 日以降、お客様のアカウントに、Dv2 VM の修正後の金額が反映されます。 変更に先立ち、お客様への通知をサブスクリプション所有者にお送りしました。 料金計算ツール、HDInsight 価格ページ、Azure portal 内の [HDInsight クラスターの作成] ブレードのいずれかを使って、ご自身のリージョン内の Dv2 VM の修正されたコストをご確認いただくことができます。

お客様が他に対処する必要ありません。 価格修正は、2021 年 4 月 25 日以降に指定されたリージョン内でご利用いただいた分にのみ適用されます。それ以前のご利用分には適用されません。 最高のパフォーマンスとコスト効率を確実に実現するために、Dv2 クラスターの価格、VCPU、RAM を確認し、Dv2 の仕様と Ev3 VM を比較して、ご自身のソリューションで新しい VM シリーズのいずれかを利用することでメリットを得られる可能性があるかどうかを検討することをお勧めします。

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

### <a name="hdinsight-interactive-query-only-supports-schedule-based-autoscale"></a>HDInsight Interactive Query がスケジュールベースの自動スケーリングのみをサポート

お客様のシナリオがますます成熟し、多様化する中、Interactive Query (LLAP) の負荷ベースの自動スケーリングに制限がいくつかあることがわかりました。 これらの制限は、LLAP クエリ ダイナミクスの性質、将来の負荷予測の精度の問題、LLAP スケジューラのタスク再配布の問題によるものです。 こうした制限により、自動スケーリングが有効になっていると、LLAP クラスター上でのクエリの実行が遅くなる可能性があります。 このパフォーマンスへの影響は、自動スケーリングのコスト面での利点を上回る可能性があります。

2021 年 5 月 15 日以降、HDInsight 内の Interactive Query ワークロードは、スケジュールベースの自動スケーリングのみをサポートしています。 新しい Interactive Query クラスター上では自動スケーリングを有効にできません。 実行中の既存のクラスターについては、前述の既知の制限付きで引き続き実行できます。 

Microsoft では、LLAP のスケジュールベースの自動スケーリングに移行することを推奨しています。  Grafana Hive ダッシュボードを使用すると、クラスターの現在の使用パターンを分析できます。 詳細については、「[Azure HDInsight クラスターを自動的にスケール調整する](hdinsight-autoscale-clusters.md)」を参照してください。 

### <a name="os-version-upgrade"></a>OS バージョンのアップグレード
現在、HDInsight クラスターは Ubuntu 16.04 LTS で実行されています。 [Ubuntu のリリース サイクル](https://ubuntu.com/about/release-cycle)に関する記事で言及されているように、Ubuntu 16.04 カーネルは、2021 年 4 月にサポート終了 (EOL) となります。 2021 年 5 月から、Ubuntu 18.04 で実行される新しい HDInsight 4.0 クラスター イメージのロールアウトが開始されます。 新しく作成された HDInsight 4.0 クラスターは、使用可能になった後、既定では Ubuntu 18.04 で実行されます。 Ubuntu 16.04 上の既存のクラスターは、完全サポートでそのまま実行されます。

HDInsight 3.6 は、Ubuntu 16.04 で引き続き実行されます。 2021 年 6 月 30 日で標準サポートが終了し、2021 年 7 月 1 日から Basic サポートに変更されます。 日付とサポート オプションの詳細については、「[Azure HDInsight のバージョン](./hdinsight-component-versioning.md#supported-hdinsight-versions)」を参照してください。 Ubuntu 18.04 は、HDInsight 3.6 ではサポートされません。 Ubuntu 18.04 を使用する場合は、クラスターを HDInsight 4.0 に移行する必要があります。 

既存のクラスターを Ubuntu 18.04 に移動する場合は、クラスターを削除して再作成する必要があります。 Ubuntu 18.04 のサポートが利用可能になった後、クラスターの作成または再作成を計画してください。 新しいイメージがすべてのリージョンで利用可能になった後、別の通知が送信されます。

Ubuntu 18.04 仮想マシン (VM) 上のエッジ ノードにデプロイされているスクリプト アクションとカスタム アプリケーションを事前にテストすることを強くお勧めします。 [18.04-LTS で単純な Ubuntu Linux VM を作成](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)し、その後、その VM で [Secure Shell (SSH) キー ペア](../virtual-machines/linux/mac-create-ssh-keys.md#ssh-into-your-vm)を作成および使用して、エッジ ノードにデプロイされているスクリプト アクションとカスタム アプリケーションを実行およびテストできます。

### <a name="disable-stardard_a5-vm-size-as-head-node-for-hdinsgiht-40"></a>HDInsgiht 4.0 のヘッド ノードとしての Stardard_A5 VM サイズの無効化
HDInsight クラスターのヘッド ノードは、クラスターの初期化と管理を担当します。 Standard_A5 VM サイズには、HDInsight 4.0 のヘッド ノードとして信頼性の問題があります。 2021 年 5 月の次回のリリース以降、お客様は、ヘッド ノードとして Standard_A5 VM サイズを使用した新しいクラスターを作成できなくなります。 E2_v3 や E2s_v3 など、他の 2 コア VM は使用できます。 既存のクラスターはそのまま実行されます。 運用 HDInsight クラスターの高可用性と信頼性を確保するために、ヘッド ノードには 4 コアの VM を強くお勧めします。

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
