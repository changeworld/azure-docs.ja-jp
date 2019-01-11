---
title: 'Azure Toolkit for IntelliJ: HDInsight クラスター向けの Spark アプリケーションを作成する '
description: Azure Toolkit for IntelliJ を使用して Scala で記述された Spark アプリケーションを開発し、HDInsight Spark クラスターに送信します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: maxluk
ms.openlocfilehash: 621d41c5c9558b5cb17d2a1e5a03d68f8af0df19
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53600643"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Apache Spark アプリケーションを作成する

Azure Toolkit for IntelliJ プラグインを使用して [Scala](https://www.scala-lang.org/) で記述された [Apache Spark](https://spark.apache.org/) アプリケーションを開発してから、それを IntelliJ 統合開発環境 (IDE) から直接 HDInsight Spark クラスターに送信します。 このプラグインには、次のような使い方があります。

* Scala Spark アプリケーションを開発して HDInsight Spark クラスターに送信する。
* Azure HDInsight Spark クラスター リソースにアクセスする。
* Scala Spark アプリケーションをローカルで開発して実行する。

プロジェクトを作成するには、[Azure Toolkit for IntelliJ を使用した Apache Spark アプリケーションの作成](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)に関するビデオを見てください。

> [!IMPORTANT]  
> このプラグインを使用して、Linux 上で HDInsight Spark クラスター専用のアプリケーションを作成し、送信することができます。
> 

## <a name="prerequisites"></a>前提条件

- HDInsight Linux での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。
- Oracle Java Development Kit。 [Oracle の Web サイト](https://aka.ms/azure-jdks)からインストールできます。
- IntelliJ IDEA。 この記事では、バージョン 2017.1 を使用します。 [JetBrains の Web サイト](https://www.jetbrains.com/idea/download/)からインストールできます。

## <a name="install-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ をインストールする
インストール手順については、「[Azure Toolkit for IntelliJ のインストール](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)」を参照してください。

## <a name="get-started"></a>作業の開始
[Azure サブスクリプションにサインイン](#sign-in-to-your-azure-subscription)するか、[HDInsight クラスターをリンク](#link-a-cluster)して開始できます。Ambari のユーザー名/パスワードまたはドメイン参加の資格情報を利用してください。


## <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインします。

1. IntelliJ IDE を起動し、Azure Explorer を開きます。 **[View]\(表示\)** メニューの **[Tools Windows]\(ツール ウィンドウ\)** を選択し、**[Azure Explorer]** を選択します。
       
   ![Azure Explorer のリンク](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. **[Azure]** ノードを右クリックし、**[Sign in]\(サインイン\)** を選択します。

1. **[Azure Sign In]\(Azure サインイン\)** ダイアログ ボックスの **[Sign in]\(サインイン\)** を選択し、Azure 資格情報を入力します。

    ![[Azure Sign In]\(Azure サインイン\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. サインイン後、**[Select Subscriptions]\(サブスクリプションの選択\)** ダイアログ ボックスに、その資格情報に関連付けられているすべての Azure サブスクリプションの一覧が表示されます。 **[Select]\(選択\)** ボタンをクリックします。

    ![[サブスクリプションの選択] ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. **[Azure Explorer]** タブで **[HDInsight]** を展開し、自分のサブスクリプションにある HDInsight Spark クラスターを表示します。
   
    ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. クラスターに関連付けられているリソース (ストレージ アカウントなど) を表示するには、クラスター名ノードをさらに展開します。
   
    ![展開されたクラスター名ノード](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>クラスターのリンク
Apache Ambari マネージド ユーザー名を使用して、通常の HDInsight クラスターをリンクできます。 同様に、ドメイン参加済み HDInsight クラスターでは、user1@contoso.com などのドメインとユーザー名を使用して、リンクできます。 Livy Service クラスターをリンクすることもできます。

1. **Azure Explorer** の **[Link a cluster]\(クラスターのリンク\)** を選択します。

   ![リンク クラスターのコンテキスト メニュー](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. クラスターをリンクするには、2 つの選択肢があります。 

   * HDInsight クラスターをリンクする場合、**[Cluster Info]\(クラスター情報\)** フィールドで **[HDInsight クラスター]** を選択し、**[Cluster Name/URL]\(クラスター名/URL\)**、**[ユーザー名]**、および **[パスワード]** を入力します。

      ![HDInsight クラスターのリンク ダイアログ](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

   * Livy Service クラスターをリンクする場合、**[Cluster Info]\(クラスター情報\)** フィールドで **[Livy Service]\(Livy サービス\)** を選び、**[Livy Endpoint]\(Livy エンドポイント\)**、**[クラスター名]** を入力します。 **[Yarn Endpoint]\(Yarn エンドポイント\)** は省略可能です。 **[認証]** フィールドには、2 つのオプションが提供されています。 **[基本認証]** と **[認証なし]** です。 **[基本認証]** を選択した場合、**[ユーザー名]** および **[パスワード]** を指定する必要があります。 認証エラーが発生した場合、ユーザー名とパスワードを確認する必要があります。
      
      ![Livy クラスターのリンク ダイアログ](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)
   
3. 入力した情報が正しい場合は、**[HDInsight]** ノードに [リンク済み] クラスターが表示されます。 これでリンクされたクラスターにアプリケーションを送信できるようになりました。

   ![リンクされたクラスター](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. また、**Azure 用エクスプローラー**からクラスターのリンクを解除することもできます。
   
   ![リンク解除されたクラスター](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="create-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスターでの Spark Scala アプリケーションの作成

1. IntelliJ IDEA を起動し、プロジェクトを作成します。 **[新しいプロジェクト]** ダイアログ ボックスで、次の手順を実行します。 

   a. **[HDInsight]** > **[Spark on HDInsight (Scala)]\(HDInsight の Spark (Scala)\)** を選択します。

   b. **[Build tool]\(ビルド ツール\)** ボックスの一覧で、ニーズに応じて次のいずれかを選択します。

      * **Maven**: Scala プロジェクト作成ウィザードをサポートする場合
      * **SBT**: 依存関係を管理し、Scala プロジェクトをビルドする場合

    ![[New Project]\(新しいプロジェクト\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. **[次へ]** を選択します。

1. Scala プロジェクト作成ウィザードは Scala プラグインをインストールしたかどうかを自動的に検出します。 **[インストール]** を選択します。

   ![Scala プラグインの確認](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. Scala プラグインをダウンロードするには **[OK]** をクリックします。 手順に従って IntelliJ を再起動します。 

   ![Scala プラグインのインストールのダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. **[New Project]\(新しいプロジェクト\)** ウィンドウで、次の操作を行います。  

    ![Spark SDK の選択](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. プロジェクト名とプロジェクトの場所を入力します。

   b. **[Project SDK]\(プロジェクトのSDK\)** ボックスの一覧で、Spark 2.x クラスターの場合は **Java 1.8** を選択し、Spark 1.x クラスターの場合は **Java 1.7** を選択します。

   c. **[Spark version]\(Spark のバージョン\)** ボックスの一覧には、Spark SDK と Scala SDK の適切なバージョンが組み合わされて表示されます。 Spark クラスターのバージョンが 2.0 より前の場合は、**Spark 1.x** を選択します。 それ以外の場合は、**Spark 2.x** を選択します。 この例では、**Spark 2.0.2 (Scala 2.11.8)** を使用します。

1. **[完了]** を選択します。

1. Spark プロジェクトでは、アーティファクトが自動的に作成されます。 次の操作を実行して、アーティファクトを表示します。

   a. **[File]\(ファイル\)** メニューの **[Project Structure]\(プロジェクトの構造\)** をクリックします。

   b. **[Project Structure]\(プロジェクトの構造)** ダイアログ ボックスで、**[Artifacts]\(アーティファクト)** を選択すると、作成された既定のアーティファクトが表示されます。 プラス記号 (**+**) をクリックして、独自のアーティファクトを作成することもできます。

      ![アーティファクト情報を示すダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. 次の操作を実行して、アプリケーション ソース コードを追加します。

   a. Project Explorer で、**[src]** を右クリックし、**[New]\(新規\)** をポイントして、**[Scala Class]\(Scala クラス\)** を選択します。
      
      ![Project Explorer から Scala クラスを作成するためのコマンド](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. **[Create New Scala Class]\(新規 Scala クラスの作成\)** ダイアログ ボックスで、名前を指定し、**[Kind]\(種類\)** ボックスの一覧で **[Object]\(オブジェクト\)** を選択して、**[OK]** をクリックします。
      
      ![[Create New Scala Class]\(新規 Scala クラスの作成\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. **MyClusterApp.scala** ファイルで、次のコードを貼り付けます。 このコードは HVAC.csv (すべての HDInsight Spark クラスターで使用可能) からデータを読み取り、CSV ファイルの 7 番目の列に 1 桁の数字のみが含まれる行を取得し、出力をクラスター用の既定のストレージ コンテナーの下にある **/HVACOut** に書き込みます。

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスターでの Spark Scala アプリケーションの実行
Scala アプリケーションを作成した後に、クラスターに送信できます。

1. プロジェクト エクスプローラーで、Java または Scala ファイルを検索して、右クリック メニューで **[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択します。
    
      ![[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\) コマンド](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. 構成ダイアログ ウィンドウで、次の値を指定して **[SparkJobRun]** をクリックします。

      ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      
    * **[Spark clusters (Linux only) (Spark クラスター (Linux のみ))]** では、アプリケーションを実行する HDInsight Spark クラスターを選択します。

    * IntelliJ プロジェクトまたはハード ドライブからアーティファクトを選択します。

    * **[Main class name] (メイン クラス名)** フィールド: 既定値は、選択されたファイルのメイン クラスです。 省略記号 (**...**) を選択して別のクラスを選ぶことにより、クラスを変更できます。   

    * **[Job Configurations] (ジョブ構成)** フィールド: 既定値は、上に示されている図のように設定されます。 ご自分のジョブの送信の値を変更したり、新しいキー/値を追加したりすることができます。 詳細:[Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html)

      ![[Spark Submission]\(Spark 送信\) ダイアログ ボックスのジョブ構成の意味](./media/apache-spark-intellij-tool-plugin/submit-job-configurations.png)

    * **[コマンド ライン引数]** フィールド: 必要に応じて、メイン クラスへのスペースで区切られた引数の値を入力できます。

    * **[Referenced Jars] (参照される JAR)** および **[Referenced Files] (参照されるファイル)** フィールド: 参照される JAR およびファイルのパスを入力できます (存在する場合)。 詳細:[Apache Spark の構成](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) 

      ![[Spark Submission]\(Spark 送信\) ダイアログ ボックスの jar ファイルの意味](./media/apache-spark-intellij-tool-plugin/jar-files-meaning.png)

       > [!NOTE]  
       > 参照される JAR および参照されるファイルをアップロードするには、[リソースをクラスターにアップロードする方法](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)に関するページを参照してください。
                         
    * **[アップロード パス]**: Jar または Scala プロジェクト リソースの送信用の保存先を示すことができます。 サポートされているのは、**[Azure BLOB]**、**[Use Spark interactive session to upload artifacts] (Spark 対話型セッションを使用して成果物をアップロードする)**、**[Use cluster default storage account] (クラスターの既定のストレージ アカウントを使用する)** の 3 つのストレージの種類と **[ADLS Gen1]** です。 次のスクリーンショットは、Azure BLOB の例です。

        ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-types.png)

        ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-blob.png)

3. **[SparkJobRun]** をクリックして、ご自分のプロジェクトを選択したクラスターに送信します。 **[Remote Spark Job in Cluster]\(クラスターのリモート Spark ジョブ\)** タブの下部にジョブ実行の進行状況が表示されます。 赤いボタンをクリックして、アプリケーションを停止できます。 ジョブ出力にアクセスする方法については、この記事の後の「Azure Toolkit for IntelliJ を使用して HDInsight Spark クラスターにアクセスして管理する」セクションを参照してください。
      
     ![[Spark Submission]\(Spark 送信\) ウィンドウ](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)


## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>HDInsight クラスターで Apache Spark アプリケーションをローカルまたはリモートでデバッグする 
クラスターに Spark アプリケーションを送信するお勧めの方法はほかにもあります。 それは、**実行/デバッグ構成**の IDE でパラメーターを設定する方法です。 詳細については、[Azure Toolkit for IntelliJ を使用して SSH 経由で HDInsight クラスターで Apache Spark アプリケーションをローカルまたはリモートでデバッグする方法](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh)に関するページを参照してください。



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用して HDInsight Spark クラスターにアクセスして管理する
Azure Toolkit for IntelliJ を使用してさまざまな操作を実行できます。

### <a name="access-the-job-view"></a>ジョブ ビューにアクセスする
1. Azure Explorer で **[HDInsight]** を展開します。Spark クラスター名を展開した後、**[Jobs]\(ジョブ\)** を選択します。  

    ![ジョブ ビューのノード](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. 右側のウィンドウの **[Spark Job View (Spark ジョブ ビュー)]** タブに、クラスター上で実行されていたすべてのアプリケーションが表示されます。 詳細情報を確認したいアプリケーションの名前を選択します。

    ![Application details](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

1. 実行中のジョブの基本情報が表示するには、ジョブ グラフにマウス ポインターを合わせます。 各ジョブについて生成されるステージのグラフと情報を確認するには、ジョブ グラフ上のノードを選択します。

    ![ジョブのステージの詳細](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. *Driver Stderr*、*Driver Stdout*、*Directory Info* などの頻繁に使用されるログを表示するには、**[Log]\(ログ\)** タブを選択します。

    ![ログの詳細](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. ウィンドウの上部にあるリンクを選択して、Spark 履歴 UI と YARN UI を (アプリケーション レベルで) 表示することもできます。

### <a name="access-the-spark-history-server"></a>Spark 履歴サーバーにアクセスする
1. Azure Explorer で、**[HDInsight]** を展開します。Spark クラスター名を右クリックし、**[Open Spark History UI]\(Spark 履歴 UI を開く\)** を選択します。 

1. 入力を求められたら、クラスターの管理者資格情報 (クラスターの設定時に指定したもの) を入力します。

1. Spark 履歴サーバーのダッシュボードでは、実行が終了したばかりのアプリケーションをアプリケーション名を使って探すことができます。 上記のコードでは、`val conf = new SparkConf().setAppName("MyClusterApp")` を使用してアプリケーション名を設定しました。 そのため、Spark アプリケーション名は **MyClusterApp** です。

### <a name="start-the-ambari-portal"></a>Ambari ポータルを起動する
1. Azure Explorer で、**[HDInsight]** を展開します。Spark クラスター名を右クリックし、**[Open Cluster Management Portal (Ambari)]\(クラスター管理ポータルを開く (Ambari)\)** を選択します。 

1. 入力を求められたら、クラスターの管理者資格情報を入力します。 これらの資格情報は、クラスターの設定プロセス中に指定したものです。

### <a name="manage-azure-subscriptions"></a>Azure サブスクリプションの管理
Azure Toolkit for IntelliJ の既定では、すべての Azure サブスクリプションからの Spark クラスターが一覧表示されます。 必要に応じて、アクセスするサブスクリプションを指定できます。 

1. Azure Explorer で、**[Azure]** ルート ノードを右クリックし、**[Manage Subscriptions]\(サブスクリプションの管理\)** を選択します。 

1. ダイアログ ボックスで、アクセスしないサブスクリプションのチェック ボックスをオフにし、**[Close]\(閉じる\)** を選択します。 Azure サブスクリプションからサインアウトする場合は、**[Sign Out]\(サインアウト\)** を選択することもできます。

## <a name="spark-console"></a>Spark コンソール
Spark Local Console(Scala) を実行したり、Spark Livy Interactive Session Console(Scala) を実行したりすることができます。

### <a name="spark-local-consolescala"></a>Spark Local Console(Scala)
1. 以前に設定していない場合は、構成を設定します。 **[Run/Debug Configurations]\(デバッグの実行/構成\)** ウィンドウで、**+**->**[Azure HDInsight Spark]** をクリックし、タブ **[Locally Run]\(ローカルで実行\)** および **[Remotely Run in Cluster]\(クラスターでリモートから実行\)** を選択します。次に、メイン クラスを選択し、**[OK]** をクリックします。

    ![ローカル コンソールの構成の設定](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)
 
2. 対応するメイン クラス ファイルを開いて **[Spark Console]\(Spark コンソール\)** を右クリックし、**[Run Spark Local Console(Scala)]\(Spark ローカル コンソール (Scala) の実行\)** をクリックします。 または、**[ツール]** メニュー ->**[Spark Console]\(Spark コンソール\)**->**[Run Spark Local Console(Scala)]\(Spark Local Console(Scala) の実行\)** に移動して、コンソールを起動します。 依存関係を自動修正するかどうかを尋ねる 2 つのダイアログ ボックスが表示されます。 **[自動修正]** ボタンをクリックします。

    ![Spark 自動修正 1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark 自動修正 2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

    ![Spark のローカル エントリ ポイント](./media/apache-spark-intellij-tool-plugin/spark-console-local-entry-script.png)

3. ローカル コンソールが正常に起動されると、 次のように表示されます。 必要な操作を行うことができます。 たとえば、「**sc.appName**」と入力して、Ctrl キーを押しながら Enter キーを押すと、結果が表示されます。 ローカルのコンソールを終了するには、赤いボタンをクリックします。

    ![ローカル コンソールの結果](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy Interactive Session Console(Scala)
これは、IntelliJ 2018.2 および 2018.3 上でのみサポートされます。

1. 以前に設定していない場合は、構成を設定します。 **[Run/Debug Configurations]\(実行/デバッグの構成\)** ウィンドウで、**+**->**[Azure HDInsight Spark]** をクリックして、**[Remotely Run in Cluster]\(クラスターでリモートから実行\)** タブを選択し、クラスター名とメイン クラスを選んで **[OK]** をクリックします。

    ![対話型コンソールの構成エントリの追加](./media/apache-spark-intellij-tool-plugin/interactive-console-add-config-entry.png)

    ![対話型コンソールの構成の設定](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

2. ご自分のメイン クラスに対応するファイルを開いて **[Spark Console]\(Spark コンソール\)** を右クリックし、**[Run Spark Livy Interactive Session Console(Scala)]\(Spark Livy Interactive Session Console(Scala) の実行\)** をクリックします。 または **[ツール]** メニューに移動して、**[Spark Console]\(Spark コンソール\)**、**[Run Spark Livy Interactive Session Console(Scala)]\(Spark Livy Interactive Session Console(Scala) の実行\)** の順にクリックして、コンソールを起動します。

3. コンソールが正常に起動したら、必要な操作を行うことができます。 たとえば、「**sc.appName**」と入力して、Ctrl キーを押しながら Enter キーを押すと、結果が表示されます。

    ![Interactive Console の結果](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>選択項目の Spark コンソールへの送信
一部のコードをローカルのコンソールまたは Livy 対話式セッション コンソール (Scala) に送信して、スクリプトの結果を事前に確認すると便利です。 Scala ファイル内の一部のコードを強調表示し、右クリックして **[Send Selection To Spark Console]\(選択項目を Spark コンソールに送信する\)** を選択します。 選択したコードがコンソールに送信され、実行されます。 結果は、コンソール内のコードの後に表示されます。 コンソールは、エラーが存在する場合は、エラーをチェックします。 

   ![選択項目の Spark コンソールへの送信](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用できるように既存の IntelliJ IDEA アプリケーションを変換する
IntelliJ IDEA で作成した既存の Spark Scala アプリケーションを、Azure Toolkit for IntelliJ に対応するように変換することができます。 その後、プラグインを使用して、そのアプリケーションを HDInsight Spark クラスターに送信できます。

1. IntelliJ IDEA で作成した既存の Spark Scala アプリケーションの、関連付けられている .iml ファイルを開きます。

1. ルート レベルに、次のような **module** 要素があります。
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   **module** 要素が次のようになるように、この要素を編集して `UniqueKey="HDInsightTool"` を追加します。
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. 変更を保存します。 これで、アプリケーションは Azure Toolkit for IntelliJ との互換性を持つようになります。 これをテストするには、Project Explorer でプロジェクト名を右クリックします。 これで、ポップアップ メニューで、**[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択できるようになります。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>ローカル実行で、"*Please use a larger heap size*" というエラーが発生した場合
Spark 1.6 で 32 ビットの Java SDK を使用している場合、ローカル実行時に次のようなエラーが発生することがあります。

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

これらのエラーは、Spark を実行するのに十分なヒープ サイズがないために発生します  Spark には少なくとも 471 MB のヒープが必要となります  (詳細については、[SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081) に関するページを参照してください)。簡単な解決方法としては、64 ビットの Java SDK を使用することが考えられます。 または、IntelliJ で JVM 設定に以下のオプションを追加します。

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![IntelliJ の [VM options]\(VM オプション\) ボックスへのオプションの追加](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>FAQ
クラスターがビジー状態の場合、以下のエラーが表示されることがあります。

![クラスターがビジー状態のときの Intellij get エラー](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![クラスターがビジー状態のときの Intellij get エラー](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>フィードバックと既知の問題
現在、Spark の出力を直接表示する機能はサポートされていません。

ご提案やフィードバックがある場合、またはこのプラグインを使用していて問題が発生した場合は、hdivstool@microsoft.com に電子メールをお送りください。

## <a name="seealso"></a>次のステップ
* [概要:Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>デモ
* Scala プロジェクトを作成する (ビデオ): [Apache Spark Scala アプリケーションを作成する](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* リモート デバッグ (ビデオ): [Azure Toolkit for IntelliJ を使用して HDInsight クラスターで Apache Spark アプリケーションをリモートでデバッグする](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>シナリオ
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用して対話型データ分析を実行する](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure Toolkit for IntelliJ を使用して VPN 経由で Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ を使用して SSH 経由で Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Azure Toolkit for Eclipse 上の HDInsight Tools を使用して Apache Spark アプリケーションを作成する](apache-spark-eclipse-tool-plugin.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
