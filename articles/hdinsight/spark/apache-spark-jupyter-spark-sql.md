---
title: Azure HDInsight での Apache Spark クラスターの作成 | Microsoft Docs
description: HDInsight で Apache Spark クラスターを作成する方法に関する HDInsight Spark のクイック スタート。
keywords: spark クイック スタート,対話型 spark,対話型クエリ,hdinsight spark,azure spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: jgao
ms.openlocfilehash: 1fdae7d6e47ff70e83e8153cdc22c0b2881d2743
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Azure HDInsight での Apache Spark クラスターの作成

Azure HDInsight で Apache Spark クラスターを作成し、Hive テーブルに対して Spark SQL クエリを実行する方法を説明します。 HDInsight での Spark について詳しくは、「[概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 このチュートリアルを開始する前に、Azure サブスクリプションが必要です。 [Azure の無料アカウント作成](https://azure.microsoft.com/free)に関するページをご覧ください。

## <a name="create-hdinsight-spark-cluster"></a>HDInsight Spark クラスターの作成

[Azure Resource Manager テンプレート](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)を使用して HDInsight Spark クラスターを作成します。 テンプレートは [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/) から入手できます。 その他のクラスター作成方法については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

1. 次の画像をクリックして Azure ポータルでテンプレートを開きます。         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 次の値を入力します。

    ![Azure Resource Manager テンプレートを使用して HDInsight Spark クラスターを作成する](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Azure Resource Manager テンプレートを使用して HDInsight で Spark クラスターを作成する")

    * **サブスクリプション**: このクラスターを作成するために使用する Azure サブスクリプションを選択します。
    * **リソース グループ**: リソース グループを作成するか、既存のリソース グループを選択します。 リソース グループは、プロジェクトの Azure リソースを管理するために使用されます。
    * **場所**: リソース グループの場所を選択します。 テンプレートでは、この場所をクラスターの作成および既定のクラスター ストレージに使用します。
    * **クラスター名**: 作成する HDInsight クラスターの名前を入力します。
    * **クラスターのログイン名とパスワード**: 既定のログイン名は admin です。クラスター ログイン用のパスワードを選択します。
    * **SSH のユーザー名とパスワード**。 SSH ユーザー用のパスワードを選択します。

3. **[上記の使用条件に同意する]**、**[ダッシュボードにピン留めする]** の順に選択し、**[購入]** をクリックします。 "**Template deployment のデプロイ中**" という新しいタイルが表示されます。 クラスターの作成には約 20 分かかります。

HDInsight クラスターを作成する際に問題が発生した場合は、適切なアクセス許可がない可能性があります。 詳細については、「[アクセス制御の要件](../hdinsight-administer-use-portal-linux.md#create-clusters)」を参照してください。

> [!NOTE]
> この記事では、[クラスター ストレージとして Azure Storage BLOB](../hdinsight-hadoop-use-blob-storage.md) を使用する Spark クラスターを作成します。 既定のストレージとして [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md) を使用する Spark クラスターを作成することもできます。 手順については、 [Data Lake Store を使用した HDInsight クラスターの作成](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。
>
>

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook の作成

[Jupyter Notebook](http://jupyter.org) は、各種のプログラミング言語でデータを対話的に操作したり、コードとマークダウン テキストとを結合したり、単純な視覚化を行ったりすることができるインタラクティブなノートブック環境です。 HDInsight の Spark には [Zeppelin Notebook](apache-spark-zeppelin-notebook.md) も含まれています。 このチュートリアルでは Jupyter Notebook を使用します。

**Jupyter Notebook を作成するには**

1. [Azure Portal](https://portal.azure.com/)を開きます。

2. 作成した Spark クラスターを開きます。 手順については、「[クラスターの一覧と表示](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters)」を参照してください。

3. ポータルで **[クラスター ダッシュボード]** をクリックし、**[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。

   ![Jupyter Notebook を開いて対話型の Spark SQL クエリを実行する](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter Notebook を開いて対話型の Spark SQL クエリを実行する")

   > [!NOTE]
   > ブラウザーで次の URL を開くことで、クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. **[新規]** をクリックし、**[Pyspark]** をクリックして、Notebook を作成します。 Jupyter Notebook では、HDInsight クラスター上で **PySpark**、**PySpark3**、**Spark** の 3 つのカーネルをサポートしています。 このチュートリアルでは、**PySpark** カーネルを使用します。 これらのカーネルと **PySpark** を使用するメリットについては、[HDInsight での Apache Spark クラスターと Jupyter Notebook カーネルの使用](apache-spark-jupyter-notebook-kernels.md)に関する記事を参照してください。

   ![対話型の Spark SQL クエリを実行する Jupyter Notebook を作成する](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "対話型の Spark SQL クエリを実行する Jupyter Notebook を作成する")

   Untitled(Untitled.pynb) という名前の新しい Notebook が作成されて開かれます。

4. 上部の Notebook 名をクリックし、目的のわかりやすい名前を入力します。

    ![対話型の Spark クエリを実行する Jupyter Notebook に名前を指定する](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "対話型の Spark クエリを実行する Jupyter Notebook に名前を指定する")

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Hive テーブルに対して Spark SQL ステートメントを実行する

SQL (構造化照会言語) は、データ照会とデータ定義のための言語として最も一般的かつ広く使用されています。 Spark SQL を Apache Spark の拡張機能として導入することで、使い慣れた SQL 構文を使って構造化データを扱うことができます。

Spark SQL では、SQL と HiveQL の両方がクエリ言語としてサポートされます。 その機能には、Python、Scala、Java でのバインディングも含まれます。 それによって、外部データベース、構造化データ ファイル (JSON など)、Hive テーブルなど、さまざまな場所に格納されたデータを照会することができます。

Hive テーブルではなく CSV ファイルからデータを読み取る例については、「[HDInsight Spark クラスターに対して対話型クエリを実行する](apache-spark-load-data-run-query.md)」を参照してください。

**Spark SQL を実行するには**

1. Notebook を初めて起動すると、カーネルがバックグラウンドでいくつかのタスクを実行します。 カーネルの準備ができるまで待ちます。 Notebook のカーネル名の横に白丸が表示されたら、カーネルの準備ができています。 黒丸は、カーネルがビジー状態であることを示します。

    ![HDInsight Spark での Hive クエリ](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "HDInsight Spark での Hive クエリ")

2. カーネルの準備ができたら、次のコードを空のセルに貼り付け、**Shift + Enter** キーを押してコードを実行します。 コマンドを実行すると、クラスター上の Hive テーブルが一覧表示されます。

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    HDInsight Spark クラスターで Jupyter Notebook を使用すると、Spark SQL を使用して Hive クエリを実行するために使用できるプリセット `sqlContext` が手に入ります。 `%%sql` により、プリセット `sqlContext` を使用して Hive クエリを実行するよう Jupyter Notebook に指示します。 クエリは、すべての HDInsight クラスターに既定で付属する Hive テーブル (**hivesampletable**) から先頭の 10 行を取得します。 結果が得られるまで約 30 秒かかります。 出力は次のようになります。 

    ![HDInsight Spark での Hive クエリ](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "HDInsight Spark での Hive クエリ")

    `%%sql` マジックとプリセット コンテキストの詳細については、[HDInsight クラスターで利用可能な Jupyter カーネル](apache-spark-jupyter-notebook-kernels.md)に関する記事を参照してください。

    Jupyter でクエリを実行するたびに、Web ブラウザー ウィンドウのタイトルに **[(ビジー)]** ステータスと Notebook のタイトルが表示されます。 また、右上隅にある **PySpark** というテキストの横に塗りつぶされた円も表示されます。
    
2. 別のクエリを実行して、`hivesampletable` のデータを確認します。

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    画面が更新され、クエリ出力が表示されます。

    ![HDInsight Spark での Hive クエリ出力](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "HDInsight Spark での Hive クエリ出力")

2. Notebook の **[ファイル]** メニューの **[Close and Halt]\(閉じて停止\)** をクリックします。 Notebook をシャットダウンすると、クラスターのリソースが解放されます。

3. 次の手順を後で実行する場合は、必ずこの記事で作成した HDInsight クラスターを削除してください。 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次の手順 

この記事では、HDInsight Spark クラスターを作成し、基本的な Spark SQL クエリを実行する方法を学習しました。 HDInsight Spark クラスターを使用してサンプル データに対話型のクエリを実行するには、次の記事に進みます。

> [!div class="nextstepaction"]
>[HDInsight Spark クラスターに対して対話型クエリを実行する](apache-spark-load-data-run-query.md)



