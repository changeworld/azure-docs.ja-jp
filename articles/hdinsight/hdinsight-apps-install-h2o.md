---
title: 発行済みアプリケーションのインストール - H2O Sparkling Water - Azure HDInsight
description: H2O Sparkling Water サードパーティ Hadoop アプリケーションをインストールして使用します。
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: e1e43bc06b30959bc5e7e692d88bd0d053a03ad4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39593555"
---
# <a name="install-published-application---h2o-sparkling-water"></a>発行済みアプリケーションのインストール - H2O Sparkling Water

この記事では、[H2O Sparkling Water](http://www.h2o.ai/) の発行済み Hadoop アプリケーションを Azure HDInsight にインストールして実行する方法について説明します。 HDInsight アプリケーション プラットフォームの概要と、独立系ソフトウェア ベンダー (ISV) によって発行された、利用可能なアプリケーションの一覧については、[サードパーティ Hadoop アプリケーションのインストール](hdinsight-apps-install-applications.md)に関するページを参照してください。 独自のアプリケーションのインストール手順については、[カスタム HDInsight アプリケーションのインストール](hdinsight-apps-install-custom-applications.md)のページを参照してください。

## <a name="about-h2o-sparkling-water"></a>H2O Sparkling Water について

H2O Sparkling Water は、オープン ソースの完全分散型インメモリ機械学習プラットフォームであり、直線的なスケーラビリティを備えています。 H2O Sparkling Water では、H2O の高速かつスケーラブルな機械学習アルゴリズムと Spark の各種機能を組み合わせることが可能です。 Sparkling Water により、ユーザーは H2O Flow UI を使用して Scala、R、Python からの計算を強化できます。

H2O Sparkling Water の提供内容:

* **使いやすい Web UI と親しみやすいインターフェイス** – Web ベースの直感的な H2O Flow GUI、または R、Python、Java、Scala、JSON、H2O API などのプログラミング環境を使用して、迅速に設定して開始します。
* **すべての一般的な種類のデータベースとファイルに対するデータ非依存のサポート** – Microsoft Excel、R Studio、Tableau などの中から簡単にビッグ データを探索してモデル化します。 HDFS、S3、SQL、NoSQL のデータ ソースからのデータに接続します。
* **非常にスケーラブルなビッグ データの加工と分析** – H2O の大結合では、R data.table 操作よりも 7 倍高速に実行して、100 億掛ける 100 億の行結合に直線的にスケーリングできます。
* **リアルタイムのデータ スコア付け** – プレーンな従来の Java オブジェクト (POJO)、モデル最適化 Java オブジェクト (MOJO)、または H2O REST API を使用して、モデルを運用環境にすばやくデプロイします。

### <a name="resource-links"></a>リソース リンク

* [H2O.ai エンジニアリング ロードマップ](http://jira.h2o.ai/)
* [H2O.ai ホーム](http://www.h2o.ai/)
* [H2O.ai ドキュメント](http://docs.h2o.ai/)
* [H2O.ai サポート](https://support.h2o.ai/)
* [H2O.ai オープン ソース コードベース](https://github.com/h2oai/)

## <a name="prerequisites"></a>前提条件

新しい HDInsight クラスター (または既存のクラスター) にこのアプリをインストールするには、次の構成が必要です。

* クラスター レベル: Standard または Premium
* クラスターの種類: Spark
* クラスターのバージョン: 3.5 または 3.6

## <a name="install-the-h2o-sparkling-water-published-application"></a>H2O Sparkling Water によって発行されたアプリケーションのインストール

このアプリケーションと利用可能な他の ISV アプリケーションをインストールする詳細な手順については、[サードパーティ Hadoop アプリケーションのインストール](hdinsight-apps-install-applications.md)に関するページを参照してください。

## <a name="launch-h2o-sparkling-water"></a>H2O Sparkling Water の起動

1. インストール後、Jupyter Notebook (`https://<ClusterName>.azurehdinsight.net/jupyter`) を開いて、Azure Portal のクラスターから H2O Sparkling Water (h2o-sparklingwater) の使用を開始できます。 また、ポータルのクラスター ウィンドウから **[クラスター ダッシュボード]** を選択して **[Jupyter Notebook]** を選択し、Jupyter を取得することもできます。 資格情報を入力するよう求められます。 クラスター作成時に指定されたクラスターの Hadoop 資格情報を入力します。

2. Jupyter で、H2O-PySparkling-Examples、PySpark Examples、Scala Examples という 3 つのフォルダーを確認します。 **H2O-PySparkling-Examples** フォルダーを選択します。

    ![Jupyter Notebook ホーム](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. 新しい Notebook を作成する際の最初の手順は、Spark 環境の構成です。 この情報は、**Sentiment_analysis_with_Sparkling_Water** サンプルに含まれています。 Spark 環境を構成するとき、必ず正しい jar を使用し、最初のセルの出力によって提供される IP アドレスを指定してください。

    ![Jupyter Notebook ホーム](./media/hdinsight-apps-install-h2o/spark-config.png)

4. H2O クラスターを開始します。

    ![クラスターの開始](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. H2O クラスターが立ち上がったら、**`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`** に移動して H2O Flow を開きます。

    > [!NOTE]
    > H2O Flow を開くことができない場合、ブラウザーのキャッシュを削除してみてください。 それでもアクセスできない場合はおそらくクラスターのリソースが足りていません。 クラスター ウィンドウの **[クラスターのスケール設定]** オプションで、ワーカー ノードの数を増やしてください。

    ![H2O Flow ダッシュボード](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. 右側のメニューから **Million_Songs.flow** サンプルを選択します。 警告が表示されたら、**[Load Notebook]\(Notebook の読み込み\)** をクリックします。 このデモは、実際のデータを使用して数分間実行されるように設計されています。 目的は、曲のリリースが 2004 年の前であったかそれとも後であったかを、二項分類を使用してデータから予測することです。

    ![Million_Songs.flow の選択](./media/hdinsight-apps-install-h2o/million-songs.png)

7. **milsongs-cls-train.csv.gz** が含まれるパスを見つけ、パス全体を **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz** で置換します。

8. **milsongs-cls-test.csv.gz** が含まれるパスを見つけ、それを **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz** で置換します。

9. Notebook セル内のすべてのステートメントを実行するには、ツール バーにある **[Run All]\(すべて実行\)** ボタンを選択します。

    ![[Run All]\(すべて実行\)](./media/hdinsight-apps-install-h2o/run-all.png)

10. 数分後、次のような出力が表示されます。

    ![出力](./media/hdinsight-apps-install-h2o/output.png)

これで完了です。 わずか数分の内に Spark で人工知能を利用しました。 次は、さまざまな種類の機会学習アルゴリズムのデモを行う H2O Flow のサンプルをさらに試すことができます。

## <a name="next-steps"></a>次の手順

* [H2O ドキュメント](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): 未発行の HDInsight アプリケーションを HDInsight にデプロイする方法について確認します。
* [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md): カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について確認します。
* [MSDN: HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight アプリケーションを定義する方法について確認します。
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
* [HDInsight で空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md): HDInsight クラスターへのアクセスのほか、HDInsight アプリケーションのテストとホストに空のエッジ ノードを使用する方法を確認します。
