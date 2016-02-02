<properties
	pageTitle="Apache Storm チュートリアル: HDInsight での Linux ベースの Storm の使用 |Microsoft Azure"
	description="Linux ベースの HDInsight での Apache Storm および Storm Starter サンプルを使用したビッグ データ分析の概要Storm を使用してデータをリアルタイムに処理する方法について説明します。"
	keywords="Apache Storm, Apache Storm チュートリアル, ビッグ データの分析, Storm Starter"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/12/2016"
   ms.author="larryfr"/>


# Apache Storm チュートリアル: Storm Starter サンプルを使用した HDInsight でのビッグ データ分析の概要

Apache Storm は、データ ストリームの処理を目的とし、スケーラビリティとフォールト トレランスに優れた、分散型のリアルタイム計算システムです。Azure HDInsight の Storm を使用して、Storm でリアルタイムで ビッグ データ分析を実行するクラウドベースの Storm クラスターを作成できます。

> [AZURE.NOTE] この記事の手順では、Linux ベースの HDInsight クラスターを作成します。HDInsight クラスターで Windows ベースの Storm を作成する手順については、「[Apache Storm チュートリアル: Storm Starter サンプルを使用した HDInsight でのビッグ データ分析の概要](hdinsight-apache-storm-tutorial-get-started.md)」を参照してください。

## 開始する前に

