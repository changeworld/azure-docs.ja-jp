---
title: "Azure HDInsight での Java HBase アプリケーションのビルド | Microsoft Docs"
description: "Apache Maven を使用して Java ベースの Apache HBase アプリケーションをビルドし、Azure クラウドの Linux ベースの HDInsight にデプロイする方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: 
ms.assetid: 1d1ed180-e0f4-4d1c-b5ea-72e0eda643bc
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: d91e05e458c6689f04dedd85a3e4adc60e6de72d


---
# <a name="use-maven-to-build-java-applications-that-use-hbase-with-linux-based-hdinsight-hadoop"></a>Linux ベースの HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築する
Apache Maven を使用して Java で [Apache HBase](http://hbase.apache.org/) アプリケーションを作成し、ビルドする方法について説明します。 その後、Linux ベースの HDInsight クラスターでアプリケーションを使用します。

[Maven](http://maven.apache.org/) は、Java プロジェクトのソフトウェア、ドキュメント、レポートを作成するためのソフトウェア プロジェクト管理および包含ツールです。 この記事では、このツールを使用して、Linux ベースの HDInsight クラスターで HBase テーブルの作成、クエリ、削除を実行する基本的な Java アプリケーションを作成する方法について説明します。

> [!IMPORTANT]
> このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

## <a name="requirements"></a>必要条件
* [Java プラットフォーム JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 以降
* [Maven](http://maven.apache.org/)
* [Linux ベースの Azure HDInsight クラスターと HBase](hdinsight-hbase-tutorial-get-started-linux.md#create-hbase-cluster)
  
  > [!NOTE]
  > このドキュメントの手順は、HDInsight クラスター バージョン 3.2、3.3、3.4 でテスト済みです。 例で指定される既定値は、HDInsight 3.4 クラスターに対応しています。
  > 
  > 
* **SSH と SCP を熟知していること**。 HDInsight での SSH と SCP の使用方法の詳細については、次の記事をご覧ください。
  
  * **Linux、Unix、または OS X クライアント**: 「 [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する (プレビュー)](hdinsight-hadoop-linux-use-ssh-unix.md)
  * **Windows クライアント**: 「 [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する (プレビュー)](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="create-the-project"></a>プロジェクトを作成する
1. 開発環境のコマンド ラインから、プロジェクトを作成する場所にディレクトリを変更します。例: `cd code/hdinsight`
2. Maven でインストールされた **mvn** コマンドを使用し、プロジェクトのスキャフォールディングを生成します。
   
        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
    これにより、**artifactID** パラメーターで指定した名前 (この例では **hbaseapp**) で、新しいディレクトリが現在のディレクトリに作成されます。このディレクトリには、次の項目が含まれます。
   
   * **pom.xml**: プロジェクト オブジェクト モデル ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) には、プロジェクトのビルドに使用される情報と構成の詳細が含まれています。
   * **src**: アプリケーションを作成する **main\java\com\microsoft\examples** ディレクトリに含まれるディレクトリです。
3. **src\test\java\com\microsoft\examples\apptest.java** ファイルはこの例では使用しないため、削除します。

## <a name="update-the-project-object-model"></a>プロジェクト オブジェクト モデルを更新する
1. **pom.xml** ファイルを編集し、`<dependencies>` セクション内に次のコードを追加します。
   
        <dependency>
            <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>
   
    これは Maven に対して、**hbase-client** のバージョン **1.1.2** がプロジェクトに必要であることを伝えます。 これはコンパイル時に、既定の Maven リポジトリからダウンロードされます。 [Maven セントラル リポジトリ検索](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) を使用して、この依存関係についての詳細を確認できます。
   
   > [!IMPORTANT]
   > バージョン番号は、HDInsight クラスターに付属の HBase のバージョンと一致する必要があります。 次の表を使用して、正しいバージョン番号を調べてください。
   > 
   > 
   
   | HDInsight クラスターのバージョン | 使用する HBase のバージョン |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 および 3.4 |1.1.2 |
   
    HDInsight のバージョンとコンポーネントの詳細については、「 [HDInsight で使用できる Hadoop コンポーネントの種類を教えてください](hdinsight-component-versioning.md)」を参照してください。
2. HDInsight 3.3 または 3.4 クラスターを使用している場合は、 `<dependencies>` セクションに次のコードも追加する必要があります。
   
        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
   
    これにより、Hbase Version 1.1.x で必要な phoenix コア コンポーネントが読み込まれます。
3. **pom.xml** ファイルに次のコードを追加します。 これは、ファイルの `<project>...</project>` タグ内に配置する必要があります (たとえば `</dependencies>` と `</project>` の間)。
   
        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
            <resource>
              <directory>${basedir}/conf</directory>
              <filtering>false</filtering>
              <includes>
                <include>hbase-site.xml</include>
              </includes>
            </resource>
          </resources>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
                        <version>3.3</version>
              <configuration>
                  <source>1.7</source>
                  <target>1.7</target>
              </configuration>
              </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                  <goals>
                    <goal>shade</goal>
                  </goals>
                </execution>
              </executions>
            </plugin>
          </plugins>
        </build>
   
    これにより、HBase の構成情報が含まれたリソース (**conf\hbase-site.xml**) が構成されます。
   
   > [!NOTE]
   > コードを介して構成値を設定することもできます。 その方法については、**CreateTable** サンプル内のコメントをご覧ください。
   > 
   > 
   
    これによって、[Maven Compiler Plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) と [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/) も構成されます。 トポロジのコンパイルにはコンパイラ プラグインが使用されます。 シャードのプラグインは、Maven でビルドされる JAR パッケージ内のライセンスの重複を防ぐために使用されます。 ライセンス ファイルの重複は、HDInsight クラスターでの実行時に発生するエラーの原因となるためです。 maven-shade-plugin を `ApacheLicenseResourceTransformer` 実装で使用すると、エラーを回避できます。
   
    また、maven-shade-plugin は、アプリケーションで必要とされるすべての依存関係を含む uberjar (または fatjar) も生成します。
4. **pom.xml** ファイルを保存します。
5. **conf** という名前の新しいディレクトリを **hbaseapp** ディレクトリ内に作成します。 このディレクトリを使用して、HBase に接続するための構成情報を保持します。
6. 次のコマンドを使用して、HDInsight サーバーから **conf** ディレクトリに HBase の構成をコピーします。 **USERNAME** には、SSH ログイン名を指定します。 **CLUSTERNAME** には、HDInsight クラスター名を指定します。
   
        scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
   
   > [!NOTE]
   > SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。 アカウントで SSH キーを使用した場合は、 `-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。 次の例は、 `~/.ssh/id_rsa`から秘密キーを読み込みます。
   > 
   > `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`
   > 
   > 

## <a name="create-the-application"></a>アプリケーションを作成する
1. **hbaseapp\src\main\java\com\microsoft\examples** ディレクトリに移動し、app.java ファイルの名前を **CreateTable.java** に変更します。
2. **CreateTable.java** ファイルを開き、既存の内容を次のコードで置き換えます。
   
        package com.microsoft.examples;
        import java.io.IOException;
   
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;
   
        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();
   
            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
   
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");
   
            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);
   
            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);
   
            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };
   
            HTable table = new HTable(config, "people");
   
            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }
   
    これは、**people** という名前のテーブルを作成して定義済みユーザーを設定する **CreateTable** クラスです。
3. **CreateTable.java** ファイルを保存します。
4. **hbaseapp\src\main\java\com\microsoft\examples** ディレクトリに、**SearchByEmail.java** という名前の新しいファイルを作成します。 このファイルの内容として以下を使用します。
   
        package com.microsoft.examples;
        import java.io.IOException;
   
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;
   
        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();
   
            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }
   
            // Open the table
            HTable table = new HTable(config, "people");
   
            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");
   
            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );
   
            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);
   
            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }
   
    **SearchByEmail** クラスを使用し、メール アドレスによって行を照会できます。 正規表現フィルターが使用されるため、このクラスを使用するときに文字列または正規表現を指定できます。
5. **SearchByEmail.java** ファイルを保存します。
6. **hbaseapp\src\main\hava\com\microsoft\examples** ディレクトリに、**DeleteTable.java** という名前の新しいファイルを作成します。 このファイルの内容として以下を使用します。
   
        package com.microsoft.examples;
        import java.io.IOException;
   
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
   
        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();
   
            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);
   
            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }
   
    このクラスは、このサンプルのクリーンアップに使用するため、最初に **CreateTable** クラスで作成されたテーブルを無効にし、次にそれを削除します。
7. **DeleteTable.java** ファイルを保存します。

## <a name="build-and-package-the-application"></a>アプリケーションをビルドおよびパッケージ化する
1. **hbaseapp** ディレクトリで次のコマンドを使用して、アプリケーションが含まれた JAR ファイルをビルドします。
   
        mvn clean package
   
    これにより、前のビルド アーティファクトを整理し、まだインストールされていない依存関係をダウンロードして、アプリケーションをビルドしてパッケージ化します。
2. コマンドが完了すると、**hbaseapp\target** ディレクトリに **hbaseapp-1.0-SNAPSHOT.jar** という名前のファイルが格納されます。
   
   > [!NOTE]
   > **hbaseapp-1.0-SNAPSHOT.jar** ファイルは、アプリケーションの実行に必要なすべての依存関係を含む uber jar (fat jar とも呼ばれる) です。
   > 
   > 

## <a name="upload-the-jar-file-and-run-jobs"></a>JAR ファイルをアップロードしてジョブを実行する
1. 次のコマンドを使用して、HDInsight クラスターに jar をアップロードします。 **USERNAME** には、SSH ログイン名を指定します。 **CLUSTERNAME** には、HDInsight クラスター名を指定します。
   
        scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.
   
    これにより、SSH ユーザー アカウントのホーム ディレクトリにファイルがアップロードされます。
   
   > [!NOTE]
   > SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。 アカウントで SSH キーを使用した場合は、 `-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。 次の例は、 `~/.ssh/id_rsa`から秘密キーを読み込みます。
   > 
   > `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`
   > 
   > 
2. SSH を使用して、HDInsight クラスターに接続します。 **USERNAME** には、SSH ログイン名を指定します。 **CLUSTERNAME** には、HDInsight クラスター名を指定します。
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
   > [!NOTE]
   > SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。 アカウントで SSH キーを使用した場合は、 `-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。 次の例は、 `~/.ssh/id_rsa`から秘密キーを読み込みます。
   > 
   > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   > 
   > 
3. 接続したら、次のコマンドを使用して、Java アプリケーションを使用する新しい HBase テーブルを作成します。
   
        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
   
    これにより、**people** という名前の新しい HBase テーブルが作成され、データが設定されます。
4. 次に、次のコマンドを使用して、テーブルに格納されている電子メール アドレスを検索します。
   
        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
   
    次の結果が表示されます。
   
        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

## <a name="delete-the-table"></a>テーブルを削除する
サンプルの操作が終了したら、Azure PowerShell セッションから次のコマンドを使用し、このサンプルで使用された **people** テーブルを削除します。

    hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable




<!--HONumber=Jan17_HO4-->


