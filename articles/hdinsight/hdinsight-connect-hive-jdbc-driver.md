<properties
 pageTitle="JDBC を使用して Azure HDInsight の Hive をクエリする"
 description="JDBC を使用して Azure HDInsight の Hive に接続し、クラウドに格納されたデータに対してクエリをリモートで実行する方法について説明します。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/23/2016"
 ms.author="larryfr"/>

#Hive の JDBC ドライバーを使用して Azure HDInsight の Hive に接続します。

[AZURE.INCLUDE [ODBC JDBC セレクター](../../includes/hdinsight-selector-odbc-jdbc.md)]

このドキュメントでは、Java アプリケーションから JDBC を使用して、HDInsight クラスターに Hive クエリをリモートで送信する方法を学習します。SQuirreL SQL クライアントから接続する方法と、プログラムを使用して Java から接続する方法を学習します。

Hive JDBC インターフェイスの詳細については、[HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface) を参照してください。

##前提条件

この記事の手順を完了するには、次のものが必要です。

* HDInsight クラスターでの Hadoop。Linux または Windows ベースのクラスターが動作します。

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)。SQuirreL は、JDBC クライアント アプリケーションです。

この記事からリンクされている Java アプリケーションの例をビルドおよび実行するには、以下が必要です。

* [Java Developer Kit (JDK) バージョン 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) またはそれ以降。