Apache Storm チュートリアルを正常に完了するには、次の条件を満たす必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- **SSH と SCP を熟知していること**。HDInsight での SSH と SCP の使用方法の詳細については、次の記事をご覧ください。

    - **Linux、Unix、または OS X クライアント**: 「[Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する (プレビュー)](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

	- **Windows クライアント**: 「[HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する (プレビュー)](hdinsight-hadoop-linux-use-ssh-windows.md)」をご覧ください。

## Storm クラスターを作成する

HDInsight の Storm では、ログ ファイルとクラスターに送信されるトポロジを格納する Azure Blob Storage を使用します。次の手順を使用して、クラスターに使用する Azure ストレージ アカウントを作成します。

1. [Azure ポータル][preview-portal]にサインインします。

2. **[新規]**、__[データ分析]__、__[HDInsight]__ の順にクリックします。

	![Creating a new cluster in the Azure Portal](./media/hdinsight-apache-storm-tutorial-get-started-linux/new-cluster.png)

3. __[クラスター名]__ を入力し、__[クラスターの種類]__ として __[Storm]__ を選択します。クラスターを使用できる場合は、__[クラスター名]__ の横に緑色のチェック マークが表示されます。

	![クラスター名、クラスターの種類、および OS の種類](./media/hdinsight-apache-storm-tutorial-get-started-linux/clustername.png)

	__[Ubuntu]__ を選択して Linux ベースの HDInsight クラスターを作成します。
    
    > [AZURE.NOTE] このドキュメントの手順では、__[バージョン]__ フィールドを既定値のままにしておいてください。
	
4. 複数のサブスクリプションがある場合は、__[サブスクリプション]__ エントリを選択し、クラスターで使用する Azure サブスクリプションを選択します。

5. __[リソース グループ]__ では、エントリを選択して既存のリソース グループの一覧を表示し、クラスターを作成するグループを選択できます。または、__[新規作成]__ をクリックし、新しいリソース グループの名前を入力できます。新しいグループ名を使用できる場合は、緑のチェック マークが表示されます。

	> [AZURE.NOTE] このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。

6. __[資格情報]__ を選択し、__[クラスターのログイン ユーザー名]__ に対応する __[クラスターのログイン パスワード]__ を入力します。さらに、SSH ユーザーを認証するために使用される __[SSH ユーザー名]__ と、__[パスワード]__ または __[公開キー]__ のどちらかを入力する必要があります。最後に、__[選択]__ ボタンをクリックして資格情報を設定します。

	![[クラスターの資格情報] ブレード](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

	HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

6. __[データ ソース]__ では、エントリを選択することで既存のデータ ソースを選択するか、新しいデータ ソースを作成できます。

	![[データ ソース] ブレード](./media/hdinsight-apache-storm-tutorial-get-started-linux/datasource.png)
	
	現在、HDInsight クラスターのデータ ソースとして Azure ストレージ アカウントを選択できます。次の説明を参照して、__[データ ソース]__ ブレードのエントリを理解してください。
	
	- __[選択方法]__: サブスクリプションのストレージ アカウントを参照可能にする場合は、__[すべてのサブスクリプションから]__ を設定します。既存のストレージ アカウントの __[ストレージ名]__ と __[アクセス キー]__ を入力する場合は、__[アクセス キー]__ を設定します。
    
    - __[ストレージ アカウントの選択]__: お使いのサブスクリプションのストレージ アカウントが既に存在する場合は、これを使って、クラスターに使用するアカウントを選択します。
	
	- __新規作成__: これを使用して、新しいストレージ アカウントを作成します。表示されたフィールドに、ストレージ アカウントの名前を入力します。名前を使用できる場合は、緑色のチェック マークが表示されます。
	
	- __[既定のコンテナーの選択]__: これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。任意の名前を入力できますが、特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。
	
	- __[場所]__: ストレージ アカウントが存在するリージョン、またはストレージ アカウントの作成先のリージョンです。
	
		> [AZURE.IMPORTANT] 既定のデータ ソースの場所を選択すると、HDInsight クラスターの場所も設定されます。クラスターと既定のデータ ソースは、同じリージョンに存在する必要があります。
    
    - __[クラスター AAD ID]__: これを使用して、クラスターが Azure Data Lake Store へのアクセスに使用する Azure Active Directory ID を選択します。
    
        > [AZURE.NOTE] これはこのドキュメントでは使用しないため、既定の設定のままでかまいません。HDInsight でのこのエントリと Azure Data Lake Store の使用については、「[Create an HDInsight cluster with Data Lake Store using Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。
		
	- __[選択]__: これを使用してデータ ソースの構成を保存します。
	
7. __[ノード価格レベル]__ を選択して、このクラスターに対して作成されるノードに関する情報を表示します。既定では、worker ノードの数は __4__ に設定されます。クラスターの推定コストがブレードの下部に表示されます。

	![[ノード価格レベル] ブレード](./media/hdinsight-apache-storm-tutorial-get-started-linux/nodepricingtiers.png)
	
    ノードの種類をそれぞれ選択し、クラスターでこれらのノードに使用される VM の種類を変更できます。このドキュメントの手順では、これらは既定の設定のままにします。
    
	__[選択]__ ボタンを使用して、__[ノード価格レベル]__ 情報がを保存します。

8. __[オプションの構成]__ を選択します。このブレードでは、クラスターを__Virtual Network__に参加させたり、__[スクリプト操作]__ を使用してクラスターをカスタマイズしたりできるほか、__カスタム メタストア__を使用して Hive と Oozie 用のデータを保持したりできます。

	![[オプションの構成] ブレード](./media/hdinsight-apache-storm-tutorial-get-started-linux/optionalconfiguration.png)
    
    このドキュメントの手順では、これらの設定は __[構成されていません]__ のままにします。

9. __[スタート画面にピン留めする]__ が選択されていることを確認し、__[作成]__ をクリックします。これでクラスターが作成され、Azure ポータルのスタート画面にクラスター用のタイルが追加されます。アイコンはクラスターがプロビジョニング中であることを示し、プロビジョニングが完了すると、[HDInsight] アイコンを表示するように変化します。

	| プロビジョニング中 | プロビジョニング完了 |
	| ------------------ | --------------------- |
	| ![スタート画面のプロビジョニング中インジケーター](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioning.png) | ![プロビジョニングされたクラスターのタイル](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioned.png) |

	> [AZURE.NOTE] クラスターが作成されるまで、通常は約 15 分かかります。プロビジョニング プロセスをチェックするには、スタート画面のタイルまたはページの左側の __[通知]__ エントリを使用します。

##HDInsight での Storm Starter サンプルの実行

[storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) の例は、HDInsight クラスターに含まれています。次の手順では、WordCount の例を実行します。

1. SSH を使用して HDInsight クラスターに接続します。

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	SSH ユーザー アカウントを保護するためにパスワードを使用している場合は、パスワードの入力を求められます。公開キーを使用している場合、`-i` パラメーターを使用して、対応する秘密キーを指定することが必要な場合があります。たとえば、「`ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`」のように入力します。
		
	Linux ベースの HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。
	
	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows)

2. 次のコマンドを実行してトポロジの例を開始します。

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology wordcount
		
	> [AZURE.NOTE] HDinsight が新しいバージョンの Storm で更新されると、ファイル名の `0.9.3.2.2.4.9-1` の部分は変わる可能性があります。

    クラスター上で、’wordcount’ というフレンドリ名の WordCount トポロジの例が開始されます。文はランダムに生成され、文中の各単語の出現回数がカウントされます。

    > [AZURE.NOTE] トポロジをクラスターに送信する場合、まずクラスターを含む jar ファイルをコピーしてから、`storm` コマンドを実行します。この場合、ファイルが保存されているクライアントから `scp` コマンドを実行します。たとえば、`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar` のように指定します。
    >
    > WordCount の例と他の Storm スターターの例は、`/usr/hdp/current/storm-client/contrib/storm-starter/`のクラスターに既に含まれています。

##トポロジの監視

Storm UI には、トポロジの実行を操作する Web インターフェイスがあり、HDInsight クラスターに含まれています。

次の手順により、Storm UI を使用してトポロジを監視します。

1. Web ブラウザーを開き、https://CLUSTERNAME.azurehdinsight.net/stormui に移動します。__CLUSTERNAME__ はクラスターの名前です。Storm UI が表示されます。

	> [AZURE.NOTE] ユーザー名とパスワードの入力が求められたら、クラスターの作成時に使用したクラスター管理者名 (admin) とパスワードを入力します。

2. **[Topology summary]** で、**[名前]** 列の **[wordcount]** エントリを選択します。これにより、トポロジの詳細が表示されます。

	![Storm Starter WordCount トポロジの情報が含まれている Storm ダッシュボード。](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

	このページには、次の情報が表示されます。

	* **トポロジの統計** - 時間枠で整理された、トポロジのパフォーマンスに関する基本的な情報。

		> [AZURE.NOTE] 特定の時間枠を選択すると、ページの他のセクションに表示される情報の時間枠に変更されます。

	* **スパウト** - 各スパウトによって返された最後のエラーを含む、スパウト関する基本的な情報。

	* **ボルト** - ボルトに関する基本的な情報。

	* **トポロジの構成** - トポロジの構成に関する詳細情報。

	このページには、トポロジで実行できるアクションも表示されます。

	* **アクティブ化** - アクティブ化が解除されたトポロジの処理を再開します。

	* **アクティブ化の解除** - 実行中のトポロジを一時停止します

	* **再調整** - トポロジの並列処理を調整します。クラスターのノード数を変更した場合は、実行中のトポロジを再調整する必要があります。この操作で、クラスター内のノード数の増減に合わせて、トポロジの並列処理を調整できます。詳細については、「[Understanding the parallelism of a Storm topology (Storm トポロジの並列処理)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)」を参照してください。

	* **強制終了** - 指定したタイムアウト後に Storm トポロジを停止します。

3. このページで、**[スパウト]** または **[ボルト]** セクションからエントリを選択します。選択したコンポーネントに関する情報が表示されます。

	![選択したコンポーネントに関する情報が含まれている Storm ダッシュボード。](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

	このページには次の情報が表示されます。

	* **スパウト / ボルトの統計** - 時間枠で整理された、コンポーネントのパフォーマンスに関する基本的な情報。

		> [AZURE.NOTE] 特定の時間枠を選択すると、ページの他のセクションに表示される情報の時間枠に変更されます。

	* **入力の統計** (ボルトのみ) - ボルトによって使用されるデータを生成するコンポーネントに関する情報。

	* **出力の統計** - このボルトによって出力されるデータに関する情報。

	* **エグゼキュータ** - このコンポーネントのインスタンスに関する情報。

	* **エラー** - このコンポーネントで生成されたエラー。

4. スパウトかボルトに関する詳細を表示した状態で、**[エグゼキュータ]** セクションの **[ポート]** 列でエントリを選択 し、コンポーネントの特定のインスタンスの詳細を表示します。

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	このデータでは、**seven** という単語が 1493957 回発生したことを確認できます。これは、このトポロジが開始されてから発生した回数です。

##トポロジを停止する

ワードカウント トポロジの **[トポロジの概要]** ページに戻り、**[トポロジのアクション]** セクションで **[強制終了]** を選択します。メッセージが表示されたら、トポロジを停止するまでの待機秒数として「10」を入力します。タイムアウト期間後は、ダッシュボードの **[Storm UI]** セクションにアクセスしても、トポロジは表示されません。

##概要

この Apache Storm チュートリアルでは、Storm Starter を使用して、HDInsight クラスターで Storm を作成する方法と、Storm ダッシュボードを使用して Storm トポロジをデプロイ、監視、管理する方法について説明しました。

##<a id="next"></a>次のステップ

* 次のドキュメントには、HDInsight の Storm と使用できるその他のサンプルの一覧が含まれています。

	* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0128_2016-->