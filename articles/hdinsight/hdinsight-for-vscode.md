---
title: Azure HDInsight for Visual Studio Code
description: Spark & Hive Tools (Azure HDInsight) for Visual Studio Code を使用し、クエリとスクリプトを作成して送信する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 9a81868d678b4c0277e904e879c73185a378bf70
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435684"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Spark & Hive Tools for Visual Studio Code を使用する

Spark & Hive Tools for Visual Studio Code を使用して Apache Hive バッチ ジョブ、インタラクティブ Hive クエリ、および Apache Spark 用 PySpark スクリプトを作成および送信する方法について説明します。 最初に Visual Studio Code に Spark & Hive Tools をインストールする方法を説明した後、Spark & Hive Tools にジョブを送信する方法を説明します。  

Spark & Hive Tools は、Windows、Linux、macOS など、Visual Studio Code でサポートされているプラットフォームにインストールできます。 プラットフォームごとに以下の前提条件に注意してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次の項目が必要です。

- Azure HDInsight クラスター。 クラスターを作成するには、[HDInsight での Hadoop の使用](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)に関するページをご覧ください。 または、Apache Livy エンドポイントをサポートする Spark および Hive クラスターを使用します。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 Mono は Linux と macOS でのみ必要です。
- [Visual Studio Code 用の PySpark 対話型環境](set-up-pyspark-interactive-environment.md)。
- ローカル ディレクトリ。 この記事では、**C:\HD\HDexample** を使用します。

## <a name="install-spark--hive-tools"></a>Spark & Hive Tools をインストールする

前提条件を満たしたら、これらの手順に従って、Spark & Hive Tools for Visual Studio Code をインストールできます。

1. Visual Studio Code を開きます。

2. メニュー バーから **[View]\(表示\)**  >  **[Extensions]\(拡張機能\)** に移動します。

3. 検索ボックスに、「**Spark & Hive**」と入力します。

