---
title: Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する
description: Microsoft Power BI を使用して、Azure HDInsight で処理された対話型クエリの Hive データを視覚化する方法について説明します。
keywords: hdinsight,hadoop,hive,対話型クエリ,対話型 hive,LLAP,directquery
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/14/2018
ms.openlocfilehash: 4dcfcb5e70b9eb6626be1f3528781a8c5b1bd5c4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39593028"
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Azure HDInsight の直接クエリを使用して Microsoft Power BI で対話型クエリの Hive データを視覚化する | Microsoft Docs

Microsoft Power BI を Azure HDInsight 対話型クエリ クラスターに接続し、直接クエリを使用して Hive データを視覚化する方法について説明します。 このチュートリアルでは、hivesampletable Hive テーブルから Power BI にデータを読み込みます。 Hive テーブルには、携帯電話の使用データが含まれます。 その使用データを世界地図に示します。

![HDInsight Power BI の地図レポート](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Power BI Desktop の汎用 ODBC コネクタを介してインポートするために、[Hive ODBC ドライバー](../hadoop/apache-hadoop-connect-hive-power-bi.md)を利用することができます。 ただし、Hive クエリ エンジンの非対話的な性質を与えられた BI ワークロードに対しては、この方法はお勧めしません。 この場合は、[HDInsight 対話型クエリ コネクタ](./apache-hadoop-connect-hive-power-bi-directquery.md)および [HDInsight Spark コネクタ](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)を選択した方が、良いパフォーマンスを得られます。

## <a name="prerequisites"></a>前提条件
この記事の操作を始める前に、以下を用意する必要があります。

* **HDInsight クラスター**。 このクラスターは、Hive を含む HDInsight クラスターか、新しくリリースされた対話型クエリ クラスターのいずれかです。 クラスターの作成については、「[クラスターの作成](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**。 [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331) からコピーをダウンロードできます。

## <a name="load-data-from-hdinsight"></a>HDInsight からデータを読み込む

hivesampletable Hive テーブルはすべての HDInsight クラスターに付属しています。

1. Power BI Desktop にサインインします。
2. **[ホーム]** タブをクリックし、**[外部データ]** リボンの **[データを取得]** をクリックし、**[その他]** を選択します。

    ![HDInsight Power BI でデータを開く](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. **[データの取得]** ウィンドウで、検索ボックスに「**hdinsight**」と入力します。 **[HDInsight 対話型クエリ (ベータ)]** が表示されない場合、Power BI Desktop を最新バージョンに更新する必要があります。
4. **[HDInsight 対話型クエリ (ベータ)]** をクリックし、**[接続]** をクリックします。
5. **[続行]** をクリックして、**[コネクタのプレビュー]** 警告ダイアログを閉じます。
6. **[HDInsight 対話型クエリ]** から、次の情報を選択または入力します。

    - **サーバー**: 対話型クエリ クラスターの名前を入力します。たとえば、*myiqcluster.azurehdinsight.net* などです。
    - **データベース**: このチュートリアルでは、「**default**」と入力します。
    - **データ接続モード**: このチュートリアルでは、**DirectQuery** を選択します。

    ![HDInsight 対話型クエリ Power BI directquery 接続](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. Click **OK**.
8. HTTP ユーザー資格情報を入力して、**[OK]** をクリックします。  既定のユーザー名は **admin** です。
9. 左側のウィンドウから **hivesampletale** を選択して、**[読み込み]** をクリックします。

    ![HDInsight 対話型クエリ Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>データの視覚化

前の手順の続きです。

1. [視覚化] ウィンドウで **[マップ]** を選択します。  地球儀のアイコンです。

    ![HDInsight Power BI のレポート カスタマイズ](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. [フィールド] ウィンドウで **[country]** と **[devicemake]** を選択します。 データが地図に描かれます。
3. 地図を広げます。

## <a name="next-steps"></a>次の手順
この記事では、Power BI を使用して HDInsight からデータを視覚化する方法について学習しました。  詳細については、次の記事を参照してください。

* [Azure HDInsight の Microsoft Power BI で ODBC を使用して Hive データを視覚化する](../hadoop/apache-hadoop-connect-hive-power-bi.md)。 
* [Zeppelin を使用して Azure HDInsight で Hive クエリを実行する](./../hdinsight-connect-hive-zeppelin.md)。
* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [Power Query を使用して Excel を Hadoop に接続する](../hadoop/apache-hadoop-connect-excel-power-query.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Hive クエリを実行する](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](../hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./../hdinsight-upload-data.md)。
