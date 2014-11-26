<properties linkid="hdinsight-emulator-release-notes" urlDisplayName="HDInsight Emulator release notes" pageTitle="Release notes: Microsoft HDInsight Emulator for Azure | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Get late-breaking information about the most recent releases of the HDInsight Hadoop Emulator." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Release notes: Microsoft HDInsight Emulator for Azure" authors="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cgronlun" />

# リリース ノート: Microsoft HDInsight Emulator for Azure

> [WACOM.NOTE]
> バージョン番号を確認する最も簡単な方法は、[プログラムの追加と削除] で [Microsoft HDInsight Emulator for Azure] (Version 1.0.0.0 以上の場合) または [Microsoft HDInsight 開発者プレビュー] (Version 1.0.0.0 未満の場合) の項目を探すことです。

## v1.0.0.0、2013 年 10 月 28 日リリース

-   これは、Microsoft HDInsight Emulator for Azure (旧称 Microsoft HDInsight 開発者プレビュー) の一般公開 (GA) リリースです。

-   この製品のプレビュー リリースと同様に、このリリースも引き続き開発者シナリオを対象としており、そのため単一ノード デプロイメントのみをサポートします。

### 新機能

-   Apache Hadoop のすべてのサービスを自動起動または手動起動に簡単に設定できるように、スクリプトが追加されました。既定では、以前と同様に自動起動ですが、C:\\Hadoop にインストールされた set-onebox-manualstart.cmd または set-onebox-autostart.cmd スクリプトを使用して、すべてのサービスを変更できるようになりました。

-   インストール時に必要な依存コンポーネントの数が大幅に減少したため、迅速にインストールできます。

-   GettingStarted フォルダーにインストールされている RunSamples.ps1 スクリプトで Pig のサンプルを実行するときに使用するコマンドのバグが修正されました。

-   このバージョンには、Azure HDInsight クラスター Version 1.6 で使用できる Hortonworks Data Platform サービスに相当する Hortonworks Data Platform Version 1.1 の更新が含まれます。

## v0.11.0.0、2013 年 09 月 30 日リリース

### 新機能

-   HDInsight ダッシュボードが削除されました。

-   このバージョンには、Azure HDInsight 上の Hortonworks Data Platform Preview に相当する Hortonworks Data Platform 開発者プレビューの更新が含まれます。

## v0.10.0.0、2013 年 8 月 9 日リリース

### 新機能

-   このバージョンには、Azure HDInsight 上の Hortonworks Data Platform Preview に相当する Hortonworks Data Platform 開発者プレビューの更新が含まれます。

## v0.8.0.0、2013 年 6 月 7 日リリース

### 新機能

-   このバージョンには、Azure HDInsight 上の Hortonworks Data Platform Preview に相当する Hortonworks Data Platform 開発者プレビューの更新が含まれます。

## v0.6.0.0、2013 年 5 月 13 日リリース

### 新機能

-   Hive Server 2 がインストールされるようになりました。これは、この更新と同時にリリースされた Microsoft ODBC Driver for Hive の新しい Version 0.9.2.0 で必要になります。

-   すべてのサービスが自動起動に設定され、コンピューターの再起動後に再びすべてを開始する必要がなくなりました。

## v0.4.0.0、2013 年 3 月 25 日リリース

### 新機能

-   Microsoft HDInsight 開発者プレビューおよび Hortonworks Data Platform for Windows 開発者プレビューの新リリース。

-   Apache Hadoop、Hive、Pig、Sqoop、Oozie、HCatalog、Templeton を含みます。

-   新しい Microsoft HDInsight ダッシュボードには次の機能があります。

-   ローカル インストールのほか、Azure HDInsight サービスを使用してリモート実行されているクラスターも含めて、複数のクラスターに接続します。

-   HDInsight サービスの詳細については、[][]<http://azure.microsoft.com/ja-jp/documentation/services/hdinsight/></a> を参照してください。

-   ローカル クラスター上の WASB を構成します。

-   詳細な手順は以下を参照してください。

-   新しい対話的な Hive コンソールで Hive クエリを作成し編集します。

-   ジョブの履歴と結果を表示してダウンロードできます。

### リリース ノート

ポート番号

-   ローカル HDInsight インストール上の REST API エンドポイントと Azure HDInsight サービスは、同じサービスについて異なるポート番号でアクセスされます。

    ローカル:
    Oozie: http://localhost:11000/oozie/v1/admin/status
    Templeton: http://localhost:50111/templeton/v1/status
    ODBC: DSN 構成または接続文字列でポート 10000 を使用します。

    HDInsight サービス:
    Oozie: http://ServerFQDN:563/oozie/v1/admin/status
    Templeton: http://ServerFQDN:563/templeton/v1/status
    ODBC: DSN 構成または接続文字列でポート 563 を使用します。

-   ローカル クラスター上の ASV の構成

    ダッシュボードには、"ローカル (hdfs)" という名前の既定のローカル クラスターが表示されます。ローカル インストールのストレージとして ASV を使用する場合は、以下の手順を実行します。

    1.  C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf にある core-site.xml にアカウント タグを追加します。

            <property>
                <name>fs.azure.account.key.{AccountName}</name>
                <value>{Key}</value>
            </property>

            <property>
                <name>fs.default.name</name>
                <!-- cluster variant -->
                <value>asv://ASVContainerName@ASVAccountName</value>
                <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
                <final>true</final>
            </property>

            <property>
                <name>dfs.namenode.rpc-address</name>
                <value>hdfs://localhost:8020</value>
                <description>A base for other temporary directories.</description>
            </property>

        例:

            <property>
                <name>fs.azure.account.key.MyHadoopOnAzureAccountName</name>
                <value>8T45df897/a5fSox1yMjYkX66CriiGgA5zptSXpPdG4o1Qw2mnbbWQN1E9i/i7kihk6FWNhvLlqe02NXEVw6rP==</value>
            </property>

            <property>
                <name>fs.default.name</name>
                <!-- cluster variant -->
                <value>asv://MyASVContainer@MyASVAccount</value>
                <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
                <final>true</final>
            </property>

    2.  デスクトップから Hadoop コマンド シェルを昇格モードで開き、次のコマンドを実行します。

            %HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

    3.  asv://{container}@{account}/{path} というフル URI を使用してそのアカウントにある任意のファイルにアクセスします (HTTPS を使用してデータにアクセスする場合は asvs://)。例:

            hadoop fs -lsr 
            asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

    4.  現在登録されているローカル クラスターを削除し、新しい ASV 資格情報を使って再登録します。

