---
title: Azure HDInsight 上の Apache Hadoop で SCP を使用する
description: このドキュメントでは、ssh および scp コマンドを使用して HDInsight に接続する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 559746a817442602c76ba91f12c195be1d7f3cc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188431"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>Azure HDInsight 上の Apache Hadoop で SCP を使用する

この記事では、HDInsight クラスターを使用してファイルを安全に転送する方法について説明します。

## <a name="copy-files"></a>ファイルのコピー

`scp` ユーティリティを使用すると、クラスター内の個々のノード間でファイルをコピーできます。 たとえば、次のコマンドは、ローカル システムの `test.txt` ディレクトリをプライマリ ヘッド ノードにコピーします。

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

`:` の後にパスが指定されていないため、ファイルは `sshuser` ホーム ディレクトリに配置されます。

次の例は、`test.txt` ファイルをプライマリ ヘッド ノードの `sshuser` ホーム ディレクトリからローカル システムにコピーします。

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp` は、クラスター内の個々のノードのファイル システムだけにアクセスできます。 クラスター用の HDFS と互換性のあるストレージ内のデータにアクセスするために使用することはできません。

SSH セッションから使用するためのリソースをアップロードする必要がある場合は、`scp` を使用します。 たとえば、Python スクリプトをアップロードし、SSH セッションからスクリプトを実行します。

HDFS と互換性のあるストレージに直接データを読み込む方法の詳細については、以下のドキュメントを参照してください。

* [Azure Storage を使用する HDInsight](hdinsight-hadoop-use-blob-storage.md)
* [Azure Data Lake Storage を使用する HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="next-steps"></a>次のステップ

* [HDInsight で SSH を使用する](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [HDInsight でエッジ ノードを使用する](hdinsight-apps-use-edge-node.md#access-an-edge-node)
