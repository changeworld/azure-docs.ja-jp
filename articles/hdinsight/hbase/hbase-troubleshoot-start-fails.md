---
title: Azure HDInsight で Apache HBase Master を開始できない
description: Azure HDInsight で Apache HBase Master (HMaster) を開始できない
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935454"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Azure HDInsight で Apache HBase Master (HMaster) を開始できない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="scenario-atomic-renaming-failure"></a>シナリオ: Atomic renaming failure (アトミックな名前変更の失敗)

### <a name="issue"></a>問題

スタートアップ プロセス中に予期しないファイルが識別されました。

### <a name="cause"></a>原因

HMaster は、起動プロセス中、データをスクラッチ (.tmp) フォルダーからデータ フォルダーに移動するなど、多くの初期化手順を実行します。 また、HMaster は WAL (先書きログ) フォルダーを参照して、応答しないリージョン サーバーがあるかどうかも確認します。 これらのすべての状況の間に、これらのフォルダーに対して基本的な `list` コマンドが実行されます。 いずれかのフォルダーに予期しないファイルが見つかった場合は、例外がスローされて起動に失敗します。

### <a name="resolution"></a>解決策

このような状況の場合は、呼び出し履歴をチェックし、どのフォルダーが問題の原因になっているかを調べます (WAL フォルダーか .tmp フォルダーかなど)。 次に、Cloud Explorer または hdfs コマンドを使用して問題のファイルを特定します。 問題のファイルは、通常、`*-renamePending.json` ファイル (WASB ドライバーでアトミックな名前変更操作を実装するために使用されるジャーナル ファイル) です。 この実装のバグが原因で、プロセスのクラッシュが発生した場合にそのようなファイルが残されることがあります。 このファイルは、Cloud Explorer を使用して強制的に削除してください。 また、この場所に $ の特性を持つ一時ファイルが存在する場合もあります。 このファイルは、Cloud Explorer では表示できず、hdfs の `ls` コマンドを使用してのみ表示できます。 hdfs コマンド `hdfs dfs -rm //\$\$\$.\$\$\$` を使用すると、このファイルを削除することができます。

問題のファイルが削除されると、HMaster はすぐに起動します。

---

## <a name="scenario-no-server-address-listed"></a>シナリオ: サーバー アドレスがリストされない

### <a name="issue"></a>問題

HMaster ログには、"No server address listed in hbase: meta for region xxx (リージョン xxx の hbase: meta にサーバー アドレスがリストされていません)" のようなエラー メッセージが表示されます。

### <a name="cause"></a>原因

HBase を再起動した後、HMaster を初期化できませんでした。

### <a name="resolution"></a>解決策

1. HBase シェルで次のコマンドを実行します (適宜、実際の値に置き換えてください)。

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. hbase: namespace のエントリを削除します (hbase: namespace テーブルをスキャンする際に同じエラーが報告される場合があるため)。

1. Ambari UI からアクティブ HMaster を再起動し、HBase を実行状態にします。

1. HBase シェルで次のコマンドを実行し、オフライン状態のすべてのテーブルを立ち上げます。

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>シナリオ: java.io.IOException:タイムアウト

### <a name="issue"></a>問題

HMaster は、`java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned` のような致命的例外によってタイムアウトになりました。

### <a name="cause"></a>原因

タイムアウトは、HMaster の既知の欠陥です。 一般的なクラスター スタートアップ タスクは時間がかかることがあります。 namespace テーブルがまだ割り当てられていない場合、HMaster はシャットダウンします。 時間のかかるスタートアップ タスクが発生するのは、フラッシュされていないデータが大量に存在し、5 分のタイムアウトでは間に合わない場合です。

### <a name="resolution"></a>解決策

1. Ambari UI にアクセスして、[HBase] -> [Configs]/(構成/) に移動し、カスタム `hbase-site.xml` に次の設定を追加します。

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. 必要なサービスを再起動します (主に HMaster サービス。場合によっては他の HBase サービスも含む)。

---

## <a name="scenario-frequent-regionserver-restarts"></a>シナリオ: Regionserver の頻繁な再起動

### <a name="issue"></a>問題

ノードが定期的に再起動します。 Regionserver ログに、次のようなエントリが表示される場合があります。

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>原因

Regionserver JVM GC の長い一時停止。 一時停止により、Regionserver が応答しなくなり、zk セッション タイムアウトの 40 秒以内に HMaster にハートビートを送信できなくなります。 HMaster は、Regionserver が停止していると考えて、Regionserver を中止して再起動します。

### <a name="resolution"></a>解決策

Zookeeper のセッション タイムアウトを変更します。hbase サイトの設定 `zookeeper.session.timeout` だけでなく、Zookeeper の zoo.cfg 設定 `maxSessionTimeout` も変更する必要があります。

1. Ambari UI にアクセスし、 **[HBase] -> [Configs]\(構成\) -> [Settings]\(設定\)** に移動し、[Timeouts]\(タイムアウト\) セクションで Zookeeper セッション タイムアウトの値を変更します。

1. Ambari UI にアクセスし、 **[Zookeeper] -> [Configs]\(構成\) -> [Custom]\(カスタム\)** zoo.cfg に移動し、次の設定を変更します。 値が hbase の `zookeeper.session.timeout` と同じであることを確認します。

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. 必要なサービスを再起動します。

---

## <a name="scenario-log-splitting-failure"></a>シナリオ: ログの分割エラー

### <a name="issue"></a>問題

HMasters が HBase クラスターで起動できませんでした。

### <a name="cause"></a>原因

セカンダリ ストレージ アカウントに対して HDFS と HBase の設定が正しく構成されていません。

### <a name="resolution"></a>解決策

hbase.rootdir: wasb://@.blob.core.windows.net/hbase を設定し、Ambari でサービスを再起動します。

---

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティを適切なリソース (回答、サポート、エキスパートなど) に結び付けます。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
