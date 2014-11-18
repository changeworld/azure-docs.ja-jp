<properties urlDisplayName="Use Hadoop Oozie in HDInsight" pageTitle="HDInsight での Hadoop Oozie の使用 | Azure" metaKeywords="" description="ビッグ データ ソリューションとして HDInsight で Hadoop Oozie を使用します。Oozie ワークフローを定義し、Oozie ジョブを送信する方法について説明します。" metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight での Hadoop Oozie の使用" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# HDInsight の Hadoop での Oozie の使用

HDInsight でワークフローを定義する方法とワークフローを実行する方法について説明します。Oozie コーディネーターについては、「[HDInsight での時間ベースの Oozie コーディネーターの使用][HDInsight での時間ベースの Oozie コーディネーターの使用]」を参照してください。

**所要時間:** 40 分

## この記事の内容

1.  [Oozie とは][Oozie とは]
2.  [前提条件][前提条件]
3.  [Oozie ワークフロー ファイルを定義する][Oozie ワークフロー ファイルを定義する]
4.  [Oozie プロジェクトを展開してチュートリアルを準備する][Oozie プロジェクトを展開してチュートリアルを準備する]
5.  [ワークフローを実行する][ワークフローを実行する]
6.  [次のステップ][次のステップ]

## <span id="whatisoozie"></span></a>Oozie とは

Apache Oozie は Hadoop ジョブを管理するワークフローおよび調整システムです。Hadoop スタックと統合されていて、Apache MapReduce、Apache Pig、Apache Hive、Apache Sqoop の Hadoop ジョブをサポートしています。Java プログラムやシェル スクリプトのような、システム特有のジョブのスケジュールを設定するのに使用することもできます。

実装するワークフローは、2 つのアクションを含みます。

![ワークフロー図][ワークフロー図]

1.  Hive アクションは、HiveQL スクリプトを実行して、log4j ログ ファイルのログ レベル タイプごとの出現回数をカウントします。各 log4j ログは、ログ タイプと重要度を表す [LOG LEVEL] フィールドから成る行によって構成されています。次に例を示します。

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    Hive スクリプトの出力は次のようになります。

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Hive の詳細については、「[HDInsight での Hive の使用][HDInsight での Hive の使用]」を参照してください。

2.  Sqoop アクションは、Azure SQL データベースのテーブルに HiveQL アクションの出力をエクスポートします。Sqoop の詳細については、「[HDInsight での Sqoop の使用][HDInsight での Sqoop の使用]」を参照してください。

> [WACOM.NOTE] HDInsight クラスターでサポートされている Oozie のバージョンについては、「[HDInsight で提供されるクラスター バージョンの新機能][HDInsight で提供されるクラスター バージョンの新機能]」を参照してください。

> [WACOM.NOTE] このチュートリアルでは、HDInsight クラスター Version 2.1 と 3.0 を取り扱います。この記事に関して、HDInsight エミュレーターを使用したテストは実施されていません。

## <span id="prerequisites"></span></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   **コンピューター**。Azure PowerShell がインストールされ構成されている必要があります。手順については、[Azure PowerShell のインストールおよび構成に関するページ][Azure PowerShell のインストールおよび構成に関するページ]を参照してください。PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを *RemoteSigned* に設定する必要があります。「[Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)][Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)]」を参照してください。
-   **HDInsight クラスター**。HDInsight クラスターの作成については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」または「[Azure HDInsight の概要][Azure HDInsight の概要]」を参照してください。このチュートリアルを読み進めるには、次のデータが必要です。

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">クラスター プロパティ</th>
    <th align="left">PowerShell 変数名</th>
    <th align="left">値</th>
    <th align="left">説明</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">HDInsight クラスター名</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">このチュートリアルを実行する HDInsight クラスター。</td>
    </tr>
    <tr class="even">
    <td align="left">HDInsight クラスター ユーザー名</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">HDInsight クラスター ユーザーのユーザー名。</td>
    </tr>
    <tr class="odd">
    <td align="left">HDInsight クラスター ユーザー パスワード</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">HDInsight クラスター ユーザーのパスワード。</td>
    </tr>
    <tr class="even">
    <td align="left">Azure Storage アカウント名</td>
    <td align="left">$storageAccountName</td>
    <td align="left"></td>
    <td align="left">HDInsight クラスターで利用できる Azure Storage アカウント。このチュートリアルでは、クラスターのプロビジョニング プロセス中に指定された既定のストレージ アカウントを使用します。</td>
    </tr>
    <tr class="odd">
    <td align="left">Azure BLOB コンテナー名。</td>
    <td align="left">$containerName</td>
    <td align="left"></td>
    <td align="left">この例では、既定の HDInsight クラスター ファイル システムで使用する Azure BLOB ストレージ コンテナーを使用します。既定では、HDInsight クラスターと同じ名前です。</td>
    </tr>
    </tbody>
    </table>

