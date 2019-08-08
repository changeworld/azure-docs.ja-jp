---
title: Apache Spark ストリーミング ジョブが Azure HDInsight での処理に通常よりも時間がかかる
description: Apache Spark ストリーミング ジョブが Azure HDInsight での処理に通常よりも時間がかかる
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 7c7baaaa5fd08a102b3b55c38fb2c4bf892480c5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679408"
---
# <a name="scenario-apache-spark-streaming-jobs-take-longer-than-usual-to-process-in-azure-hdinsight"></a>シナリオ: Apache Spark ストリーミング ジョブが Azure HDInsight での処理に通常よりも時間がかかる

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときの問題のトラブルシューティングの手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Spark ストリーミング ジョブの一部が低速であるか、通常よりも処理に時間がかかっていることが確認されました。 Spark ストリーミング アプリケーションの場合、メッセージの各バッチは、Spark に送信される 1 つのジョブに対応します。 ジョブの処理に通常 X 秒かかる場合、通常より 2 から 3 分多くかかることがあります。

## <a name="cause"></a>原因

原因の 1 つとして、Apache Kafka プロデューサーが Kafka クラスターへの書き込みを完了するまでに 2 分以上かかることが考えられます。 Kafka の問題をさらにデバッグするために、Kafka プロデューサーを使用してメッセージを送信するログをコードに追加し、それを Kafka クラスターのログと関連付けることができます。

もう 1 つの原因として、WASB への頻繁な読み取りと書き込みが行われることで、後続のマイクロバッチが遅延することが考えられます。 `Filesystem.listStatus` の WASB 実装は、重複を削除するための `O(n!)` アルゴリズムによって非常に遅くなります。 `BlobListItem` から `FileMetadata` および `FileStatus` への余分な変換により、非常に多くのメモリが使用されます。 たとえば、このアルゴリズムは 70 万件のファイルを一覧表示するのに 30 分以上かかります。 そのため、`ListBlobs` がマイクロバッチごとに SparkSQL によって積極的に呼び出される場合、後続のマイクロバッチが遅れ、スケジュールに大幅な遅延が生じる結果となります。 [この修正プログラム](https://issues.apache.org/jira/browse/HADOOP-15547)により問題は修正されますが、これが環境内に存在しない場合、`ListBlobs` の待機時間が長くなります。 また、1 時間ごとにファイルを削除した場合でも、ガベージ コレクション プロセスがまだ完了していないため、バックエンドの一覧取得においてすべての行 (削除済みのものを含む) を反復処理する必要があります。 修正プログラムによって問題の一部が解決される場合がありますが、ガベージ コレクションの問題によって、バッチのストリーム処理で遅延が発生する可能性があります。

## <a name="resolution"></a>解決策

[HADOOP-15547](https://issues.apache.org/jira/browse/HADOOP-15547) 修正プログラムを適用します。 これが不可能な場合は、HDFS をチェック ポイントの場所として使用できます。 `checkpointDirectory` を `hdfs://mycluster/checkpoint` のように設定します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上させます。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート要求を送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
