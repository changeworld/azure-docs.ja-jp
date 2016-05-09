<properties
   pageTitle="Apache Ambari Web UI を使用して HDInsight クラスターを監視および管理する | Microsoft Azure"
   description="Ambari を使用して Linux ベースの HDInsight クラスターを監視および管理する方法を説明します。このドキュメントでは、HDInsight クラスターに含まれている Ambari Web UI を使用する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/22/2016"
   ms.author="larryfr"/>

#Ambari Web UI を使用した HDInsight クラスターの管理

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari には使いやすい Web UI と REST API が用意されているため、Hadoop クラスターを簡単に管理および監視できます。Linux ベースの HDInsight クラスターに含まれている Ambari は、クラスターの監視と構成の変更を行うために使用します。

このドキュメントでは、HDInsight クラスターに含まれている Ambari Web UI を使用する方法について説明します。

> [AZURE.NOTE] この記事の情報は、Linux ベースの HDInsight クラスターにのみ適用されます。Windows ベースの HDInsight クラスターでは、Ambari REST API を使用した監視のみを利用できます。「[Ambari API を使用した HDInsight の Windows ベースの Hadoop の監視](hdinsight-monitor-use-ambari-api.md)」をご覧ください。

##<a id="whatis"></a>Ambari とは

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> は、Hadoop クラスターのプロビジョニング、管理、監視に使用する Web UI を簡単に使用できる方法を提供することで Hadoop の管理を簡略化します。開発者は、<a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">Ambari REST API</a> を使用して、これらの機能をアプリケーションに統合することができます。

Ambari Web UI は既定で Linux ベースの HDInsight クラスターに付属しています。

##接続