-   **Azure SQL データベース**。コンピューターから SQL データベース サーバーに対するアクセスを許可するようにファイアウォール ルールを構成する必要があります。SQL データベースの作成方法とファイアウォールの構成方法については、「[Azure SQL データベースの概要][Azure SQL データベースの概要]」を参照してください。この記事には、このチュートリアルに必要な SQL データベース テーブルを作成する PowerShell スクリプトが紹介されています。

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">SQL データベースのプロパティ</th>
    <th align="left">PowerShell 変数名</th>
    <th align="left">値</th>
    <th align="left">説明</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">SQL データベース サーバー名</td>
    <td align="left">$sqlDatabaseServer</td>
    <td align="left"></td>
    <td align="left">Sqoop によるデータのエクスポート先となる SQL データベース サーバー。</td>
    </tr>
    <tr class="even">
    <td align="left">SQL データベース ログイン名</td>
    <td align="left">$sqlDatabaseLogin</td>
    <td align="left"></td>
    <td align="left">SQL データベースのログイン名。</td>
    </tr>
    <tr class="odd">
    <td align="left">SQL データベース ログイン パスワード</td>
    <td align="left">$sqlDatabaseLoginPassword</td>
    <td align="left"></td>
    <td align="left">SQL データベースのログイン パスワード。</td>
    </tr>
    <tr class="even">
    <td align="left">SQL データベース名</td>
    <td align="left">$sqlDatabaseName</td>
    <td align="left"></td>
    <td align="left">Sqoop によるデータのエクスポート先となる Azure SQL データベース。</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] 既定では、Azure SQL データベースは Azure HDInsight のような Azure サービスからの接続を許可します。このファイアウォール設定が無効になっている場合は、Azure 管理ポータルから有効にする必要があります。SQL データベースの作成方法とファイアウォール ルールの構成方法については、「[SQL データベースの作成と構成][SQL データベースの作成と構成]」を参照してください。

> [WACOM.NOTE] テーブルに値を入力します。そうしておくと、このチュートリアルを読み進める際に役に立ちます。

## <span id="defineworkflow"></span></a>Oozie ワークフローと関連 HiveQL スクリプトを定義する

Oozie ワークフロー定義は hPDL (XML プロセス定義言語) で書かれています。既定のワークフロー ファイル名は *workflow.xml* です。ワークフロー ファイルはローカルに保存し、このチュートリアルの後の方で、Azure PowerShell を使用して HDInsight クラスターに展開します。

ワークフローの Hive アクションは、HiveQL スクリプト ファイルを呼び出します。このスクリプト ファイルは HiveQL ステートメントを 3 つ含んでいます。

