<properties
   pageTitle="HDInsight の R Server (プレビュー) の使用開始 | Azure"
   description="R Server (プレビュー) を含む HDInsight (Hadoop) クラスターで Apache Spark を作成し、クラスターで R スクリプトを送信する方法について説明します。"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="08/19/2016"
   ms.author="jeffstok"
/>

# HDInsight の R Server (プレビュー) の使用開始

HDInsight の Premium レベル サービスには、HDInsight (プレビュー) クラスターの一部として R Server が含まれています。これにより、R スクリプトで MapReduce と Spark を使用して、分散計算を実行することができます。このドキュメントでは、HDInsight で新しい R Server を作成し、分散 R 計算の Spark の使用方法を示す R スクリプトを実行する方法を学習します。

![このドキュメントのワークフロー図](./media/hdinsight-getting-started-with-r/rgettingstarted.png)

## 前提条件

* __Azure サブスクリプション__: このチュートリアルを開始する前に、Azure サブスクリプションが必要です。詳細については、[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* __Secure Shell (SSH) クライアント__: SSH クライアントを使用して、HDInsight クラスターにリモート接続し、クラスター上で直接コマンドを実行します。Linux、Unix、OS X システムでは、`ssh` コマンドで SSH クライアントを起動します。Windows システムの場合は [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) をお勧めします。

    * __SSH キー (省略可能)__: クラスターへの接続に使用する SSH アカウントは、パスワードまたはパブリック キーを使用してセキュリティで保護できます。パスワードを使用する方が簡単で、公開/秘密のキー ペアを作成しなくても使い始めることができます。ただし、キーを使用した方が安全です。
    
        このドキュメントの手順では、パスワードを使用していることを想定しています。HDInsight で SSH キーを作成して使用する方法については、次のドキュメントを参照してください。
        
        * [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

## クラスターを作成する

> [AZURE.NOTE] このドキュメントの手順では、基本的な構成情報を使用して HDInsight で R サーバーを作成します。その他のクラスター構成設定 (追加ストレージの追加、Azure 仮想ネットワークの使用、Hive 用のメタストアの使用など) については、[Linux ベースの HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. __[新規]__、__[データ + 分析]__、__[HDInsight]__ の順にクリックします。

    ![新しいクラスターの作成の画像](./media/hdinsight-getting-started-with-r/newcluster.png)

3. クラスターの名前を __[クラスター名]__ フィールドに入力します。複数の Azure サブスクリプションがある場合、__サブスクリプション__ エントリを使用して、使用するサブスクリプションを選択します。

    ![クラスター名とサブスクリプションの選択](./media/hdinsight-getting-started-with-r/clustername.png)

4. __クラスターの種類の選択__を選択します。__クラスターの種類__ ブレードで、次のオプションを選択します。

    * __クラスターの種類__: Spark での R サーバー
    
    * __クラスター レベル__: Premium

    その他のオプションは既定値のままにして、 __[選択]__ ボタンを選択し、クラスターの種類を保存します。
    
    ![[クラスターの種類] ブレードのスクリーン ショット](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)
    
    > [AZURE.NOTE] クラスターの種類を選択してから __[Premium]__ を選択すると、他の HDInsight クラスーの種類 (Hadoop、HBase など) に R サーバーを追加することもできます。

5. **[リソース グループ]** を選択して既存のリソース グループの一覧を表示し、その中にクラスターを作成するグループを選択します。または、**[新規作成]** をクリックし、新しいリソース グループの名前を入力します。新しいグループ名を使用できることを示す緑のチェック マークが表示されます。

    > [AZURE.NOTE] このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。
    
    __[選択]__ ボタンを使用し、リソース グループを保存します。

6. **[資格情報]** を選択し、**[クラスターのユーザー名]** と **[クラスターのログイン パスワード]** を入力します。

    __[SSH ユーザー名]__ を入力します。__Secure Shell (SSH)__ クライアントを使用してクラスターにリモート接続する場合は、SSH を使用します。このダイアログ ボックスで SSH ユーザーを指定するか、クラスターを作成した後に (クラスターの [構成] タブで) SSH ユーザーを指定することができます。R Server は、__[SSH ユーザー名]__ に "remoteuser" が入力されることを想定して構成されています。別のユーザー名を使用する場合は、クラスターを作成した後に他の手順を実行する必要があります。
    
    ![[資格情報] ブレード](./media/hdinsight-getting-started-with-r/clustercredentials.png)

    __SSH 認証の種類__: 公開キーを使用したい場合を除き、認証の種類として__パスワード__を選択します。RTVS、RStudio、または別のデスクトップ IDE など、リモート クライアントを使用してクラスターの R Server にアクセスする場合は、公開キーと秘密キーのペアが必要になります。

	公開キーと秘密キーのペアを作成して使用するには、[公開キー] を選択して次の手順に従います。次の手順では、Cygwin と、ssh-keygen または同等のものがインストールされていることを前提としています。

	-    ラップトップ コンピューターのコマンド プロンプトから、公開キーと秘密キーのペアを生成します。
	  
		    ssh-keygen -t rsa -b 2048 –f <private-key-filename>
      
    -    これにより、秘密キー ファイルと、<秘密キーのファイル名>.pub という名前の公開キー ファイルが作成されます (davec と davec.pub など)。HDI クラスターの資格情報を割り当てるときに、公開キー ファイル (*.pub) を指定します。
      
		![[資格情報] ブレード](./media/hdinsight-getting-started-with-r/publickeyfile.png)
      
	-    ノート パソコンでの秘密キー ファイルのアクセス許可の変更
      
			chmod 600 <private-key-filename>
      
	-    リモート ログインの場合、次のように秘密キー ファイルと SSH を使用します。
	  
			ssh –i <private-key-filename> remoteuser@<hostname public ip>
      
	  または、クライアントの R Server の Hadoop Spark コンピューティング コンテキストの定義の一部として使用します (オンラインの [RevoScaleR Hadoop Spark ファースト ステップ ガイド](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)の「[Creating a Compute Context for Spark (Spark 用のコンピューティング コンテキストの作成)](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark)」セクションで、「Using Microsoft R Server as a Hadoop Client (Hadoop クライアントとして Microsoft R Server を使用する)」を参照してください)。

7. **[データ ソース]** を選択し、クラスターのデータ ソースを選択します。__[ストレージ アカウントの選択]__ を選択し、アカウントを選択して既存のストレージ アカウントを選択するか、__[ストレージ アカウントの選択]__ セクションで __[新規]__ リンクを使用して新しいアカウントを作成します。

    __[新規]__ を選択した場合、新しいストレージ アカウントの名前を入力する必要があります。名前を使用できる場合は、緑色のチェック マークが表示されます。

    __[既定のコンテナー]__ は、既定ではクラスターの名前になります。この値はこのままにします。
    
    __[場所]__ を選択し、ストレージ アカウントを作成するリージョンを選択します。
    
    > [AZURE.IMPORTANT] 既定のデータ ソースの場所を選択すると、HDInsight クラスターの場所も設定されます。クラスターと既定のデータ ソースは、同じリージョンに存在する必要があります。

    **[選択]** ボタンを使用し、データ ソースの構成を保存します。
    
    ![[データ ソース] ブレード](./media/hdinsight-getting-started-with-r/datastore.png)

8. **[ノード価格レベル]** を選択して、このクラスターのために作成されるノードに関する情報を表示します。大きなクラスターが必要になることがわかっている場合を除き、worker ノードの数は既定値の `4` のままにします。クラスターの推定コストがブレード内に表示されます。

	> [AZURE.NOTE] 必要に応じて、後でポータルから ([クラスター]、[設定]、[クラスターのスケール設定]) クラスターのサイズを変更して、worker ノードの数を変更できます。これは、使用しないクラスターをアイドル状態にする場合や、大規模なタスクのニーズに合わせて容量を追加する場合に役立ちます。

	クラスター、データ ノード、エッジ ノードのサイズを変更するときに、次の点に留意します。
   
    - データが大きいとき、Spark での分散 R Server 分析のパフォーマンスは、worker ノードの数に比例します。
    - R Server 分析のパフォーマンスは、分析されているデータのサイズに比例します。次に例を示します。
        - データのサイズが中程度までの場合は、エッジ ノードのローカルのコンピューティング コンテキストで分析するときにパフォーマンスが最高になります。ローカルと Spark コンピューティング コンテキストのパフォーマンスが最高になるシナリオの詳細については、HDInsight での R Server のコンピューティング コンテキスト オプションを参照してください。<br>
        - エッジ ノードにログインして R スクリプトを実行し、ScaleR rx 関数以外のすべてをエッジ ノードで<strong>ローカル</strong>に実行した場合、エッジ ノードのメモリとコアの数が適宜調整されます。ノート パソコンからリモート コンピューティング コンテキストとして HDI の RServer を使用する場合、同じことがあてはまります。
    
    ![[ノード価格レベル] ブレード](./media/hdinsight-getting-started-with-r/pricingtier.png)

    **[選択]** ボタンを使用し、ノードの価格構成を保存します。
    
9. **[新しい HDInsight クラスター]** ブレードで、**[スタート画面にピン留めする]** が選択されていることを確認し、**[作成]** を選択します。これでクラスターが作成され、Azure ポータルのスタート画面にクラスター用のタイルが追加されます。アイコンはクラスターが作成中であることを示し、作成が完了すると、[HDInsight] アイコンを表示するように変化します。

    | 作成中 | 作成の完了時 |
    | ------------------ | --------------------- |
    | ![スタート画面の作成中インジケーター](./media/hdinsight-getting-started-with-r/provisioning.png) | ![Created cluster tile](./media/hdinsight-getting-started-with-r/provisioned.png) |

    > [AZURE.NOTE] クラスターが作成されるまで、通常は約 15 分かかります。作成プロセスをチェックするには、スタート画面のタイル、またはページの左側にある **[通知]** エントリを使用してください。

## R Server エッジ ノードに接続する

SSH を使用して HDInsight クラスターの R Server エッジ ノードに接続します。

    ssh USERNAME@r-server.CLUSTERNAME-ssh.azurehdinsight.net
    
> [AZURE.NOTE] クラスターを選択し、__[すべての設定]__、__[アプリ]__、__[RServer]__ の順にクリックし、Azure ポータルで `R-Server.CLUSTERNAME-ssh.azurehdinsight.net` のアドレスを確認することもできます。これにより、エッジ ノードの SSH エンドポイント情報が表示されます。
>
> ![エッジ ノードの SSH エンドポイントの画像](./media/hdinsight-getting-started-with-r/sshendpoint.png)
    
SSH ユーザー アカウントを保護するためにパスワードを使用している場合は、パスワードの入力を求められます。公開キーを使用している場合、`-i` パラメーターを使用して、対応する秘密キーを指定することが必要な場合があります。たとえば、「`ssh -i ~/.ssh/id_rsa USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net`」のように入力します。
    
Linux ベースの HDInsight での SSH の使用方法の詳細については、次の記事を参照してください。

* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

接続すると、次のようなプロンプトが表示されます。

    username@ed00-myrser:~$

## R コンソールを使用する

1. SSH セッションから、次のコマンドを使用して R コンソールを起動します。

        R
    
    次のような出力が表示されます。
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.

        >

2. `>` プロンプトから R コードを入力できます。R Server には、Hadoop との対話を容易にし、分散計算を実行するためのパッケージが含まれています。たとえば、次のコマンドを使用して、HDInsight クラスターの既定のファイル システムのルートを表示します。

        rxHadoopListFiles("/")
    
    WASB スタイルのアドレス指定も使用できます。
    
        rxHadoopListFiles("wasbs:///")

## Microsoft R Server または Microsoft R Client のリモート インスタンスから HDI の R Server を使用する

クラスターにアクセスするための公開キーと秘密キーのペアの使用について説明する前のセクションに従って、デスクトップまたはラップトップで実行されている Microsoft R Server または Microsoft R Client のリモート インスタンスから HDI Hadoop Spark コンピューティング コンテキストへのアクセスを設定することができます (オンラインの [RevoScaleR Hadoop Spark ファースト ステップ ガイド](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)の「[Creating a Compute Context for Spark (Spark 用のコンピューティング コンテキストの作成)](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark)」セクションで、「Using Microsoft R Server as a Hadoop Client (Hadoop クライアントとして Microsoft R Server を使用する)」を参照してください)。そのためには、ラップトップで RxSpark コンピューティング コンテキストを定義するときに、オプション (hdfsShareDir、shareDir、sshUsername、sshHostname、sshSwitches、sshProfileScript) を指定する必要があります。次に例を示します。

    
    myNameNode <- "default"
    myPort <- 0 
 
    mySshHostname  <- 'rkrrehdi1-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key
 
    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")
 
    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

    
 
## コンピューティング コンテキストを使用する

コンピューティング コンテキストを使用すると、計算をエッジ ノードでローカルに実行するか、HDInsight クラスターのノード全体に分散するかを制御することができます。
        
1. R コンソールから次をコマンドを実行し、HDInsight の既定のストレージにサンプル データを読み込みます。

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"
        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)
        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012") 
        # Make the directory
        rxHadoopMakeDir(inputDir)
        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. 次に、いくつかのデータ情報を作成し、データを利用できるようにデータ ソースを 2 つ定義しましょう。

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()
        # Create info list for the airline data
        airlineColInfo <- list(
            DAY_OF_WEEK = list(type = "factor"),
            ORIGIN = list(type = "factor"),
            DEST = list(type = "factor"),
            DEP_TIME = list(type = "integer"),
            ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. ローカルのコンピューティング テキストを使用し、データに対してロジスティック回帰を実行してみましょう。

        # Set a local compute context
        rxSetComputeContext("local")
        # Run a logistic regression
        system.time(
            modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )
        # Display a summary 
        summary(modelLocal)

    次のような行で終了する出力が表示されます。

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                        Estimate Std. Error z value Pr(>|z|)
        (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
        ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
        ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
        ......
        DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
        DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
        DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
        DEST=BPT         Dropped    Dropped Dropped  Dropped
        ---
        Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

        Condition number of final variance-covariance matrix: 11904202
        Number of iterations: 7

4. 次に、Spark コンテキストを使用して同じロジスティック回帰を実行してみましょう。Spark コンテキストを使用すると、HDInsight クラスターのすべてのワーカー ノードに処理が分散されます。

        # Define the Spark compute context 
        mySparkCluster <- RxSpark()
        # Set the compute context 
        rxSetComputeContext(mySparkCluster)
        # Run a logistic regression 
        system.time(  
            modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        # Display a summary
        summary(modelSpark)

    > [AZURE.NOTE] MapReduce を使用して、クラスター ノード全体に計算を分散することもできます。コンピューティング コンテキストの詳細については、[HDInsight Premium での R Server のコンピューティング コンテキストのオプション](hdinsight-hadoop-r-server-compute-contexts.md)に関するページをご覧ください。

## 複数のノードに R コードを分散する

R Server では、既存の R コードを容易に取得し、`rxExec` を使用してクラスター内の複数のノード全体で実行することができます。これは、パラメーター スイープまたはシミュレーションを行うときに便利です。`rxExec` を使用する方法の例を次に示します。

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
    
Spark や MapReduce のコンテキストを使用している場合は、コード (`Sys.info()["nodename"]`) が実行されているworker ノードのノード名の値が返されます。たとえば、4 ノードのクラスタでは次のような出力が表示されます。

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"

## R パッケージをインストールする

エッジ ノードに追加の R パッケージをインストールする場合、SSH 経由でエッジ ノードに接続していれば、R コンソールから `install.packages()` を直接使用できます。ただし、クラスターのワーカー ノードに R パッケージをインストールする場合は、スクリプト アクションを使用する必要があります。

スクリプト アクションは、HDInsight クラスターの構成を変更するか、追加のソフトウェアをインストールするために使用する Bash スクリプトです。この場合は追加の R パッケージをインストールするために使用します。スクリプト アクションを使用して追加のパッケージをインストールするには、次の手順に従います。

> [AZURE.IMPORTANT] スクリプト アクションを使用した追加の R パッケージのインストールは、クラスターが作成された後にのみ使用できます。スクリプトは完全にインストールと構成がされている R Server に依存するため、クラスターの作成中は使用できません。

1. [Azure ポータル](https://portal.azure.com)で、HDInsight クラスター上の R Server を選択します。

2. クラスター ブレードで、__[すべての設定]__、__[スクリプト アクション]__ の順に選択します。__[スクリプト アクション]__ ブレードで、__[新規で送信]__ を選択し、新しいスクリプト アクションを送信します。

    ![[スクリプト アクション] ブレードの画像](./media/hdinsight-getting-started-with-r/newscriptaction.png)

3. __[スクリプト アクションの送信]__ ブレードで、次の情報を指定します。

  - __名前__: このスクリプトを識別するための表示名
  - __Bash スクリプト URI__: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`
  - __ヘッド__: __オフ__に設定します
  - __ワーカー__: __オン__に設定します
  - __Zookeeper__: __オフ__に設定します
  - __パラメーター__: インストールする R パッケージ。たとえば、`bitops stringr arules` のように指定します。
  - __このスクリプトの保持…__: __オン__に設定します

    > [AZURE.NOTE] 1.既定では、R パッケージはすべて、インストールされている R Server のバージョンと一貫性のある Microsoft MRAN リポジトリのスナップショットからインストールされます。パッケージの新しいバージョンをインストールする場合、非互換性の問題が発生するリスクがありますが、これはパッケージ一覧の最初の要素として `useCRAN` を指定すること (例: `useCRAN bitops, stringr, arules`) によって行うことができます。
    > 2. 一部の R パッケージには、Linux システムの他のライブラリが必要です。便宜上、上位 100 の最も一般的な R パッケージで必要な依存関係が事前インストールされています。ただし、インストールする R パッケージでこれら以外のライブラリが必要な場合、ここで使用する基本スクリプトをダウンロードし、システム ライブラリをインストールする手順を追加します。その後、変更後のスクリプトを Azure ストレージ内のパブリック BLOB コンテナーにアップロードし、変更後のスクリプトを使用してパッケージをインストールする必要があります。スクリプト アクションを開発する方法の詳細については、[スクリプト アクション開発](hdinsight-hadoop-script-actions-linux.md)に関するページを参照してください。

    ![スクリプト アクションの追加](./media/hdinsight-getting-started-with-r/scriptaction.png)

4. __[作成]__ を選択してスクリプトを実行します。スクリプトが完了すると、R パッケージをすべてのワーカー ノードで使用できるようになります。
    
## 次のステップ

ここでは、R Server を含む新しい HDInsight クラスターを作成する方法、SSH セッションから R コンソールを使用する方法の基本を説明しました。次のリンクを使用して、HDInsight で R Server を使用するための他の方法を見てみましょう。

- [Add RStudio Server to HDInsight premium (HDInsight Premium への RStudio Server の追加)](hdinsight-hadoop-r-server-install-r-studio.md)

- [Compute context options for R Server on HDInsight Premium (HDInsight Premium での R Server の計算コンテキストのオプション)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure Storage options for R Server on HDInsight Premium (HDInsight Premium での R Server の Azure Storage オプション)](hdinsight-hadoop-r-server-storage.md)

### Azure リソース マネージャーのテンプレート

Azure Resource Manager テンプレートを使用して HDInsight で R Server の作成を自動化する場合は、次のサンプル テンプレートを参照してください。

* [Create an R Server on HDInsight cluster using an SSH public key (SSH 公開キーを使用して HDInsight クラスターで R Server を作成する)](http://go.microsoft.com/fwlink/p/?LinkID=780809)
* [Create an R Server on HDInsight cluster using an SSH password (SSH パスワードを使用して HDInsight クラスターで R Server を作成する)](http://go.microsoft.com/fwlink/p/?LinkID=780810)

どちらのテンプレートも、新しい HDInsight クラスターおよび関連付けられたストレージ アカウントを作成し、Azure CLI、Azure PowerShell、または Azure ポータルから使用することができます。

Azure Resource Manager テンプレートの使用に関する全般的な情報については、「[ARM テンプレートを使用した HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-create-linux-clusters-arm-templates.md)」を参照してください。

<!---HONumber=AcomDC_0921_2016-->