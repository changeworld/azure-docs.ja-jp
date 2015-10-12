<properties
	pageTitle="Maven を使用した HBase アプリケーションのビルド | Microsoft Azure"
	description="Apache Maven を使用して Java ベースの Apache HBase アプリケーションをビルドし、Azure クラウドの Linux ベースの HDInsight にデプロイする方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="larryfr"/>

#Use Maven to build Java applications that use HBase with HDInsight (Hadoop) (HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築)

Apache Maven を使用して Java で [Apache HBase](http://hbase.apache.org/) アプリケーションを作成し、ビルドする方法について説明します。その後、Linux ベースの HDInsight クラスターでアプリケーションを使用します。

[Maven](http://maven.apache.org/) は、Java プロジェクトのソフトウェア、ドキュメント、レポートを作成するためのソフトウェア プロジェクト管理および包含ツールです。この記事では、このツールを使用して、Linux ベースの HDInsight クラスターで HBase テーブルの作成、クエリ、削除を実行する基本的な Java アプリケーションを作成する方法について説明します。

##必要条件

* [Java プラットフォーム JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 以降

* [Maven](http://maven.apache.org/)

* [Linux ベースの Azure HDInsight クラスターと HBase](../hdinsight-hbase-get-started-linux.md#create-hbase-cluster)

* **SSH と SCP を熟知していること**HDInsight での SSH と SCP の使用方法の詳細については、次の記事をご覧ください。

    * **Linux、Unix、または OS X クライアント**: 「[Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する (プレビュー)](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

    * **Windows クライアント**: 「[HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する (プレビュー)](hdinsight-hadoop-linux-use-ssh-windows.md)」をご覧ください。

##プロジェクトを作成する

1. 開発環境のコマンド ラインから、プロジェクトを作成する場所にディレクトリを変更します。例: `cd code/hdinsight`

2. Maven でインストールされた __mvn__ コマンドを使用し、プロジェクトのスキャフォールディングを生成します。

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	これにより、__artifactID__ パラメーターで指定した名前 (この例では **hbaseapp**) で、新しいディレクトリが現在のディレクトリに作成されます。 このディレクトリには、次の項目が含まれます。

	* __pom.xml__: プロジェクト オブジェクト モデル ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) には、プロジェクトのビルドに使用される情報と構成の詳細が含まれています。

	* __src__: アプリケーションを作成する __main\\java\\com\\microsoft\\examples__ ディレクトリに含まれるディレクトリです。

3. __src\\test\\java\\com\\microsoft\\examples\\apptest.java__ ファイルはこの例では使用しないため、削除します。

##プロジェクト オブジェクト モデルを更新する

1. __pom.xml__ ファイルを編集し、`<dependencies>` セクション内に次のコードを追加します。

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

	これは Maven に対して、__hbase-client__ のバージョン __0.98.4-hadoop2__ がプロジェクトに必要であることを伝えます。これはコンパイル時に、既定の Maven リポジトリからダウンロードされます。[Maven セントラル リポジトリ検索](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar)を使用して、この依存関係についての詳細を確認できます。

2. __pom.xml__ ファイルに次のコードを追加します。これは、ファイルの `<project>...</project>` タグ内に配置する必要があります (たとえば `</dependencies>` と `</project>` の間)。

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
          	    <source>1.6</source>
          	    <target>1.6</target>
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

	これにより、HBase の構成情報が含まれたリソース (__conf\\hbase-site.xml__) が構成されます。

	> [AZURE.NOTE]コードを介して構成値を設定することもできます。その方法については、__CreateTable__ サンプル内のコメントをご覧ください。

	これによって、[Maven Compiler Plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) と [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/) も構成されます。トポロジのコンパイルにはコンパイラ プラグインが使用されます。シャードのプラグインは、Maven でビルドされる JAR パッケージ内のライセンスの重複を防ぐために使用されます。ライセンス ファイルの重複は、HDInsight クラスターでの実行時に発生するエラーの原因となるためです。maven-shade-plugin を `ApacheLicenseResourceTransformer` 実装で使用すると、エラーを回避できます。

	また、maven-shade-plugin は、アプリケーションで必要とされるすべての依存関係を含む uberjar (または fatjar) も生成します。

3. __pom.xml__ ファイルを保存します。

4. __conf__ という名前の新しいディレクトリを __hbaseapp__ ディレクトリ内に作成します。このディレクトリを使用して、HBase に接続するための構成情報を保持します。

5. 次のコマンドを使用して、HDInsight サーバーから __conf__ ディレクトリに HBase の構成をコピーします。**USERNAME** を SSH ログインの名前に置き換えます。**CLUSTERNAME** を HDInsight クラスターの名前に置き換えます。

		scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

	> [AZURE.NOTE]SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。アカウントで SSH キーを使用した場合は、`-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。次の例は、`~/.ssh/id_rsa` から秘密キーを読み込みます。
	>
	> `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##アプリケーションを作成する

1. __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__ ディレクトリに移動し、app.java ファイルの名前を __CreateTable.java__ に変更します。

2. __CreateTable.java__ ファイルを開き、既存の内容を次のコードで置き換えます。

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
			//   in code instead of an hbase-site.xml file
			//
		    // config.set("hbase.zookeeper.quorum",
		    //            "zookeepernode0,zookeepernode1,zookeepernode2");
		    //config.set("hbase.zookeeper.property.clientPort", "2181");
		    //config.set("hbase.cluster.distributed", "true");

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

	これは、__people__ という名前のテーブルを作成して定義済みユーザーを設定する __CreateTable__ クラスです。

3. __CreateTable.java__ ファイルを保存します。

4. __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__ ディレクトリに、__SearchByEmail.java__ という名前の新しいファイルを作成します。このファイルの内容として以下を使用します。

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

	__SearchByEmail__ クラスを使用し、電子メール アドレスによって行を照会できます。正規表現フィルターが使用されるため、このクラスを使用するときに文字列または正規表現を指定できます。

5. __SearchByEmail.java__ ファイルを保存します。

6. __hbaseapp\\src\\main\\hava\\com\\microsoft\\examples__ ディレクトリに、__DeleteTable.java__ という名前の新しいファイルを作成します。このファイルの内容として以下を使用します。

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

	このクラスは、このサンプルのクリーンアップに使用するため、最初に __CreateTable__ クラスで作成されたテーブルを無効にし、次にそれを削除します。

7. __DeleteTable.java__ ファイルを保存します。

##アプリケーションをビルドおよびパッケージ化する

2. __hbaseapp__ ディレクトリで次のコマンドを使用して、アプリケーションが含まれた JAR ファイルをビルドします。

		mvn clean package

	これにより、前のビルド アーティファクトを整理し、まだインストールされていない依存関係をダウンロードして、アプリケーションをビルドしてパッケージ化します。

3. コマンドが完了すると、__hbaseapp\\target__ ディレクトリに __hbaseapp-1.0-SNAPSHOT.jar__ という名前のファイルが格納されます。

	> [AZURE.NOTE]__hbaseapp-1.0-SNAPSHOT.jar__ ファイルは、アプリケーションの実行に必要なすべての依存関係を含む uberjar (fatjar とも呼ばれる) です。

##JAR ファイルをアップロードしてジョブを実行する

1. 次のコマンドを使用して、HDInsight クラスターに jar をアップロードします。**USERNAME** を SSH ログインの名前に置き換えます。**CLUSTERNAME** を HDInsight クラスターの名前に置き換えます。

		scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

	これにより、SSH ユーザー アカウントのホーム ディレクトリにファイルがアップロードされます。

	> [AZURE.NOTE]SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。アカウントで SSH キーを使用した場合は、`-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。次の例は、`~/.ssh/id_rsa` から秘密キーを読み込みます。
	>
	> `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. SSH を使用して、HDInsight クラスターに接続します。**USERNAME** を SSH ログインの名前に置き換えます。**CLUSTERNAME** を HDInsight クラスターの名前に置き換えます。

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	> [AZURE.NOTE]SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。アカウントで SSH キーを使用した場合は、`-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。次の例は、`~/.ssh/id_rsa` から秘密キーを読み込みます。
	>
	> `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. 接続したら、次のコマンドを使用して、Java アプリケーションを使用する新しい HBase テーブルを作成します。

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

	これにより、__people__ という名前の新しい HBase テーブルが作成され、データが設定されます。

4. 次に、次のコマンドを使用して、テーブルに格納されている電子メール アドレスを検索します。

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

	次の結果が表示されます。

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

##テーブルを削除する

サンプルの操作が終了したら、Azure PowerShell セッションから次のコマンドを使用し、このサンプルで使用された __people__ テーブルを削除します。

	hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

<!---HONumber=Oct15_HO1-->