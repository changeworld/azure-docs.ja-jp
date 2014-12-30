<properties title="Getting started using Storm with Hadoop in HDInsight" pageTitle="Microsoft Azure HDInsight (Hadoop) での Apache Storm の使用" description="HDInsight (Hadoop) で Apache Storm を使用して、リアルタイムでデータを処理する方法について説明します" metaKeywords="Azure hdinsight storm, Azure hdinsight realtime, azure hadoop storm, azure hadoop realtime, azure hadoop real-time, azure hdinsight real-time" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="larryfr" />

#HDInsight (Hadoop) での Storm の使用

Apache Storm は、データ ストリームの処理を目的とした、拡張性の高い、フォールト トレランスに優れた、分散型のリアルタイム計算システムです。Azure HDInsight を使用して、Storm でリアルタイム データ分析を実行するクラウドベースの Hadoop クラスターを作成できます。

##このチュートリアルでは、次の内容を説明します。

* [HDInsight Storm クラスターのプロビジョニング方法](#provision)

* [クラスターへの接続方法](#connect)

* [Storm トポロジの実行方法](#run)

* [Storm トポロジの監視方法](#monitor)

* [Storm トポロジの停止方法](#stop)

* [次のステップ](#next)

##開始する前に

このチュートリアルを正常に完了するには、次の条件を満たす必要があります。

* Azure サブスクリプション

* Windows Azure PowerShell

* Apache Storm を使い慣れていない場合は、最初に「[HDInsight Storm の概要](/ja-jp/documentation/articles/hdinsight-storm-overview) 」を参照してください。

##<a id="provision"></a>Azure ポータルでの Storm クラスターのプロビジョニング

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. [Azure 管理ポータル][azureportal]にサインインします

2. 左側にある **[HDInsight]** をクリックし、ページの左下隅にある **[+ 新規]** をクリックします。

3. 2 番目の列にある [HDInsight] アイコンをクリックし、**[カスタム]** を選択します。

4. **[クラスターの詳細]** ページで、新しいクラスターの名前を入力し、**[クラスターの種類]** に **[Storm]** を選択します。矢印を選択して続行します。

	![cluster details](./media/hdinsight-storm-getting-started/wizard1.png)

5. このクラスターに使用する**データ ノード**の数と、クラスターで作成される**リージョン/仮想ネットワーク**を入力します。矢印を選択して続行します。

	> [WACOM.NOTE] この記事で使用するクラスターのコストを最小限に抑えるには、**クラスター サイズ**を 1 に削減し、使用終了後にクラスターを削除します。

	![data nodes and region](./media/hdinsight-storm-getting-started/wizard2.png)

6. 管理者の**ユーザー名**と**パスワード**を入力し、矢印を選択して続行します。

	![account and password](./media/hdinsight-storm-getting-started/wizard3.png)

4. **ストレージ アカウント**の場合、**[新しいストレージを作成する]** を選択するか、既存のストレージ アカウントを選択します。使用する**アカウント名**と**既定のコンテナー**を選択するか、入力します。左下にあるチェック アイコンをクリックして、Storm クラスターを作成します。

	![storage account](./media/hdinsight-storm-getting-started/wizard4.png)

##HDInsight Storm の使用

HDInsight Storm のプレビュー リリースでは、Storm を操作するために、リモート デスクトップを使用してクラスターに接続する必要があります。次の手順に従って、HDInsight クラスターに接続し、Storm コマンドを使用します。

###<a id="connect"></a>クラスターへの接続

1. [Azure の管理ポータル][management]を使用して、HDInsight クラスターにリモート デスクトップ接続できます。このポータルで、HDInsight クラスターを選択し、__[構成]__ ページの下部にある __[リモートを有効にする]__ を選択します。

    ![enable remote](./media/hdinsight-storm-getting-started/enableremotedesktop.png)

    メッセージが表示されたら、リモート セッションに使用するユーザー名とパスワードを入力します。リモート アクセスに有効期限を選択する必要もあります。

	![remote desktop config](./media/hdinsight-storm-getting-started/configremotedesktop.png)

2. リモート アクセスが有効になったら、__[接続]__ を選択して接続を開始します。これにより、リモート デスクトップ セッションの開始に使用できる __.rdp__ ファイルがダウンロードされます。

    ![connect](./media/hdinsight-storm-getting-started/connect.png)

	> [WACOM.NOTE] リモート マシンへの接続中に、証明書を信頼するために、メッセージがいくつか表示される場合があります。

3. 接続したら、デスクトップの __[Hadoop コマンド ライン]__ アイコンを使用して Hadoop コマンド ラインを開きます。

	![hadoop cli](./media/hdinsight-storm-getting-started/hadoopcommandline.png)

3. ディレクトリを Storm コマンドが格納されているディレクトリに変更するために、Hadoop コマンド ラインから次のコマンドを使用します。

	cd %storm_home%\bin

4. 使用できるコマンドの一覧を表示するには、`storm` をパラメーターなしで入力します。

HDInsight クラスターには Storm トポロジの例がいくつか付属します。サンプルの **WordcountTopology** は次の手順で使用します。HDInsight Storm に付属する例の詳細については、「[次のステップ](#next)」を参照してください。

###<a id="run"></a>Storm トポロジを実行するには

**WordCountTopology** を実行するには、次のコマンドレットを使用します。

	storm jar ..\contrib\storm-starter\storm-starter-<version>-jar-with-dependencies.jar storm.starter.WordCountTopology wordcount

これは、**wordcount** トポロジを **storm.starter.WordCountTopology** クラスから実行することを Storm に指示します。このクラスは、**storm-starter-&lt;version>-jar-with-dependencies.jar** ファイルに含まれます。このファイルは、その他の Storm の例と共に、%storm_home% ディレクトリの contrib フォルダーにあります。

> [WACOM.NOTE] 例を含む JAR ファイルのバージョンは、定期的に変わる可能性があります。このコマンドを実行するときに、クラスターに付随するファイルのバージョンを指定します。

コマンドを入力しても、何も返されません。**Storm トポロジがいったん開始されると、停止されるまで実行されます。**WordCountTopology はランダムな文を生成し、停止されるまで各単語の出現回数を計算します。

###<a id="monitor"></a>Storm トポロジの状態を監視するには

WordCountTopology サンプルはディレクトリに出力を書き込みませんが、Storm UI Web ページを使用して、出力だけでなく、トポロジの状態を表示することができます。

1. リモート デスクトップを使用して HDInsight クラスターに接続します。

2. デスクトップから **Storm UI** リンクを開きます。Storm UI Web ページが表示されます。**[Topology summary]** の下にある **[wordcount]** エントリを選択します。

	![storm ui](./media/hdinsight-storm-getting-started/stormui.png)

	**スパウト**や**ボルト**など、トポロジを構成するコンポーネントを含む、トポロジの統計が表示されます。

5. ページにある **[spout]** リンクを選択し、**[Emitted]** と **[Transferred]** 列が 0 より大きな数字を持つ **[Executors (All time)]** リストのエントリから **Port** 番号を 1 つ選択します。

	![spouts and bolts](./media/hdinsight-storm-getting-started/stormuiboltsnspouts.png)

	![selecting executors](./media/hdinsight-storm-getting-started/executors.png)

	> [WACOM.NOTE] クラスターのノードの数と実行しているトポロジによっては、トポロジの処理が始まるまで数分かかる場合があります。**[Emitted]** と **[Transferred]** の値が増加し始めるまで、定期的にページを最新の情報に更新します。

6. 次のような情報が記載されたページが表示されます。

		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [snow white and the seven dwarfs]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16774]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16775]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8359]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8360]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [i am at two with nature]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:23, stream: default, id: {}, [two]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [two, 8236]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:22, stream: default, id: {}, [a]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [a, 8280]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:19, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16776]

	このスニペットから、スパウトが "snow white and the seven dwarfs" を発したことがわかりますが、これは個別の単語に分割されました。また、処理の開始以降、各単語がトポロジによって処理された回数が引き続き計算されています。たとえば、上の出力を見ると、単語 "dwarfs" はスパウトによって 8360 回発されています。

###<a id="stop"></a>Storm トポロジを停止するには

**WordCountTopology** は停止されるまで実行されます。停止するには、次のコマンドを実行します。

	storm kill wordcount

このコマンドの直後に Storm UI Web ページを表示すると、**[Topology summary]** の **[wordcount]** の状態が "KILLED" に変更されていることがわかります。数秒後、トポロジは **[Topology summary]** セクションに表示されなくなります。

##<a id="next"></a>次のステップ

* **サンプル ファイル** - HDInsight Storm クラスターでは、**%storm_home%\contrib** ディレクトリにいくつかの例が用意されています。次の例がそれぞれ含まれます。

	* ソース コード - storm-starter-0.9.1.2.1.5.0-2057-sources.jar など

	* Java ドキュメント - storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar など

	* 例 - storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar など

	'jar' コマンドを使用して、ソース コードまたは Java ドキュメントを抽出します。たとえば、'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar' のように指定します。

	> [WACOM.NOTE] Java ドキュメントは Web ページで構成されます。抽出後、ブラウザーを使用して **index.html** ファイルを表示します。

* [Storm と HDInsight を使用したセンサー データの分析](/ja-jp/documentation/articles/hdinsight-storm-sensor-data-analysis)

* [HDInsight の Storm で SCP.NET と C# を使用したストリーミング データ処理アプリケーションの開発](/ja-jp/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/

<!--HONumber=35_1-->