1.  **DROP TABLE ステートメント** は、log4j Hive テーブルが存在する場合、削除します。
2.  **CREATE TABLE ステートメント** は、log4j ログ ファイルの場所を指す log4j Hive 外部テーブルを作成します。フィールド区切り記号はコンマ (,) です。既定の行区切り記号は "\\n" です。Hive 外部テーブルは、Oozie ワークフローを複数回実行する場合に、データ ファイルが元の場所から削除されないようにするために使用されています。
3.  **INSERT OVERWRITE ステートメント**は、log4j Hive テーブルの各ログ レベル タイプの出現回数をカウントし、その出力を Azure Storage BLOB (WASB) の場所に保存します。

Hive パスには既知の問題があります。この問題に見舞われるのは、Oozie ジョブを送信するときです。この問題の解決方法は [TechNet Wiki][TechNet Wiki] をご覧ください。

**ワークフローによって呼び出される HiveQL スクリプト ファイルを定義するには**

1.  次の内容を持つテキスト ファイルを作成します。

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    スクリプトでは 3 つの変数が使用されています。

    -   ${hiveTableName}
    -   ${hiveDataFolder}
    -   ${hiveOutputFolder}

    ワークフロー定義ファイル (このチュートリアルでは workflow.xml) は、実行時にこの HiveQL スクリプトにこれらの値を渡します。

2.  ファイルを **C:\\Tutorials\\UseOozie\\useooziewf.hql** という名前で保存します。エンコーディングは、**ANSI (ASCII)** を使用します (使っているテキスト エディターにこのオプションがない場合は、メモ帳を使用します)。チュートリアルでは、このスクリプト ファイルは後で HDInsight クラスターに展開されます。

**ワークフローを定義するには**

1.  次の内容を持つテキスト ファイルを作成します。

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/> 

            <action name="RunHiveScript">
                <hive xmlns="uri:oozie:hive-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.job.queue.name</name>
                            <value>${queueName}</value>
                        </property>
                    </configuration>
                    <script>${hiveScript}</script>
                    <param>hiveTableName=${hiveTableName}</param>
                    <param>hiveDataFolder=${hiveDataFolder}</param>
                    <param>hiveOutputFolder=${hiveOutputFolder}</param>
                </hive>
                <ok to="RunSqoopExport"/>
                <error to="fail"/>
            </action>

            <action name="RunSqoopExport">
                <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.compress.map.output</name>
                            <value>true</value>
                        </property>
                    </configuration>
                <arg>export</arg>
                <arg>--connect</arg> 
                <arg>${sqlDatabaseConnectionString}</arg> 
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg> 
                <arg>--export-dir</arg> 
                <arg>${hiveOutputFolder}</arg> 
                <arg>-m</arg> 
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\001"</arg>
                </sqoop>
                <ok to="end"/>
                <error to="fail"/>
            </action>

            <kill name="fail">
                <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>

           <end name="end"/>
        </workflow-app>

    ワークフローでは 2 つのアクションが定義されています。start-to アクションは *RunHiveScript* です。このアクションが実行され *ok* を返した場合、次のアクションは *RunSqoopExport* です。

    RunHiveScript には、変数がいくつかあります。その値は、Azure PowerShell を使用してコンピューターから Oozie ジョブを送信するときに渡します。

    | ワークフローの変数 | 説明                                                                                                                                                                                              |
    |--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | ${jobTracker}      | Hadoop ジョブ トラッカーの URL を指定します。HDInsight クラスター Version 2.0 および 3.0 の **jobtrackerhost:9010** を使用します。                                                                |
    | ${nameNode}        | Hadoop 名前ノードの URL を指定します。既定のファイル システムの WASB アドレスを使用します。たとえば、*wasb://\<containerName\>@\<storageAccountName\>.blob.core.windows.net* のように指定します。 |
    | ${queueName}       | ジョブの送信先になるキュー名を指定します。**default** を使用します。                                                                                                                              |

    | Hive アクションの変数 | 説明                                                  |
    |-----------------------|-------------------------------------------------------|
    | ${hiveDataFolder}     | Hive の CREATE TABLE コマンドのソース ディレクトリ。  |
    | ${hiveOutputFolder}   | INSERT OVERWRITE ステートメントの出力フォルダー。     |
    | ${hiveTableName}      | log4j データ ファイルを参照する Hive テーブルの名前。 |

    | Sqoop アクションの変数         | 説明                                                                                                                   |
    |--------------------------------|------------------------------------------------------------------------------------------------------------------------|
    | ${sqlDatabaseConnectionString} | SQL データベース接続文字列。                                                                                           |
    | ${sqlDatabaseTableName}        | データのエクスポート先となる SQL データベース テーブル。                                                               |
    | ${hiveOutputFolder}            | Hive の INSERT OVERWRITE ステートメントの出力フォルダー。これは Sqoop エクスポートの export-dir と同じフォルダーです。 |

    Oozie ワークフローとワークフロー アクションの使用法の詳細については、[Apache Oozie 4.0 のマニュアル][Apache Oozie 4.0 のマニュアル] (HDInsight クラスター Version 3.0) または [Apache Oozie 3.3.2 のマニュアル][Apache Oozie 3.3.2 のマニュアル] (HDInsight クラスター Version 2.1) を参照してください。

