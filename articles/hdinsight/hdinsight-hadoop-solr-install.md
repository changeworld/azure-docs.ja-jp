<properties
	pageTitle="Script Action を使用して Hadoop クラスターに Solr をインストールする | Microsoft Azure"
	description="Solr を使用して HDInsight クラスターをカスタマイズする方法について説明します。Solr をインストールするスクリプトを使用するために、Script Action の構成オプションを使用します。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/07/2015"
	ms.author="nitinme"/>

# HDInsight Hadoop クラスターに Solr をインストールして使用する

**Script Action** クラスター カスタマイズを使用して、Azure HDInsight の Hadoop の任意の種類のクラスターに Solr をインストールできます。Script Action では、クラスターの作成時のみ、スクリプトを実行してクラスターをカスタマイズできます。詳細については、[Script Action を使用した HDInsight クラスターのカスタマイズ][hdinsight-cluster-customize]に関するページを参照してください。

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [HDInsight クラスターでの Solr のインストール](hdinsight-hadoop-solr-install.md)

このトピックでは、Script Action を使用して、Solr をインストールする方法を学習します。Solr は強力な検索プラットフォームで、Hadoop が管理するデータに対してエンタープライズ レベルの検索機能を提供します。HDInsight クラスターに Solr をインストールした後で、Solr を使用したデータの検索方法についても説明します。

> [AZURE.NOTE]このトピックで使用するサンプル スクリプトは、既定の構成で Windows ベースの Solr クラスターを作成します。この構成とは異なるコレクションやシャード、スキーマ、レプリカなどで Solr クラスターを構成する場合には、それに応じてスクリプトと Solr バイナリを変更する必要があります。
>
> Linux ベースのクラスターでの Solr の操作については、「[HDInsight Hadoop クラスターに R をインストールして使用する (Linux)](hdinsight-hadoop-solr-install-linux.md)」を参照してください。

## <a name="whatis"></a>Solr とは何か

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> は、データに対して強力なフルテキスト検索ができるエンタープライズ検索プラットフォームです。Hadoop が大量のデータの保存と管理を可能にするのに対し、Apache Solr は迅速にデータを取得するための検索機能を提供します。このトピックでは、HDInsight クラスターをカスタマイズして Solr をインストールする方法を説明します。

## <a name="install"></a>Solr をインストールする方法

HDInsight クラスターに Solr をインストールするためのサンプル スクリプトは、[https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1) の読み取り専用 Azure ストレージ BLOB から入手できます。このセクションでは、Azure ポータルを使用してクラスターをプロビジョニングする際に、サンプル スクリプトを使用する方法について説明します。


> [AZURE.NOTE]サンプル スクリプトは、HDInsight クラスター version 3.1 でのみ機能します。HDInsight クラスター バージョンの詳細については、「[HDInsight クラスター バージョン](hdinsight-component-versioning.md)」をご覧ください。