Ambari Web UI はお使いの HDInsight クラスター( HTTPS://CLUSTERNAME.azurehdidnsight.net) にあります。__CLUSTERNAME__ はお使いのクラスターの名前になります。

> [AZURE.IMPORTANT] HDInsight の Ambari に接続するには、HTTPS が必要です。クラスターの作成時に指定した管理者アカウント名 (既定値は __admin__) とパスワードを使用して、Ambari を認証する必要もあります。

##SSH プロキシ

> [AZURE.NOTE] クラスター用の Ambari にはインターネットから直接アクセスできますが、Ambari Web UI の一部のリンク (JobTracker など) はインターネット上で公開されていません。そのため、これらの機能にアクセスしようとすると、Secure Shell (SSH) トンネルを使用してプロキシ Web トラフィックをクラスター ヘッド ノードに送信しない限り、サーバーが見つからないことを示すエラー メッセージが表示されます。

Ambari と連携する SSH トンネルの作成の詳細については、「[SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)」を参照してください。

##Ambari Web UI

Ambari Web UI に接続すると、そのページに対する認証が求められます。クラスターの作成時に利用したクラスター管理者ユーザー (既定では Admin) とパスワードを使用します。

ページが開くと、上部にバーがあります。ここには、次の情報とコントロールが含まれています。

![ambari ナビゲーション](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari ロゴ** - ダッシュボードを表示します。これを使用してクラスターを監視できます。

* **[クラスター名 # ops]** - 進行中の Ambari 操作の数を表示します。クラスター名または **[# ops]** を選択すると、バックグラウンドでの操作の一覧が表示されます。

* **[# alerts]** - クラスターの警告または重要なアラートの数 (ある場合)。選択すると、アラートの一覧が表示されます。

* **[Dashboard]** - ダッシュボードが表示されます。

* **[Services]** - クラスターのサービスの情報と構成設定。

* **[Hosts]** - クラスター内のノードの情報と構成設定。

* **[Alerts]** - 情報、警告、重要なアラートのログ。

* **[Admin]** - クラスターにインストールされたソフトウェア スタック/サービス、サービス アカウント情報、Kerberos セキュリティ。

* **[admin] ボタン** - Ambari の管理、ユーザー設定、ログアウトを行います。

##監視

###アラート

Ambari には多数のアラートがあり、そのステータスは次のいずれかになります。

* **OK**

* **Warning**

* **CRITICAL**

* **UNKNOWN**

**[OK]** 以外のアラートでは、アラート数を表示する **[# alerts]** エントリがページ上部に表示されます。このエントリを選択すると、アラートとそのステータスが表示されます。

アラートはいくつかの既定のグループにまとめられます。このグループは、**[Alerts]** ページで表示できます。

![alerts ページ](./media/hdinsight-hadoop-manage-ambari/alerts.png)

グループを管理するには、**[Actions]** メニューを使用して、**[Manage Alert Groups]** を選択します。ここでは、既存のグループの変更や、グループの新規作成を行えます。

![manage alert groups ダイアログ](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

**[Actions]** メニューからアラート通知を作成することもできます。ここでは、特定のアラート/重要度の組み合わせが発生したときに、**電子メール**または **SNMP** により通知を送信するトリガーを作成できます。たとえば、**[YARN Default]** グループのいずれかのアラートが **[Critical]** に設定されたときにアラートを送信できます。

![Create alert ダイアログ](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

###プロビジョニング

ダッシュボードの **[Metrics]** タブには、クラスターのステータスを一目で簡単に確認できる一連のウィジェットが用意されています。**[CPU Usage]** などのいくつかのウィジェットをクリックすると、追加の情報が表示されます。

![metrics のダッシュボード](./media/hdinsight-hadoop-manage-ambari/metrics.png)

**[Heatmaps]** タブには、緑から赤までの色分けによるメトリックが表示されます。

![heatmaps のダッシュボード](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

クラスター内のノードの詳細については、**[Hosts]** を選択し、目的のノードを選択します。

![ホストの詳細](./media/hdinsight-hadoop-manage-ambari/host-details.png)

###サービス

ダッシュボードの **[Services]** サイド バーでは、クラスターで実行中のサービスのステータスを視覚的に簡単に確認できます。さまざまなアイコンで、ステータスまたは実行する必要のある操作が示されます。サービスをリサイクルする必要があることを示す黄色のリサイクル記号などがあります。

![サービスのサイド バー](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

サービスを選択すると、サービスの詳細が表示されます。

![サービスの概要情報](./media/hdinsight-hadoop-manage-ambari/service-details.png)

####クイック リンク:

一部のサービスでは、ページの上部に **[Quick Links]** が表示されます。これを使用すると、サービスに固有の次のような Web UI にアクセスできます。

* **Job History** - MapReduce ジョブ履歴

* **Resource Manager** - YARN リソース マネージャー UI

* **NameNode** - Hadoop 分散ファイル システム (HDFS) NameNode UI

* **Oozie Web UI** - Oozie UI

これらのいずれかのリンクを選択すると、ブラウザーに新しいタブが開き、選択したページが表示されます。

> [AZURE.NOTE] いずれかのサービスの **[Quick Links]** リンクを選択すると、クラスターへのプロキシ Web トラフィックに Secure Sockets Layer (SSL) トンネルを使用していない限り、サーバーが見つからないことを示すエラー メッセージが表示されます。これは、この情報を表示するために使用される Web アプリケーションがインターネット上で公開されないためです。
>
> HDInsight での SSL トンネルの使用の詳細については、「[SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)」を参照してください。

##管理

###Ambari ユーザー、グループ、およびアクセス許可

ユーザー、グループ、アクセス許可の管理は、HDInsight クラスターでは使用しないことをお勧めします。

###ホスト

**[Hosts]** ページには、クラスター内のすべてのホストが一覧表示されます。ホストを管理するには、次の手順に従います。

![hosts ページ](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE] ホストの追加、使用停止、再任命は HDInsight クラスターでは使用しないことをお勧めします。

1. 管理するホストを選択します。

2. **[Actions]** メニューを使用して、実行する操作を選択します。

	* **[Start all components]** - ホスト上のすべてのコンポーネントを開始します。

	* **[Stop all components]** - ホスト上のすべてのコンポーネントを停止します。

	* **[Restart all components]** - ホスト上のすべてのコンポーネントを停止してから起動します。

	* **[Turn on maintenance mode]** - ホストのアラートを抑制します。これは、アラートを生成する操作を実行する場合に有効になります (実行中のサービスが依存しているサービスを再起動する場合など)。

	* **[Turn off maintenance mode]** - ホストを通常の警告に戻します。

	* **[Stop]** - ホスト上の DataNode または NodeManagers を停止します。

	* **[Start]** - ホスト上の DataNode または NodeManagers を起動します。

	* **[Restart]** - ホスト上の DataNode または NodeManagers を停止して起動します。

	* **[Decommission]** - クラスターからホストを削除します。

		> [AZURE.NOTE] HDInsight クラスターではこの操作は使用しないでください。

	* **[Recommission]** - 使用停止にしたホストをクラスターに追加します。

		> [AZURE.NOTE] HDInsight クラスターではこの操作は使用しないでください。

###<a id="service"></a>サービス

**[Dashboard]** または **[Services]** ページでサービスの一覧の下部にある **[Actions]** ボタンを使用して、すべてのサービスを停止し、開始します。

![service actions](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [AZURE.WARNING] このメニューには __[サービスの追加]__ が表示されますが、これを使用してサービスを HDInsight クラスターに追加しないでください。新しいサービスは、クラスターのプロビジョニング中にスクリプト アクションを使用して追加する必要があります。スクリプト アクションの使用の詳細については、「[Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。


**[Actions]** ボタンではすべてのサービスを再起動できますが、特定のサービスを開始、停止、または再起動する必要がある場合があります。個々のサービスで操作を実行するには、次の手順に従います。

1. **[Dashboard]** または **[Services]** ページでサービスを選択します。

2. **[Summary]** タブの上部で **[Service Actions]** ボタンを使用して実行する操作を選択します。これにより、すべてのノードでサービスが再起動されます。

	![service action](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE] クラスターの実行中にサービスを再起動すると、アラートが生成される場合があります。これを回避するには、**[Service Actions]** ボタンを使用して、再起動を実行する前に、サービスの **[Maintenance mode]** を有効にします。

3. 操作を選択したら、ページ上部の **[# op]** エントリが増分され、バックグラウンド操作が実行されていることが示されます。バックグラウンド操作を表示するように設定されている場合は、バックグラウンド操作の一覧が表示されます。

	> [AZURE.NOTE] サービスの **[Maintenance mode]** を有効にした場合は、操作が完了したら、**[Service Actions]** ボタンを使用してこれを忘れずに無効にしてください。

サービスを構成するには、次の手順を実行します。

1. **[Dashboard]** または **[Services]** ページでサービスを選択します。

2. **[Configs]** タブをクリックします。現在の構成が表示されます。以前の構成の一覧も表示されます。

	![構成](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. 表示されたフィールドを使用して構成を変更し、**[Save]** を選択します。または、以前の構成を選択し、**[Make current]** を選択して以前の設定にロールバックします。

##Ambari ビュー

Ambari ビューを使うと、開発者は [Ambari ビュー フレームワーク](https://cwiki.apache.org/confluence/display/AMBARI/Views)を使用して Ambari Web UI に UI 要素をプラグインできます。HDInsight には、Hadoop クラスター タイプの異なる次のビューが用意されています。

* Yarn Queue Manager: このキュー マネージャーでは、シンプルな UI により YARN キューを表示、変更できます。
* Hive ビュー: Hive ビューを使用すると、Web ブラウザーから直接 Hive クエリを実行できます。クエリの保存、結果の表示、結果のクラスター ストレージへの保存、または結果のローカル システムへのダウンロードを行えます。Hive ビューの使用法の詳細については、[HDInsight での Hive ビューの使用](hdinsight-hadoop-use-hive-ambari-view.md)に関するページを参照してください。
* Tez ビュー: このビューでは、Tez ジョブの実行方法や、ジョブで使用するリソース関する情報を表示することで、ジョブをより良く理解、最適化できます。

<!---HONumber=AcomDC_0427_2016-->