2.  ファイルを **C:\\Tutorials\\UseOozie\\workflow.xml** という名前で保存します。エンコーディングは、ANSI (ASCII) を使用します (使っているテキスト エディターにこのオプションがない場合は、メモ帳を使用します)。

## <span id="deploy"></span></a>Oozie プロジェクトを展開してチュートリアルを準備する

Azure PowerShell スクリプトを実行して、以下を実行します。

-   HiveQL スクリプト (useoozie.hql) を Azure BLOB ストレージ (wasb:///tutorials/useoozie/useoozie.hql) にコピーします。
-   workflow.xml を wasb:///tutorials/useoozie/workflow.xml にコピーします。
-   データ ファイル (/example/data/sample.log) を wasb:///tutorials/useoozie/data/sample.log にコピーします。
-   Sqoop のエクスポート データを格納する SQL データベース テーブルを作成します。テーブル名は *log4jLogCount* です。

**HDInsight ストレージについて**

HDInsight はデータ ストレージとして Azure BLOB ストレージを使用します。これは *WASB* または *Azure ストレージ - BLOB* と呼ばれています。WASB は、HDFS を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

HDInsight クラスターをプロビジョニングするときに、HDFS と同じように、Azure ストレージ アカウントと、そのアカウントに対応する特定の BLOB ストレージ コンテナーを、既定のファイル システムとして指定します。プロビジョニング プロセスを実行するときに、このストレージ アカウントに加えて、同じ Azure サブスクリプションまたは別の Azure サブスクリプションに属する付加的なストレージ アカウントを追加することもできます。付加的なストレージ アカウントを追加する方法の詳細については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。このチュートリアルで使用する PowerShell スクリプトを簡単にするために、ファイルはすべて、*/tutorials/useoozie* にある既定のファイル システム コンテナーに格納されています。既定で、このコンテナーの名前は、HDInsight クラスター名と同じです。
WASB の構文は次のとおりです。

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight クラスター Version 3.0 では、*wasb://* 構文のみがサポートされます。旧バージョンの *"asv://"* 構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.0 クラスターではサポートされず、以降のバージョンでもサポートされません。

> [WACOM.NOTE] WASB のパスは仮想パスです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

既定のファイル システム コンテナーに格納されているファイルは、次の URI のどれを使用しても HDInsight からアクセスできます (例として workflow.xml を使用しています)。

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

ストレージ アカウントから直接ファイルにアクセスする場合、ファイルの BLOB 名は次のようになります。

    tutorials/useoozie/workflow.xml

**Hive の内部テーブルと外部テーブルについて**

Hive の内部テーブルと外部テーブルについて知っておく必要のある事項がいくつかあります。

-   CREATE TABLE コマンドは、マネージ テーブルとも呼ばれる内部テーブルを作成します。データ ファイルは既定のコンテナーに配置する必要があります。
-   CREATE TABLE コマンドは、データ ファイルを既定のコンテナーにある /hive/warehouse/<tablename> フォルダーに移動します。
-   CREATE EXTERNAL TABLE コマンドは外部テーブルを作成します。データ ファイルは既定のコンテナーの外部に配置することもできます。
-   CREATE EXTERNAL TABLE コマンドはデータ ファイルを移動しません。
-   CREATE EXTERNAL TABLE コマンドでは、LOCATION 句で指定されたフォルダーにサブフォルダーがあることが許されていません。チュートリアルで sample.log ファイルのコピーを作成しているのは、これが理由です。

詳細については、「[HDInsight: Hive Internal and External Tables Intro (HDInsight: Hive の内部テーブルと外部テーブルの概要)][HDInsight: Hive Internal and External Tables Intro (HDInsight: Hive の内部テーブルと外部テーブルの概要)]」を参照してください。

**チュートリアルを準備するには**

1.  Windows PowerShell ISE を開きます (Windows 8 のスタート画面で、「**PowerShell\_ISE**」と入力し、**[Windows PowerShell ISE]** をクリックします。「[Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)][Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)])」を参照してください。
2.  下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

        Add-AzureAccount

    Azure アカウント資格情報の入力を求められます。サブスクリプション接続を追加するこの方法はタイム アウトし、12 時間後には、このコマンドレットを再度実行する必要があります。

    > [WACOM.NOTE] Azure サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合は、**Select-AzureSubscription** コマンドレットを使用して現在のサブスクリプションを選択します。