1. 「[カスタム オプションを使用したクラスターのプロビジョニング](hdinsight-provision-clusters.md#portal)」に関するページの説明に従い、**[カスタム作成]** オプションを使用してプロビジョニングを開始します。
2. ウィザードの **[スクリプトのアクション]** ページで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

	![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Script Action を使ってクラスターをカスタマイズする")

	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>名前</td>
		<td>スクリプト アクションの名前を指定します。たとえば、「<b>Install Solr</b>」のような名前にします。</td></tr>
	<tr><td>スクリプト URI</td>
		<td>クラスターをカスタマイズするために呼び出すスクリプトの Uniform Resource Identifier (URI) を指定します。例: <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
	<tr><td>ノードの種類</td>
		<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
	<tr><td>パラメーター</td>
		<td>スクリプトで必要な場合は、パラメーターを指定します。Solr をインストールするスクリプトにパラメーターは必要ないため、この部分は空白のままにすることもできます。</td></tr>
</table>複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。スクリプトの追加後、チェックマークをクリックしてクラスターのプロビジョニングを開始します。


## <a name="usesolr"></a>HDInsight で Solr を使用する方法

最初に、いくつかのデータ ファイルに対し Solr のインデックスを作成する必要があります。これにより、インデックス付きデータに対して、Solr を使用して検索クエリを実行できます。HDInsight クラスターで Solr を使用するには、次の手順を実行します。

1. **リモート デスクトップ プロトコル (RDP) を使用してインストールされている Solr で HDInsight クラスターにリモート接続します**。Solr のインストールによって作成したクラスターに対し、Azure ポータルでリモート デスクトップを有効にし、クラスターにリモート接続します。手順については、「<a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP を使用した HDInsight クラスターへの接続</a>」をご覧ください。

2. **データ ファイルをアップロードして Solr のインデックスを作成します**。Solr のインデックスを作成する場合、検索が必要なドキュメントをその中に配置します。Solr のインデックスを作成するには、クラスターに RDP をリモート接続してデスクトップに移動します。次に、Hadoop コマンド ラインを開いて **C:\\apps\\dist\\solr-4.7.2\\example\\exampledocs** に移動します。次のコマンドを実行します。

		java -jar post.jar solr.xml monitor.xml

	コンソールで、次の出力が表示します。

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	post.jar ユーティリティは、**solr.xml** と **monitor.xml** という 2 つのサンプル ドキュメントで Solr のインデックスを作成します。post.jar ユーティリティとサンプル ドキュメントは Solr のインストールで利用できるようになります。

3. **Solr ダッシュボードを使用して、インデックス付きドキュメント内を検索します**。HDInsight クラスターへの RDP セッションで Internet Explorer を開き、****http://headnodehost:8983/solr/#/** で Solr のダッシュボードを起動します。左側のウィンドウの **[Core Selector]** ボックスから、**[collection1]** を選択し、メニューの中から **[Query]** をクリックします。例として、Solr 内のすべてのドキュメントを選択して返すために、次の値を指定します。

	* **[q]** ボックスに「***:***」を入力します。これにより、Solr でインデックス付けされたすべてのドキュメントが返されます。ドキュメント内の特定の文字列を検索する場合には、ここにその文字列を入力することができます。
	
	* **[wt]** ボックスでは、出力形式を選択します。既定値は、**json** です。**[Execute Query]** をクリックします。

	![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Solr ダッシュボードでクエリを実行する")
	
	これによる出力は、Solr のインデックス作成のために使用した 2 つのドキュメントを返します。出力結果は、以下のようになります。

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }


4. **インデックス付きデータを、Solr から HDInsight クラスターに関連付けられている Azure BLOB ストレージにバックアップすることをお勧めします**。インデックス付きデータを Solr のクラスター ノードから Azure BLOB ストレージ にバックアップすることをお勧めします。そのために、次の手順を実行してください。

	1. RDP セッションから Internet Explorer を開き、次の URL をポイントします。

			http://localhost:8983/solr/replication?command=backup

		次のように、応答が表示されます。

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. リモート セッションで、{SOLR\_HOME}{Collection}\\data に移動します。サンプル スクリプトを通じて作成したクラスターの場合、この部分は **C:\\apps\\dist\\solr-4.7.2\\example\\solr\\collection1\\data** となります。この場所には、**snapshot.*timestamp*** のような名前でスナップショット フォルダーが作成されています。

	3. スナップショット フォルダーを zip 圧縮して Azure BLOB ストレージにアップロードします。Hadoop コマンド ラインで、次のコマンドを使用して、スナップショット フォルダーの場所に移動します。

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		このコマンドは、クラスターに関連付けられている既定のストレージ アカウント内のコンテナーの下にある /example/data/ にスナップショットをコピーします。


## 関連項目

- [HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]。クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Spark をインストールします。Spark はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。
- [HDInsight クラスターに R をインストールする][hdinsight-install-r]。クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに R をインストールします。R は、統計計算用のオープン ソースの言語および環境です。R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。また、広範なグラフィカル機能も提供します。
- [HDInsight クラスターでの Giraph のインストール](hdinsight-hadoop-giraph-install.md): クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Giraph をインストールします。Giraph は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。


[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!---HONumber=Oct15_HO1-->