4. 検索結果から **Spark & Hive Tools** を選び、 **[インストール]** を選択します。

   ![Spark & Hive for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 必要に応じて **[再読み込み]** を選択します。

## <a name="open-a-work-folder"></a>作業フォルダーを開く

作業フォルダーを開いて Visual Studio Code でファイルを作成するには、これらの手順を実行します。

1. メニュー バーから、 **[ファイル]**  >  **[フォルダーを開く...]**  >  **[C:\HD\HDexample]** に移動し、 **[フォルダーの選択]** ボタンをクリックします。 左側の **[Explorer]\(エクスプローラー\)** ビューにフォルダーが表示されます。

2. **[エクスプローラー]** ビューで **HDexample** フォルダーを選択し、作業フォルダーの横にある **[新しいファイル]** アイコンを選択します。

   ![Visual Studio Code の [新しいファイル] アイコン](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. `.hql` (Hive クエリ) または `.py` (Spark スクリプト) のいずれかのファイル拡張子を使用して、新しいファイルに名前を付けます。 この例では **HelloWorld.hql** を使用します。

## <a name="set-the-azure-environment"></a>Azure 環境を設定する

国内のクラウド ユーザーの場合は、まずこれらの手順に従って Azure 環境を設定してから、**Azure: Sign In** コマンドを使用して Azure にサインインします。

1. **[ファイル]**  >  **[ユーザー設定]**  >  **[設定]** に移動します。
2. 次の文字列を検索します: **Azure:クラウド**。
3. 一覧から国内のクラウドを選択します。

   ![既定のログイン エントリの構成を設定する](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Azure アカウントに接続する

Visual Studio Code からクラスターにスクリプトを送信する前に、自分の Azure アカウントに接続するか、クラスターをリンクする必要があります (Apache Ambari のユーザー名とパスワード資格情報、またはドメイン参加アカウントを使用)。 Azure に接続するには、これらの手順に従います。

1. メニュー バーから、 **[表示]**  >  **[コマンド パレット...]** に移動し、「**Azure:Sign In**」と入力します。

    ![Spark & Hive Tools for Visual Studio Code のログイン](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. サインインの手順に従って Azure にサインインします。 接続すると、Visual Studio Code ウィンドウの下部にあるステータス バーに Azure アカウント名が表示されます。  

## <a name="link-a-cluster"></a>クラスターのリンク

### <a name="link-azure-hdinsight"></a>リンク: Azure HDInsight

[Apache Ambari](https://ambari.apache.org/) マネージド ユーザー名を使用して通常のクラスターをリンクするか、またはドメイン ユーザー名 (`user1@contoso.com` など) を使用して Enterprise Security Pack のセキュリティ保護された Hadoop クラスターをリンクできます。

1. メニュー バーから、 **[表示]**  >  **[コマンド パレット...]** に移動し、「**Spark / Hive:Link a Cluster**」と入力します。

   ![コマンド パレットの link cluster コマンド](./media/hdinsight-for-vscode/link-cluster-command.png)

2. リンクされるクラスターの種類として **[Azure HDInsight]** を選択します。

3. HDInsight クラスターの URL を入力します。

4. 自分の Ambari ユーザー名を入力します。既定値は「**admin**」です。

5. 自分の Ambari パスワードを入力します。

6. クラスターの種類を選択します。

7. クラスターの表示名を設定します (省略可能)。

8. **[OUTPUT]\(出力\)** ビューを確認します。

   > [!NOTE]  
   > リンクされたユーザー名とパスワードは、クラスターが Azure サブスクリプションにログインし、かつクラスターにリンクしていた場合に使用されます。  

### <a name="link-generic-livy-endpoint"></a>リンク: ジェネリック Livy エンドポイント

1. メニュー バーから、 **[表示]**  >  **[コマンド パレット...]** に移動し、「**Spark / Hive:Link a Cluster**」と入力します。

2. リンクされるクラスターの種類として、 **[Generic Livy Endpoint]\(ジェネリック Livy エンドポイント\)** を選択します。

3. ジェネリック Livy エンドポイントを入力します。 例: http\://10.172.41.42:18080。

4. 承認の種類として、 **[基本]** または **[なし]** を選択します。  **[基本]** を選択する場合:  
    &emsp;a. 自分の Ambari ユーザー名を入力します。既定値は「**admin**」です。  
    &emsp;b. 自分の Ambari パスワードを入力します。

5. **[OUTPUT]\(出力\)** ビューを確認します。

## <a name="list-clusters"></a>クラスターを一覧表示する

1. メニュー バーから、 **[表示]**  >  **[コマンド パレット...]** に移動し、「**Spark / Hive:List Cluster**」と入力します。

2. 目的のサブスクリプションを選択します。

3. **[OUTPUT]\(出力\)** ビューを確認します。 このビューには、リンクされたクラスターと、お使いの Azure サブスクリプションのすべてのクラスターが表示されます。

    ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>既定のクラスターを設定する

1. [前に](#open-a-work-folder)説明した **HDexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前に](#open-a-work-folder)作成した **HelloWorld.hql** ファイルを選択します。 それがスクリプト エディターで開かれます。

3. スクリプト エディターを右クリックして、 **[Spark / Hive: Set Default Cluster]\(Spark / Hive: 既定のクラスターの設定\)** を選択します。  

4. お使いの Azure アカウントに[接続](#connect-to-an-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. 現在のスクリプト ファイルの既定のクラスターとしてクラスターを選択します。 ツールによって、構成ファイルである **.VSCode\settings.json** が自動的に更新されます。

   ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>対話型 Hive クエリと Hive バッチ スクリプトを送信する

Spark & Hive Tools for Visual Studio Code を使用すると、対話型 Hive クエリと Hive バッチ スクリプトをクラスターに送信できます。

1. [前に](#open-a-work-folder)説明した **HDexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前に](#open-a-work-folder)作成した **HelloWorld.hql** ファイルを選択します。 それがスクリプト エディターで開かれます。

3. 次のコードをコピーし、Hive ファイルに貼り付けて保存します。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. お使いの Azure アカウントに[接続](#connect-to-an-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. スクリプト エディターを右クリックし、 **[Hive: Interactive]** を選択してクエリを送信するか、キーボード ショートカットの Ctrl + Alt + I を使用します。  **[Hive:Batch]** を選択してスクリプトを送信するか、キーボード ショートカットの Ctrl + Alt + H を使用します。  

6. 既定のクラスターを指定していない場合は、クラスターを選択します。 また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。 しばらくすると、新しいタブにクエリの結果が表示されます。

   ![対話型 Apache Hive クエリの結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **[結果]** パネル:結果全体を CSV、JSON、または Excel ファイルとしてローカル パスに保存したり、複数の行だけを選択したりできます。

    - **[メッセージ]** パネル:**行**の番号を選択すると、実行されているスクリプトの最初の行にジャンプします。

## <a name="submit-interactive-pyspark-queries"></a>対話型の PySpark クエリを送信する

対話型 PySpark クエリを送信するには、これらの手順に従います。

1. [前に](#open-a-work-folder)説明した **HDexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前の](#open-a-work-folder)手順に従って、新しい **HelloWorld.py** ファイルを作成します。

3. 次のコードをコピーしてスクリプト ファイルに貼り付けます。

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

4. お使いの Azure アカウントに[接続](#connect-to-an-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. すべてのコードを選択し、スクリプト エディターを右クリックして、 **[Spark: PySpark Interactive]** を選択してクエリを送信します。 または、Ctrl + Alt + I ショートカットを使用します。

   ![PySpark Interactive のコンテキスト メニュー](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. 既定のクラスターを指定していない場合は、クラスターを選択します。 しばらくすると、**Python Interactive** の結果が新しいタブに表示されます。また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。

   ![PySpark Interactive の Python Interactive ウィンドウ](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. 「 **%%info**」と入力し、Shift + Enter キーを押してジョブ情報を表示します (省略可能)。

   ![PySpark Interactive: ジョブ情報の表示](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. ツールでは **Spark SQL** クエリもサポートされています。

   ![PySpark Interactive: 結果の表示](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   送信の状態は、クエリの実行時に下部のステータス バーの左側に表示されます。 **[PySpark Kernel (busy)]\(PySpark カーネル (ビジー)\)** 状態のときに他のクエリを送信しないでください。  

   > [!NOTE]
   >
   > 設定で **[Python Extension Enabled]\(Python 拡張機能が有効\)** チェック ボックスがオフ (既定の設定ではオン) の場合、送信される PySpark 対話結果には古いウィンドウが使用されます。
   >
   > ![Python Interactive の Python 拡張機能の無効](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>PySpark バッチ ジョブを送信する

1. [前に](#open-a-work-folder)説明した **HDexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前の](#open-a-work-folder)手順に従って、新しい **BatchFile.py** ファイルを作成します。

3. 次のコードをコピーしてスクリプト ファイルに貼り付けます。

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

4. お使いの Azure アカウントに[接続](#connect-to-an-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. スクリプト エディターを右クリックし、 **[Spark:PySpark Batch]** を選択するか、キーボード ショートカットの Ctrl + Alt + H を使用します。

6. PySpark ジョブの送信先となるクラスターを選択します。

   ![Python ジョブの送信の結果の出力](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Python ジョブを送信すると、Visual Studio Code の **[OUTPUT]\(出力\)** ウィンドウに送信ログが表示されます。 Spark UI URL と Yarn UI URL も表示されます。 Web ブラウザーで URL を開いて、ジョブの状態を追跡することができます。

## <a name="apache-livy-configuration"></a>Apache Livy の構成

[Apache Livy](https://livy.incubator.apache.org/) の構成がサポートされています。 これは、ワークスペース フォルダーにある **.VSCode\settings.json** ファイルで構成できます。 現在、Livy の構成では Python スクリプトのみがサポートされています。 詳しくは、[Livy の README](https://github.com/cloudera/livy/blob/master/README.rst ) を参照してください。

<a id="triggerlivyconf"></a>**Livy の構成をトリガーする方法**

方法 1  
1. メニュー バーから **[File]\(ファイル\)**  >  **[Preferences]\(基本設定\)**  >  **[Settings]\(設定\)** に移動します。
2. **[検索設定]** ボックスに「**HDInsight Job Submission: Livy Conf**」と入力します。  
3. 関連する検索結果に対して **[Edit in settings.json]\(settings.json で編集\)** を選択します。

方法 2: ファイルを送信すると、.vscode フォルダーが自動的に作業フォルダーに追加されます。 Livy の構成は **.vscode\settings.json** を選択することで確認できます。

+ プロジェクトの設定:

    ![HDInsight Apache Livy の構成](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >**driverMemory** と **executorMemory** の設定では、値と単位を設定します。 次に例を示します。1 g、1,024 m。

+ サポートされている Livy の構成:

    **POST/バッチ**要求本文

    | name | description | 型 |
    | :- | :- | :- |
    | file | 実行するアプリケーションを含むファイル | パス (必須) |
    | proxyUser | ジョブの実行時に権限を借用するユーザー | String |
    | className | アプリケーション Java/Spark のメイン クラス | String |
    | args | アプリケーションのコマンドライン引数 | 文字列のリスト |
    | jars | このセッションで使用される Jar | 文字列のリスト | 
    | pyFiles | このセッションで使用される Python ファイル | 文字列のリスト |
    | files | このセッションで使用されるファイル | 文字列のリスト |
    | driverMemory | ドライバー プロセスに使用するメモリの量 | String |
    | driverCores | ドライバー プロセスに使用するコアの数 | int |
    | executorMemory | 実行プログラム プロセスごとに使用するメモリの量 | String |
    | executorCores | 実行プログラムごとに使用するコアの数 | int |
    | numExecutors | このセッションで起動する実行プログラムの数 | int |
    | archives | このセッションで使用するアーカイブ | 文字列のリスト |
    | queue | 送信先の YARN キューの名前| String |
    | name | このセッションの名前 | String |
    | conf | Spark の構成プロパティ | キーのマップ = val |

    応答本文   作成された Batch オブジェクト。

    | name | description | 型 |
    | :- | :- | :- |
    | id | セッション ID | int |
    | appId | このセッションのアプリケーション ID | String |
    | appInfo | アプリケーションの詳細情報 | キーのマップ = val |
    | log | ログの行 | 文字列のリスト |
    | 状態 |バッチの状態 | String |

    > [!NOTE]
    > 割り当てられた Livy の構成は、スクリプトの送信時に出力ウィンドウに表示されます。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>エクスプローラーから Azure HDInsight と統合する

**Azure HDInsight** のエクスプローラーを通じて直接、クラスター内の Hive テーブルをプレビューできます。

1. Azure アカウントに[接続](#connect-to-an-azure-account)します (まだ接続していない場合)。

2. 左端の列の **Azure** アイコンを選択します。

3. 左側のウィンドウから、 **[AZURE:HDINSIGHT]** を展開します。 利用可能なサブスクリプションとクラスターが一覧表示されます。

4. クラスターを展開して、Hive メタデータのデータベースとテーブルのスキーマを表示します。

5. Hive テーブルを右クリックします。 例: **hivesampletable**。 **[Preview]\(プレビュー\)** を選択します。

   ![Spark & Hive for Visual Studio Code の Hive テーブルのプレビュー](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. **[結果のプレビュー]** ウィンドウが開きます。

   ![Spark & Hive for Visual Studio Code の結果ウィンドウのプレビュー](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- [結果] パネル

   結果全体を CSV、JSON、または Excel ファイルとしてローカル パスに保存したり、複数の行だけを選択したりできます。

- [メッセージ] パネル
   1. テーブルの行数が 100 行を超えると、次のメッセージが表示されます: "The first 100 rows are displayed for Hive table (Hive テーブルの先頭から 100 行を表示しています)"。
   2. テーブルの行数が 100 行以下の場合、次のようなメッセージが表示されます: "60 rows are displayed for Hive table (Hive テーブルの 60 行を表示しています)"。
   3. テーブルの内容がない場合は、次のメッセージが表示されます: "0 rows are displayed for Hive table (Hive テーブルの 0 行を表示しています)"。

        >[!NOTE]
        >
        >Linux でテーブル データをコピーするには、xclip をインストールします。
        >
        >![Linux の Spark & Hive for Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>その他の機能

Spark & Hive for Visual Studio Code では、次の機能もサポートされています。

- **IntelliSense のオートコンプリート**。 キーワード、メソッド、変数、その他のプログラミング要素の候補がポップアップ表示されます。 オブジェクトの種類ごとに異なるアイコンで表されます：

    ![Spark & Hive for Visual Studio Code の IntelliSense オブジェクト](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense エラー マーカー**。 言語サービスにより、Hive スクリプトの編集エラーに下線が引かれます。     
- **構文の強調表示**。 言語サービスにより、異なる色を使用して、変数、キーワード、データ型、関数、その他のプログラミング要素が区別されます。

    ![Spark & Hive Tools for Visual Studio Code の構文の強調表示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>読み取り専用ロール

クラスターに対する読み取り専用ロールを割り当てられたユーザーは、HDInsight クラスターにジョブを送信することも、Hive データベースを表示することもできなくなります。 クラスター管理者に連絡し、[Azure portal](https://ms.portal.azure.com/) で [**HDInsight クラスター オペレーター**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)へとロールをアップグレードしてもらいます。 有効な Ambari 資格情報を持っている場合は、次のガイダンスに従って、クラスターを手動でリンクすることができます。

### <a name="browse-the-hdinsight-cluster"></a>HDInsight クラスターを参照する  

Azure HDInsight のエクスプローラー上で選択して HDInsight クラスターを展開したときに、そのクラスターに対して読み取り専用ロールを持っている場合は、クラスターをリンクするよう求められます。 自分の Ambari 資格情報を使用してクラスターにリンクするには、次の方法を使用します。

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>HDInsight クラスターにジョブを送信する

HDInsight クラスターにジョブを送信するときに、そのクラスターに対して読み取り専用ロールを持っている場合は、クラスターをリンクするよう求められます。 Ambari 資格情報を使用してクラスターにリンクするには、次の手順を使用します。

### <a name="link-to-the-cluster"></a>クラスターにリンクする

1. 有効な Ambari ユーザー名を入力します。
2. 有効なパスワードを入力します。

   ![Spark & Hive Tools for Visual Studio Code のユーザー名](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & Hive Tools for Visual Studio Code のパスワード](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >`Spark / Hive: List Cluster` を使用して、リンクされたクラスターを確認できます。
  >
  >![Spark & Hive Tools for Visual Studio Code のリンクされた閲覧者](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 アカウントを参照する

Azure HDInsight のエクスプローラー上で選択して Data Lake Storage Gen2 アカウントを展開したときに、お使いの Azure アカウントに Gen2 ストレージへのアクセス権がない場合は、ストレージ アクセス キーの入力を求められます。 アクセス キーが検証されると、Data Lake Storage Gen2 アカウントは自動的に展開されます。

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2 を使用して HDInsight クラスターにジョブを送信する

Data Lake Storage Gen2 を使用して HDInsight クラスターにジョブを送信するときに、お使いの Azure アカウントに Gen2 ストレージへの書き込みアクセス権がない場合は、ストレージ アクセス キーの入力を求められます。 アクセス キーが検証されると、ジョブは正常に送信されます。

![Spark & Hive Tools for Visual Studio Code のアクセス キー](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> ストレージ アカウントのアクセス キーは Azure portal から取得できます。 詳細については、「[ストレージ アカウント アクセス キーを管理する](../storage/common/storage-account-keys-manage.md)」を参照してください。

## <a name="unlink-cluster"></a>クラスターのリンク解除

1. メニュー バーから、 **[ビュー]**  >  **[コマンド パレット]** の順に移動し、「**Spark / Hive: Unlink a Cluster**」と入力します。  

2. リンクを解除するクラスターを選択します。  

3. 検証のため、 **[出力]** ビューを確認します。  

## <a name="sign-out"></a>サインアウトする  

メニュー バーから、 **[ビュー]**  >  **[コマンド パレット]** の順に移動し、「**Azure: Sign Out**」と入力します。

## <a name="next-steps"></a>次のステップ

Spark & Hive for Visual Studio Code の使用については、[Spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706) のデモ ビデオをご覧ください。
