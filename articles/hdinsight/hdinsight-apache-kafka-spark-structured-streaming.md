---
title: Kafka での Apache Spark 構造化ストリーミング - Azure HDInsight | Microsoft Docs
description: Apache Spark ストリーミング (DStreams) を使って Apache Kafka 内外でデータを取得する方法について説明します。 この例では、Jupyter Notebook を使用して HDInsight 上で Spark からデータをストリームします。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2018
ms.author: larryfr
ms.openlocfilehash: 49c13bbea537d7de60ecf509bc28675191c0b34d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="use-spark-structured-streaming-with-kafka-on-hdinsight"></a>HDInsight 上で Kafka を用いて Spark 構造化ストリーミングを使用する

Spark 構造化ストリーミングを使って、Azure HDInsight 上で Apache Kafka からデータを読み込む方法を説明します。

Spark 構造化ストリーミングは、Spark SQL に組み込まれたストリーミング処理エンジンであり、 静的データに対してバッチ計算と同様にストリーミング計算を表現できるようになります。 構造化ストリーミングの詳細については、「[Structured Streaming Programming Guide [Alpha]](http://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html)」(構造化ストリーミングのプログラミング ガイド [アルファ]) をご覧ください。

> [!IMPORTANT]
> この例では、HDInsight 3.6 上で Spark 2.2 を使用します。
>
> このドキュメントの手順では、HDInsight の Spark クラスターと HDInsight の Kafka クラスターの両方を含む Azure リソース グループを作成します。 これらのクラスターは両方とも、Spark クラスターが Kafka クラスターと直接通信できるように、Azure Virtual Network 内に配置します。
>
> このドキュメントの手順を完了したら、余分に課金されないようにするためにクラスターは削除してください。

## <a name="create-the-clusters"></a>クラスターの作成

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka ブローカーへのアクセスは提供されていません。 Kafka を使用するものは、すべて同じ Azure 仮想ネットワークに属している必要があります。 このチュートリアルでは、Kafka クラスターと Spark クラスターの両方を同じ Azure 仮想ネットワーク内に配置します。 

次の図に、Spark と Kafka の間の通信フローを示します。

![Azure 仮想ネットワークにおける Spark クラスターと Kafka クラスターの図](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka サービスは、仮想ネットワーク内の通信に制限されます。 SSH や Ambari など、クラスター上の他のサービスは、インターネット経由でアクセスできます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

参考として、次の手順では、Azure Resource Manager テンプレートを使用して、仮想ネットワーク内に Kafka クラスターと Spark クラスターを作成します。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager テンプレートは、**https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** にあります。

    このテンプレートでは、次のリソースを作成します。

    * HDInsight 3.6 クラスター上の Kafka。
    * HDInsight 3.6 クラスター上の Spark 2.2.0。
    * Azure Virtual Network (HDInsight クラスターを含む)

    > [!IMPORTANT]
    > この例で使用する構造化ストリーミングのノートブックでは、HDInsight 3.6 上に Spark が必要です。 HDInsight 上で以前のバージョンの Spark を使用している場合は、ノートブックを使用するとエラーを受信します。

2. 次の情報に従って、**[カスタマイズされたテンプレート]** セクションの各エントリに入力します。

    | Setting | 値 |
    | --- | --- |
    | [サブスクリプション] | お使いの Azure サブスクリプション |
    | リソース グループ | リソースが含まれるリソース グループ。 |
    | 場所 | リソースが作成される Azure リージョン。 |
    | [Spark Cluster Name]\(Spark クラスター名\) | Spark クラスターの名前。 |
    | [Kafka Cluster Name]\(Kafka クラスター名\) | Kafka クラスターの名前。 |
    | [Cluster Login User Name]\(クラスター ログイン ユーザー名\) | クラスターの管理者ユーザー名。 |
    | [クラスター ログイン パスワード] | クラスターの管理者ユーザー パスワード。 |
    | [SSH ユーザー名] | クラスター用に作成する SSH ユーザー。 |
    | [SSH パスワード] | SSH ユーザーのパスワード。 |
   
    ![カスタマイズされたテンプレートのスクリーンショット](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

4. 最後に、**[ダッシュボードにピン留めする]** をオンにし、**[購入]** をクリックします。 

> [!NOTE]
> クラスターの作成には最大で 20 分かかります。

## <a name="get-the-notebook"></a>ノートブックを入手する

このドキュメントで説明した例のコードは、[https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming) で入手できます。

## <a name="upload-the-notebooks"></a>ノートブックをアップロードする

プロジェクトから HDInsight クラスター上の Spark へノートブックをアップロードするには、以下の手順を使用します。

1. Web ブラウザーで、Spark クラスターの Jupyter Notebook に接続します。 次の URL の`CLUSTERNAME` をお使いの __Spark__ クラスターの名前に置き換えます。

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    プロンプトが表示されたら、クラスターの作成時に使用したログイン (管理者) パスワードを入力します。

2. ページの右上隅の __[アップロード]__ ボタンを使用して、__spark-structured-streaming-kafka.ipynb__ ファイルをクラスターにアップロードします。 __[開く]__ を選択して、アップロードを開始します。

    ![[アップロード] ボタンを使用して、Notebook を選択してアップロード](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![KafkaStreaming.ipynb ファイルを選択する](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. ノートブックの一覧で __spark-structured-streaming-kafka.ipynb__ エントリを検索し、横にある __[アップロード]__ ボタンを選択します。

    ![ノートブックをアップロードするために KafkaStreaming.ipynb エントリの [アップロード] ボタンを使用する](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>ノートブックを使用する

ファイルをアップロードした後、__spark-structured-streaming-kafka.ipynb__ エントリを選択してノートブックを開きます。 HDInsight 上の Kafka で Spark 構造化ストリーミングを使用する方法については、ノートブックの指示に従ってください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成したリソースをクリーンアップするために、リソース グループを削除することができます。 リソース グループを削除すると、関連付けられている HDInsight クラスター、およびリソース グループに関連付けられているその他のリソースも削除されます。

Azure Portal を使用してリソース グループを削除するには:

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、__[リソース グループ]__ を選択して、リソース グループの一覧を表示します。
2. 削除するリソース グループを見つけて、一覧の右側にある __[詳細]__ ボタン ([...]) を右クリックします。
3. __[リソース グループの削除]__ を選択し、確認します。

> [!WARNING]
> HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。
> 
> HDInsight クラスター上の Kafka を削除すると、Kafka に格納されているすべてのデータが削除されます。

## <a name="next-steps"></a>次の手順

この記事では、Spark 構造化ストリームの使用方法を説明しました。Spark および Kafka の操作に関する詳細については、以下のドキュメントをご覧ください。

* [Kafka で Spark ストリーミングを (DStream) を使用する方法](hdinsight-apache-spark-with-kafka.md)
* [HDInsight で Jupyter Notebook と Spark を使い始める](spark/apache-spark-jupyter-spark-sql.md)