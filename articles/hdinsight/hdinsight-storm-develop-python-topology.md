<properties
   pageTitle="HDinsight での Storm トポロジの Python コンポーネントの使用 |Microsoft Azure"
   description="Azure HDInsight で Apache Storm から Python コンポーネントを使用する方法について説明します。Java ベースと Clojure ベースの両方の Storm トポロジから Python コンポーネントを使用する方法を学習します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/19/2016"
   ms.author="larryfr"/>

#HDInsight での Python を使用した Apache Storm トポロジの開発

Apache Storm では複数の言語がサポートされています。これにより、1 つのトポロジでの複数の言語からのコンポーネントの結合も可能になります。このドキュメントでは、HDInsight での Java および Clojure ベース Storm トポロジにおける Python コンポーネントの使用方法を学習します。

##前提条件

* Python 2.7 以上

* Java JDK 1.7 以上

* [Leiningen](http://leiningen.org/)

##Storm の複数言語サポート

Storm は任意のプログラミング言語を使用して記述されたコンポーネントで使用するために設計されていますが、その場合、コンポーネントが [Storm の Thrift 定義](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)の使用方法を理解している必要があります。Python では、Storm と簡単に連動できるようにするための Apache Storm プロジェクトの一部として、モジュールが提供されます。このモジュールは [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) にあります。

Apache Storm は Java 仮想マシン (JVM) で実行される Java プロセスであるために、その他の言語で記述されたコンポーネントはサブプロセスとして実行されます。JVM で実行されている Storm ビットは、stdin/stdout 経由で送信された JSON メッセージを使用してこれらのサブプロセスと通信します。コンポーネント間の通信の詳細については、[多言語プロトコル](https://storm.apache.org/documentation/Multilang-protocol.html)に関するドキュメントを参照してください。

###Storm モジュール

Storm モジュール (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) は、Storm で使用する Python コンポーネントを作成するために必要なビットを提供します。

これにより、タプルを出力するための `storm.emit` やログに書き込むための `storm.logInfo` などが提供されます。このファイルに目を通し、提供内容を理解することをお勧めします。

##課題

__storm.py__ モジュールを使用して、データを使用する Python スパウトやデータを処理するボルトを作成できますが、コンポーネント間の通信を関連付ける Storm トポロジ定義全体は引き続き Java または Clojure を使用して書き込まれます。さらに、Java を使用する場合は、Python コンポーネントへのインターフェイスとして機能する Java コンポーネントを作成もする必要もあります。

また、Storm クラスターは分散環境で実行されるため、Python コンポーネントで必要なすべてのモジュールが、クラスター内のすべての worker ノードで使用できることを確認する必要があります。Storm の場合、多言語リソースではこれを容易に行うことはできません。つまり、トポロジの jar ファイルの一部として依存関係をすべて含めるか、クラスター内の各 worker ノードに依存関係を手動でインストールする必要があります。

これらの欠点の克服を試みる [Pyleus](https://github.com/Yelp/pyleus) や [Streamparse](https://github.com/Parsely/streamparse) などのプロジェクトがいくつかあります。これらはいずれも、Linux ベースの HDInsight クラスターで実行できますが、クラスターのセットアップ時にカスタマイズが必要であり、HDInsight クラスターでは完全にテストされていないため、このドキュメントの重要なポイントではありません。HDInsight でのこれらのフレームワークの使用に関する注意事項は、このドキュメントの最後に示されています。

###Java とClojure トポロジの定義

トポロジを定義する 2 つの方法の 1 つである Clojure は、トポロジ定義で Python コンポーネントを直接参照できるため、非常に簡単でわかりやすい方法です。Java ベースのトポロジ定義の場合、Python コンポーネントから返されるタプルでのフィールド宣言などを処理する Java コンポーネントを定義する必要もあります。

このドキュメントでは両方の方法について説明し、プロジェクト例も示します。

##Java トポロジを使用する Python コンポーネント

> [AZURE.NOTE] この例は、__JavaTopology__ ディレクトリの [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) にあります。これは Maven ベースのプロジェクトです。Maven に慣れていない場合は、Storm トポロジの Maven プロジェクトの作成の詳細について、[HDInsight での Apache Storm を使用する Java ベース トポロジの開発](hdinsight-storm-develop-java-topology.md)に関するページを参照してください。

Python (または他の JVM 言語コンポーネント) を使用する Java ベース トポロジは最初は Java コンポーネントを使用しているように思えますが、各 Java スパウト/ボルトを見ると、コードは次のようになっています。

    public SplitBolt() {
        super("python", "countbolt.py");
    }

この場合、Java は Python を呼び出し、実際のボルト ロジックを含むスクリプトを実行します。Java スパウト/ボルト (この例の場合) は、基になる Python コンポーネントによって出力されるタプルでフィールドを宣言するだけです。

この例では、実際の Python ファイルは `/multilang/resources` ディレクトリに格納されます。次のように、`/multilang` ディレクトリは __pom.xml__ で参照されます。

<resources> <resource> <!-- Where the Python bits are kept --> <directory>${basedir}/multilang</directory> </resource> </resources>

これには、このプロジェクトから構築される jar の `/multilang` フォルダー内のファイルがすべて含まれます。

> [AZURE.IMPORTANT] ここでは、`/multilang/resources` ではなく、`/multilang` ディレクトリのみが指定されることに注意してください。Storm には `resources` ディレクトリの JVM 以外のリソースが必要であるため、内部的に既に検索されています。このフォルダーにコンポーネントを配置することで、Java コードでは名前での参照のみが可能になります。たとえば、「`super("python", "countbolt.py");`」のように入力します。考えてみると、Storm が多言語リソースへのアクセス時に `resources` ディレクトリをルート (/) と見なすようにする別の方法もあります。
>
> このプロジェクト例の場合、`storm.py` モジュールは `/multilang/resources` ディレクトリに含まれます。

###プロジェクトをビルドして実行する

このプロジェクトをローカルで実行する場合は、単に以下の Maven コマンドを使用して、ローカル モードでビルドおよび実行します。

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

プロセスを強制終了するには、Ctrl キーを押しながら C キーを押します。

Apache Storm を実行して HDInsight クラスターにプロジェクトをデプロイするには、次の手順を使用します。

1. 以下を使用して、uber jar をビルドします。

        mvn package

    これで、このプロジェクトの `/target` ディレクトリに __WordCount--1.0-SNAPSHOT.jar__ という名前のファイルが作成されます。

2. 次のいずれかの方法を使用して、Hadoop クラスターに jar ファイルをアップロードします。

    * __Linux ベース__ HDInsight クラスターの場合: `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` を使用して、jar ファイルをクラスターにコピーします。その場合、USERNAME を SSH 名に、CLUSTERNAME を HDInsight クラスター名に置き換えます。

        ファイルのアップロードが完了したら、SSH を使用してクラスターに接続し、`storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount` を使用してトポロジを開始します。

    * __Windows ベース__ HDInsight クラスターの場合: ブラウザーで HTTPS://CLUSTERNAME.azurehdinsight.net/ に移動し、Storm ダッシュボードに接続します。CLUSTERNAME を HDInsight クラスター名に置き換え、プロンプトが表示されたら、管理者の名前とパスワードを入力します。

        フォームを使用して、以下のアクションを実行します。

        * __Jar ファイル__: __[参照]__ を選択してから __WordCount-1.0-SNAPSHOT.jar__ ファイルを選択します。
        * __クラス名__: 「`com.microsoft.example.WordCount`」と入力します。
        * __追加パラメーター__: トポロジを識別するための `wordcount` などのわかりやすい名前を入力します。

        最後に、__[送信]__ を選択してトポロジを開始します。

> [AZURE.NOTE] Storm トポロジは、開始すると停止 (強制終了) するまで実行されます。 トポロジを停止するには、コマンドラインから `storm kill TOPOLOGYNAME` コマンドを使用するか (Linux クラスターへの SSH セッションなどの場合)、Storm UI を使用して、トポロジを選択してから __[強制終了]__ ボタンをクリックします。

##Clojure トポロジを使用する Python コンポーネント

> [AZURE.NOTE] この例は、__ClojureTopology__ ディレクトリの [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) にあります。

このトポロジは、[新しい Clojure プロジェクトを作成](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project)するための [Leiningen](http://leiningen.org) を使用して作成されたものです。その後、スキャフォールディングされたプロジェクトには次の変更が加えられました。

* __project.clj__: Storm の依存関係が追加され、HDInsight サーバーへのデプロイ時に問題が生じる可能性がある項目は除外されました。
* __resources/resources__: Leiningen は既定の `resources` ディレクトリを作成しますが、ここに格納されたファイルはこのプロジェクトから作成された jar ファイルのルートに追加されるように見え、Storm は `resources` という名前のサブディレクトリのファイルを予期します。したがって、サブディレクトリが追加され、Python ファイルは `resources/resources` に格納されています。実行時に、これは Phthon コンポーネントにアクセスするためのルート (/) として扱われます。
* __src/wordcount/core.clj__: このファイルはトポロジ定義を含み、__project.clj__ ファイルから参照されます。Storm トポロジを定義するための Clojure の使用に関する詳細については、「[Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html)」を参照してください。

###プロジェクトをビルドして実行する

__プロジェクトをローカルでビルドして実行するには__、次のコマンドを使用します。

    lein do clean, run

トポロジを停止するには、__Ctrl キーを押しながら C キーを押します__。

__uberjar をビルドして、HDInsight にデプロイするには__、次の手順を使用します。

1. 以下のコマンドを使用して、トポロジと必要な依存関係を含む uberjar を作成します。

        lein uberjar

    これで、`target\uberjar+uberjar` ディレクトリに `wordcount-1.0-SNAPSHOT.jar` という名前の新しいファイルが作成されます。
    
2. 以下のいずれかの方法を使用し、HDInsight クラスターにトポロジをデプロイして実行します。

    * __Linux ベースの HDInsight__
    
        1. `scp` を使用して、HDInsight クラスターのヘッド ノードにファイルをコピーします。次に例を示します。
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            USERNAME は HDInsight クラスターの SSH ユーザーに、CLUSTERNAME は HDInsight クラスター名に置き換えます。
            
        2. ファイルがクラスターにコピーされたら、SSH を使用してクラスターに接続し、ジョブを送信します。HDInsight での SSH の使用方法については、以下のいずれかの記事を参照してください。
        
            * [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. 接続したら、次のコマンドを使用してトポロジを開始します。
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __Windows ベースの HDInsight__
    
        1. ブラウザーで HTTPS://CLUSTERNAME.azurehdinsight.net/ に移動して、Storm ダッシュボードに接続します。CLUSTERNAME を HDInsight クラスター名に置き換え、プロンプトが表示されたら、管理者の名前とパスワードを入力します。

        2. フォームを使用して、以下のアクションを実行します。

            * __Jar ファイル__: __[参照]__ を選択してから __wordcount-1.0-SNAPSHOT.jar__ ファイルを選択します。
            * __クラス名__: 「`wordcount.core`」と入力します。
            * __追加パラメーター__: トポロジを識別するための `wordcount` などのわかりやすい名前を入力します。

            最後に、__[送信]__ を選択してトポロジを開始します。

> [AZURE.NOTE] Storm トポロジは、開始すると停止 (強制終了) するまで実行されます。 トポロジを停止するには、コマンドラインから `storm kill TOPOLOGYNAME` コマンドを使用するか (Linux クラスターへの SSH セッション)、Storm UI を使用して、トポロジを選択してから __[強制終了]__ ボタンをクリックします。

##Pyleus フレームワーク

[Pyleus](https://github.com/Yelp/pyleus) は、以下を指定することで、Storm で Python を簡単に使用できるようにするためのフレームワークです。

* __YAML ベースのトポロジ定義__: これにより、Java や Clojure の知識を必要としない、より簡単なトポロジの定義方法が提供されます。
* __MessagePack ベースのシリアライザー__: JSON の代わりに、既定のシリアル化として MessagePack が使用されます。これにより、コンポーネント間でより速くメッセージングを行うことができます。
* __依存関係の管理__: Virtualenv を使用することで、Python の依存関係がすべての worker ノードに確実にデプロイされるようになります。これには、Virtualenv を worker ノードにインストールする必要があります。

> [AZURE.IMPORTANT] Pyleus では、開発環境に Storm が必要です。基本的な Apache Storm 0.9.3 ディストリビューションを使用すると、HDInsight で提供されるバージョンの Storm と jar の互換性がなくなるようです。したがって、次の手順では、開発環境として HDInsight クラスターを使用します。

ビルド環境として HDInsight ヘッド ノードを使用することで、例の Pyleus トポロジを正常にビルドできます。

1. HDInsight クラスター上に新しい Storm をプロビジョニングする場合、Python Virtualenv がクラスター ノード上に存在することを確認する必要があります。新しい Linux ベースの HDInsight クラスターを作成する場合は、[クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster.md)で次のスクリプト アクション設定を使用します。

    * __名前__: ここには単にわかりやすい名前を指定します。
    * \_\_ スクリプト URI\_\_: 値として `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` を使用します。このスクリプトは、ノードに Python Virtualenv をインストールします。
    
        > [AZURE.NOTE] このドキュメントの後述の Streamparse フレームワークで使用されるいくつかのディレクトリも作成されます。
        
    * __Nimbus__: この項目をオンにすると、スクリプトが Nimbus (ヘッド) ノードに適用されるようになります。
    * __Supervisor__: この項目をオンにすると、スクリプトが Supervisor (worker) ノードに適用されるようになります。
    
    その他の項目は空白のままにします。

1. クラスターが作成されたら、以下のように SSH を使用して接続します。

    * [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

2. SSH 接続の場合は、以下を使用して新しい仮想環境を作成し、Pyleus をインストールします。

        virtualenv pyleus_venv
        source pyleus_venv
        pip install pyleus

3. 次に、以下を使用して、Pyleus の git リポジトリをダウンロードし、WordCount の例をビルドします。

        sudo apt-get install git
        git clone https://github.com/Yelp/pyleus.git
        pyleus build pyleus/examples/word_count/pyleus_topology.yaml
    
    ビルドが完了すると、現在のディレクトリに `word_count.jar` という名前の新しいファイルが生成されます。
    
4. Storm クラスターにトポロジを送信するには、次のコマンドを使用します。

        pyleus submit -n localhost word_count.jar
    
    `-n` パラメーターは Nimbus ホストを指定します。ここではヘッド ノードを利用しているため、`localhost` を使用できます。
    
    `pyleus` コマンドを使用して、他の Storm アクションを実行することもできます。以下を使用すると、実行中のトポロジがリストされてから、`word_count` トポロジが強制終了されます。
    
        pyleus list -n localhost
        pyleus kill -n localhost word_count

##Streamparse フレームワーク

[Streamparse](https://github.com/Parsely/streamparse) は、以下を指定することで、Storm で Python を簡単に使用できるようにするためのフレームワークです。

* __スキャフォールディング__: これを使用すると、簡単にプロジェクトのスキャフォールディングを作成し、ロジックを追加するためにファイルを変更できるようになります。
* __Clojure DSL 関数__: Clojure トポロジ定義での Python コンポーネントの使用詳細度を下げます。
* __依存関係の管理__: Virtualenv を使用することで、Python の依存関係がすべての worker ノードに確実にデプロイされるようになります。これには、Virtualenv を worker ノードにインストールする必要があります。
* __リモート デプロイメント__: Streamparse は SSH オートメーションを使用して worker ノードにコンポーネントをデプロイすることができ、Nimbus と通信するために SSH トンネルを作成できます。したがって、開発環境から HDInsight などの Linux ベース クラスターに簡単にデプロイできます。

> [AZURE.IMPORTANT] Streamparse は、Windows では使用できない [Unix 信号](https://en.wikipedia.org/wiki/Unix_signal)を予期するコンポーネントに依存します。開発環境は Linux、Unix、または OS X である必要があり、HDInsight クラスターは Linux ベースである必要があります。

1. HDInsight クラスター上に新しい Storm をプロビジョニングする場合、Python Virtualenv がクラスター ノード上に存在することを確認する必要があります。新しい Linux ベースの HDInsight クラスターを作成する場合は、[クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster.md)で次のスクリプト アクション設定を使用します。

    * __名前__: ここには単にわかりやすい名前を指定します。
    * \_\_ スクリプト URI\_\_: 値として `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` を使用します。このスクリプトは、ノードに Python Virtualenv をインストールし、Streamparse によって使用されるディレクトリを作成します。
    * __Nimbus__: この項目をオンにすると、スクリプトが Nimbus (ヘッド) ノードに適用されるようになります。
    * __Supervisor__: この項目をオンにすると、スクリプトが Supervisor (worker) ノードに適用されるようになります。
    
    その他の項目は空白のままにします。
    
    > [AZURE.WARNING] Streamparse を使用してリモートでデプロイするには、__公開キー__を使用して、HDInsight クラスターの SSH ユーザーをセキュリティで保護する必要もあります。
    >
    > HDInsight での SSH キーの使用について詳しくは、次のいずれかのドキュメントを参照してください。
    >
    > * [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

2. クラスターのプロビジョニング時に、次のコマンドを使用して、開発環境に Streamparse をインストールします。

        pip install streamparse
        
3. Streamparse がインストールされたら、次のコマンドを使用して例のプロジェクトを作成します。

        sparse quickstart wordcount
        
    これで `wordcount` という名前の新しいディレクトリが作成され、例の Word Count プロジェクトが設定されます。

4. 以下のコマンドを使用して、ディレクトリを `wordcount` ディレクトリに変更し、ローカル モードでトポロジを開始します。

        cd wordcount
        sparse run

    トポロジを停止するには、Ctrl キーを押しながら C キーを押します。

###トポロジのデプロイ

Linux ベースの HDInsight クラスターが作成されたら、次の手順を使用して、トポロジをクラスターにデプロイします。

1. 以下のコマンドを使用して、クラスターの worker ノードの完全修飾ドメイン名を見つけます。

        curl -u admin:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts | grep '"host_name" : "worker'
    
    これで、クラスターのホスト情報が取得され、grep にパイプされてから、worker ノードの項目に返されます。次のような結果が表示されます。
    
        "host_name" : "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    
    `"workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"` 情報は、次の手順で使用するため保存します。

2. `wordcount` ディレクトリの __config.json__ ファイルを開き、次の項目を変更します。

    * __user__: これを、HDInsight クラスター用に構成した SSH ユーザー アカウント名に設定します。これは、プロジェクトのデプロイ時に、クラスターに対する認証に使用されます。
    * __nimbus__: これを `CLUSTERNAME-ssh.azurehdinsight.net` に設定します。CLUSTERNAME はクラスターの名前に置き換えます。これは、クラスターのヘッド ノードである Nimbus ノードとの通信時に使用されます。
    * __workers__: worker 項目に、curl を使用して取得した worker ノードのホスト名を設定します。次に例を示します。
    
        ```
"workers": [
    "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net",
    "workernode1.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    ]
        ```
    
    * __virtualenv\_root__: これを "/virtualenv" に設定します。
    
    これで、スクリプト アクションでのプロビジョニング時に作成された `/virtualenv` ディレクトリを含む、HDInsight クラスターのプロジェクトが構成されます。

4. Streamparse の HDInsight へのデプロイにはヘッド ノードを介して worker に認証を転送する必要があるため、`ssh-agent` をワークステーションで開始する必要があります。ほとんどのオペレーティング システムでは、自動的に開始されます。実行されていることを確認するには、次のコマンドを使用します。

        echo "$SSH_AUTH_SOCK"
    
    これにより、`ssh-agent` が実行されている場合は次のような応答が返されます。
    
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
    
    > [AZURE.NOTE] 完全なパスは、使用するオペレーティング システムに応じて異なる場合があります。たとえば、OS X では、パスは `/private/tmp/com.apple.launchd.vq2rfuxaso/Listeners` のようになる場合があります。ただし、エージェントが実行されている場合はいくつかのパスが返されます。
    
    何も返されない場合は、`ssh-agent` コマンドを使用して、エージェントを開始します。
    
5. HDInsight サーバーの認証に使用するキーをエージェントが認識していることを確認します。エージェントで使用できるキーをリストするには、次のコマンドを使用します。

        ssh-add -L
    
    これで、エージェントに追加されている秘密キーが返されます。HDInsight を認証するための SSH キーの作成時に生成した秘密キーの内容と、結果を比較することができます。
    
    情報が返されない場合、または返される情報が秘密キーと一致しない場合は、以下を使用してエージェントに秘密キーを追加します。
    
        ssh-add /path/to/key/file
    
    たとえば、「`ssh-add ~/.ssh/id_rsa`」のように入力します。

4. HDInsight クラスターで転送を使用する必要があることを認識するように SSH を構成する必要もあります。`~/.ssh/config` に以下を追加します。このファイルが存在しない場合は、作成し、内容として以下を使用します。

        Host *.azurehdinsight.net
          ForwardAgent yes
        
        Host *.internal.cloudapp.net
          ProxyCommand ssh CLUSTERNAME-ssh.azurehdinsight.net nc %h %p
    
    CLUSTERNAME を、使用する HDInsight クラスターの名前に置き換えます。
    
    これで、接続先の任意の *.azurehdinsight.net システムを介して SSH 資格情報の転送を有効にするように、ワークステーション上の SSH エージェントが構成されます。この場合は、クラスターのヘッド ノードです。次に、ヘッド ノードから個々の worker ノード (internal.cloudapp.net) へのプロキシ経由の SSH トラフィックに使用するコマンドを構成します。 これにより、Streamparse をヘッド ノードに接続し、SSH アカウントのキー認証を使用して、そこから各 worker ノードに接続できるようになります。
    
5. 最後に、以下のコマンドを使用して、ローカル開発環境から HDInsight クラスターにトポロジを送信します。

        sparse submit
    
    これで、HDInsight クラスターに接続され、トポロジと Python 依存関係がデプロイされてから、トポロジが開始されます。

##次のステップ

このドキュメントでは、Storm トポロジから Python コンポーネントを使用する方法を学習しました。HDInsight で Phthon を使用する他の方法については、次のドキュメントを参照してください。

* [HDInsight 用 Python ストリーミング プログラムの開発](hdinsight-hadoop-streaming-python.md)
* [HDInsight における Python と Hive および Pig の使用](hdinsight-python.md)

<!---HONumber=AcomDC_0420_2016-->