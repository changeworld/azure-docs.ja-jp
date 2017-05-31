---
title: "JDBC で Hive をクエリする - Azure HDInsight | Microsoft Docs"
description: "JDBC を使用して Azure HDInsight の Hadoop クラスター上の Hive に接続する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 928f8d2a-684d-48cb-894c-11c59a5599ae
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: dd76e2450be2b05d011de7ded49bfa9630190e71
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017


---
# <a name="query-hive-through-jdbc"></a>JDBC で Hive をクエリする

[!INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Java アプリケーションから JDBC を使用して、Hive クエリを Azure HDInsight の Hadoop に発行する方法につい説明します。 このドキュメントの情報では、プログラミングによって SQuirrel SQL クライアントから接続する方法を示します。

Hive JDBC インターフェイスの詳細については、 [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)を参照してください。

## <a name="prerequisites"></a>前提条件

* HDInsight クラスターでの Hadoop。 Linux または Windows ベースのクラスターが動作します。

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight 3.3 の廃止](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)に関するページを参照してください。

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)。 SQuirreL は、JDBC クライアント アプリケーションです。

* [Java Developer Kit (JDK) バージョン 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) またはそれ以降。

* [Apache Maven](https://maven.apache.org)。 Maven は、この記事に関連付けられているプロジェクトで使用される Java プロジェクトのプロジェクト ビルド システムです。

## <a name="jdbc-connection-string"></a>JDBC 接続文字列

Azure の HDInsight クラスターに対する JDBC 接続は 443 を使用して行われ、トラフィックは SSL を使用してセキュリティで保護されます。 クラスターが背後に存在するパブリックのゲートウェイは HiveServer2 が実際にリッスンするポートにトラフィックをリダイレクトします。 接続文字列の例を次に示します。

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

`CLUSTERNAME` を、使用する HDInsight クラスターの名前に置き換えます。

## <a name="authentication"></a>認証

接続を確立するときに、HDInsight クラスターの管理者名とパスワードを使用して、クラスター ゲートウェイを認証する必要があります。 SQuirreL SQL などの JDBC クライアントから接続する場合、クライアント設定で管理者名とパスワードを入力する必要があります。

Java アプリケーションから接続を確立する場合、名前とパスワードを使用する必要があります。 たとえば、次の Java コードは、接続文字列、管理者名、およびパスワードを使用して新しい接続を開きます。

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>SQuirreL SQL クライアントとの接続

SQuirreL SQL は、HDInsight クラスターを使用して Hive クエリをリモートから実行するために使用できる JDBC クライアントです。 次の手順では、既に SQuirreL SQL がインストールされていると仮定します。

1. HDInsight クラスターから Hive の JDBC ドライバーをコピーします。

    * **Linux ベースの HDInsight** の場合、次の手順を使用して、必要な jar ファイルをダウンロードします。

        1. ファイルを含むディレクトリを作成します。 たとえば、「 `mkdir hivedriver`」のように入力します。

        2. コマンドラインで次のコマンドを使用して、HDInsight クラスターからファイルをコピーします。

            ```bash
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
            ```

            `USERNAME` をクラスターの SSH ユーザー アカウント名に置き換えます。 `CLUSTERNAME` を HDInsight クラスター名に置き換えます。

    * **Windows ベースの HDInsight**の場合、次の手順を使用して、jar ファイルをダウンロードします。

        1. Azure Portal から HDInsight クラスターを選択し、 **[リモート デスクトップ]** アイコンを選択します。

            ![[リモート デスクトップ] アイコン](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. [リモート デスクトップ] ブレードで、**[接続]** を使用してクラスターに接続します。 [リモート デスクトップ] が有効でない場合、フォームを使用してユーザー名とパスワードを入力し、**[有効にする]** を選択して、クラスターの [リモート デスクトップ] を有効にします。

            ![[リモート デスクトップ] ブレード](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)

            **[接続]** を選択した後に、.rdp ファイルがダウンロードされます。 このファイルを使用して、リモート デスクトップ クライアントを起動します。 メッセージが表示されたら、リモート デスクトップのアクセス用に入力したユーザー名とパスワードを使用します。

        3. 接続されたら、次のファイルをリモート デスクトップ セッションからローカル コンピューターにコピーします。 `hivedriver` という名前のローカル ディレクトリに置きます。

            * C:\apps\dist\hive-0.14.0.2.2.9.1-7\lib\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\lib\hadoop-auth-2.6.0.2.2.9.1-7.jar

            > [!NOTE]
            > パスとファイル名に含まれるバージョン番号は、使用しているクラスターと異なる場合があります。

        4. ファイルのコピーが完了したら、リモート デスクトップ セッションを切断します。

2. SQuirreL SQL アプリケーションを起動します。 ウィンドウの左側から **[Drivers]** を選択します。

    ![ウィンドウの左側の [Drivers] タブ](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

3. **[Drivers\(ドライバー\)]** ダイアログの上部にあるアイコンから、**+** アイコンを選択してドライバーを作成します。

    ![[Drivers] アイコン](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

4. [Add Driver\(ドライバーの追加\)] ダイアログで、次の情報を追加します。

    * **Name**: Hive
    * **Example URL**: `jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`
    * **Extra Class Path**: [Add] ボタンを使用して、以前にダウンロードした jar ファイルを追加します
    * **Class Name**: org.apache.hive.jdbc.HiveDriver

   ![[Add Driver] ダイアログ](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)

   **[OK]** をクリックして設定を保存します。

5. SQuirreL SQL ウィンドウの左側で、**[Aliases]** を選択します。 次に **+** アイコンをクリックし、接続のエイリアスを作成します。

    ![新しいエイリアスの追加](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

6. **[Add Alias]** ダイアログに次の値を使用します。

    * **Name**: Hive on HDInsight

    * **Driver**: ドロップダウンを使用して、**[Hive]** ドライバーを選択します

    * **URL**: jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

        **CLUSTERNAME** を、使用する HDInsight クラスターの名前に置き換えます。

    * **User Name**: HDInsight クラスターのクラスター ログイン アカウント名。 既定では、 `admin`です。

    * **Password**: クラスター ログイン アカウントのパスワード。

    ![[Add Alias] ダイアログ](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)

    **[Test]** ボタンを使用して、接続が機能することを確認します。 **[Connect to: Hive on HDInsight]** ダイアログが表示されたら、**[Connect]** を選択してテストを実行します。 テストが成功した場合、**[Connection successful\(接続成功\)]** ダイアログが表示されます。

    **[Add Alias\(エイリアスの追加\)]** ダイアログの下部にある **[OK]** ボタンを使用して、接続エイリアスを保存します。

7. SQuirreL SQL の上部にある **[Connect to]** ドロップダウンから、**[Hive on HDInsight]** を選択します。 メッセージが表示されたら、**[Connect]** を選択します。

    ![接続ダイアログ](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

8. 接続されたら、SQL クエリ ダイアログに次のクエリを入力し、**[Run]** アイコンを選択します。 結果領域にクエリの結果が表示されます。

        select * from hivesampletable limit 10;

    ![結果を含む SQL クエリ ダイアログ](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Java アプリケーションの例からの接続

Java クライアントを使用して HDInsight の Hive をクエリする例は、 [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)にあります。 リポジトリの指示に従い、サンプルを作成して実行します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>SQL 接続を開こうとしたときに、予期しないエラーが発生した

**症状**: バージョン 3.3 または 3.4 の HDInsight クラスターに接続するときに、予期しないエラーが発生したというエラーが表示される場合があります。 このエラーのスタック トレースは、次の行で始まります。

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**原因**: このエラーの原因は、SQuirreL で使用される commons-codec.jar ファイルと、Hive JDBC コンポーネントで必要な commons-codec.jar ファイルのバージョンの不一致です。

**解決策**: このエラーを解決するには、次の手順を使用します。

1. HDInsight クラスターから commons-codec jar ファイルをダウンロードします。

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. SQuirreL を終了し、SQuirreL がインストールされているシステム上のディレクトリに移動します。 SquirreL ディレクトリ内の `lib` ディレクトリにある既存の commons-codec jar を、HDInsight クラスターからダウンロードしたファイルに置き換えます。

3. SQuirreL を再起動します。 これで、HDInsight の Hive に接続するときにエラーが発生しなくなります。

## <a name="next-steps"></a>次のステップ

これで、JDBC を使用して Hive を操作する方法に関する説明は終わりです。次のリンクを使用して、Azure HDInsight を操作するその他の方法について調べることもできます。

* [HDInsight へのデータのアップロード](hdinsight-upload-data.md)
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)