3.  次のスクリプトをスクリプト ウィンドウにコピーし、最初の 6 個の変数を設定します。

        # WASB variables
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<BlobStorageContainerName>"

        # SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"  
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  
        $sqlDatabaseTableName = "log4jLogsCount"

        # Oozie files for the tutorial  
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"

        # WASB folder for storing the Oozie tutorial files.
        $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    変数の詳細については、このチュートリアルの「[前提条件][前提条件]」セクションを参照してください。

4.  スクリプト ウィンドウで、スクリプトの末尾に以下を追加します。

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        function uploadOozieFiles()
        {       
            Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
            Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
            Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
        }

        function prepareHiveDataFile()
        {
            Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
            Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
        }

        function prepareSQLDatabase()
        {
            # SQL query string for creating log4jLogsCount table
            $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [Level] [nvarchar](10) NOT NULL,
                    [Total] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                (
                [Level] ASC
                )
                )"

            #Create the log4jLogsCount table
            Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
            $conn = New-Object System.Data.SqlClient.SqlConnection
            $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
            $conn.open()
            $cmd = New-Object System.Data.SqlClient.SqlCommand
            $cmd.connection = $conn
            $cmd.commandtext = $cmdCreateLog4jCountTable
            $cmd.executenonquery()

            $conn.close()
        }

        # upload workflow.xml, coordinator.xml, and ooziewf.hql
        uploadOozieFiles;

        # make a copy of example/data/sample.log to example/data/log4j/sample.log
        prepareHiveDataFile;

        # create log4jlogsCount table on SQL database
        prepareSQLDatabase;

5.  **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。次のように出力されます。

    ![チュートリアルの準備の出力][チュートリアルの準備の出力]

## <span id="run"></span></a>Oozie プロジェクトを実行する

現在、Azure PowerShell には Oozie ジョブを定義するコマンドレットが用意されていません。PowerShell コマンドレットの
 Invoke-RestMethod を使用して Oozie Web サービスを呼び出すことができます。Oozie Web サービス API は、HTTP REST JSON API です。Oozie Web サービス API の詳細については、[Apache Oozie 4.0 のマニュアル][Apache Oozie 4.0 のマニュアル] (HDInsight クラスター Version 3.0) または [Apache Oozie 3.3.2 のマニュアル][Apache Oozie 3.3.2 のマニュアル] (HDInsight クラスター Version2.1) を参照してください。

**Oozie ジョブを送信するには**

