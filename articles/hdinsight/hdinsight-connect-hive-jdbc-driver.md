<properties
 pageTitle="JDBC を使用して Azure HDInsight の Hive をクエリする"
 description="JDBC を使用して Azure HDInsight の Hive に接続し、クラウドに格納されたデータに対してクエリをリモートで実行する方法について説明します。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="12/04/2015"
 ms.author="larryfr"/>

#Hive の JDBC ドライバーを使用して Azure HDInsight の Hive に接続します。

[AZURE.INCLUDE [ODBC JDBC セレクター](../../includes/hdinsight-selector-odbc-jdbc.md)]

このドキュメントでは、Java アプリケーションから JDBC を使用して、HDInsight クラスターに Hive クエリをリモートで送信する方法を学習します。Hive JDBC インターフェイスの詳細については、[HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface) を参照してください。

##前提条件

この記事の手順を完了するには、次のものが必要です。

* HDInsight クラスターでの Hadoop。Linux または Windows ベースのクラスターが動作します。

* [Java Developer Kit (JDK) バージョン 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) またはそれ以降。

* [Apache Maven](https://maven.apache.org)。Maven は、この記事に関連付けられているプロジェクトで使用される Java プロジェクトのプロジェクト ビルド システムです。

##接続文字列

Azure の HDInsight クラスターに対する JDBC 接続は 443 を使用して行われ、トラフィックは SSL を使用してセキュリティで保護されます。クラスターが背後に存在するパブリックのゲートウェイは HiveServer2 が実際にリッスンするポートにトラフィックをリダイレクトします。したがって、一般的な接続文字列は次のようになります。

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

##認証

接続を確立するときに、HDInsight クラスターの管理者名とパスワードを指定する必要があります。これらは、ゲートウェイに対する要求を認証します。たとえば、次の Java コードは、接続文字列、管理者名、およびパスワードを使用して新しい接続を開きます。

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##クエリ

接続が確立されると、Hive に対してクエリを実行することができます。たとえば、次の Java コードはテーブルから __SELECT__ を実行し、その結果を 3 行のみに限定して、次のように表示します。

    sql = "SELECT querytime, market, deviceplatform, devicemodel, state, country from " + tableName + " LIMIT 3";
    stmt2 = conn.createStatement();
    System.out.println("\nRetrieving inserted data:");

    res2 = stmt2.executeQuery(sql);

    while (res2.next()) {
      System.out.println( res2.getString(1) + "\t" + res2.getString(2) + "\t" + res2.getString(3) + "\t" + res2.getString(4) + "\t" + res2.getString(5) + "\t" + res2.getString(6));
    }

##Java プロジェクトの例

Java クライアントを使用して HDInsight の Hive をクエリする例は、[https://github.com/Blackmist/hdinsight-hive-jdbc](https://github.com/Blackmist/hdinsight-hive-jdbc) にあります。リポジトリの指示に従い、サンプルを作成して実行します。

##次のステップ

これで、JDBC を使用して Hive を操作する方法に関する説明は終わりです。次のリンクを使用して、Azure HDInsight を操作するその他の方法について調べることもできます。

* [HDInsight へのデータのアップロード](hdinsight-upload-data.md)
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_1210_2015-->