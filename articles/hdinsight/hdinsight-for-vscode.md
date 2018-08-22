---
title: Azure HDInsight Tools - Hive、LLAP、pySpark に Visual Studio Code を使用する
description: Azure HDInsight Tools for Visual Studio Code を使用して、クエリとスクリプトを作成および送信する方法について説明します。
keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,インタラクティブ Hive,インタラクティブ クエリ
services: hdinsight
author: jejiang
ms.author: jejiang
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: 255f1ecb3c55ef94b6f4f3393257b3054ff1b725
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038605"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tool for Visual Studio Code の使用

Azure HDInsight Tools for Visual Studio Code (VS Code) を使用して、Hive バッチ ジョブ、対話型 Hive クエリ、PySpark スクリプトを作成および送信する方法について説明します。 Azure HDInsight Tools は、VS Code でサポートされているプラットフォームにインストールできます。 これには、Windows、Linux、macOS が含まれます。 プラットフォームごとの前提条件については、以下を参照してください。


## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、次のものが必要です。

- HDInsight クラスター。 クラスターを作成するには、[HDInsight での Hadoop の使用]( hdinsight-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)。
- [Mono](http://www.mono-project.com/docs/getting-started/install/)。 Mono は Linux と macOS にのみ必要です。

## <a name="install-the-hdinsight-tools"></a>HDInsight Tools をインストールする
   
前提条件の各項目のインストールが完了したら、Azure HDInsight Tools for VS Code をインストールできます。 

**Azure HDInsight Tools をインストールするには**

1. Visual Studio Code を開きます。

2. 左側のウィンドウで、**[拡張機能]** を選択します。 検索ボックスに「**HDInsight**」と入力します。

3. **Azure HDInsight Tools** の横にある **[インストール]** をクリックします。 数秒後、**[インストール]** ボタンが **[再度読み込む]** に変わります。

4. **[再読み込み]** をクリックして、**Azure HDInsight Tools** の拡張機能をアクティブにします。

5. **[ウィンドウの再読み込み]** をクリックして確認します。 **拡張機能**ウィンドウに **Azure HDInsight Tools** が表示されます。

   ![HDInsight for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>HDInsight ワークスペースを開設する

Azure に接続するには、まず VS Code にワークスペースを作成します。

**ワークスペースを開設するには**

1. **[ファイル]** メニューの **[フォルダーを開く]** を選択します。 作業フォルダーとして既存のフォルダーを指定するか、新しいフォルダーを作成します。 フォルダーが左側のウィンドウに表示されます。

2. 左側のウィンドウで、作業フォルダーの横にある **[新しいファイル]** アイコンをクリックします。

   ![[新しいファイル]](./media/hdinsight-for-vscode/new-file.png)

3. ファイル拡張子 .hql (Hive クエリ) または .py (Spark スクリプト) のいずれかを使って、新しいファイルに名前を付けます。 作業フォルダーに **XXXX_hdi_settings.json** 構成ファイルが自動的に追加されます。

4. **[エクスプローラー]** から **XXXX_hdi_settings.json** を開くか、スクリプト エディターを右クリックして **[構成の設定]** を選択します。 ファイルのサンプルに示すように、ログイン エントリ、既定のクラスター、ジョブの送信の各パラメーターを構成できます。 残りのパラメーターを空のままにすることもできます。

## <a name="connect-to-hdinsight-cluster"></a>HDInsight クラスターへの接続

VS Code から HDInsight クラスターにスクリプトを送信するには、(Ambari のユーザー名/パスワードかドメイン参加アカウントを利用し) 先に Azure アカウントに接続するか、クラスターをリンクする必要があります。

**Azure に接続するには**

1. 新しい作業フォルダーと新しいスクリプト ファイルを作成します (まだない場合)。

2. スクリプト エディターを右クリックし、コンテキスト メニューの **[HDInsight: Login]** を選択します。 また、**Ctrl + Shift + P** キーを押し、「**HDInsight: Login**」と入力することもできます。

    ![HDInsight Tools for Visual Studio Code のログイン](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. サインインするには、**[出力]** ウィンドウのサインインの指示に従います。

    **Azure:** ![HDInsight Tools for Visual Studio Code のログイン情報](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    接続すると、VS Code ウィンドウの左下のステータス バーに Azure アカウント名が表示されます。 

    > [!NOTE]
    > Azure 認証の既知の問題により、ブラウザーをプライベート モードまたはシークレット モードで開く必要があります。 Azure アカウントで 2 要素認証が有効になっている場合は、PIN 認証ではなく、電話認証を使用することをお勧めします。
  

4. スクリプト エディターを右クリックして、コンテキスト メニューを開きます。 コンテキスト メニューから次のタスクを実行できます。

    - ログアウト
    - クラスターの一覧表示
    - 既定のクラスターの設定
    - Submit interactive Hive queries
    - Hive バッチ スクリプトの送信
    - 対話型 PySpark クエリの送信
    - PySpark バッチ スクリプトの送信
    - 構成の設定

<a id="linkcluster"></a>**クラスターにリンクするには**

Ambari 管理対象ユーザー名を使用することで、ノーマル クラスターをリンクできます。また、ドメイン ユーザー名 (user1@contoso.com など) を使用することで、セキュリティ Hadoop クラスターをリンクすることもできます。
1. **Ctrl+Shift+P** を押してコマンド パレットを開き、「**HDInsight: Link a cluster**」と入力します。

   ![リンク クラスターのコマンド](./media/hdinsight-for-vscode/link-cluster-command.png)

2. HDInsight クラスターの URL を入力し、[ユーザー名] と [パスワード] を入力し、クラスターの種類を選択します。検証に成功するとその旨が表示されます。
   
   ![リンク クラスターのダイアログ](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > リンクされたユーザー名とパスワードは、クラスターが Azure サブスクリプションにログインし、かつクラスターにリンクしていた場合に使用されます。 
   
3. **List cluster** コマンドを使用すると、リンクされたクラスターを確認できます。 これでリンクされたクラスターにスクリプトを送信できるようになりました。

   ![リンクされたクラスター](./media/hdinsight-for-vscode/linked-cluster.png)

4. コマンド パレットに「**HDInsight: Unlink a cluster**」と入力してクラスターのリンクを解除することもできます。

## <a name="list-hdinsight-clusters"></a>List HDInsight clusters

接続をテストするために、HDInsight クラスターを一覧表示できます。

**Azure サブスクリプションの HDInsight クラスターを一覧表示するには**
1. ワークスペースを開き、Azure に接続します。 詳細については、「[HDInsight ワークスペースを開設する](#open-hdinsight-workspace)」および「[Azure への接続](#connect-to-azure)」をご覧ください。

2. スクリプト エディターを右クリックして、コンテキスト メニューから **[HDInsight: List Cluster]** を選択します。 

3. Hive および Spark クラスターが、**[出力]** ウィンドウに表示されます。

    ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>既定のクラスターを設定する
1. ワークスペースを開いて、Azure に接続します。 「[HDInsight ワークスペースを開設する](#open-hdinsight-workspace)」および「[Azure への接続](#connect-to-azure)」をご覧ください。

2. スクリプト エディターを右クリックし、**[HDInsight: Set Default Cluster]** をクリックします。 

3. 現在のスクリプト ファイルの既定のクラスターとしてクラスターを選択します。 構成ファイル (**XXXX_hdi_settings.json**) が自動的に更新されます。 

   ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Azure 環境を設定する 
1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。

2. 「**HDInsight: Set Azure Environment**」と入力します。

3. 既定のログイン エントリとして、Azure または AzureChina のいずれかを選択します。

4. 一方、ツールによって既定のログイン エントリが **XXXX_hdi_settings.json** に既に保存されています。 また、この構成ファイル内で直接、更新することもできます。 

   ![既定のログイン エントリの構成を設定する](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Submit interactive Hive queries

HDInsight Tools for VS Code を使用すると、対話型 Hive クエリを HDInsight 対話型クエリ クラスターに送信できます。

1. 新しい作業フォルダーと新しい Hive スクリプト ファイルを作成します (まだない場合)。

2. Azure アカウントに接続し、既定のクラスターを構成します (まだ構成していない場合)。

3. 次のコードをコピーし、Hive ファイルに貼り付けて保存します。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. スクリプト エディターを右クリックし、**[HDInsight: Hive Interactive]** を選択してクエリを送信します。 また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。 その後すぐに、クエリの結果が新しいタブに表示されます。

   ![対話型 Hive の結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **[結果]** パネル: 結果全体を CSV、JSON、または Excel ファイルとしてローカル パスに保存したり、複数の行だけを選択したりできます。

    - **[メッセージ]** パネル: **行**の番号を選択すると、実行中のスクリプトの最初の行にジャンプします。

対話型クエリは、[Hive バッチ ジョブ](#submit-hive-batch-scripts)よりもはるかに短時間で実行されます。

## <a name="submit-hive-batch-scripts"></a>Hive バッチ スクリプトの送信

1. 新しい作業フォルダーと新しい Hive スクリプト ファイルを作成します (まだない場合)。

2. Azure アカウントに接続し、既定のクラスターを構成します (まだ構成していない場合)。

3. 次のコードをコピーし、Hive ファイルに貼り付けて保存します。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. スクリプト エディターを右クリックし、**[HDInsight: Hive Batch]** を選択して Hive ジョブを送信します。 

4. 送信先のクラスターを選択します。  

    Hive ジョブを送信したら、送信成功に関する情報とジョブ ID が、**[出力]** パネルに表示されます。 また、Hive ジョブによって **Web ブラウザー**が開かれ、ジョブのリアルタイムのログと状態が表示されます。

   ![Hive ジョブの結果の送信](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[対話型 Hive クエリ](#submit-interactive-hive-queries)は、バッチ ジョブよりもはるかに短時間で送信されます。

## <a name="submit-interactive-pyspark-queries"></a>対話型 PySpark クエリの送信
HDInsight Tools for VS Code を使用すると、対話型 PySpark クエリを Spark クラスターに送信することもできます。
1. 新しい作業フォルダーと .py 拡張子の新しいスクリプト ファイルを作成します (まだない場合)。

2. Azure アカウントに接続します (まだ接続していない場合)。

3. 以下のコードをコピーして、スクリプト ファイルに貼り付けます。
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. これらのスクリプトを強調表示します。 スクリプト エディターを右クリックし、**[HDInsight: PySpark Interactive]** を選択します。

5. VS Code に **Python** 拡張機能がまだインストールされていない場合は、次の図に示す **[インストール]** ボタンをクリックします。

    ![HDInsight for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. システムに Python 環境をインストールします (まだインストールしていない場合)。 
   - Windows の場合、[Python](https://www.python.org/downloads/) をダウンロードしてインストールします。 次に、システムの PATH に `Python` と `pip` が含まれていることを確認します。

   - macOS および Linux での手順については、「[Visual Studio Code 用の PySpark 対話型環境を設定する](set-up-pyspark-interactive-environment.md)」をご覧ください。

7. PySpark クエリの送信先のクラスターを選択します。 その後すぐに、クエリの結果が右側の新しいタブに表示されます。

   ![Python ジョブの送信の結果](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. このツールでは、**SQL 句**のクエリもサポートしています。

   ![Python ジョブの送信の結果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) 送信の状態は、クエリの実行時に下部のステータス バーの左側に表示されます。 **[PySpark Kernel (busy)]\(PySpark カーネル (ビジー)\)** 状態のときに他のクエリを送信しないでください。 

>[!NOTE]
>クラスターはセッション情報を保持できます。 定義済みの変数、関数、対応する値がセッションで保持されるので、同じクラスターの複数のサービス呼び出しで参照できます。 
 

## <a name="submit-pyspark-batch-job"></a>PySpark バッチ ジョブの送信

1. 新しい作業フォルダーと .py 拡張子の新しいスクリプト ファイルを作成します (まだない場合)。

2. Azure アカウントに接続します (また接続していない場合)。

3. 以下のコードをコピーして、スクリプト ファイルに貼り付けます。

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. スクリプト エディターを右クリックし、**[HDInsight: PySpark Batch]** を選択します。 

5. PySpark ジョブの送信先のクラスターを選択します。 

   ![Python ジョブの送信の結果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Python ジョブを送信したら、VS Code の **[出力]** ウィンドウに送信ログが表示されます。 **Spark UI URL** と **Yarn UI URL** も表示されます。 URL を Web ブラウザーで開くと、ジョブの状態を追跡できます。

>[!NOTE]
>PySpark3 は Livy 0.4 (これは HDI Spark 2.2 クラスターです) ではサポートされなくなりました。 "PySpark" だけが Python に対してサポートされます。 Python3 では Spark 2.2 への送信が失敗することは既知の問題です。
   
## <a name="livy-configuration"></a>Livy の構成
Livy の構成がサポートされます。ワークスペース フォルダーでのプロジェクトの設定で設定できます。 詳しくは、[Livy の README](https://github.com/cloudera/livy/blob/master/README.rst ) をご覧ください。

+ プロジェクトの設定:

    ![Livy の構成](./media/hdinsight-for-vscode/hdi-livyconfig.png)

+ サポートされている Livy の構成:   

    **POST/バッチ**   
    要求本文

    | name | description | type | 
    | :- | :- | :- | 
    | file | 実行するアプリケーションを含むファイル | パス (必須) | 
    | proxyUser | ジョブを実行するときに偽装するユーザー | 文字列 | 
    | className | アプリケーションの Java/Spark のメイン クラス | 文字列 |
    | args | アプリケーションのコマンド ライン引数 | string のリスト | 
    | jars | このセッションで使用される Jar | 文字列のリスト | 
    | pyFiles | このセッションで使用される Python ファイル | 文字列のリスト |
    | ファイルのアップロード | このセッションで使用されるファイル | 文字列のリスト |
    | driverMemory | ドライバーのプロセスに使用するメモリの量 | 文字列 |
    | driverCores | ドライバーのプロセスに使用するコアの数 | int |
    | executorMemory | Executor プロセスごとに使用するメモリの量 | 文字列 |
    | executorCores | Executor ごとに使用するコアの数 | int |
    | numExecutors | このセッションに対して起動する Executor の数 | int |
    | archives | このセッションで使用されるアーカイブ | 文字列のリスト |
    | キュー | 送信対象の YARN キューの名前 | 文字列 |
    | name | このセッションの名前 | 文字列 |
    | conf | Spark の構成のプロパティ | キーと値のマップ |

    応答本文   
    作成された Batch オブジェクト

    | name | description | type | 
    | :- | :- | :- | 
    | id | セッション ID | int | 
    | appId | このセッションのアプリケーション ID |  String |
    | appInfo | アプリケーションの詳細情報 | キーと値のマップ |
    | log | ログの行 | string のリスト |
    | state |   バッチの状態 | 文字列 |


## <a name="additional-features"></a>その他の機能

HDInsight for VS Code では、次の機能をサポートしています。

- **IntelliSense オートコンプリート**。 キーワード、メソッド、変数などの候補がポップアップ表示されます。 オブジェクトの種類ごとに異なるアイコンで表されます。

    ![HDInsight Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense エラー マーカー**。 言語サービスでは、Hive スクリプトの編集エラーに下線が引かれます。     
- **構文の強調表示**。 言語サービスでは、変数、キーワード、データ型、関数などを区別するために、異なる色が使用されます。 

    ![HDInsight Tools for Visual Studio Code の構文の強調表示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>次の手順

### <a name="demo"></a>デモ
* HDInsight for VS Code: [ビデオ](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>ツールと拡張機能

* [Azure Toolkit for IntelliJ を使用して VPN を介して Spark アプリケーションをリモートでデバッグする](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ を使用して SSH を介して Spark アプリケーションをリモートでデバッグする](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して Spark アプリケーションを作成する](spark/apache-spark-eclipse-tool-plugin.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](spark/apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight の Microsoft Power BI で Hive データを視覚化する](hadoop/apache-hadoop-connect-hive-power-bi.md)。
* [Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [Visual Studio Code 用の PySpark 対話型環境を設定する](set-up-pyspark-interactive-environment.md)
* [Zeppelin を使用して Azure HDInsight で Hive クエリを実行する](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](spark/apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](spark/apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](spark/apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](spark/apache-spark-job-debugging.md)



