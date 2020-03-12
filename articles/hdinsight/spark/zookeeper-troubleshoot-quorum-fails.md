---
title: Apache ZooKeeper サーバーが Azure HDInsight にクォーラムを形成できない
description: Apache ZooKeeper サーバーが Azure HDInsight にクォーラムを形成できない
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250234"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper サーバーが Azure HDInsight にクォーラムを形成できない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache ZooKeeper サーバーが正常ではない場合の症状として、リソース マネージャー/名前ノードの両方がスタンバイ モードになっていること、単純な HDFS 操作が機能していないこと、`zkFailoverController` が停止されて開始できないこと、Zookeeper エラーによって Yarn/Spark/Livy ジョブが失敗することがあります。 セキュリティで保護された Spark クラスターまたはインタラクティブ Hive クラスターで LLAP デーモンを起動できない場合もあります。 次のようなエラー メッセージが表示される場合があります。

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

Zookeeper サーバーが /var/log/zookeeper/zookeeper-zookeeper-server-\*.out で任意の Zookeeper ホストにログオンすると、次のエラーが表示される場合もあります。

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>原因

スナップショット ファイルの容量が大きいか、スナップショット ファイルが破損している場合、ZooKeeper サーバーはクォーラムの形成に失敗します。そのため、ZooKeeper に関連するサービスが異常になります。 ZooKeeper サーバーではデータ ディレクトリから古いスナップショット ファイルが削除されず、代わりにユーザーが定期的なタスクを実行して ZooKeeper の正常性を維持する必要があります。 詳しくは、[ZooKeeper の強みと制限事項](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)に関するページをご覧ください。

## <a name="resolution"></a>解像度

ZooKeeper データ ディレクトリ `/hadoop/zookeeper/version-2` と `/hadoop/hdinsight-zookeeper/version-2` を調べて、スナップショット ファイルのサイズが大きいかどうかを確認します。 大きなスナップショットが存在する場合は、次の手順を実行します。

1. `/hadoop/zookeeper/version-2` と `/hadoop/hdinsight-zookeeper/version-2` 内のスナップショットをバックアップします。

1. `/hadoop/zookeeper/version-2` と `/hadoop/hdinsight-zookeeper/version-2` 内のスナップショットをクリーンアップします。

1. Apache Ambari UI からすべての ZooKeeper サーバーを再起動します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

- [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

- [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

- さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