* [Apache Maven](https://maven.apache.org)。Maven は、この記事に関連付けられているプロジェクトで使用される Java プロジェクトのプロジェクト ビルド システムです。

##接続文字列

Azure の HDInsight クラスターに対する JDBC 接続は 443 を使用して行われ、トラフィックは SSL を使用してセキュリティで保護されます。クラスターが背後に存在するパブリックのゲートウェイは HiveServer2 が実際にリッスンするポートにトラフィックをリダイレクトします。したがって、一般的な接続文字列は次のようになります。

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

__CLUSTERNAME__ を、使用する HDInsight クラスターの名前に置き換えます。

##認証

接続を確立するときに、HDInsight クラスターの管理者名とパスワードを使用して、クラスター ゲートウェイを認証する必要があります。SQuirreL SQL などの JDBC クライアントから接続する場合、クライアント設定で管理者名とパスワードを入力する必要があります。

Java アプリケーションから接続を確立する場合、名前とパスワードを使用する必要があります。たとえば、次の Java コードは、接続文字列、管理者名、およびパスワードを使用して新しい接続を開きます。

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##SQuirreL SQL クライアントとの接続

SQuirreL SQL は、HDInsight クラスターを使用して Hive クエリをリモートから実行するために使用できる JDBC クライアントです。次の手順は、SQuirreL SQL を既にインストールしていると想定し、Hive 用のドライバーをダウンロードおよび構成する手順について説明します。

1. HDInsight クラスターから Hive の JDBC ドライバーをコピーします。

    * __Linux ベースの HDInsight__ の場合、次の手順を使用して、必要な jar ファイルをダウンロードします。

        1. ファイルを含める新しいディレクトリを作成します。たとえば、「`mkdir hivedriver`」のように入力します。

        2. コマンド プロンプト、Bash、PowerShell やその他のコマンド ライン プロンプトからディレクトリを新しいディレクトリに変更し、次のコマンドを使用して HDInsight クラスターからファイルをコピーします。

                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .

            __USERNAME__ をクラスターの SSH ユーザー アカウント名に置き換えます。__CLUSTERNAME__ を HDInsight クラスター名に置き換えます。

            > [AZURE.NOTE] Windows 環境では、scp ではなく、PSCP ユーティリティを使用する必要があります。[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) からダウンロードすることができます。

    * __Windows ベースの HDInsight__ の場合、次の手順を使用して、必要な jar ファイルをダウンロードします。

        1. Azure ポータルから HDInsight クラスターを選択し、__[リモート デスクトップ]__ アイコンを選択します。

            ![[リモート デスクトップ] アイコン](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. [リモート デスクトップ] ブレードで、__[接続]__ を使用してクラスターに接続します。[リモート デスクトップ] が有効でない場合、フォームを使用してユーザー名とパスワードを入力し、__[有効にする]__ を選択して、クラスターの [リモート デスクトップ] を有効にします。

            ![[リモート デスクトップ] ブレード](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)

            __[接続]__ を選択した後に、.rdp ファイルがダウンロードされます。このファイルを使用して、リモート デスクトップ クライアントを起動します。メッセージが表示されたら、リモート デスクトップのアクセス用に入力したユーザー名とパスワードを使用します。

        3. 接続されたら、次のファイルをリモート デスクトップ セッションからローカル コンピューターにコピーします。`hivedriver` という名前のローカル ディレクトリに置きます。

            * C:\\apps\\dist\\hive-0.14.0.2.2.9.1-7\\lib\\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\lib\\hadoop-auth-2.6.0.2.2.9.1-7.jar

            > [AZURE.NOTE] パスとファイル名に含まれるバージョン番号は、使用しているクラスターと異なる場合があります。

        4. ファイルのコピーが完了したら、リモート デスクトップ セッションを切断します。

3. SQuirreL SQL アプリケーションを起動します。ウィンドウの左側から __[Drivers]__ を選択します。

    ![ウィンドウの左側の [Drivers] タブ](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

4. __[Drivers]__ ダイアログの上部にあるアイコンから、__[+]__ アイコンを選択して新しいドライバーを作成します。

    ![[Drivers] アイコン](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

5. [Add Driver] ダイアログで、次の情報を追加します。

    * __Name__: Hive
    * __Example URL__: jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
    * __Extra Class Path__: [Add] ボタンを使用して、以前にダウンロードした jar ファイルを追加します
    * __Class Name__: org.apache.hive.jdbc.HiveDriver

    ![[Add Driver] ダイアログ](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)

    __[OK]__ をクリックして設定を保存します。

6. SQuirreL SQL ウィンドウの左側で、__[Aliases]__ を選択します。次に __[+]__ アイコンをクリックし、新しい接続のエイリアスを作成します。

    ![新しいエイリアスの追加](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

7. __[Add Alias]__ ダイアログに次の値を使用します。

    * __Name__: Hive on HDInsight
    * __Driver__: ドロップダウンを使用して、__[Hive]__ ドライバーを選択します
    * __URL__: jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

        __CLUSTERNAME__ を、使用する HDInsight クラスターの名前に置き換えます。

    * __User Name__: HDInsight クラスターのクラスター ログイン アカウント名。既定では、`admin` です。
    * __Password__: クラスター ログイン アカウントのパスワード。これは、HDInsight クラスターを作成するときに指定したパスワードです。

    ![[Add Alias] ダイアログ](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)

    __[Test]__ ボタンを使用して、接続が機能することを確認します。__[Connect to: Hive on HDInsight]__ ダイアログが表示されたら、__[Connect]__ を選択してテストを実行します。テストが成功した場合、__[Connection successful]__ ダイアログが表示されます。

    __[Add Alias]__ ダイアログの下部にある __[OK]__ ボタンを使用して、接続エイリアスを保存します。

8. SQuirreL SQL の上部にある __[Connect to]__ ドロップダウンから、__[Hive on HDInsight]__ を選択します。メッセージが表示されたら、__[Connect]__ を選択します。

    ![接続ダイアログ](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

9. 接続されたら、SQL クエリ ダイアログに次のクエリを入力し、__[Run]__ アイコンを選択します。結果領域にクエリの結果が表示されます。

        select * from hivesampletable limit 10;

    ![結果を含む SQL クエリ ダイアログ](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

##Java アプリケーションの例からの接続

Java クライアントを使用して HDInsight の Hive をクエリする例は、[https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) にあります。リポジトリの指示に従い、サンプルを作成して実行します。

##トラブルシューティング

### SQL 接続を開こうとしたときに予期しないエラーが発生しました。

__症状__: バージョン 3.3 または 3.4 の HDInsight クラスターに接続するときに、予期しないエラーが発生したというエラーが表示される場合があります。このエラーのスタック トレースは、次の行で始まります。

    java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
    at java.util.concurrent.FutureTas...(FutureTask.java:122)
    at java.util.concurrent.FutureTask.get(FutureTask.java:206)

__原因__: このエラーの原因は、SQuirreL で使用される commons-codec.jar ファイルと、HDInsight クラスターからダウンロードした Hive JDBC コンポーネントで必要な commons-codec.jar ファイルのバージョンの不一致です。

__解決策__: このエラーを解決するには、次の手順を使用します。

1. HDInsight クラスターから commons-codec jar ファイルをダウンロードします。

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. SQuirreL を終了し、SQuirreL がインストールされているシステム上のディレクトリに移動します。SquirreL ディレクトリ内の `lib` ディレクトリにある既存の commons-codec jar を、HDInsight クラスターからダウンロードしたファイルに置き換えます。

3. SQuirreL を再起動します。これで、HDInsight の Hive に接続するときにエラーが発生しなくなります。

##次のステップ

これで、JDBC を使用して Hive を操作する方法に関する説明は終わりです。次のリンクを使用して、Azure HDInsight を操作するその他の方法について調べることもできます。

* [HDInsight へのデータのアップロード](hdinsight-upload-data.md)
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->