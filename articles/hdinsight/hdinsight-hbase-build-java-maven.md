<properties
	pageTitle="Maven を使用した HBase アプリケーションのビルド | Microsoft Azure"
	description="Apache Maven を使用して Java ベースの Apache HBase アプリケーションをビルドし、Azure HDInsight にデプロイする方法について説明します。"
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
	ms.date="07/06/2014"
	ms.author="larryfr"/>

#Use Maven to build Java applications that use HBase with HDInsight (Hadoop) (HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築)

Apache Maven を使用して Java で [Apache HBase](http://hbase.apache.org/) アプリケーションを作成し、ビルドする方法について説明します。その後、このアプリケーションを Azure HDInsight (Hadoop) で使用します。

[Maven](http://maven.apache.org/) は、Java プロジェクトのソフトウェア、ドキュメント、レポートを作成するためのソフトウェア プロジェクト管理および包含ツールです。この記事では、このツールを使用して、Azure HDInsight クラスターでの HBase テーブルの作成、クエリ、および削除を実行する基本的な Java アプリケーションを作成する方法について説明します。

##必要条件

* [Java プラットフォーム JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 以降

* [Maven](http://maven.apache.org/)

* [Azure HDInsight クラスターと HBase](hdinsight-hbase-get-started.md#create-hbase-cluster)

##プロジェクトを作成する

1. 開発環境のコマンド ラインから、プロジェクトを作成する場所にディレクトリを変更します。例: `cd code\hdinsight`

2. Maven でインストールされた __mvn__ コマンドを使用し、プロジェクトのスキャフォールディングを生成します。

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	これにより、__artifactID__ パラメーターにより指定された名前で、新しいディレクトリが現在のディレクトリに作成されます (この例では **hbaseapp**)。 このディレクトリには、次の項目が含まれます。

	* __pom.xml__: プロジェクト オブジェクト モデル ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) には、プロジェクトのビルドに使用される情報と構成の詳細が含まれています。

	* __src__: アプリケーションを作成する __main\\java\\com\\microsoft\\examples__ ディレクトリに含まれるディレクトリです。

3. __src\\test\\java\\com\\microsoft\\examples\\apptest.java__ ファイルはこの例では使用されないため、削除します。

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

	これにより、HBase の構成情報が含まれているリソース (__conf\\hbase-site.xml__) が構成されます。

	> [AZURE.NOTE]コードを介して構成値を設定することもできます。その方法については、__CreateTable__ サンプル内のコメントをご覧ください。

	これによって、[Maven Compiler Plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) と [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/) も構成されます。トポロジのコンパイルにはコンパイラ プラグインが使用されます。シャードのプラグインは、Maven でビルドされる JAR パッケージ内のライセンスの重複を防ぐために使用されます。ライセンス ファイルの重複は、HDInsight クラスターでの実行時に発生するエラーの原因となるためです。maven-shade-plugin を `ApacheLicenseResourceTransformer` 実装で使用すると、エラーを回避できます。

	また、maven-shade-plugin は、アプリケーションで必要とされるすべての依存関係を含む uberjar (または fatjar) も生成します。

3. __pom.xml__ ファイルを保存します。

4. __conf__ という名前の新しいディレクトリを __hbaseapp__ ディレクトリ内に作成します。__conf__ ディレクトリに、__hbase-site.xml__ という名前の新しいファイルを作成し、内容として以下を使用します。

		<?xml version="1.0"?>
		<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
		<!--
		/**
		 * Copyright 2010 The Apache Software Foundation
		 *
		 * Licensed to the Apache Software Foundation (ASF) under one
		 * or more contributor license agreements.  See the NOTICE file
		 * distributed with this work for additional information
		 * regarding copyright ownership.  The ASF licenses this file
		 * to you under the Apache License, Version 2.0 (the
		 * "License"); you may not use this file except in compliance
		 * with the License.  You may obtain a copy of the License at
		 *
		 *     http://www.apache.org/licenses/LICENSE-2.0
		 *
		 * Unless required by applicable law or agreed to in writing, software
		 * distributed under the License is distributed on an "AS IS" BASIS,
		 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		 * See the License for the specific language governing permissions and
		 * limitations under the License.
		 */
		-->
		<configuration>
		  <property>
		    <name>hbase.cluster.distributed</name>
		    <value>true</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.quorum</name>
		    <value>zookeeper0,zookeeper1,zookeeper2</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.property.clientPort</name>
		    <value>2181</value>
		  </property>
		</configuration>

	このファイルは、HDInsight クラスター用の HBase 構成の読み込みに使用されます。

	> [AZURE.NOTE]これは、HDInsight クラスター用の最小限の設定が含まれた最小限の hbase-site.xml ファイルです。HDInsight で使用される完全バージョンの hbase-site.xml 構成ファイルについては、「[Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md#rdp)」をご覧ください。hbase-site.xml ファイルは C:\\apps\\dist\\hbase-&lt;version number>-hadoop2\\conf ディレクトリにあります。ファイル パスの "version number" の部分は、クラスターで HBase が更新されると変わります。

3. __hbase-site.xml__ ファイルを保存します。

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

1. コマンド プロンプトを開き、ディレクトリを __hbaseapp__ ディレクトリに変更します。

2. 次のコマンドを使用し、アプリケーションを含む JAR ファイルをビルドします。

		mvn clean package

	これにより、前のビルド アーティファクトを整理し、まだインストールされていない依存関係をダウンロードして、アプリケーションをビルドしてパッケージ化します。

3. コマンドが完了すると、__hbaseapp\\target__ ディレクトリに __hbaseapp-1.0-SNAPSHOT.jar__ という名前のファイルが格納されます。

	> [AZURE.NOTE]__hbaseapp-1.0-SNAPSHOT.jar__ ファイルは、アプリケーションの実行に必要なすべての依存関係を含む uberjar (fatjar とも呼ばれる) です。

##JAR ファイルをアップロードしてジョブを開始する

> [AZURE.NOTE]「[HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」で説明されているように、ファイルを HDInsight にアップロードするには多くの方法があります。次の手順では、[Azure PowerShell](../install-configure-powershell.md) を使用します。

1. Azure PowerShell をインストールし、構成した後で、__hbase-runner.psm1￼__ という名前の新しいファイルを作成します。このファイルの内容として以下を使用します。

		<#
		.SYNOPSIS
		    Copies a file to the primary storage of an HDInsight cluster.
		.DESCRIPTION
		    Copies a file from a local directory to the blob container for
		    the HDInsight cluster.
		.EXAMPLE
		    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
		        -clusterName "MyHDInsightCluster"

		.EXAMPLE
		    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
		        -clusterName "MyHDInsightCluster"
		        -emailRegex "contoso.com"

		.EXAMPLE
		    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
		        -clusterName "MyHDInsightCluster"
		        -emailRegex "^r" -showErr
		#>

		function Start-HBaseExample {
		    [CmdletBinding(SupportsShouldProcess = $true)]
		    param(
		        #The class to run
		        [Parameter(Mandatory = $true)]
		        [String]$className,

		        #The name of the HDInsight cluster
		        [Parameter(Mandatory = $true)]
		        [String]$clusterName,

		        #Only used when using SearchByEmail
		        [Parameter(Mandatory = $false)]
		        [String]$emailRegex,

		        #Use if you want to see stderr output
		        [Parameter(Mandatory = $false)]
		        [Switch]$showErr
		    )

		    Set-StrictMode -Version 3

		    # Is the Azure module installed?
		    FindAzure

		    # The JAR
		    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

		    # The job definition
		    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
		      -JarFile $jarFile `
		      -ClassName $className `
		      -Arguments $emailRegex

		    # Get the job output
		    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		    Wait-AzureHDInsightJob -Job $job
		    if($showErr)
		    {
		        Write-Host "STDERR"
		        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		    }
		    Write-Host "Display the standard output ..." -ForegroundColor Green
		    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
		}

		<#
		.SYNOPSIS
		    Copies a file to the primary storage of an HDInsight cluster.
		.DESCRIPTION
		    Copies a file from a local directory to the blob container for
		    the HDInsight cluster.
		.EXAMPLE
		    Add-HDInsightFile -localPath "C:\temp\data.txt"
		        -destinationPath "example/data/data.txt"
		        -ClusterName "MyHDInsightCluster"
		.EXAMPLE
		    Add-HDInsightFile -localPath "C:\temp\data.txt"
		        -destinationPath "example/data/data.txt"
		        -ClusterName "MyHDInsightCluster"
		        -Container "MyContainer"
		#>

		function Add-HDInsightFile {
		    [CmdletBinding(SupportsShouldProcess = $true)]
		    param(
		        #The path to the local file.
		        [Parameter(Mandatory = $true)]
		        [String]$localPath,

		        #The destination path and file name, relative to the root of the container.
		        [Parameter(Mandatory = $true)]
		        [String]$destinationPath,

		        #The name of the HDInsight cluster
		        [Parameter(Mandatory = $true)]
		        [String]$clusterName,

		        #If specified, overwrites existing files without prompting
		        [Parameter(Mandatory = $false)]
		        [Switch]$force
		    )

		    Set-StrictMode -Version 3

		    # Is the Azure module installed?
		    FindAzure

		    # Does the local path exist?
		    if (-not (Test-Path $localPath))
		    {
		        throw "Source path '$localPath' does not exist."
		    }

		    # Get the primary storage container
		    $storage = GetStorage -clusterName $clusterName

		    # Upload file to storage, overwriting existing files if -force was used.
		    Set-AzureStorageBlobContent -File $localPath -Blob $destinationPath -force:$force `
		                                -Container $storage.container `
		                                -Context $storage.context
		}

		function FindAzure {
		    # Is the Azure module installed?
		    if (-not(Get-Module -ListAvailable Azure))
		    {
		        throw "Azure PowerShell not found! For help, see http://www.windowsazure.com/documentation/articles/install-configure-powershell/"
		    }

		    # Is there an active Azure subscription?
		    $sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		    if(-not($sub))
		    {
		        throw "No active Azure subscription found! If you have a subscription, use the Add-AzureAccount or Import-PublishSettingsFile cmdlets to make the Azure account available to Windows PowerShell."
		    }
		}

		function GetStorage {
		    param(
		        [Parameter(Mandatory = $true)]
		        [String]$clusterName
		    )
		    $hdi = Get-AzureHDInsightCluster -name $clusterName
		    # Does the cluster exist?
		    if (!$hdi)
		    {
		        throw "HDInsight cluster '$clusterName' does not exist."
		    }
		    # Create a return object for context & container
		    $return = @{}
		    $storageAccounts = @{}
		    # Get the primary storage account information
		    $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
		    $storageAccountKey = $hdi.DefaultStorageAccount.StorageAccountKey
		    # Build the hash of storage account name/keys
		    $storageAccounts.Add($hdi.DefaultStorageAccount.StorageAccountName, $storageAccountKey)
		    foreach($account in $hdi.StorageAccounts)
		    {
		        $storageAccounts.Add($account.StorageAccountName, $account.StorageAccountKey)
		    }
		    # Get the storage context, as we can't depend
		    # on using the default storage context
		    $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		    # Get the container, so we know where to
		    # find/store blobs
		    $return.container = $hdi.DefaultStorageAccount.StorageContainerName
		    # Return storage accounts to support finding all accounts for
		    # a cluster
		    $return.storageAccounts = $storageAccounts

		    return $return
		}
		# Only export the verb-phrase things
		export-modulemember *-*

	このファイルには 2 つのモジュールが含まれます。

	* __Add-HDInsightFile__ - HDInsight へのファイルのアップロードに使用されます

	* __Start-HBaseExample__ - 前に作成されたクラスの実行に使用されます

2. __hbase-runner.psm1__ ファイルを保存します。

3. 新しい Azure PowerShell ウィンドウを開き、ディレクトリを __hbaseapp__ ディレクトリに変更して、次のコマンドを実行します。

		PS C:\ Import-Module c:\path\to\hbase-runner.psm1

	前に作成した __hbase-runner.psm1__ ファイルの場所にパスを変更します。これにより、この Azure PowerShell セッションのモジュールが登録されます。

2. 次のコマンドを使用して、__hbaseapp-1.0-SNAPSHOT.jar__ を HDInsight クラスターにアップロードします。

		Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

	__hdinsightclustername__ を、使用する HDInsight クラスターの名前で置き換えます。このコマンドは、HDInsight クラスターのプライマリ ストレージの __example/jars__ の場所に __hbaseapp-1.0-SNAPSHOT.jar__ をアップロードします。

3. ファイルをアップロードした後で、次のコードを使用し、__hbaseapp__ を使用して新しいテーブルを作成します。

		Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

	__hdinsightclustername__ を、使用する HDInsight クラスターの名前で置き換えます。

	このコマンドにより、HDInsight クラスターに __people__ という名前の新しいテーブルが作成されます。このコマンドを実行しても、コンソール ウィンドウに出力結果は表示されません。

2. テーブル内のエントリを検索するには、次のコマンドを使用します。

		Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

	__hdinsightclustername__ を、使用する HDInsight クラスターの名前で置き換えます。

	このコマンドは **SearchByEmail** クラスを使用して、__contactinformation__ の列ファミリの __email__ 列に文字列 __contoso.com__ が含まれている行を検索します。次の結果が表示されます。

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

	`-emailRegex` の値に __fabrikam.com__ を使用すると、__fabrikam.com__ が電子メール フィールドに含まれているユーザーが返されます。この検索は正規表現ベースのフィルターを使用して実装されるため、電子メールが "r" という文字で始まるエントリを返す __^r__ などの正規表現を使用することもできます。

##テーブルを削除する

サンプルの操作が終了したら、Azure PowerShell セッションから次のコマンドを使用し、このサンプルで使用された __people__ テーブルを削除します。

	Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

__hdinsightclustername__ を、使用する HDInsight クラスターの名前で置き換えます。

##トラブルシューティング

###Start-HBaseExample を使用したときに、結果が表示されないか、予期しない結果が表示される

`-showErr` パラメーターを使用して、ジョブの実行中に生成された標準エラー (STDERR) を表示します。

<!---HONumber=July15_HO2-->