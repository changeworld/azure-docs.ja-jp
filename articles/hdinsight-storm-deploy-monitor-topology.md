<properties 
   pageTitle="HDInsight | Azure での Storm トポロジの展開と管理" 
   description="HDInsight での Apache Storm に含まれる Storm ダッシュボードを使用して Storm トポロジを展開、監視、管理する方法について説明します。" 
   services="hdinsight" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#HDInsigh での Apache Storm の展開と管理

Storm ダッシュボードでは、Web ブラウザーを使用して Apache Storm トポロジを HDInsight クラスターに簡単に展開して実行できます。また、ダッシュボードを使用して実行中のトポロジを監視、管理できます。Visual Studio を使用する場合、HDInsight Tools for Visual Studio でも Visual Studio と同様の機能が提供されます。

Storm ダッシュボードと HDInsight Tools の Storm 機能は、共に Storm REST API に依存し、これを使用して独自の監視と管理ソリューションを作成できます。

##前提条件

* **HDInsight での Apache Storm** -  <a href="../hdinsight-storm-getting-started/" target="_blank">「HDInsight (Hadoop) での Storm の使用」</a> でクラスターの作成手順をご確認ください。

* **Storm ダッシュボード**用 - HTML5 をサポートする最新の Web ブラウザー

* **Visual Studio** 用 - Azure SDK 2.5.1 以降と HDInsight Tools for Visual Studio。HDInsight tools for Visual Studio のインストールと構成方法については、 <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">「Get started using HDInsight Tools for Visual Studio (HDInsight Tools for Visual Studio を使用する)」</a> をご覧ください。

	下記いずれかのバージョンの Visual Studio

	* Visual Studio 2012 <a href="http://www.microsoft.com/ja-jp/download/details.aspx?id=39305" target="_blank">(アップデート 4)</a>

	* Visual Studio 2013 <a href="http://www.microsoft.com/ja-jp/download/details.aspx?id=44921" target="_blank">(アップデート 4)</a> または <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio 2013 Community</a>

	* <a href="http://visualstudio.com/ja-jp/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

	> [AZURE.NOTE] 現在、HDInsight Tools for Visual Studio では HDInsight cluster バージョン 3.2 の Storm のみサポートしています。

##Storm ダッシュボード

Storm ダッシュボードは、Storm クラスターで使用できます。URL は、**https://&lt;clustername>.azurehdinsight.net/** (**clustername** はお使いの HDInsight クラスターの Storm の名前) です。また、Azure ポータルのクラスター ダッシュボードにある **[Storm ダッシュボード]** リンクからダッシュボードにアクセスできます。

![the portal with storm dashboard highlighted][hdinsight-dashboard]

Storm ダッシュボードの上部で、**[Submit Topology (トポロジの送信)]** を選択します。ページの指示に従ってサンプル トポロジを実行するか、作成したトポロジをアップロードして実行します。

![the submit topology page][storm-dashboard-submit]

###Storm UI

Storm ダッシュボードで、**[Storm UI]** リンクを選択します。クラスターの情報と、実行中のトポロジに関する情報が表示されます。

![the storm ui][storm-dashboard-ui] 

####メイン ページ

Storm UI のメイン ページには、次の情報が表示されます。

* **クラスターの概要** - Storm クラスターの基本情報

* **トポロジの概要** - 実行中のトポロジの一覧このセクションのリンクを使用して、各トポロジの詳細情報を表示します。

* **スーパーバイザの概要** - Storm のスーパーバイザに関する情報

* **Nimbus 構成** - クラスターの Nimbus 構成

####トポロジの概要

**[トポロジの概要]** セクションのリンクを選択すると、トポロジに関する次の情報が表示されます。

* **トポロジの概要** -トポロジの基本情報

* **トポロジのアクション** - トポロジで実行できる管理アクション

	* **アクティブ化** - アクティブ化が解除されたトポロジの処理を再開します
	
	* **アクティブ化の解除** - 実行中のトポロジを一時停止します
	
	* **再調整** - トポロジの並列処理を調整します。クラスターのノード数を変更した場合は、実行中のトポロジを再調整する必要があります。この操作で、クラスター内のノード数の増減に合わせて、トポロジの並列処理を調整できます。
	
		詳細については、 <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">「Understanding the parllelism of a Storm topology (Storm トポロジの並列処理)」をご覧ください。</a>
	
	* **強制終了** - 指定したタイムアウト後に Storm トポロジを停止します。

* **トポロジの統計** - トポロジに関する統計。**Window** 列にあるリンクを使用して、ページで残りのエントリの時間枠を設定します。

* **スパウト** - トポロジで使用するスパウト。このセクションにあるリンクを使用して、各スパウトの詳細情報を表示します。

* **ボルト** - トポロジで使用するボルト。このセクションにあるリンクを使用して、各ボルトの詳細情報を表示します。

* **トポロジの構成** - 選択したトポロジの構成

####スパウトとボルトの概要

**[スパウト]** または **[ボルト]** でアイテムを選択すると、そのアイテムに関する次の情報が表示されます。

* **コンポーネントの概要** - スパウトやボルトの基本情報

