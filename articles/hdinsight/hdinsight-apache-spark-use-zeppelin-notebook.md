---
title: "HDInsight Linux の Apache Spark クラスターに Zeppelin Notebook をインストールする | Microsoft Docs"
description: "HDInsight Linux の Spark クラスターに Zeppelin Notebook をインストールして使用する手順を説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: cb276220-fb02-49e2-ac55-434fcb759629
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: cc59d7785975e3f9acd574b516d20cd782c22dac
ms.openlocfilehash: 3fb67d1ecbaa9fd36bb88212e1a3fcd5208cb169


---
# <a name="install-zeppelin-notebooks-for-apache-spark-cluster-on-hdinsight-linux"></a>HDInsight Linux の Apache Spark クラスターに Zeppelin Notebook をインストールする
Apache Spark クラスターに Zeppelin Notebook をインストールする方法と、Zeppelin Notebook を使用して Spark ジョブを実行する方法について説明します。

> [!IMPORTANT]
> Spark クラスターでは、既定で Zeppelin Notebook を使用できるようになりました。 Spark クラスターに明示的に Zeppelin Notebook をインストールする必要はありません。 詳しくは、「[HDInsight Linux の Apache Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-zeppelin-notebook.md)」をご覧ください。
>
>

**前提条件:**

* このチュートリアルを開始する前に、Azure サブスクリプションが必要です。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。
* SSH クライアント。 Linux および UNIX のディストリビューション、または Macintosh OS X の場合、オペレーティング システムに `ssh` コマンドが用意されています。 Windows の場合は [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

  > [!NOTE]
  > `ssh`または PuTTY 以外の SSH クライアントを使用する場合は、クライアントのマニュアルで SSH トンネルの確立方法を確認してください。
  >
  >
* SOCKS プロキシを使用するように構成できる Web ブラウザー
* **(省略可能)**: [FoxyProxy](http://getfoxyproxy.org/,) などのプラグイン。FoxyProxy は、トンネル経由で特定の要求のみをルーティングするルールを適用できます。

  > [!WARNING]
  > FoxyProxy などのプラグインを使用しない場合、ブラウザー経由で送信されたすべての要求が、トンネル経由でルーティングされる場合があります。 その結果、ブラウザーの Web ページの読み込みが低速になる可能性があります。
  >
  >

## <a name="install-zeppelin-on-a-spark-cluster"></a>Spark クラスターへの Zeppelin のインストール
Zeppelin は、スクリプト アクションを使用して Spark クラスターにインストールできます。 スクリプト アクションは、既定で使用できないクラスターにコンポーネントをインストールするためにカスタム スクリプトを使用します。 HDInsight .NET SDK を使用するか、または Azure PowerShell を使用すると、カスタム スクリプトを使用して、Azure ポータルから Zeppelin をインストールできます。 スクリプトによる Zeppelin のインストールは、クラスター作成の一環として、またはクラスターの稼働後に実行できます。 これを行う方法については、以降のセクションのリンクで説明します。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用
Zeppelin をインストールするスクリプト アクションを実行するために Azure ポータルを使用する方法については、「 [スクリプト アクションを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)」をご覧ください。 その記事の手順は、いくつか変更する必要があります。

* Zeppelin をインストールするには、スクリプトを使用する必要があります。 HDInsight の Spark クラスターに Zeppelin をインストールするカスタム スクリプトは、次のリンクから入手できます。

  * Spark 1.6.0 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Spark 1.5.2 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* スクリプト アクションは、ヘッドノードでのみ実行する必要があります。
* スクリプトにパラメーターは必要ありません。

### <a name="using-hdinsight-net-sdk"></a>HDInsight .NET SDK の使用
Zeppelin をインストールするスクリプト アクションを実行するために HDInsight .NET SDK を使用する方法については、「 [スクリプト アクションを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)」を参照してください。 その記事の手順は、いくつか変更する必要があります。

* Zeppelin をインストールするには、スクリプトを使用する必要があります。 HDInsight の Spark クラスターに Zeppelin をインストールするカスタム スクリプトは、次のリンクから入手できます。

  * Spark 1.6.0 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Spark 1.5.2 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* スクリプトにパラメーターは必要ありません。
* 作成しているクラスターの種類を Spark に設定します。

### <a name="using-azure-powershell"></a>Azure PowerShell の使用
次の PowerShell スニペットを使用すると、Zeppelin がインストールされている HDInsight Linux の Spark クラスターを作成できます。 所有している Spark クラスターのバージョンに応じて、対応するカスタム スクリプトへのリンクを含めるために、次の PowerShell スニペットを更新する必要があります。

* Spark 1.6.0 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Spark 1.5.2 クラスターの場合 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    Login-AzureRMAccount

    # PROVIDE VALUES FOR THE VARIABLES
    $clusterAdminUsername="admin"
    $clusterAdminPassword="<<password>>"
    $clusterSshUsername="adminssh"
    $clusterSshPassword="<<password>>"
    $clusterName="<<clustername>>"
    $clusterContainerName=$clusterName
    $resourceGroupName="<<resourceGroupName>>"
    $location="<<region>>"
    $storage1Name="<<storagename>>"
    $storage1Key="<<storagekey>>"
    $subscriptionId="<<subscriptionId>>"

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
    $clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
    $passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
    $clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)

    $azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
    $azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
    $azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"

    Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"

    New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"

## <a name="set-up-ssh-tunneling-to-access-a-zeppelin-notebook"></a>Zeppelin Notebook にアクセスするための SSH トンネリングのセットアップ
HDInsight Linux の Spark クラスターで実行されている Zeppelin Notebook にアクセスするには、SSH トンネルを使用します。 以下の手順では、ssh コマンド ライン (Linux) および PuTTY (Windows) を使用して SSH トンネルを作成する方法を示します。

### <a name="create-a-tunnel-using-the-ssh-command-linux"></a>SSH コマンドを使用してトンネルを作成する (Linux)
次のように `ssh` コマンドを使用して、SSH トンネルを作成します。 **USERNAME** は、実際の HDInsight クラスターの SSH ユーザーに置き換えます。また、**CLUSTERNAME** は、HDInsight クラスターの名前に置き換えます。

    ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

これで、ローカル ポート 9876 へのトラフィックを SSH 経由でクラスターにルーティングする接続が作成されます。 オプションは次のとおりです。

* **D 9876** - トンネル経由でトラフィックをルーティングするローカル ポートです。
* **C** - すべてのデータを圧縮します (Web トラフィックの大部分はテキストであるため)。
* **2** - SSH プロトコル バージョンを強制的に 2 のみに指定します。
* **q** - Quiet モードです。
* **T** - pseudo-tty の割り当てを無効にします (ポートの転送だけを行うため)。
* **n** - STDIN を読み取らないようにします (ポートの転送だけを行うため)。
* **N** - リモート コマンドを実行しません (ポートの転送だけを行うため)。
* **f** - バックグラウンドで実行します。

SSH キーを使用してクラスターを構成した場合は、SSH 秘密キーのパスを `-i` パラメーターで指定する必要があることがあります。

コマンドが完了すると、ローカル コンピューター上でポート 9876 に送信されるトラフィックは、クラスターのヘッド ノードに Secure Sockets Layer (SSL) 経由でルーティングされ、そのヘッド ノードで生成されたかのように見えます。

### <a name="create-a-tunnel-using-putty-windows"></a>PuTTY を使用してトンネルを作成する (Windows)
次の手順に従ってPuTTY を使用して、SSH トンネルを作成します。

1. PuTTY を開き、接続情報を入力します。 HDInsight で PuTTY を使用する方法については、「 [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md) 」を参照してください。
2. ダイアログの左にある **[カテゴリ]** セクションで、**[接続]**、**[SSH]** の順に展開し、**[トンネル]** を選択します。
3. **[SSH ポートの転送を管理するオプション]** フォームに次の情報を入力します。

   * **[ソース ポート]** - 転送するクライアント上のポート (**9876** など)。
   * **[宛先]** - Linux ベースの HDInsight クラスターの SSH アドレス (**mycluster-ssh.azurehdinsight.net** など)。
   * **[動的]** - 動的な SOCKS プロキシを有効にします。

     ![トンネリング オプションの画像](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)
4. **[追加]** をクリックして設定を追加し、**[開く]** をクリックして SSH 接続を開きます。
5. プロンプトが表示されたら、サーバーにログインします。 これにより、SSH セッションが確立され、トンネルが有効になります。

### <a name="use-the-tunnel-from-your-browser"></a>ブラウザーからトンネルを使用する
> [!NOTE]
> このセクションの手順では、FireFox ブラウザーを使用します。FireFox は、Linux、UNIX、Macintosh OS X、Windows の各システムで無料で使用できるためです。 Google Chrome、Microsoft Edge、Apple Safari などの最近のブラウザーでも動作するはずですが、一部の手順で使用されている FoxyProxy プラグインがブラウザーによっては動作しない可能性があります。
>
>

1. **SOCKS v5** プロキシとして **localhost:9876** を使用するようにブラウザーを構成します。 Firefox の設定は次のようになります。 9876 以外のポートを使用する場合は、そのポート番号に変更します。

    ![Firefox の設定の画像](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

   > [!NOTE]
   > **[リモート DNS]** を選択するとドメイン ネーム システム (DNS) 要求は HDInsight クラスターを使用して解決されます。 選択しないと、DNS はローカルに解決されます。
   >
   >
2. Firefox でプロキシ設定を有効または無効にして、 [http://www.whatismyip.com/](http://www.whatismyip.com/) などのサイトにアクセスして、トラフィックがトンネルを経由しているかどうかを確認します。 設定が有効の場合、IP アドレスは Microsoft Azure データセンター内のコンピューターの IP アドレスになります。

### <a name="browser-extensions"></a>ブラウザー拡張
ブラウザーをトンネル ワークを使用するように構成する場合、通常、一部のトラフィックをトンネルを経由で送信しないように設定する必要があることがあります。 [FoxyProxy](http://getfoxyproxy.org/) などのブラウザー拡張機能では、URL 要求に対するパターン マッチングがサポートされるため (FoxyProxy Standard または Plus のみ)、特定の URL の要求のみがトンネル経由で送信されます。

FoxyProxy Standard をインストール済みの場合は、次の手順を使用して、HDInsight の トラフィックだけをトンネル経由で転送するように構成します。

1. ブラウザーで FoxyProxy 拡張を開きます。 Firefox などで、アドレス フィールドの横にある FoxyProxy アイコンを選択します。

    ![foxyproxy のアイコン](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)
2. **[新しいプロキシの追加]**、**[全般]** タブの順に選択し、**HDInsightProxy** のプロキシ名を入力します。

    ![foxyproxy の全般](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)
3. **[プロキシの詳細]** タブを選択し、次のフィールドを入力します。

   * **[ホストまたは IP アドレス]** - ローカル マシンで SSH トンネルを使用するため「localhost」と入力します。
   * **[ポート]** - SSH トンネル用に使用するポート。
   * **[SOCKS プロキシ]** - ブラウザーでのプロキシとしてのトンネルの使用を有効にします。
   * **[SOCKS v5]** - プロキシに必要なバージョンを設定します。

     ![foxyproxy のプロキシ](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)
4. **[URL パターン]** タブを選択し、**[新しいパターンの追加]** を選択します。 以下を使用してパターンを定義し、 **[OK]**をクリックします。

   * **[パターン名]** - **zeppelinnotebook** - パターンのフレンドリ名です。
   * **[URL パターン]** - **\*hn0*** - Zeppelin Notebook がホストされているエンドポイントの内部の完全修飾ドメイン名と一致するパターンを定義します。Zeppelin Notebook はクラスターの headnode0 だけで利用可能であり、エンドポイントは通常は `http://hn0-<string>.internal.cloudapp.net` であるため、パターン **hn0** を使用すると、要求は Zeppelin エンドポイントにリダイレクトされることになります。

       ![foxyproxy のパターン](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)
5. **[OK]** をクリックしてプロキシを追加し、**[プロキシの設定]** を閉じます。
6. FoxyProxy ダイアログの上部で、**[モードの選択]** を **[定義済みのパターンと優先順位に基づいてプロキシを使用]** に変更し、**[閉じる]** を選択します。

    ![foxyproxy の選択モード](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

これらを完了すると、 **hn0** という文字列を含む URL の要求のみが SSL トンネル経由で送信されます。

## <a name="access-the-zeppelin-notebook"></a>Zeppelin Notebook へのアクセス
SSH トンネリングをセットアップしたら、以下の手順で、Spark クラスター上の Zeppelin Notebook にアクセスできます。 このセクションでは、%sql および %hive の各ステートメントを実行する方法を説明します。

1. Web ブラウザーから、次のエンドポイントを開きます。

        http://hn0-myspar:9995

   * **hn0** は headnode0 を表します。
   * **myspar** は Spark クラスター名の最初の 6 文字です。
   * **9995** は Zeppelin Notebook にアクセスできるポートです。
2. 新しい Notebook を作成します。 ヘッダー ウィンドウで **[Notebook]** をクリックし、**[Note の新規作成]** をクリックします。

    ![新しい Zeppelin Notebook を作成します](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "Create a new Zeppelin notebook")

    同じページの **[Notebook]** 見出しの下に、**Note XXXXXXXXX** で名前が始まる新しい Notebook が表示されます。 新しい Notebook をクリックします。
3. 新しい Notebook の Web ページで、見出しをクリックし、必要に応じて Notebook の名前を変更します。 Enter キーを押して名前の変更を保存します。 また、Notebook のヘッダーの右上隅に **[接続]** というステータスが表示されることを確認します。

    ![Zeppelin Notebook のステータス](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Zeppelin notebook status")

### <a name="run-sql-statements"></a>SQL ステートメントを実行する
1. サンプル データを一時テーブルに読み込みます。 HDInsight の Spark クラスターを作成すると、サンプル データ ファイル **hvac.csv** が、関連するストレージ アカウントの **\HdiSamples\SensorSampleData\hvac** にコピーされます。

    新しい Notebook に既定で作成される空の段落に、次のスニペットを貼り付けます。

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0),
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()

        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")

    **Shift + Enter** キーを押すか、段落の **[プレイ]** ボタンをクリックして、スニペットを実行します。 段落の右上隅にあるステータスが、[準備完了]、[保留中]、[実行中]、[完了] の順に進行します。 出力が同じ段落の下に表示されます。 スクリーンショットは次のようになります。

    ![生データから一時テーブルを作成します](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Create a temporary table from raw data")

    各段落にタイトルを指定することもできます。 右上隅の **[設定]** アイコンをクリックし、**[タイトルの表示]** をクリックします。
2. **hvac** テーブルに対して Spark SQL ステートメントを実行できます。 次のクエリを新しい段落に貼り付けます。 このクエリは、ビル ID と、特定の日の各ビルの目標温度と実温度の差を取得します。 **Shift + Enter**キーを押します。

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date
        from hvac
        where date = "6/1/13"

    先頭にある **%sql** ステートメントは、Spark SQL インタープリターを使用するように Notebook に指示します。 定義済みのインタープリターは、Notebook ヘッダーの **[インタープリター]** タブで見ることができます。

    次のスクリーンショットでは出力を示します。

    ![Notebook を使用して Spark SQL ステートメントを実行します](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Run a Spark SQL statement using the notebook")

     表示オプション (四角で囲ってある部分) をクリックして、同じ出力の異なる表現に切り替えることができます。 **[設定]** をクリックして、出力のキーと値の構成を選択します。 上記の画面キャプチャでは、**buildingID** をキーとして使用し、**temp_diff** の平均を値として使用しています。
3. クエリの変数を使用して Spark SQL ステートメントを実行することもできます。 次のスニペットでは、クエリで変数 **Temp**と照会できる値を定義する方法を示します。 初めてクエリを実行すると、変数に指定した値がドロップダウンに自動的に設定されます。

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}"

    このスニペットを新しい段落に貼り付けて、 **Shift + Enter**キーを押します。 次のスクリーンショットでは出力を示します。

    ![Notebook を使用して Spark SQL ステートメントを実行します](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Run a Spark SQL statement using the notebook")

    後続のクエリでは、ドロップダウンから新しい値を選択し、クエリを再実行できます。 **[設定]** をクリックして、出力のキーと値の構成を選択します。 上の画面キャプチャでは、**buildingID** をキーとして、**temp_diff** の平均を値として、**targettemp** をグループとして使用しています。
4. Spark SQL インタープリターを再起動して、アプリケーションを終了します。 上部の **[インタープリター]** タブをクリックし、Spark インタープリターの **[再起動]** をクリックします。

    ![Zeppelin インタープリターを再起動します](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Restart the Zeppelin intepreter")

### <a name="run-hive-statements"></a>Hive ステートメントを実行する
1. Zeppelin Notebook で **[Interpreter]** ボタンをクリックします。

    ![Hive インタープリターを更新します](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Update Hive interpreter")
2. **Hive** インタープリターの場合、**[edit]** をクリックします。

    ![Hive インタープリターを更新します](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Update Hive interpreter")

    次のプロパティを更新します。

   * **[default.password]** を、HDInsight Spark クラスターの作成時に管理者ユーザーに指定したパスワードに設定します。
   * **[default.url]** を `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2` に設定します。 **\<spark_cluster_name>** を Spark クラスターの名前で置き換えます。
   * **[default.user]** を、クラスターの作成時に指定した管理者ユーザーの名前に設定します。 たとえば、 *admin*です。
3. **[Save]** をクリックし、Hive インタープリターを再起動することを求めるメッセージが表示されたら、**[OK]** をクリックします。
4. 新しい Notebook を作成し、次のステートメントを実行してクラスター上のすべての Hive テーブルを一覧表示します。

        %hive
        SHOW TABLES

    既定では、HDInsight クラスターには **hivesampletable** という名前のサンプルのテーブルがあるため、次のような出力が表示されます。

    ![Hive の出力](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Hive output")
5. 次のステートメントを実行してテーブル内のレコードを一覧表示します。

        %hive
        SELECT * FROM hivesampletable LIMIT 5

    出力は次のように表示されます。

    ![Hive の出力](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Hive output")

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md



<!--HONumber=Nov16_HO3-->


