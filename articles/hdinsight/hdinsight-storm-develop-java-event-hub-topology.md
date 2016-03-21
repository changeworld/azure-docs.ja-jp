<properties
   pageTitle="Java を使用した HDInsight の Storm での Event Hubs のイベントの処理 | Azure"
   description="Maven で作成した Java Storm トポロジを使用して Event Hubs を処理する方法について説明します。"
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/01/2016"
   ms.author="larryfr"/>

# HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (Java)

Azure Event Hubs では、Web サイト、アプリ、デバイスで発生する大量のデータを処理できます。Event Hubs スパウトでは、HDInsight で Apache Storm を使用してこのデータをリアルタイムで簡単に分析できます。また、Event Hubs のボルトを使用して Storm から Event Hub にデータを書き込むこともできます。

このチュートリアルでは、Event Hubs スパウトとボルトを使用して、Java ベースの Storm トポロジでデータの読み取りと書き込みを行う方法について説明します。

## 前提条件

* HDInsight クラスター上の Apache Storm。基本について説明した次のいずれかの記事を参照してクラスターを作成します。

    - [Linux ベースのクラスター](hdinsight-apache-storm-tutorial-get-started-linux.md): SSH を使用して、Linux、Unix、OS X、または Windows クライアントからクラスターを操作する場合に選択します

    - [Windows ベースのクラスター](hdinsight-apache-storm-tutorial-get-started.md): PowerShell を使用して、Windows クライアントからクラスターを操作する場合に選択します

    > [AZURE.NOTE] 2 つのクラスターの違いは、SSH を使用してトポロジを送信する先がクラスターか Web フォームか、という点のみです。

* [Azure Event Hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)。