* **スパウト/ボルトの統計** - スパウトやボルトの統計。**[Window]** 列にあるリンクを使用して、ページで残りのエントリの時間枠を設定します。

* **入力の状態** (ボルトのみ) - ボルトが消費する入力ストリームに関する情報

* **出力の状態** - このスパウトやボルトから出力されるストリームに関する情報

* **エグゼキュータ** - スパウトやボルトのインスタンスに関する情報特定のエグゼキュータの **[ポート]** エントリを選択して、このインスタンスで生成された診断情報のログを閲覧します

* **エラー** - このスパウトやボルトのエラー情報。

##HDInsight Tools for Visual Studio

HDInsight Tools は、C# またはハイブリッド トポロジを Storm クラスターに送信する際に使用できます。次の例では、サンプル アプリケーションを使用します。HDInsight Tools を使用して独自のトポロジを作成する方法の詳細については、「[develop C# topologies using the HDInsight Tools for Visual Studio (HDInsight Tools for Visual Studio を使用して C# トポロジを開発する)](../hdinsight-storm-develop-csharp-visual-studio-topology/)」をご覧ください。

次の手順を使用して、サンプルをお使いの HDInsight クラスターに展開し、トポロジを表示、管理します。

1. 最新バージョンの HDInsight Tools for Visual Studio をまだインストールしていない場合は、 <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">「Get started using HDInsight Tools for Visual Studio (HDInsight Tools for Visual Studio を使用する)」</a>をご覧ください。

2. Visual Studio を開いて、**[ファイル]**、**[新規]**、**[プロジェクト]** の順に選択します。

3. **[新しいプロジェクト]** ダイアログで、**[インストール済]**、**[テンプレート]** の順に展開して **[HDInsight]** を選択します。テンプレートの一覧から **[Storm Sample]** を選択します。ダイアログの下部に、アプリケーションの名前を入力します。

	![image](./media/hdinsight-storm-deploy-monitor/sample.png)

1. **[ソリューション エクスプローラー]** で、プロジェクトを右クリックして **[Submit to Storm on HDInsight (HDInsight の Storm に送信)]** を選択します。

	> [AZURE.NOTE] メッセージが表示されたら、Azure サブスクリプションのログイン資格情報を入力します。２ つ以上のサブスクリプションをお持ちの場合は、HDInsight クラスター の Storm があるサブスクリプションにログインします。

2. **[Storm クラスター]** ドロップダウンから HDInsight クラスターの Storm を選択して、**[送信]** を選択します。送信が成功したかどうかは、**[出力]** ウィンドウを使用して確認できます。

3. トポロジが正常に送信されたら、クラスターの **[Storm トポロジ]** が表示されます。実行中のトポロジに関する情報を表示するには、一覧からトポロジを選択します。

	![visual studio monitor](./media/hdinsight-storm-deploy-monitor/vsmonitor.png)

	> [AZURE.NOTE] また、**Storm トポロジ**は、**[サーバー エクスプローラー]** で **[Azure]**、**[HDInsight]** の順に展開して、HDInsight クラスターの Storm を右クリックして **[View Storm Topologies (Storm トポロジの表示)]** を選択して表示できます。

	スパウトやボルトのリンクを使用してこれらのコンポーネントに関する情報を表示します。各アイテムを選択すると新しいウィンドウが開きます。

4. **[トポロジの概要]** ビューで、**[強制終了]** を選択してトポロジを停止します。

	> [AZURE.NOTE] Storm トポロジは強制終了されるまで、またはクラスターが削除されるまで実行し続けます。

##REST API

Storm UI は、REST API を基に構築されているため、API を使用して同様の管理や監視機能を実行できます。REST API を使用して、Storm トポロジの管理や監視用のカスタム ツールを作成できます。

Storm REST API の詳細については、 <a href="https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md" target="_base">https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md</a>をご覧ください。以下は、HDInsight での Apache Storm で REST API を使用する場合の情報です。

###ベース URI

HDInsight クラスターの REST API のベース URI は、**https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/** (**clustername** は HDInsight クラスターの Storm の名前) です。

###認証

REST API への要求では、HDInsight クラスターの管理者名とパスワードを使用して、**基本認証**を使用する必要があります。 

> [AZURE.NOTE] 基本認証はクリア テキストを使用して送信されるため、**常に** HTTPS を使用してクラスターとの通信を保護する必要があります。

###戻り値

REST API から返される情報は、クラスターと同じ Azure 仮想ネットワーク上にあるクラスターやマシン上でのみ使用できます。たとえば、Zookeeper サービスに返された完全修飾ドメイン名 (FQDN) は、インターネットからはアクセスできません。

##次のステップ

ここまでで、Storm ダッシュボードを使用してトポロジを展開、監視する方法について説明しました。続けて、「[develop C# topologies using the HDInsight Tools for Visual Studio (HDInsight Tools for Visual Studio を使用して C# トポロジを開発する)](../hdinsight-storm-develop-csharp-visual-studio-topology/)」や「[Develop Java topologies for Storm on HDInsight using Maven (Maven を使用して HDInsight で Storm の Java トポロジを開発する)](../hdinsight-storm-develop-java-topology/)」もご覧ください。


[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor/storm-ui-summary.png

<!--HONumber=45--> 
