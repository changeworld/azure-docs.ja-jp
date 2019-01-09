---
title: Azure HDInsight Tools - Hive、LLAP、PySpark に Visual Studio Code を使用する | Microsoft Docs
description: Azure HDInsight Tools for Visual Studio Code を使用して、クエリとスクリプトを作成および送信する方法について説明します。
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: 23cbdb4d72a6b88563d9d2c2bd0e4e3c8223377f
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723673"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tool for Visual Studio Code の使用

Azure HDInsight Tools for Visual Studio Code (VS Code) を使用して Apache Hive バッチ ジョブ、対話型 Hive クエリ、および Apache Spark 用 PySpark スクリプトを作成および送信する方法について説明します。 最初に VS Code に HDInsight Tools をインストールする方法を説明した後、Hive および Spark にジョブを送信する方法を説明します。 

Azure HDInsight Tools は、VSCode でサポートされている Windows、Linux、macOS などのプラットフォームにインストールできます。 以下では、さまざまなプラットフォームに対する前提条件について説明します。


## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、次のものが必要です。

- HDInsight クラスター。 クラスターを作成するには、[HDInsight での Hadoop の使用](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 Mono は Linux と macOS にのみ必要です。

## <a name="install-the-hdinsight-tools"></a>HDInsight Tools をインストールする
   
前提条件の各項目のインストールが完了したら、Azure HDInsight Tools for VS Code をインストールできます。 

### <a name="to-install-azure-hdinsight-tools"></a>Azure HDInsight Tools をインストールするには

1. Visual Studio Code を開きます。

2. 左側のウィンドウで、**[拡張機能]** を選択します。 検索ボックスに「**HDInsight**」と入力します。

3. **Azure HDInsight Tools** の横にある **[インストール]** をクリックします。 数秒後、**[インストール]** ボタンが **[再度読み込む]** に変わります。

4. **[再読み込み]** をクリックして、**Azure HDInsight Tools** の拡張機能をアクティブにします。

5. **[ウィンドウの再読み込み]** をクリックして確認します。 **拡張機能**ウィンドウに **Azure HDInsight Tools** が表示されます。

   ![HDInsight for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>HDInsight ワークスペースを開設する

Azure に接続するには、まず VS Code にワークスペースを作成します。

### <a name="to-open-a-workspace"></a>ワークスペースを開設するには

1. **[ファイル]** メニューの **[フォルダーを開く]** を選択します。 作業フォルダーとして既存のフォルダーを指定するか、新しいフォルダーを作成します。 フォルダーが左側のウィンドウに表示されます。

2. 左側のウィンドウで、作業フォルダーの横にある **[新しいファイル]** アイコンをクリックします。

   ![[新しいファイル]](./media/hdinsight-for-vscode/new-file.png)

3. ファイル拡張子 .hql (Hive クエリ) または .py (Spark スクリプト) のいずれかを使って、新しいファイルに名前を付けます。 

## <a name="connect-to-hdinsight-cluster"></a>HDInsight クラスターへの接続

VS Code から HDInsight クラスターにスクリプトを送信するには、(Ambari のユーザー名/パスワードかドメイン参加アカウントを利用し) 先に Azure アカウントに接続するか、クラスターをリンクする必要があります。

### <a name="to-connect-to-azure"></a>Azure に接続するには

1. 新しい作業フォルダーと新しいスクリプト ファイルを作成します (まだない場合)。

2. スクリプト エディターを右クリックし、コンテキスト メニューの **[HDInsight:Login]** を選択します。 また、**Ctrl + Shift + P** キーを押し、「**HDInsight:Login**」と入力することもできます。

    ![HDInsight Tools for Visual Studio Code のログイン](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. サインインするには、**[出力]** ウィンドウのサインインの指示に従います。
    + グローバル環境では、HDInsight のサインインによって Azure のサインイン プロセスがトリガーされます。

        ![Azure でのサインイン手順](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + その他の環境では、サインイン手順に従います。

        ![その他の環境でのサインイン手順](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

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
    - Set configuration

<h3 id="linkcluster">クラスターにリンクするには</h3>

[Apache Ambari](https://ambari.apache.org/) マネージド ユーザー名を使用して通常のクラスターをリンクするか、またはドメイン ユーザー名 (user1@contoso.com など) を使用して Enterprise Security Pack のセキュリティ保護された Hadoop クラスターをリンクできます。
1. **Ctrl + Shift + P** キーを押してコマンド パレットを開き、「**HDInsight:Link a cluster**」と入力します。

   ![リンク クラスターのコマンド](./media/hdinsight-for-vscode/link-cluster-command.png)

2. HDInsight クラスターの URL を入力し、[ユーザー名] と [パスワード] を入力し、クラスターの種類を選択します。検証に成功するとその旨が表示されます。
   
   ![リンク クラスターのダイアログ](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]  
   > リンクされたユーザー名とパスワードは、クラスターが Azure サブスクリプションにログインし、かつクラスターにリンクしていた場合に使用されます。 
   
3. **List Cluster** コマンドを使用すると、リンクされたクラスターを確認できます。 これでリンクされたクラスターにスクリプトを送信できるようになりました。

   ![リンクされたクラスター](./media/hdinsight-for-vscode/linked-cluster.png)

4. コマンド パレットに「**HDInsight:Unlink a cluster**」と入力してクラスターのリンクを解除することもできます。


### <a name="to-link-a-generic-apache-livy-endpoint"></a>ジェネリック Apache Livy エンドポイントをリンクするには

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開き、「**HDInsight:Link a cluster**」と入力します。
2. **[Generic Livy Endpoint]\(ジェネリック Livy エンドポイント\)** を選択します。
3. ジェネリック Livy エンドポイント (http://10.172.41.42:18080 など) を入力します。
4. ジェネリック Livy エンドポイントの承認が必要な場合は、**[基本]** を選択します。それ以外の場合は、**[なし]** を選択します。
5. 手順 4 で **[Basic]\(基本\)** を選択した場合、ユーザー名を入力します。
6. 手順 4 で **[Basic]\(基本\)** を選択した場合、パスワードを入力します。
7. ジェネリック Livy エンドポイントが正常にリンクされました。

   ![リンクされたジェネリック Livy クラスター](./media/hdinsight-for-vscode/link-cluster-process-generic-livy.png)

## <a name="list-hdinsight-clusters"></a>List HDInsight clusters

接続をテストするために、HDInsight クラスターを一覧表示できます。

### <a name="to-list-hdinsight-clusters-under-your-azure-subscription"></a>Azure サブスクリプションの HDInsight クラスターを一覧表示するには
1. ワークスペースを開き、Azure に接続します。 詳細については、「[HDInsight ワークスペースを開設する](#open-hdinsight-workspace)」および「[Azure への接続](#connect-to-hdinsight-cluster)」をご覧ください。

2. スクリプト エディターを右クリックして、**[HDInsight:List Cluster]** をコンテキスト メニューから選択します。 

3. HDInsight クラスターが **[出力]** ウィンドウに表示されます。

    ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>既定のクラスターを設定する
1. ワークスペースを開いて、Azure に接続します。 「[HDInsight ワークスペースを開設する](#open-hdinsight-workspace)」および「[Azure への接続](#connect-to-hdinsight-cluster)」をご覧ください。

2. スクリプト エディターを右クリックして、**[HDInsight:Set Default Cluster]** を選択します。 

3. 現在のスクリプト ファイルの既定のクラスターとしてクラスターを選択します。 構成ファイル (**.VSCode\settings.json**) が自動的に更新されます。 

   ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Azure 環境を設定する
1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。

2. 「**HDInsight:Set Azure Environment**」と入力します。

3. 既定のログイン エントリとして環境 ("Azure" や "AzureChina" など) を選択します。

4. 一方、ツールによって既定のログイン エントリが **.VSCode\settings.json** に既に保存されています。 また、この構成ファイル内で直接、更新することもできます。 

   ![既定のログイン エントリの構成を設定する](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>対話型 Hive クエリ、Hive バッチ スクリプトを送信する

HDInsight Tools for VS Code を使用すると、対話型 Hive クエリ、Hive バッチ スクリプトを HDInsight クラスターに送信できます。

1. 新しい作業フォルダーと新しい Hive スクリプト ファイルを作成します (まだない場合)。

2. Azure アカウントに接続するか、クラスターをリンクします。

3. 次のコードをコピーし、Hive ファイルに貼り付けて保存します。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. スクリプト エディターを右クリックし、**[HDInsight:Hive Interactive]** を選択してクエリを送信するか、ショートカット **Ctrl + Alt + I** を使用します。**[HDInsight:Hive Batch]** を選択してスクリプトを送信するか、ショートカット**Ctrl + Alt + H** を使用します。 

5. 既定のクラスターを指定していない場合は、クラスターを選択します。 また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。 しばらくすると、新しいタブにクエリの結果が表示されます。

   ![対話型 Hive の結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **[結果]** パネル:結果全体を CSV、JSON、または Excel ファイルとしてローカル パスに保存したり、複数の行だけを選択したりできます。

    - **[メッセージ]** パネル:**行**の番号を選択すると、実行中のスクリプトの最初の行にジャンプします。

## <a name="submit-interactive-pyspark-queries"></a>対話型 PySpark クエリの送信

### <a name="to-submit-interactive-pyspark-queries-to-hdinsight-spark-clusters"></a>HDInsight Spark クラスターに対話型 PySpark クエリを送信するには。

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
4. このスクリプトを強調表示します。 次に、スクリプト エディターを右クリックして、**[HDInsight:PySpark Interactive]** を選択するか、ショートカット **Ctrl + Alt + I** を使用します。

5. VS Code に **Python** 拡張機能がまだインストールされていない場合は、次の図に示す **[インストール]** ボタンをクリックします。

    ![HDInsight for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. システムに Python 環境をインストールします (まだインストールしていない場合)。 
   - Windows の場合、[Python](https://www.python.org/downloads/) をダウンロードしてインストールします。 次に、システムの PATH に `Python` と `pip` が含まれていることを確認します。

   - macOS および Linux での手順については、「[Visual Studio Code 用の PySpark 対話型環境を設定する](set-up-pyspark-interactive-environment.md)」をご覧ください。

7. PySpark クエリの送信先のクラスターを選択します。 その後すぐに、クエリの結果が右側の新しいタブに表示されます。

   ![Python ジョブの送信の結果](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. ツールでは **SQL 句**のクエリもサポートされています。

   ![Python ジョブの送信の結果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) 送信の状態は、クエリの実行時に下部のステータス バーの左側に表示されます。 **[PySpark Kernel (busy)]\(PySpark カーネル (ビジー)\)** 状態のときに他のクエリを送信しないでください。 

>[!NOTE]  
>クラスターはセッション情報を保持できます。 定義済みの変数、関数、対応する値がセッションで保持されるので、同じクラスターの複数のサービス呼び出しで参照できます。 

### <a name="to-disable-environment-check"></a>環境チェックを無効にするには

既定では、HDInsight ツールは対話型 PySpark クエリを送信するときに環境をチェックして依存パッケージをインストールします。 環境チェックを無効にするには、**[USER SETTINGS]\(ユーザー設定\)** で **hdinsight.disablePysparkEnvironmentValidation** を **yes** に設定します。

   ![設定から環境チェックを設定する](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check.png)

または、ダイアログが表示されたときに **[Disable Validation]\(検証の無効化\)** ボタンをクリックします。

   ![ダイアログから環境チェックを設定する](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check-dialog.png)

### <a name="pyspark3-is-not-supported-with-spark2223"></a>Spark2.2/2.3 では PySpark3 はサポートされていません

Spark 2.2 クラスターと Spark2.3 クラスターでは PySpark3 はサポートされなくなりました。"PySpark" のみが Python 向けにサポートされています。 Python3 で Spark 2.2/2.3 への送信が失敗するのは既知の問題です。

   ![python3 への送信でエラーが発生する](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Python2.x を使用する手順に従います: 

1. ローカル コンピューターに Python 2.7 をインストールし、システム パスに追加します。

2. VSCode を再起動します。

3. ステータス バーにある **[Python XXX]** をクリックして Python 2 に切り替えてから、ターゲットの Python を選択します。

   ![Python のバージョンを選択する](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)

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
4. スクリプト エディターを右クリックして、**[HDInsight:PySpark Batch]** を選択するか、ショートカット **Ctrl + Alt + H** を使用します。 

5. PySpark ジョブの送信先のクラスターを選択します。 

   ![Python ジョブの送信の結果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Python ジョブを送信したら、VS Code の **[出力]** ウィンドウに送信ログが表示されます。 **Spark UI URL** と **Yarn UI URL** も表示されます。 URL を Web ブラウザーで開くと、ジョブの状態を追跡できます。

## <a name="apache-livy-configuration"></a>Apache Livy の構成

[Apache Livy](https://livy.incubator.apache.org/) の構成がサポートされており、ワークスペース フォルダーの **.VSCode\settings.json** で設定できます。 現在、Livy の構成では Python スクリプトのみをサポートします。 詳しくは、[Livy の README](https://github.com/cloudera/livy/blob/master/README.rst ) をご覧ください。

<a id="triggerlivyconf"></a>**Livy の構成をトリガーする方法**
   
**[ファイル]** メニューの **[Preferences]\(環境設定\)** を選択し、コンテキスト メニューの **[設定]** を選択します。 **[WORKSPACE SETTINGS]\(ワークスペース設定\)** タブをクリックして、Livy の構成の設定を開始できます。

ファイルを送信することもできます。その場合、.vscode フォルダーが自動的に作業フォルダーに追加されます。 Livy の構成は **.vscode\settings.json** をクリックして確認できます。

+ プロジェクトの設定:

    ![Livy の構成](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>**driverMomory** および **executorMomry** の設定で、単位付きの値 (例: 1g または 1024m) を設定します。 

+ サポートされている Livy の構成:   

    **POST/バッチ**   
    要求本文

    | name | description | type | 
    | :- | :- | :- | 
    | file | 実行するアプリケーションを含むファイル | パス (必須) | 
    | proxyUser | ジョブを実行するときに偽装するユーザー | string | 
    | className | アプリケーションの Java/Spark のメイン クラス | string |
    | args | アプリケーションのコマンド ライン引数 | string のリスト | 
    | jars | このセッションで使用される Jar | 文字列のリスト | 
    | pyFiles | このセッションで使用される Python ファイル | 文字列のリスト |
    | ファイルのアップロード | このセッションで使用されるファイル | 文字列のリスト |
    | driverMemory | ドライバーのプロセスに使用するメモリの量 | string |
    | driverCores | ドライバーのプロセスに使用するコアの数 | int |
    | executorMemory | Executor プロセスごとに使用するメモリの量 | string |
    | executorCores | Executor ごとに使用するコアの数 | int |
    | numExecutors | このセッションに対して起動する Executor の数 | int |
    | archives | このセッションで使用されるアーカイブ | 文字列のリスト |
    | キュー | 送信対象の YARN キューの名前 | string |
    | name | このセッションの名前 | string |
    | conf | Spark の構成のプロパティ | キーと値のマップ |

    応答本文   
    作成された Batch オブジェクト

    | name | description | type | 
    | :- | :- | :- | 
    | id | セッション ID | int | 
    | appId | このセッションのアプリケーション ID |  String |
    | appInfo | アプリケーションの詳細情報 | キーと値のマップ |
    | log | ログの行 | string のリスト |
    | state |   バッチの状態 | string |

>[!NOTE]
>スクリプトを送信すると、割り当てられている Livy の構成が出力ウィンドウに表示されます。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>エクスプローラーから Azure HDInsight と統合する

Azure HDInsight が左側のパネルに追加されました。 クラスターを直接、参照および管理することができます。

1. **[AZURE HDINSIGHT]** を展開します。サインインしていない場合、**[Azure にサインイン...]** リンクが表示されます。

    ![サインイン リンクの画像](./media/hdinsight-for-vscode/hid-azure-hdinsight-sign-in.png)

2. **[Azure にサインイン]** をクリックします。サインイン リンクとコードが右下に表示されます。

    ![その他の環境でのサインイン手順](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin-code.png)

3. **[Copy & Open]\(コピーして開く\)** ボタンをクリックしてブラウザーを開き、コードを貼り付けて **[Continue]\(続行\)** ボタンをクリックします。サインインの成功に関するヒントが表示されます。

4. サインインすると、利用可能なサブスクリプションとクラスター (Spark、Hadoop、および HBase がサポートされています) が **[AZURE HDINSIGHT]** に一覧表示されます。 

   ![Azure HDInsight サブスクリプション](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

5. クラスターを展開して、Hive メタデータのデータベースとテーブルのスキーマを表示します。

   ![Azure HDInsight クラスター](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

## <a name="additional-features"></a>その他の機能

HDInsight for VS Code では、次の機能をサポートしています。

- **IntelliSense オートコンプリート**。 キーワード、メソッド、変数などの候補がポップアップ表示されます。 オブジェクトの種類ごとに異なるアイコンで表されます。

    ![HDInsight Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense エラー マーカー**。 言語サービスでは、Hive スクリプトの編集エラーに下線が引かれます。     
- **構文の強調表示**。 言語サービスでは、変数、キーワード、データ型、関数などを区別するために、異なる色が使用されます。 

    ![HDInsight Tools for Visual Studio Code の構文の強調表示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>次の手順

### <a name="demo"></a>デモ
* HDInsight for VS Code:[ビデオ](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>ツールと拡張機能

* [Azure Toolkit for IntelliJ を使用して VPN 経由で Apache Spark アプリケーションをリモートでデバッグする](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ を使用して SSH 経由で Apache Spark アプリケーションをリモートでデバッグする](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Azure Toolkit for Eclipse 上の HDInsight Tools を使用して Apache Spark アプリケーションを作成する](spark/apache-spark-eclipse-tool-plugin.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](spark/apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight の Microsoft Power BI で Apache Hive データを視覚化する](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [Visual Studio Code 用の PySpark 対話型環境を設定する](set-up-pyspark-interactive-environment.md)
* [Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>シナリオ
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](spark/apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](spark/apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](spark/apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](spark/apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](spark/apache-spark-job-debugging.md)