1.  Windows PowerShell ISE を開きます (Windows 8 のスタート画面で、「**PowerShell\_ISE**」と入力し、**[Windows PowerShell ISE]** をクリックします。「[Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)][Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)])」を参照してください。

2.  次のスクリプトをスクリプト ウィンドウにコピーし、最初の 10 個の変数を設定します (6 個目の $storageUri はスキップします)。

        #HDInsight cluster variables
        $clusterName = "<HDInsightClusterName>"
        $clusterUsername = "<HDInsightClusterUsername>"
        $clusterPassword = "<HDInsightClusterUserPassword>"

        #Azure Blob storage (WASB) variables
        $storageAccountName = "<StorageAccountName>"
        $storageContainerName = "<BlobContainerName>"
        $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

        #Azure SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  

        #Oozie WF variables
        $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
        $waitTimeBetweenOozieJobStatusCheck=10

        #Hive action variables
        $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
        $hiveTableName = "log4jlogs"
        $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
        $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

        #Sqoop action variables
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
        $sqlDatabaseTableName = "log4jLogsCount"

        $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    変数の詳細については、このチュートリアルの「[前提条件][前提条件]」セクションを参照してください。

3.  スクリプトの末尾に次のコードを追加します。この部分は、Oozie ペイロードを定義します。

        #OoziePayload used for Oozie web service submission
        $OoziePayload =  @"
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

           <property>
               <name>nameNode</name>
               <value>$storageUrI</value>
           </property>

           <property>
               <name>jobTracker</name>
               <value>jobtrackerhost:9010</value>
           </property>

           <property>
               <name>queueName</name>
               <value>default</value>
           </property>

           <property>
               <name>oozie.use.system.libpath</name>
               <value>true</value>
           </property>

           <property>
               <name>hiveScript</name>
               <value>$hiveScript</value>
           </property>

           <property>
               <name>hiveTableName</name>
               <value>$hiveTableName</value>
           </property>

           <property>
               <name>hiveDataFolder</name>
               <value>$hiveDataFolder</value>
           </property>

           <property>
               <name>hiveOutputFolder</name>
               <value>$hiveOutputFolder</value>
           </property>

           <property>
               <name>sqlDatabaseConnectionString</name>
               <value>&quot;$sqlDatabaseConnectionString&quot;</value>
           </property>

           <property>
               <name>sqlDatabaseTableName</name>
               <value>$SQLDatabaseTableName</value>
           </property>

           <property>
               <name>user.name</name>
               <value>$clusterUsername</value>
           </property>

           <property>
               <name>oozie.wf.application.path</name>
               <value>$oozieWFPath</value>
           </property>

        </configuration>
        "@

4.  スクリプトの末尾に次のコードを追加します。この部分は、Oozie Web サービスの状態をチェックします。

        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

5.  スクリプトの末尾に次のコードを追加します。この部分は、Oozie ジョブを作成して開始します。

        # create Oozie job
        Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
        Write-Host "`n--------`n$OoziePayload`n--------"
        $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieJobId = $jsonResponse[0].("id")
        Write-Host "Oozie job id is $oozieJobId..."

        # start Oozie job
        Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
        $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
        $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug

6.  スクリプトの末尾に次のコードを追加します。この部分は、Oozie ジョブの状態をチェックします。

        # get job status
        Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

        Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
        $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")

        while($JobStatus -notmatch "SUCCEEDED|KILLED")
        {
            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")
        }

        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

7.  HDInsight クラスターが Version 2.1 である場合は、"https://$clusterName.azurehdinsight.net:443/oozie/v2/" を "https://$clusterName.azurehdinsight.net:443/oozie/v1/" に置き換えてください。HDInsight クラスター Version 2.1 は、Web サービスの Version 2 をサポートしていません。

8.  **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。次のように出力されます。

    ![チュートリアルのワークフローの実行の出力][チュートリアルのワークフローの実行の出力]

9.  エクスポートしたデータを表示するには、SQL データベースに接続します。