## v0.3.0.0、2012 年 12 月 13 日リリース

-   ダッシュボードの Web サイトで Windows 資格情報の代わりに匿名認証を使用するように変更されました。これにより、以前のバージョンのリリース ノートに記載されていたログイン プロンプトに関する問題が解消されます。

-   エクスポートと一部のインポートにおける Sqoop のバグを修正しました。

### リリースの問題

-   JavaScript コンソールの読み込みに失敗します。詳細については、Version 0.2.0.0 のリリース ノートを参照してください。
-   Sqoop コマンド ラインに次のような警告が表示されます。これは今後の更新プログラムで修正される予定であり、無視してもかまいません。

        c:\Hadoop\sqoop-1.4.2\bin>sqoop version 
        Setting HBASE_HOME to 
        Warning: HBASE_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\hbase-0.94.2] does not exist HBase imports will fail. 
        Please set HBASE_HOME to the root of your HBase installation. 
        Setting ZOOKEEPER_HOME to 
        Warning: ZOOKEEPER_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\zookeeper-3.4.3] does not exist 
        Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation. 
        Sqoop 1.4.2 
        git commit id 3befda0a456124684768348bd652b0542b002895 
        Compiled by  on Thu 11/29/2012- 3:26:26.10

## v0.2.0.0、2012 年 12 月 3 日リリース

-   MSI に対するセマンティック バージョン管理の導入

-   MSDN フォーラムで報告されたさまざまなインストール バグ、特に HDInsight ダッシュボードのインストール関係のバグの修正

-   見つけやすいように [スタート] メニュー項目を追加

-   Hive コンソールの複数行入力の修正

-   入門用コンテンツの細かな更新

### リリースの問題

-   JavaScript コンソールの読み込みに失敗します。

    -   一部のインストールで、ページに HTTP 404 エラーが表示され JavaScript コンソールの読み込みに失敗します。この問題を回避するには、コンソールを使用して http://localhost:8080 に直接移動します。
-   HDInsight ダッシュボードに移動するとログイン プロンプトが表示されます。

    -   HDInsight ダッシュボードに移動すると、ログイン ダイアログ ボックスが表示されるという報告がいくつかありました。その場合は、現在のユーザーのログイン情報を入力すると、ダッシュボードに移動できます。

## v1.0.0.0、2012 年 10 月 23 日リリース

-   最初のリリース

### リリースの問題

-   Hive コンソール

    -   送信した Hive コマンドに改行文字が含まれていると、構文エラーになります。改行を削除すると、クエリは意図したとおりに実行ざれます。

## 一般的な既知の問題

-   Hadoop ユーザーのパスワードの有効期限

    コンピューターに適用された AD ポリシーによっては、Hadoop ユーザーのパスワードが期限切れになることがあります。以下の PowerShell スクリプトは、パスワードが期限切れにならないように設定します。このスクリプトは管理コマンド プロンプトから実行することができます。

        $username = "hadoop"
        $ADS_UF_DONT_EXPIRE_PASSWD = 0x10000 # (65536, from ADS_USER_FLAG_ENUM enumeration)

        $computer = [ADSI]("WinNT://$ENV:COMPUTERNAME,computer")
        $users = $computer.psbase.children | where { $_.psbase.schemaclassname -eq "User" }

        foreach($user in $users)
        {
            if($user.Name -eq $username)
            {
                $user.UserFlags = $ADS_UF_DONT_EXPIRE_PASSWD
                $user.SetInfo()

                $user.PasswordExpired = 0
                $user.SetInfo()

                Write-Host "$username user maintenance completed. "
            }
        }

-   一時ディレクトリ

    hadoop.tmp.dir が間違った場所を指しており、C:\\hadoop\\hdfs ではなく、c:\\hdfs を指しています。このバグは、HDP 関連の次の更新プログラムで修正されます。

-   OS の制限

    HDInsight Server は、64 ビット OS にインストールする必要があります。

-   WebPI の検索結果に HDInsight が見つかりません。

    これは通常、OS の制限です。HDInsight には、64 ビットのオペレーティング システムが必要で、少なくとも Windows 7 Service Pack 1、Windows Server 2008 R2 Service Pack1、Windows 8、または Windows Server 2012 以上が必要です。

-   管理コマンド プロンプトのマニュアル

    -   **hadoop mradmin** または **hadoop defadmin** のようなコマンドを実行するには、Hadoop ユーザーとして実行する必要があります。

    -   そのユーザーとして実行しているシェルを簡単に作成するには、Hadoop コマンド プロンプトを開いて、次のコマンドを実行します。

            start-hadoopadminshell

    -   これにより Hadoop の管理者権限で実行されている新しいコマンド シェルが開きます。

## <a name="nextsteps"></a>次のステップ

-   [HDInsight Emulator の概要][HDInsight Emulator の概要]

  []: http://azure.microsoft.com/ja-jp/documentation/services/hdinsight/
  [HDInsight Emulator の概要]: ../hdinsight-get-started-emulator/
