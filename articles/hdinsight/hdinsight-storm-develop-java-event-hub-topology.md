---
title: "Java を使用した HDInsight の Storm での Event Hubs のイベントの処理 | Microsoft Docs"
description: "Maven で作成した Java Storm トポロジを使用して Event Hubs を処理する方法について説明します。"
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eaa86b706a538543816b59d8cd09ee54df43b26d


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (Java)
Azure Event Hubs では、Web サイト、アプリ、デバイスで発生する大量のデータを処理できます。 Event Hubs スパウトでは、HDInsight で Apache Storm を使用してこのデータをリアルタイムで簡単に分析できます。 また、Event Hubs のボルトを使用して Storm から Event Hub にデータを書き込むこともできます。

このチュートリアルでは、Event Hubs スパウトとボルトを使用して、Java ベースの Storm トポロジでデータの読み取りと書き込みを行う方法について説明します。

## <a name="prerequisites"></a>前提条件
* HDInsight クラスター上の Apache Storm。 基本について説明した次のいずれかの記事を参照してクラスターを作成します。
  
  * [HDInsight クラスター上の Linux ベースの Storm](hdinsight-apache-storm-tutorial-get-started-linux.md): SSH を使用して、Linux、Unix、OS X、または Windows クライアントからクラスターを操作する場合に選択します
  * [HDInsight クラスター上の Windows ベースの Storm](hdinsight-apache-storm-tutorial-get-started.md): PowerShell を使用して、Windows クライアントからクラスターを操作する場合に選択します
    
    > [!NOTE]
    > このドキュメントの手順は、HDInsight クラスター (3.3 または 3.4) 上の Storm を前提としています。 これらのクラスターには Storm 0.10.0 と Hadoop 2.7 が用意されており、ここで取り上げている例を正しく動作させるために必要な手順数が少なくて済みます。
    > 
    > HDInsight 3.2 上で Storm 0.9.3 を使用した場合の例については、この例のリポジトリの [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) ブランチを参照してください。
    > 
    > 
