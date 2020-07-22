---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a4faab9ac6d5e1c39c1120e09dae792b95892d60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564407"
---
# <a name="release-notes"></a>リリース ノート

この記事では、**最近**の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープン ソース分析を行うエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

## <a name="release-date-06112020"></a>リリース日: 2020 年 6 月 11 日

このリリースは、HDInsight 3.6 と4.0 の両方に適用されます。 HDInsight リリースは、数日以内にすべてのリージョンでご利用になれます。 ここのリリース日は、最初のリージョンのリリース日です。 以下の変更が見られない場合は、お客様のリージョンで数日以内にリリースがライブになるまでお待ちください。

## <a name="new-features"></a>新機能
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットへの移行
HDInsight では、Azure 仮想マシンを使用してクラスターをプロビジョニングするようになりました。 このリリースから、新しく作成された HDInsight クラスターでは、Azure 仮想マシン スケール セットの使用が開始されます。 変更は段階的にロールアウト中です。 破壊的変更は予期されていません。 [Azure 仮想マシン スケール セット](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)についてご確認ください。
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>HDInsight クラスターでの VM の再起動
このリリースでは、応答しないノードを再起動するために、HDInsight クラスターでの VM の再起動がサポートされます。 現時点では API からのみ実行可能で、PowerShell と CLI でのサポートが予定されています。 API の詳細については、[このドキュメント](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json)を参照してください。
 
## <a name="deprecation"></a>非推奨
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>HDInsight 3.6 Spark クラスターでの Spark 2.1 と 2.2 の非推奨化
2020 年 7 月 1 日以降、HDInsight 3.6 で Spark 2.1 と 2.2 を使用して新しい Spark クラスターを作成することはできません。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 システムやサポートが中断する可能性を回避するため、2020 年 6月 30 日までに HDInight 3.6 で Spark 2.3 に移行することを検討してください。
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>HDInsight 4.0 Spark クラスターでの Spark 2.3 の非推奨化
2020 年 7 月 1 日以降、HDInsight 4.0 で Spark 2.3 を使用して新しい Spark クラスターを作成することはできません。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 システムやサポートが中断する可能性を回避するため、2020 年 6月 30 日までに HDInight 4.0 で Spark 2.4 に移行することを検討してください。
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>HDInsight 4.0 Kafka クラスターの Kafka 1.1 の廃止
2020 年 7 月 1 日以降、HDInsight 4.0 で Kafka 1.1 を使用して新しい Kafka クラスターを作成することはできません。 既存のクラスターはそのまま実行され、Microsoft からのサポートはありません。 システムやサポートが中断される可能性を回避するため、2020 年 6 月 30 日までに HDInsight 4.0 で Kafka 2.1 に移行することを検討してください。
 
## <a name="behavior-changes"></a>動作の変更
### <a name="esp-spark-cluster-head-node-size-change"></a>ESP Spark クラスターのヘッド ノード サイズの変更 
ESP Spark クラスターに対して許される最小ノード サイズが、Standard_D13_V2 に変更されています。 コア数やメモリが少ないヘッド ノードとしての VM は、CPU とメモリの容量が比較的少ないため、ESP クラスターの問題の原因となる可能性があります。 リリース以降、ESP Spark クラスターのヘッド ノードとしては Standard_D13_V2 および Standard_E16_V3 よりも上位の SKU を使用してください。
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>ヘッド ノードには最低 4 コアの VM が必要 
HDInsight クラスターの高可用性と信頼性を確保するには、ヘッド ノード用に少なくとも 4 コアの VM が必要です。 2020 年 4 月 6 日以降、お客様は新しい HDInsight クラスターのヘッド ノードに、4 コア以上の VM のみを選択することができます。 既存のクラスターは正常に実行されます。 
 
### <a name="cluster-worker-node-provisioning-change"></a>クラスターのワーカー ノードに関するプロビジョニングの変更
ワーカー ノードのうち 80% の準備ができると、クラスターは**運用可能**ステージに入ります。 このステージでは、スクリプトやジョブの実行など、データ プレーンのすべての操作を行えます。 しかし、スケールアップやスケールダウンなど、コントロール プレーンの操作を行うことはできません。 削除のみがサポートされます。
 
**運用可能**ステージになってから、クラスターは残り 20% のワーカー ノードのために、さらに 60 分待機します。 この 60 分が経過すると、まだすべてのワーカー ノードを使用できない場合でも、クラスターは**実行中**ステージに移行します。 いったんクラスターが**実行中**ステージに入れば、クラスターを通常どおり使用できます。 スケールアップやスケールダウンなどのコントロール プレーンの操作と、スクリプトやジョブの実行などのデータ プレーンの操作の両方が受け付けられます。 要求されたワーカー ノードの一部を使用できない場合、クラスターは一部成功としてマークされます。 課金されるのは、正常にデプロイされたノードに対してです。 
 
### <a name="create-new-service-principal-through-hdinsight"></a>HDInsight を通した新しいサービス プリンシパルの作成
以前はクラスターの作成時に、Azure portal の接続されている ADLS Gen 1 アカウントにアクセスする新しいサービス プリンシパルを作成できました。 2020 年 6 月 15 日以降は、HDInsight の作成ワークフローで新しいサービス プリンシパルを作成できず、既存のサービス プリンシパルのみがサポートされます。 [Azure Active Directory を使用してサービス プリンシパルと証明書を作成する](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)ことに関するページを参照してください。

### <a name="time-out-for-script-actions-with-cluster-creation"></a>クラスター作成でのスクリプト アクションのタイムアウト
HDInsight では、クラスター作成でのスクリプト アクションの実行がサポートされています。 このリリースからは、クラスター作成でのすべてのスクリプト アクションが **60 分**以内に完了する必要があります。そうでない場合はタイムアウトします。実行中のクラスターに送信されたスクリプト アクションは影響を受けません。 詳細については[ここ](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#script-action-in-the-cluster-creation-process)を参照してください。
 
## <a name="upcoming-changes"></a>今後の変更
注意を払う必要があり、近く予定されている破壊的変更はありません。
 
## <a name="bug-fixes"></a>バグの修正
HDInsight は引き続き、クラスターの信頼性とパフォーマンスの向上を実現します。 
 
## <a name="component-version-change"></a>コンポーネントのバージョンの変更
### <a name="hbase-20-to-216"></a>HBase 2.0 から 2.1.6
HBase のバージョンが、バージョン 2.0 から 2.1.6 にアップグレードされています。
 
### <a name="spark-240-to-244"></a>Spark 2.4.0 から 2.4.4
Spark のバージョンが、バージョン 2.4.0 から 2.4.4 にアップグレードされています。
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0 から 2.1.1
Kafka のバージョンが、バージョン 2.1.0 から 2.1.1 にアップグレードされています。
 
HDInsight 4.0 と HDInsight 3.6 の現在のコンポーネント バージョンについては、[このドキュメント](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)で確認できます

## <a name="known-issues"></a>既知の問題

### <a name="hive-warehouse-connector-issue"></a>Hive Warehouse Connector の問題
このリリースでは、Hive Warehouse Connector の問題があります。 次のリリースに修正プログラムが含められる予定です。 このリリースより前に作成された既存のクラスターは、影響を受けません。 可能であれば、クラスターの削除と再作成を避けてください。 この問題についてさらに支援が必要な場合は、サポート チケットを開いてください。