**ジョブのエラー ログを確認するには**

ワークフローのトラブルシューティングを行うには、Oozie のログ ファイルを確認します。
ログ ファイルの場所は、クラスター ヘッドノードの *C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* または *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log* です。RDP の詳細については、「[管理ポータルを使用した HDInsight クラスターの管理][管理ポータルを使用した HDInsight クラスターの管理]」を参照してください。

**チュートリアルを再実行するには**

ワークフローを再実行するには、次の操作を実行する必要があります。

-   Hive スクリプトの出力ファイルを削除する
-   log4jLogsCount テーブル内のデータを削除する

使用できる PowerShell スクリプトのサンプルを次に示します。

    $storageAccountName = "<AzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

## <span id="nextsteps"></span></a>次のステップ

このチュートリアルでは、Oozie ワークフローを定義する方法と、Azure PowerShell を使用して Oozie ジョブを実行する方法を説明しました。詳細については、次の記事を参照してください。

-   [HDInsight での時間ベースの Oozie コーディネーターの使用][HDInsight での時間ベースの Oozie コーディネーターの使用]
-   [HDInsight の使用][Azure HDInsight の概要]
-   [HDInsight Emulator の概要][HDInsight Emulator の概要]
-   [HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]
-   [PowerShell を使用した HDInsight の管理][PowerShell を使用した HDInsight の管理]
-   [HDInsight へのデータのアップロード][HDInsight へのデータのアップロード]
-   [HDInsight での Sqoop の使用][HDInsight での Sqoop の使用]
-   [HDInsight での Hive の使用][HDInsight での Hive の使用]
-   [HDInsight での Pig の使用][HDInsight での Pig の使用]
-   [HDInsight 用 C\# Hadoop ストリーミング プログラムの開発][HDInsight 用 C\# Hadoop ストリーミング プログラムの開発]
-   [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)][Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)]

  [HDInsight での時間ベースの Oozie コーディネーターの使用]: ../hdinsight-use-oozie-coordinator-time/
  [Oozie とは]: #whatisoozie
  [前提条件]: #prerequisites
  [Oozie ワークフロー ファイルを定義する]: #defineworkflow
  [Oozie プロジェクトを展開してチュートリアルを準備する]: #deploy
  [ワークフローを実行する]: #run
  [次のステップ]: #nextsteps
  [ワークフロー図]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [HDInsight での Sqoop の使用]: ../hdinsight-use-sqoop/
  [HDInsight で提供されるクラスター バージョンの新機能]: ../hdinsight-component-versioning/
  [Azure PowerShell のインストールおよび構成に関するページ]: ../install-and-configure-powershell/
  [Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)]: http://technet.microsoft.com/ja-jp/library/ee176949.aspx
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [Azure SQL データベースの概要]: ../sql-database-get-started/
  [SQL データベースの作成と構成]: ../sql-database-create-configure/
  [TechNet Wiki]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [Apache Oozie 4.0 のマニュアル]: http://oozie.apache.org/docs/4.0.0/
  [Apache Oozie 3.3.2 のマニュアル]: http://oozie.apache.org/docs/3.3.2/
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [HDInsight: Hive Internal and External Tables Intro (HDInsight: Hive の内部テーブルと外部テーブルの概要)]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)]: http://technet.microsoft.com/ja-jp/library/hh847889.aspx
  [チュートリアルの準備の出力]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
  [チュートリアルのワークフローの実行の出力]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png
  [管理ポータルを使用した HDInsight クラスターの管理]: ../hdinsight-administer-use-management-portal/
  [HDInsight Emulator の概要]: ../hdinsight-get-started-emulator/
  [PowerShell を使用した HDInsight の管理]: ../hdinsight-administer-use-powershell/
  [HDInsight へのデータのアップロード]: ../hdinsight-upload-data/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
  [HDInsight 用 C\# Hadoop ストリーミング プログラムの開発]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)]: ../hdinsight-develop-deploy-java-mapreduce/
