---
title: Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する
description: Microsoft Power BI を使用して、Azure HDInsight からの対話型クエリの Hive データを視覚化する方法
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 5f4053888cc8402ab0196e40c33f1acc3e7eef44
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651133"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Azure HDInsight の直接クエリを使用して Microsoft Power BI で対話型クエリの Apache Hive データを視覚化する

この記事では、Microsoft Power BI を Azure HDInsight 対話型クエリ クラスターに接続し、直接クエリを使用して Apache Hive データを視覚化する方法について説明します。 与えられた例では、hivesampletable Hive テーブルから Power BI にデータを読み込みます。 hivesampletable Hive テーブルには、携帯電話の使用データが含まれます。 その使用データを世界地図に示します。

![HDInsight Power BI の地図レポート](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Power BI Desktop の汎用 ODBC コネクタを介してインポートするために、[Apache Hive ODBC ドライバー](../hadoop/apache-hadoop-connect-hive-power-bi.md)を利用することができます。 ただし、Hive クエリ エンジンの非対話的な性質を与えられた BI ワークロードに対しては、この方法はお勧めしません。 この場合は、[HDInsight 対話型クエリ コネクタ](./apache-hadoop-connect-hive-power-bi-directquery.md)および [HDInsight Apache Spark コネクタ](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)を選択した方が、良いパフォーマンスを得られます。

## <a name="prerequisites"></a>前提条件
この記事の操作を始める前に、以下を用意する必要があります。

* **HDInsight クラスター**。 このクラスターは、Apache Hive を含む HDInsight クラスターか、新しくリリースされた対話型クエリ クラスターのいずれかです。 クラスターの作成については、「[クラスターの作成](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**。 [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331) からコピーをダウンロードできます。

## <a name="load-data-from-hdinsight"></a>HDInsight からデータを読み込む

hivesampletable Hive テーブルはすべての HDInsight クラスターに付属しています。

1. Power BI Desktop にサインインします。

2. **[ホーム]** タブをクリックし、**[外部データ]** リボンの **[データを取得]** をクリックし、**[その他...]** を選択します。

    ![HDInsight Power BI でデータを開く](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
    
3. **[データの取得]** ウィンドウで、検索ボックスに「**hdinsight**」と入力します。 **[HDInsight 対話型クエリ (ベータ)]** が表示されない場合、Power BI Desktop を最新バージョンに更新する必要があります。

4. **[HDInsight 対話型クエリ (ベータ)]** を選択し、**[接続]** を選択します。

5. **[続行]** を選択して、**[コネクタのプレビュー]** 警告ダイアログを閉じます。

6. **[HDInsight 対話型クエリ]** から、次の情報を選択または入力します。

    - **サーバー**: 対話型クエリ クラスターの名前 (*myiqcluster.azurehdinsight.net* など) を入力します。

    - **データベース**:このチュートリアルでは、「**default**」と入力します。
    
    - **データ接続モード**: このチュートリアルでは、**[DirectQuery]** を選択します。

    ![HDInsight 対話型クエリ Power BI DirectQuery 接続](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Click **OK**.

8. HTTP ユーザー資格情報を入力して、**[OK]** をクリックします。 既定のユーザー名は **admin** です。

9. 左側のウィンドウから **hivesampletale** を選択して、**[読み込み]** をクリックします。

    ![HDInsight 対話型クエリ Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>マップ上にデータを視覚化する

前の手順の続きです。

1. [視覚化] ウィンドウで **[マップ]** を選択します。  地球儀のアイコンです。

    ![HDInsight Power BI のレポート カスタマイズ](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
    
2. [フィールド] ウィンドウで **[country]** と **[devicemake]** を選択します。 データが地図に描かれます。

3. 地図を広げます。

## <a name="next-steps"></a>次の手順
この記事では、Microsoft Power BI を使用して HDInsight からデータを視覚化する方法について学習しました。  データ視覚化の詳細については、次の記事を参照してください。

* [Azure HDInsight 上の Microsoft Power BI で ODBC を使用して Apache Hive データを視覚化する](../hadoop/apache-hadoop-connect-hive-power-bi.md)。 
* [Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する](./../hdinsight-connect-hive-zeppelin.md)。
* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [Power Query を使用して Excel を Apache Hadoop に接続する](../hadoop/apache-hadoop-connect-excel-power-query.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Apache Hive クエリを実行する](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](../hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./../hdinsight-upload-data.md)。
