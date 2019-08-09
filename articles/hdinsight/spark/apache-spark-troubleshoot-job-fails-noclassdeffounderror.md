---
title: Apache Kafka クラスターからデータを読み取る Apache Spark ストリーミング ジョブが Azure HDInsight の NoClassDefFoundError で失敗する
description: Apache Kafka クラスターからデータを読み取る Apache Spark ストリーミング ジョブが Azure HDInsight の NoClassDefFoundError で失敗する
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 986b1dd2e749a0968c744f861feb0ac0bf2376e8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700195"
---
# <a name="scenario-apache-spark-streaming-job-that-reads-data-from-an-apache-kafka-cluster-fails-with-a-noclassdeffounderror-in-azure-hdinsight"></a>シナリオ: Apache Kafka クラスターからデータを読み取る Apache Spark ストリーミング ジョブが Azure HDInsight の NoClassDefFoundError で失敗する

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Spark クラスターでは、Apache Kafka クラスターからデータを読み取る Spark ストリーミング ジョブが実行されます。 Kafka ストリーム圧縮が有効になっている場合、Spark ストリーミング ジョブは失敗します。 この場合、次のエラーが原因で、Spark ストリーミング Yarn アプリ application_1525986016285_0193 が失敗します。

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>原因

このエラーは、実行している Kafka クラスターのバージョンとは異なる `spark-streaming-kafka` jar ファイルのバージョンを指定することによって、発生する場合があります。

たとえば、Kafka クラスター バージョン 0.10.1 を実行している場合、次のコマンドを実行するとエラーが発生します。

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>解決策

`–packages` オプションを指定して Spark-submit コマンドを使用し、spark-streaming-kafka jar ファイルのバージョンが、実行している Kafka クラスターのバージョンと同じであることを確認します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得ることができます。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