* [Oracle Java Developer Kit (JDK) バージョン 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) または同等のバージョン ([OpenJDK](http://openjdk.java.net/) など)

* [Maven](https://maven.apache.org/download.cgi): Maven は Java プロジェクトのプロジェクト ビルド システムです

* テキスト エディターまたは Java 統合開発環境 (IDE)

	> [AZURE.NOTE] お使いのエディターまたは IDE には、Maven との操作用の特定の機能が搭載されている場合があります (本ドキュメントではカバーしていません)。お使いの編集環境の機能に関する詳細は、製品のマニュアルをご覧ください。

 * SSH クライアントHDInsight で SSH を使用する方法の詳細については、次の記事をご覧ください。

    - [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

* SCP クライアント。Linux、Unix、OS X システムのすべてに付属しています。Windows クライアントの場合は PSCP をお勧めします。PSCP は [PuTTY ダウンロード ページ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)から入手できます。

##例の説明

[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) の例には、2 つのトポロジが含まれています。

__com.microsoft.example.EventHubWriter__ は、Azure Event Hub にランダムなデータを書き込みます。データはスパウトで生成され、ランダムなデバイス ID とデバイス値です。この例では、文字列 ID と数値を出力する何らかのハードウェアをシミュレートしています。

__com.microsoft.example.EventHubReader__ は、Event Hub からデータ (EventHubWriter から出力されたデータ) を読み取り、/devicedata ディレクトリの HDFS (この場合は、Azure HDInsight で出力され、テストされたので、WASB) に格納します。

データは JSON ドキュメント形式に変更されてから、Event Hub に出力されます。リーダーが読み取るときに、JSON からタプルに解析されます。JSON 形式は次のとおりです。

    { "deviceId": "unique identifier", "deviceValue": some value }

Event Hub スパウトとボルトの内部書式のしくみを利用せずに、JSON ドキュメントを使用してデータを Event Hub を格納する理由は、形式がわかっているからです。

###プロジェクトの構成

**POM.xml** ファイルには、この Maven プロジェクトの構成情報が含まれています。興味深いのは次の点です。

####EventHubs Storm スパウトの依存関係

    <dependency>
      <groupId>com.microsoft.eventhubs</groupId>
      <artifactId>eventhubs-storm-spout</artifactId>
      <version>0.9.3</version>
    </dependency>

eventhubs-storm-spout パッケージの依存関係が加わります。このパッケージには、Event Hubs からの読み取りに使うスパウトと書き込みに使うボルトの両方が含まれます。

> [AZURE.NOTE] このパッケージは、Maven では使用できません。後の手順で、ローカルの Maven リポジトリに手動でインストールします。

####HdfsBolt および WASB コンポーネント

通常、HdfsBolt は、Hadoop Distributed File System HDFS にデータを格納するために使用されます。一方、HDInsight クラスターは Azure Storage (WASB) を既定のデータ ストアとして使用するので、HdfsBolt が WASB ファイル システムを理解できるようにするコンポーネントをいくつか読み込む必要があります。

      <!--HdfsBolt stuff -->
      <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-hdfs</artifactId>
        <exclusions>
          <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
          </exclusion>
          <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
          </exclusion>
        </exclusions>
        <version>0.9.3</version>
      </dependency>
      <!--
     This is a temporary workaround to make HdfsBolt work with WASB through hadoop-azure project.
     For now, we have to build hadoop-client, hadoop-hdfs and hadoop-azure from Hadoop trunk
     (which defaults to 3.0.0-SNAPSHOT version). And push those jars and dependencies to local
     mvn repo (take a look at push_lib_mvn.ps1).

     Once Hadoop 2.7 is released, we can just switch to that version.
     Note that hadoop-azure is added to Hadoop on Hadoop 2.7.
     -->
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-client</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-hdfs</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-azure</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-common</artifactId>
       <version>3.0.0-SNAPSHOT</version>
       <exclusions>
         <exclusion>
           <groupId>org.slf4j</groupId>
           <artifactId>slf4j-log4j12</artifactId>
         </exclusion>
       </exclusions>
     </dependency>
     <dependency>
       <groupId>com.microsoft.windowsazure.storage</groupId>
       <artifactId>microsoft-windowsazure-storage-sdk</artifactId>
       <version>0.6.0</version>
     </dependency>

> [AZURE.NOTE] WASB を有効にするパッケージは、Maven リポジトリで使用できません。後の手順で手動でインストールします。

####maven-compiler-plugin

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

プロジェクトに Java 7 との互換性を持たせてコンパイルするように Maven に指示します。Java 7 は、HDInsight クラスターから使用されます。

####maven-shade-plugin

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          <!-- Keep us from getting a can't overwrite file error -->
          <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
          </transformers>
          <!-- Keep us from getting a bad signature error -->
          <filters>
            <filter>
                <artifact>*:*</artifact>
                <excludes>
                    <exclude>META-INF/*.SF</exclude>
                    <exclude>META-INF/*.DSA</exclude>
                    <exclude>META-INF/*.RSA</exclude>
                </excludes>
            </filter>
        </filters>
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

プロジェクト コードと必要な依存関係の両方を含む uber jar にソリューションをパッケージ化するために使用されます。また、次の用途にも使用されます。

* 依存関係のライセンス ファイル名を変更する: この処理を実行しないと、Windows ベースの HDInsight クラスターで実行時にエラーが発生する可能性があります。

* セキュリティ/署名を除外する: この処理を実行しないと、HDInsight クラスターで実行時にエラーが発生する可能性があります

####exec-maven-plugin

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.2.1</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

次のコマンドを使用して、ローカルの開発環境でトポロジを実行できます。

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

たとえば、「`mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`」のように入力します。

####resources セクション

    <resources>
      <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
          <include>EventHubs.properties</include>
          <include>core-site.xml</include>
        </includes>
      </resource>
    </resources>

プロジェクトに必要なリソースを定義します。

- **EventHubs.properties**: Azure Event Hub への接続に使用する情報が含まれます。
- **core-site.xml**: HDInsight クラスターが使用する Azure Storage に関する情報が含まれます。

これらの両方に、Event Hub と HDInsight クラスターに関する情報を設定する必要があります。

##環境変数を構成する

開発用ワークステーションに Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* **JAVA\_HOME** - Java ランタイム環境 (JRE) がインストールされているディレクトリを指している必要があります。たとえば、Unix や Linux ディストリビューションの場合は、`/usr/lib/jvm/java-7-oracle` のような値になります。Windows の場合は、`c:\Program Files (x86)\Java\jre1.7` のような値になります。

* **PATH** - 次のパスを含む必要があります。

	* **JAVA\_HOME** または同等のパス

	* **JAVA\_HOME\\bin** または同等のパス

	* Maven がインストールされているディレクトリ

## Event Hub の構成

Event Hubs は、この例のデータ ソースです。新しい Event Hub を作成するには、次の手順に従います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)から、**[新規作成]**、**[Service Bus]**、**[Event Hub]**、**[カスタム作成]** の順に選択します。

2. **[新しい Event Hub の追加]** 画面で **Event Hub 名**を入力し、ハブを作成する **[リージョン]** を選択して、新しい名前空間を作成するか、既存の名前空間を選択します。**矢印**をクリックして続行します。

	![ウィザード ページ 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] 待機時間とコストを削減するために、HDInsight サーバーで使用する Storm と同じ **[場所]** を選択する必要があります。

2. **[Event Hub の構成]** 画面で、**パーティション カウント**と**メッセージ保持**の値を入力します。この例では、パーティション カウントに 10 を、メッセージ保持に 1 を使用します。パーティション数を書き留めておきます。この値は後で必要になります。

	![ウィザード ページ 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Event Hub が作成されたら、名前空間を選択し、**[Event Hubs]** を選択して、先ほど作成した Event Hub を選択します。

4. **[構成]** を選択し、次の情報を使用して新しいアクセス ポリシーを 2 つ作成します。

	<table>
	<tr><th>名前</th><th>アクセス許可</th></tr>
	<tr><td>ライター</td><td>送信</td></tr>
	<tr><td>閲覧者</td><td>リッスン</td></tr>
	</table>

	アクセス許可の作成後、ページの下部にある **[保存]** アイコンをクリックします。これにより、この Event Hub に対する送信 (ライター) とリッスン (リーダー) に使用する共有アクセス ポリシーが作成されます。

	![ポリシー](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. ポリシーの保存後、ページの下部にある **[共有アクセス キー生成コンポーネント]** を使用して、**ライター**と**リーダー**のポリシーのキーを取得します。後で使用できるように、これらを保存します。

## プロジェクトのダウンロードとビルド

1. GitHub からプロジェクトをダウンロードします ([hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub))。パッケージを zip アーカイブとしてダウンロードするか、[git](https://git-scm.com/) を使用してローカルにプロジェクトの複製を作成します。

2. 次のコマンドを使用して、プロジェクトに含まれるパッケージをローカルの Maven リポジトリにインストールします。その結果、Event Hub スパウトとボルトが有効になるだけでなく、HdfsBolt を使用して Azure Storage (WASB) に出力できるようになります。

		mvn -q install:install-file -Dfile=lib/eventhubs/eventhubs-storm-spout-0.9.3-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9.3 -Dpackaging=jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-azure-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-client-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-hdfs-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.jar -DpomFile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom

	> [AZURE.NOTE] PowerShell を使用している場合、`-D`パラメーターは引用符で囲む必要があります。たとえば、「`"-Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom"`」のように入力します。

	また、これらのファイルは https://github.com/hdinsight/hdinsight-storm-examplesにも掲載されています。最新バージョンはこちらを参照してください。

3. 次のコマンドを使用して、プロジェクトをビルドしてパッケージ化します。

        mvn package

    必要な依存関係がダウンロードされ、ビルドされ、プロジェクトがパッケージ化されます。出力は __EventHubExample-1.0-SNAPSHOT.jar__ というファイル名で __/target__ ディレクトリに格納されます。

## トポロジのデプロイ

このプロジェクトで作成される jar には、__com.microsoft.example.EventHubWriter__ と __com.microsoft.example.EventHubReader__ という 2 つのトポロジが含まれます。EventHubWriter トポロジを最初に開始する必要があります。これは、Event Hub にイベントが出力され、その出力が EventHubReader から読み取られるためです。

###Linux ベースのクラスターを使用する場合

1. SCP を使用して、HDInsight クラスターに jar パッケージをコピーします。USERNAME には、使用するクラスターの SSH ユーザーを指定します。CLUSTERNAME には、使用する HDInsight クラスター名を指定します。

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。アカウントで SSH キーを使用した場合は、`-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。たとえば、「`scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`」のように入力します。

    > [AZURE.NOTE] クライアントが Windows ワークステーションの場合、SCP コマンドがインストールされていない可能性があります。PSCP をお勧めします。PSCP は [PuTTY のダウンロード ページ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)からダウンロードできます。

    このコマンドで、クラスター上の SSH ユーザーのホーム ディレクトリにファイルをコピーします。

1. ファイルのアップロードが完了したら、SSH を使用して HDInsight クラスターに接続します。**USERNAME** には、SSH ログイン名を指定します。**CLUSTERNAME** には、HDInsight クラスター名を指定します。

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。アカウントで SSH キーを使用した場合は、`-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。次の例は、`~/.ssh/id_rsa` から秘密キーを読み込みます。
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

    PuTTY を使用している場合は、__[ホスト名 (または IP アドレス)]__ フィールドに `CLUSTERNAME-ssh.azurehdinsight.net` を入力し、__[開く]__ をクリックして接続します。SSH アカウント名を入力するように求められます。

    > [AZURE.NOTE] SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。アカウントで SSH キーを使用した場合、必要に応じて次の手順でキーを選択します。
    >
    > 1. **[カテゴリ]** で **[接続]**、**[SSH]** の順に展開し、**[認証]** を選択します。
    > 2. **[参照]** をクリックし、プライベート キーが含まれた .ppk ファイルを選択します。
    > 3. __[開く]__ をクリックして接続します。

2. 次のコマンドを使用して、トポロジを開始します。

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    トポロジが開始され、"reader" と "writer" というフレンドリ名が付けられます。

3. 1 ～ 2 分待つと、トポロジで Event Hub のイベントの書き込みと読み取りを実行できるようになります。次に、以下のコマンドを使用して、EventHubReader がデータを HDInsight ストレージに格納していることを確認します。

        hadoop fs -ls /devicedata

    次のようなファイルの一覧が返されます。

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

    > [AZURE.NOTE] 一部のファイルはサイズ 0 です。これは、EventHubReader でファイルが作成されても、まだデータが格納されていないためです。

    ファイルの内容を表示するには、次のコマンドを使用します。

        hadoop fs -text /devicedata/*.txt

    次のようなデータが返されます。

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    最初の列にはデバイスの ID 値、2 列目にはデバイス値が含まれます。

4. 次のコマンドを使用してトポロジを停止します。

        storm kill reader
        storm kill writer

###Windows ベースのクラスターを使用している場合

1. ブラウザーで https://CLUSTERNAME.azurehdinsight.net を開きます。メッセージが表示されたら、HDInsight クラスター管理者の資格情報を入力します。Storm ダッシュボードが表示されます。

2. __[Jar ファイル]__ ドロップダウンを使用して、ビルド環境にある EventHubExample-1.0-SNAPSHOT.jar を参照して選択します。

3. __[クラス名]__ に `com.mirosoft.example.EventHubWriter` を入力します。

4. __[追加パラメーター]__ に `writer` を入力します。最後に、__[送信]__ をクリックして jar をアップロードし、EventHubWriter トポロジを開始します。

5. トポロジが開始されたら、フォームを使用して EventHubReader を開始します。

    * __Jar ファイル__: 以前にアップロードした EventHubExample-1.0-SNAPSHOT.jar を選択します。
    * __クラス名__: `com.microsoft.example.EventHubReader` を入力します
    * __追加パラメーター__: `reader` を入力します

    [送信] をクリックして EventHubReader トポロジを開始します。

6. 数分待つと、トポロジでイベントを生成し、Azure Storage に格納できるようになります。次に、__[Storm ダッシュボード]__ ページの上部にある __[クエリ コンソール]__ タブを選択します。

7. __[クエリ コンソール]__ の __[Hive エディター]__ を選択し、既定の `select * from hivesampletable`を次の値で置き換えます。

        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasb:///devicedata/';
        select * from devicedata limit 10;

    __[選択]__ をクリックしてクエリを実行します。EventHubReader から Azure Storage (WASB) に出力されたデータから、10 行が返されます。クエリが完了すると、次のようなデータが表示されます。

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

8. ページの上部にある __[Storm ダッシュボード]__ を選択し、__[Storm UI]__ を選択します。__[Storm UI]__ から、__リーダー__ トポロジのリンクを選択し、__[強制終了]__ ボタンを使用してトポロジを停止します。__ライター__ トポロジでも同じプロセスを繰り返します。



### チェックポイント機能

EventHubSpout は Zookeeper ノードに対する状態へのチェックポイントを定期的に設定し、キューから読み取ったメッセージの現在のオフセットを保存します。これにより、コンポーネントは次のシナリオで保存されたオフセットでメッセージの受信を開始できます。

* コンポーネントのインスタンスは失敗し、再起動されます。

* ノードを追加または削除して、クラスターを拡張または圧縮します。

* トポロジが強制終了され、**同じ名前で**再起動されます。

####Windows ベースの HDInsight クラスターの場合

保存されたチェックポイントを WASB (HDInsight クラスターで使用される Azure Storage) にエクスポート、インポートできます。 これを実行するスクリプトは、**c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin** の HDInsight クラスター上の Storm に配置されています。

>[AZURE.NOTE] クラスターにインストールされた Storm のバージョンは今後変更される可能性があるため、パスのバージョン番号は異なる場合があります。

このディレクトリのスクリプトは次のとおりです。

* **stormmeta\_import.cmd**: クラスターの既定のストレージ コンテナーから Zookeeper にすべての Storm メタデータをインポートします。

* **stormmeta\_export.cmd**: Zookeeper からクラスターの既定のストレージ コンテナーにすべての Storm メタデータをエクスポートします。

* **stormmeta\_delete.cmd**: Zookeeper からのすべての Storm メタデータを削除します。

インポートのエクスポートにより、クラスターを削除する必要がある一方で、新しいクラスターを再びオンラインにする際にハブの現在のオフセットから処理を再開する場合、チェックポイントのデータを保持できます。

> [AZURE.NOTE] データは既定のストレージ コンテナーに保存されるため、新しいクラスターで以前のクラスターと同じストレージ アカウントとコンテナーを使用する**必要があります**。

##トラブルシューティング

(`hadoop fs -ls /devicedata` コマンドまたはクエリ コンソールの Hive コマンドを使用して) /devicedata ディレクトリに格納されているファイルが表示されない場合は、Storm UI を使用して、トポロジから返されるエラーを確認します。

Storm UI の詳細な使用方法については、次のトピックを参照してください。

* HDInsight クラスターで __Linux ベース__の Storm を使用している場合は、「[Linux ベースの HDInsight での Apache Storm トポロジのデプロイと管理](hdinsight-storm-deploy-monitor-topology-linux.md)」を参照してください。

* HDInsight クラスターで __Windows ベース__の Storm を使用している場合は、「[Windows ベースの HDInsight での Apache Storm トポロジのデプロイと管理](hdinsight-storm-deploy-monitor-topology-linux.md)」を参照してください。

##次のステップ

* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0302_2016-->