* [Azure Event Hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Oracle Java Developer Kit (JDK) バージョン 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) または同等のバージョン ([OpenJDK](http://openjdk.java.net/) など)
* [Maven](https://maven.apache.org/download.cgi): Maven は Java プロジェクトのプロジェクト ビルド システムです
* テキスト エディターまたは Java 統合開発環境 (IDE)
  
  > [!NOTE]
  > お使いのエディターまたは IDE には、Maven との操作用の特定の機能が搭載されている場合があります (本ドキュメントではカバーしていません)。 お使いの編集環境の機能に関する詳細は、製品のマニュアルをご覧ください。
  > 
  > 
  
  * SSH クライアント。 HDInsight で SSH を使用する方法の詳細については、次の記事をご覧ください。
    
    * [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)
* SCP クライアント。 Linux、Unix、OS X システムのすべてに付属しています。 Windows クライアントの場合は PSCP をお勧めします。PSCP は [PuTTY ダウンロード ページ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)から入手できます。

## <a name="understanding-the-example"></a>例の説明
[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) の例には、2 つのトポロジが含まれています。

**com.microsoft.example.EventHubWriter** は、Azure Event Hub にランダムなデータを書き込みます。 データはスパウトで生成され、ランダムなデバイス ID とデバイス値です。 この例では、文字列 ID と数値を出力する何らかのハードウェアをシミュレートしています。

**com.microsoft.example.EventHubReader** は、Event Hub からデータ (EventHubWriter から出力されたデータ) を読み取り、/devicedata ディレクトリの HDFS (この場合は、Azure HDInsight で出力され、テストされたので、WASB) に格納します。

データは JSON ドキュメント形式に変更されてから、Event Hub に出力されます。リーダーが読み取るときに、JSON からタプルに解析されます。 JSON 形式は次のとおりです。

    { "deviceId": "unique identifier", "deviceValue": some value }

Event Hub スパウトとボルトの内部書式のしくみを利用せずに、JSON ドキュメントを使用してデータを Event Hub を格納する理由は、形式がわかっているからです。

### <a name="project-configuration"></a>プロジェクトの構成
**POM.xml** ファイルには、この Maven プロジェクトの構成情報が含まれています。 興味深いのは次の点です。

#### <a name="the-eventhubs-storm-spout-dependency"></a>EventHubs Storm スパウトの依存関係
    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-eventhubs</artifactId>
      <version>0.10.0</version>
    </dependency>

storm-eventhubs パッケージの依存関係が加わります。このパッケージには、Event Hubs からの読み取りに使うスパウトと書き込みに使うボルトの両方が含まれます。

> [!NOTE]
> このパッケージは、Storm バージョン 0.10.0 以上でのみ利用できます。 Storm 0.9.3 を使用するときは、Microsoft から提供されているスパウト パッケージを手動でインストールする必要があります。 Storm 0.9.3 を使用した場合の例については、この例のリポジトリの [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) ブランチを参照してください。
> 
> 

#### <a name="the-hdfsbolt-and-wasb-components"></a>HdfsBolt および WASB コンポーネント
通常、HdfsBolt は、Hadoop Distributed File System HDFS にデータを格納するために使用されます。 一方、HDInsight クラスターは Azure Storage (WASB) を既定のデータ ストアとして使用するので、HdfsBolt が WASB ファイル システムを理解できるようにするコンポーネントをいくつか読み込む必要があります。

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
        <version>0.10.0</version>
        </dependency>
    <!--So HdfsBolt knows how to talk to WASB -->
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-azure</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.1</version>
        <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
        </exclusions>
    </dependency>

> [!NOTE]
> 以前のバージョンの HDInsight (バージョン 3.2 など) を使用する場合は、これらのコンポーネントを手動で登録する必要があります。 この例および以前の HDInsight クラスターに必要なカスタム ビットについては、この例のリポジトリの [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) ブランチを参照してください。
> 
> 

#### <a name="the-maven-compiler-plugin"></a>maven-compiler-plugin
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

#### <a name="the-maven-shade-plugin"></a>maven-shade-plugin
      <!-- build an uber jar -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          <transformers>
            <!-- Keep us from getting a can't overwrite file error -->
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
            <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
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

プロジェクト コードと必要な依存関係の両方を含む uber jar にソリューションをパッケージ化するために使用されます。 また、次の用途にも使用されます。

* 依存関係のライセンス ファイル名を変更する: この処理を実行しないと、Windows ベースの HDInsight クラスターで実行時にエラーが発生する可能性があります。
* セキュリティ/署名を除外する: この処理を実行しないと、HDInsight クラスターで実行時にエラーが発生する可能性があります。
* 同じインターフェイスの複数の実装は、1 つのエントリにマージする必要があります。 そのようになっていないと、WASB ファイル システムとの通信方法を Storm-HDFS ボルトが認識できないというエラーが発生します。

#### <a name="the-exec-maven-plugin"></a>exec-maven-plugin
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

たとえば、「 `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`」のように入力します。

#### <a name="the-resources-section"></a>resources セクション
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

* **EventHubs.properties**: Azure Event Hub への接続に使用する情報が含まれます。
* **core-site.xml**: HDInsight クラスターが使用する Azure Storage に関する情報が含まれます。

これらの両方に、Event Hub と HDInsight クラスターに関する情報を設定する必要があります。

## <a name="configure-environment-variables"></a>環境変数を構成する
開発用ワークステーションに Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* **JAVA_HOME** - Java ランタイム環境 (JRE) がインストールされているディレクトリを指している必要があります。 たとえば、Unix や Linux ディストリビューションの場合は、 `/usr/lib/jvm/java-7-oracle`のような値になります。 Windows の場合は、 `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - 次のパスを含む必要があります。
  
  * **JAVA_HOME** または同等のパス
  * **JAVA_HOME\bin** または同等のパス
  * Maven がインストールされているディレクトリ

## <a name="configure-event-hub"></a>Event Hub の構成
Event Hubs は、この例のデータ ソースです。 新しい Event Hub を作成するには、次の手順に従います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)から、**[新規作成]** > **[Service Bus]** > **[Event Hub]** > **[カスタム作成]** の順に選択します。
2. **[新しい Event Hub の追加]** 画面で **Event Hub 名**を入力し、ハブを作成する **[リージョン]** を選択して、新しい名前空間を作成するか、既存の名前空間を選択します。 **矢印** をクリックして続行します。
   
    ![ウィザード ページ 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)
   
   > [!NOTE]
   > 待機時間とコストを削減するために、HDInsight サーバーで使用する Storm と同じ **[場所]** を選択する必要があります。
   > 
   > 
3. **[Event Hub の構成]** 画面で、**パーティション カウント**と**メッセージ保持**の値を入力します。 この例では、パーティション カウントに 10 を、メッセージ保持に 1 を使用します。 パーティション数を書き留めておきます。この値は後で必要になります。
   
    ![ウィザード ページ 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)
4. Event Hub が作成されたら、名前空間を選択し、 **[Event Hubs]**を選択して、先ほど作成した Event Hub を選択します。
5. **[構成]**を選択し、次の情報を使用して新しいアクセス ポリシーを 2 つ作成します。
   
    <table>
    <tr><th>名前</th><th>アクセス許可</th></tr>
    <tr><td>ライター</td><td>送信</td></tr>
    <tr><td>閲覧者</td><td>リッスン</td></tr>
    </table>
   
    アクセス許可の作成後、ページの下部にある **[保存]** アイコンをクリックします。 これにより、この Event Hub に対する送信 (ライター) とリッスン (リーダー) に使用する共有アクセス ポリシーが作成されます。
   
    ![ポリシー](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)
6. ポリシーの保存後、ページの下部にある **[共有アクセス キー生成コンポーネント]** を使用して、**ライター**と**リーダー**のポリシーのキーを取得します。 後で使用できるように、これらを保存します。

## <a name="download-and-build-the-project"></a>プロジェクトのダウンロードとビルド
1. GitHub からプロジェクトをダウンロードします ( [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub))。 パッケージを zip アーカイブとしてダウンロードするか、 [git](https://git-scm.com/) を使用してローカルにプロジェクトの複製を作成します。
2. 次のコマンドを使用して、プロジェクトをビルドしてパッケージ化します。
   
        mvn package
   
    必要な依存関係がダウンロードされ、ビルドされ、プロジェクトがパッケージ化されます。 出力は **EventHubExample-1.0-SNAPSHOT.jar** というファイル名で **/target** ディレクトリに格納されます。

## <a name="deploy-the-topologies"></a>トポロジのデプロイ
このプロジェクトで作成される jar には、**com.microsoft.example.EventHubWriter** と **com.microsoft.example.EventHubReader** という 2 つのトポロジが含まれます。 EventHubWriter トポロジを最初に開始する必要があります。これは、Event Hub にイベントが出力され、その出力が EventHubReader から読み取られるためです。

### <a name="if-using-a-linux-based-cluster"></a>Linux ベースのクラスターを使用する場合
1. SCP を使用して、HDInsight クラスターに jar パッケージをコピーします。 USERNAME には、使用するクラスターの SSH ユーザーを指定します。 CLUSTERNAME には、使用する HDInsight クラスター名を指定します。
   
        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.
   
    SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。 アカウントで SSH キーを使用した場合は、 `-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。 たとえば、「 `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`」のように入力します。
   
   > [!NOTE]
   > クライアントが Windows ワークステーションの場合、SCP コマンドがインストールされていない可能性があります。 PSCP をお勧めします。PSCP は [PuTTY のダウンロード ページ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)からダウンロードできます。
   > 
   > 
   
    このコマンドで、クラスター上の SSH ユーザーのホーム ディレクトリにファイルをコピーします。
2. ファイルのアップロードが完了したら、SSH を使用して HDInsight クラスターに接続します。 **USERNAME** には、SSH ログイン名を指定します。 **CLUSTERNAME** には、HDInsight クラスター名を指定します。
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
   > [!NOTE]
   > SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。 アカウントで SSH キーを使用した場合は、 `-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。 次の例は、 `~/.ssh/id_rsa`から秘密キーを読み込みます。
   > 
   > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   > 
   > 
   
    PuTTY を使用している場合は、**[ホスト名 (または IP アドレス)]** フィールドに `CLUSTERNAME-ssh.azurehdinsight.net` を入力し、**[開く]** をクリックして接続します。 SSH アカウント名を入力するように求められます。
   
   > [!NOTE]
   > SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。 アカウントで SSH キーを使用した場合、必要に応じて次の手順でキーを選択します。
   > 
   > 1. **[カテゴリ]** で **[接続]**、**[SSH]** の順に展開し、**[認証]** を選択します。
   > 2. **[参照]** をクリックし、プライベート キーが含まれた .ppk ファイルを選択します。
   > 3. **[開く]** をクリックして接続します。
   > 
   > 
3. 次のコマンドを使用して、トポロジを開始します。
   
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader
   
    トポロジが開始され、"reader" と "writer" というフレンドリ名が付けられます。
4. 1 ～ 2 分待つと、トポロジで Event Hub のイベントの書き込みと読み取りを実行できるようになります。次に、以下のコマンドを使用して、EventHubReader がデータを HDInsight ストレージに格納していることを確認します。
   
        hadoop fs -ls /devicedata
   
    次のようなファイルの一覧が返されます。
   
        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt
   
   > [!NOTE]
   > 一部のファイルはサイズ 0 です。これは、EventHubReader でファイルが作成されても、まだデータが格納されていないためです。
   > 
   > 
   
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
5. 次のコマンドを使用してトポロジを停止します。
   
        storm kill reader
        storm kill writer

### <a name="if-using-a-windows-based-cluster"></a>Windows ベースのクラスターを使用している場合
1. ブラウザーで、https://CLUSTERNAME.azurehdinsight.net に移動します。 メッセージが表示されたら、HDInsight クラスター管理者の資格情報を入力します。 Storm ダッシュボードが表示されます。
2. **[Jar ファイル]** ドロップダウンを使用して、ビルド環境にある EventHubExample-1.0-SNAPSHOT.jar を参照して選択します。
3. **[クラス名]** に「`com.mirosoft.example.EventHubWriter`」と入力します。
4. **[追加パラメーター]** に「`writer`」と入力します。 最後に、 **[送信]** をクリックして jar をアップロードし、EventHubWriter トポロジを開始します。
5. トポロジが開始されたら、フォームを使用して EventHubReader を開始します。
   
   * **Jar ファイル**: 以前にアップロードした EventHubExample-1.0-SNAPSHOT.jar を選択します。
   * **クラス名**: 「`com.microsoft.example.EventHubReader`」と入力します。
   * **追加パラメーター**: 「`reader`」と入力します
     
     [送信] をクリックして EventHubReader トポロジを開始します。
6. 数分待つと、トポロジでイベントを生成し、Azure Storage に格納できるようになります。次に、**[Storm ダッシュボード]** ページの上部にある **[Hadoop Query Console] (Hadoop クエリ コンソール)** タブを選択します。
7. **[クエリ コンソール]** の **[Hive エディター]** を選択し、既定の `select * from hivesampletable` を次の値で置き換えます。
   
        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasbs:///devicedata/';
        select * from devicedata limit 10;
   
    **[選択]** をクリックしてクエリを実行します。 EventHubReader から Azure Storage (WASB) に出力されたデータから、10 行が返されます。 クエリが完了すると、次のようなデータが表示されます。
   
        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574
8. ページの上部にある **[Storm ダッシュボード]** を選択し、**[Storm UI]** を選択します。 **[Storm UI]** から、**リーダー** トポロジのリンクを選択し、**[強制終了]** ボタンを使用してトポロジを停止します。 **ライター** トポロジでも同じプロセスを繰り返します。

### <a name="checkpointing"></a>チェックポイント機能
EventHubSpout は Zookeeper ノードに対する状態へのチェックポイントを定期的に設定し、キューから読み取ったメッセージの現在のオフセットを保存します。 これにより、コンポーネントは次のシナリオで保存されたオフセットでメッセージの受信を開始できます。

* コンポーネントのインスタンスは失敗し、再起動されます。
* ノードを追加または削除して、クラスターを拡張または圧縮します。
* トポロジが強制終了され、 **同じ名前で**再起動されます。

#### <a name="on-windows-based-hdinsight-clusters"></a>Windows ベースの HDInsight クラスターの場合
保存されたチェックポイントを WASB (HDInsight クラスターで使用される Azure Storage) にエクスポート、インポートできます。これを実行するスクリプトは、**c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin** の HDInsight クラスター上の Storm に配置されています。

> [!NOTE]
> クラスターにインストールされた Storm のバージョンは今後変更される可能性があるため、パスのバージョン番号は異なる場合があります。
> 
> 

このディレクトリのスクリプトは次のとおりです。

* **stormmeta_import.cmd**: クラスターの既定のストレージ コンテナーから Zookeeper にすべての Storm メタデータをインポートします。
* **stormmeta_export.cmd**: Zookeeper からクラスターの既定のストレージ コンテナーにすべての Storm メタデータをエクスポートします。
* **stormmeta_delete.cmd**: Zookeeper からのすべての Storm メタデータを削除します。

インポートのエクスポートにより、クラスターを削除する必要がある一方で、新しいクラスターを再びオンラインにするときにハブの現在のオフセットから処理を再開する場合、チェックポイントのデータを保持できます。

> [!NOTE]
> データは既定のストレージ コンテナーに保存されるため、新しいクラスターで以前のクラスターと同じストレージ アカウントとコンテナーを使用する **必要があります** 。
> 
> 

## <a name="delete-your-cluster"></a>クラスターを削除する
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshooting"></a>トラブルシューティング
( `hadoop fs -ls /devicedata` コマンドまたはクエリ コンソールの Hive コマンドを使用して) /devicedata ディレクトリに格納されているファイルが表示されない場合は、Storm UI を使用して、トポロジから返されるエラーを確認します。

Storm UI の詳細な使用方法については、次のトピックを参照してください。

* HDInsight クラスターで **Linux ベース** の Storm を使用している場合は、「 [Deploy and manage Apache Storm topologies on Linux ベース HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
* HDInsight クラスターで **Windows ベース** の Storm を使用している場合は、「 [Deploy and manage Apache Storm topologies on Windows ベース HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="next-steps"></a>次のステップ
* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->


