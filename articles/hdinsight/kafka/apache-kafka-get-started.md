---
title: "Apache Kafka の開始 - Azure HDInsight | Microsoft Docs"
description: "Azure HDInsight で Apache Kafka クラスターを作成する方法について説明します。 トピック、サブスクライバー、コンシューマーの作成方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: e00ab06a26d60dd5beca11362df58f35812491d9
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>HDInsight での Apache Kafka の開始

Azure HDInsight で [Apache Kafka](https://kafka.apache.org) クラスターを作成および使用する方法について説明します。 Apache Kafka は、HDInsight で利用できるオープンソースの分散ストリーミング プラットフォームです。 発行/サブスクライブ メッセージ キューと同様の機能を備えているため、メッセージ ブローカーとして多く使われています。 Kafka は、メッセージング、アクティビティの追跡、ストリームの集計、またはデータ変換のために Apache Spark および Apache Storm と共によく使用されます。

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Kafka クラスターの作成

HDInsight クラスターで Kafka を作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、**[+ リソースの作成]**、**[データ + 分析]**、**[HDInsight]** の順に選択します。
   
    ![HDInsight クラスターの作成](./media/apache-kafka-get-started/create-hdinsight.png)

2. **[基本]** から次の情報を入力します。

    * **[クラスター名]**: HDInsight クラスターの名前。 この名前は一意である必要があります。
    * **[サブスクリプション]**: 使用するサブスクリプションを選択します。
    * **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]**: HTTPS 経由でクラスターにアクセスする場合のログイン。 これらの資格情報を使用して、Ambari Web UI や REST API などのサービスにアクセスします。
    * **[Secure Shell (SSH) username (Secure Shell (SSH) ユーザー名)]**: SSH 経由でクラスターにアクセスする際に使用されるログイン。 既定では、このパスワードは、クラスター ログイン パスワードと同じです。
    * **[リソース グループ]**: クラスターが作成されるリソース グループ。
    * **[場所]**: クラスターが作成される Azure リージョン。

        > [!IMPORTANT]
        > データの高可用性を実現するために、__3 つの障害ドメイン__を含む場所 (リージョン) を選択することをお勧めします。 詳細については、「[データの高可用性](#data-high-availability)」のセクションを参照してください。
   
 ![サブスクリプションを選択します。](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. **[クラスターの種類]** を選択し、**[クラスターの構成]** から次の値を設定します。
   
    * **[クラスターの種類]**: Kafka
    * **[バージョン]**: Kafka 0.10.0 (HDI 3.6)

    最後に、**[選択]** ボタンをクリックして設定を保存します。
     
 ![クラスターの種類の選択](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

4. クラスターの種類を選択したら、__[選択]__ ボタンを使用してクラスターの種類を設定します。 次に、__[次へ]__ ボタンを使用して、基本的な構成を完了します。

5. **[ストレージ]** で、ストレージ アカウントを選択または作成します。 このドキュメントの手順では、他のフィールドを既定値のままにします。 __[次へ]__ ボタンを使用して、ストレージの構成を保存します。

    ![HDInsight のストレージ アカウント設定](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

6. __[アプリケーション (オプション)]__ で __[次へ]__ を選んで続行します。 この例ではアプリケーションは必要ありません。

7. __[クラスター サイズ]__ で __[次へ]__ を選んで続行します。

    > [!WARNING]
    > HDInsight で Kafka の可用性を保証するには、クラスターに少なくとも 3 つのワーカー ノードが必要です。 詳細については、「[データの高可用性](#data-high-availability)」のセクションを参照してください。

    ![Kafka のクラスター サイズの設定](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > **ワーカー ノード エントリごとのディスクの数**は、HDInsight での Kafka のスケーラビリティを構成します。 HDInsight 上の Kafka は、クラスターの仮想マシンのローカル ディスクを使います。 Kafka は I/O が多いため、[Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) を使ってノードごとに高いスループットと多くの記憶域を提供します。 管理ディスクの種類は、__Standard__ (HDD) または __Premium__ (SSD) です。 Premium ディスクは、DS および GS シリーズの VM で使われます。 他の種類の VM はすべて Standard を使います。

8. __[詳細設定]__ で __[次へ]__ を選んで続行します。

9. **[概要]** でクラスターの構成を確認します。 間違った設定を変更するには、__[編集]__ リンクを使用します。 最後に、__[作成]__ ボタンを使用してクラスターを作成します。
   
    ![クラスター構成の概要](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > クラスターの作成には最大で 20 分かかります。

## <a name="connect-to-the-cluster"></a>クラスターへの接続

> [!IMPORTANT]
> 以下の手順を実行するときは、SSH クライアントを使用する必要があります。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

SSH を使用してクラスターに接続するには、SSH ユーザー アカウント名とクラスターの名前を入力する必要があります。 次の例では、`sshuser` と `clustername` を実際のアカウント名とクラスター名に置き換えてください。

```ssh sshuser@clustername-ssh.azurehdinsight.net```

メッセージが表示されたら、SSH アカウントに使用したパスワードを入力します。

詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a id="getkafkainfo"></a>Zookeeper およびブローカーのホスト情報を取得する

Kafka を使用する場合、*Zookeeper* ホストと "*ブローカー*" ホストを知っている必要があります。 これらのホストは、Kafka の API や、Kafka に付属するユーティリティの多くで使用されます。

ホスト情報が含まれる環境変数を作成するには、次の手順に従います。

1. クラスターに SSH で接続してから、次のコマンドを使用して `jq` ユーティリティをインストールします。 このユーティリティは JSON ドキュメントを解析するためのものであり、ブローカー ホストの情報取得するのに役立ちます。
   
    ```bash
    sudo apt -y install jq
    ```

2. 環境変数をクラスター名に設定するには、次のコマンドを使用します。

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

3. Zookeeper ホスト情報で環境変数を設定するには、次のコマンドを使用します。

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    プロンプトが表示されたら、クラスターのログイン アカウント (管理者) のパスワードを入力してください。

4. 環境変数が正しく設定されていることを確認するには、次のコマンドを使用します。

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    このコマンドでは、次のテキストのような情報が返されます。

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Kafka ブローカー ホスト情報で環境変数を設定するには、次のコマンドを使用します。

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    プロンプトが表示されたら、クラスターのログイン アカウント (管理者) のパスワードを入力してください。

6. 環境変数が正しく設定されていることを確認するには、次のコマンドを使用します。

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    このコマンドでは、次のテキストのような情報が返されます。
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
> [!WARNING]
> このセッションで返された情報は、常に正しいわけではありません。 クラスターをスケーリングすると、新しいブローカーが追加されるか削除されます。 障害が発生しノードが交換された場合、そのノードのホスト名が変わる可能性があります。
>
> 有効な情報を得るために、Zookeeper ホストとブローカー ホストの情報は、使用する直前に取得するようにしてください。

## <a name="create-a-topic"></a>トピックを作成する

Kafka では、*トピック* というカテゴリ内にデータのストリームを格納します。 クラスターのヘッド ノードに SSH で接続し、Kafka に付属のスクリプトを使用してトピックを作成します。

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

このコマンドを使用すると、`$KAFKAZKHOSTS` に格納されているホスト情報を使用して Zookeeper に接続されます。 次に、**test** という名前の Kafka トピックが作成されます。 トピックを一覧表示する次のスクリプトを使用して、トピックが作成されたことを確認できます。

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

このコマンドの出力では、クラスター上の Kafka トピックが一覧表示されます。

## <a name="produce-and-consume-records"></a>レコードの生成および消費

Kafka では、トピック内に*レコード*が格納されます。 レコードは、*プロデューサー*によって生成され、*コンシューマー*によって消費されます。 プロデューサーから Kafka の*ブローカー*にレコードが生成されます。 HDInsight クラスターの各ワーカー ノードが、Kafka のブローカーです。

先ほど作成した test トピックにレコードを格納し、コンシューマーを使用してそれらを読み取るには、次の手順に従います。

1. SSH セッションで、Kafka に付属のスクリプトを使用してトピックにレコードを書き込みます。
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    このコマンドの後ろには空の行があります。

2. 空の行にテキスト メッセージを入力して、Enter キーを押します。 このようにメッセージをいくつか入力してから、**Ctrl + C** キーを使用して通常のプロンプトに戻ります。 各行が、個別のレコードとして Kafka トピックに送信されます。

3. Kafka に付属のスクリプトを使用して、トピックからレコードを読み取ります。
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    このコマンドにより、レコードがトピックから取得され、表示されます。 `--from-beginning` を使用することで、すべてのレコードが取得されるように、コンシューマーにストリームの先頭から開始するように指示しています。

    > [!NOTE]
    > 以前のバージョンの Kafka を使っている場合、`--bootstrap-server $KAFKABROKERS` を `--zookeeper $KAFKAZKHOSTS` に置き換えます。

4. __Ctrl+C__ キーを使用してコンシューマーを停止します。

プロデューサーとコンシューマーをプログラムから作成することもできます。 この API の使用例については、[HDInsight における Kafka のプロデューサー API とコンシューマー API](apache-kafka-producer-consumer-api.md) に関するドキュメントを参照してください。

## <a name="data-high-availability"></a>データの高可用性

各 Azure リージョン (場所) は "_障害ドメイン_" を提供します。 障害ドメインとは、Azure データ センター内にある基になるハードウェアの論理的なグループです。 各障害ドメインは、一般的な電源とネットワーク スイッチを共有します。 HDInsight クラスター内のノードを実装する仮想マシンと管理ディスクは、これらの障害ドメインに分散されます。 このアーキテクチャにより、物理的なハードウェア障害の潜在的な影響が制限されます。

リージョン内の障害ドメインの数については、[Linux 仮想マシンの可用性](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)に関するトピックを参照してください。

> [!IMPORTANT]
> 可能な場合、3 つの障害ドメインを含む Azure リージョンを使用し、レプリケーション係数を 3 にしてトピックを作成してください。

障害ドメインが 2 つだけのリージョンを使用する場合、レプリケーション係数として 4 を使用して、レプリカを 2 つの障害ドメインに均等に分散します。

### <a name="kafka-and-fault-domains"></a>Kafka と障害ドメイン

Kafka は、障害ドメインを認識しません。 トピック用にパーティションのレプリカを作成すると、レプリカが適切に分散されず、高可用性が実現しない場合があります。 高可用性を確保するには、[Kafka パーティション再調整ツール](https://github.com/hdinsight/hdinsight-kafka-tools)を使用してください。 このツールは、SSH セッションから Kafka クラスターのヘッド ノードに対して実行する必要があります。

Kafka データの最高の可用性を確保するには、次のときにトピックのパーティションのレプリカを再調整する必要があります。

* 新しいトピックまたはパーティションの作成

* クラスターのスケールアップ

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](../hdinsight-administer-use-portal-linux.md#create-clusters)」を参照してください。

## <a name="next-steps"></a>次の手順

このドキュメントでは、HDInsight で Apache Kafka を使用する際の基本事項を学習しました。 次の各ドキュメントを参考に、Kafka の使用の詳細を確認してください。

* [Kafka ログの分析](apache-kafka-log-analytics-operations-management.md)
* [Kafka クラスター間でデータをレプリケートする](apache-kafka-mirroring.md)
* [HDInsight における Kafka Producer API と Consumer API](apache-kafka-producer-consumer-api.md)
* [HDInsight における Kafka Streams API](apache-kafka-streams-api.md)
* [HDInsight 上で Kafka を用いて Apache Spark ストリーミング (DStream) を使用する](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight 上で Kafka を用いて Apache Spark 構造化ストリーミングを使用する](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Apache Spark 構造化ストリーミングを使って HDInsight 上の Kafka から Cosmos DB にデータを移動する](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [HDInsight での Kafka に Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)
* [Azure Virtual Network 経由で Kafka に接続する](apache-kafka-connect-vpn-gateway.md)
* [Azure Container Service での Kafka の使用](apache-kafka-azure-container-services.md)
* [Azure Function App での Kafka の使用](apache-kafka-azure-functions.md)
