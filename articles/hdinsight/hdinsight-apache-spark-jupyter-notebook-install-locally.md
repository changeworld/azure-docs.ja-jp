<properties 
	pageTitle="Jupyter Notebook をコンピューターにインストールして HDInsight Spark クラスターに接続する | Microsoft Azure" 
	description="Jupyter Notebook をローカル コンピューターにインストールして Azure HDInsight 上の Apache Spark クラスターに接続する方法を説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="jhubbard" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/26/2016" 
	ms.author="nitinme"/>


# Jupyter Notebook をコンピューターにインストールして HDInsight Linux の Apache Spark クラスターに接続する

この記事では、カスタム PySpark (Python の場合) カーネルと Spark (Scala の場合) カーネル、Spark マジックと共に Jupyter Notebook をインストールし、HDInsight クラスターにノートブックを接続する方法について説明します。さまざまな理由から、Jupyter をローカル コンピューターにインストールすることが必要になるケースがあります。またローカル コンピューターへのインストールには、いくつかの課題もあります。これらの理由と課題については、この記事の最後のセクション「[Jupyter をローカル コンピューターにインストールする理由](#why-should-i-install-jupyter-on-my-computer)」を参照してください。

Jupyter と Spark マジックは大きく 3 つの手順でコンピューターにインストールします。

* Jupyter Notebook をインストールする
* PySpark カーネルと Spark カーネルおよび Spark マジックをインストールする
* HDInsight 上の Spark クラスターにアクセスするように Spark マジックを構成する

HDInsight クラスターの Jupyter Notebook で使用できるカスタム カーネルと Spark マジックの詳細については、[HDInsight の Apache Spark Linux クラスターと Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)に関するページを参照してください。

##前提条件

ここに記載したのは、Jupyter のインストールに関する前提条件ではありません。Jupyter Notebook のインストール後にノートブックを HDInsight クラスターに接続するための前提条件です。

- Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- HDInsight Linux での Apache Spark クラスター。手順については、「[HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)」を参照してください。

## Jupyter Notebook をコンピューターにインストールする

Jupyter Notebook をインストールする前に Python をインストールする必要があります。Python と Jupyter はどちらも、[Ananconda ディストリビューション](https://www.continuum.io/downloads)に同梱されています。Anaconda をインストールすると、実質的に Python のディストリビューションがインストールされます。Jupyter 環境は、Anaconda のインストール後にコマンドを実行して追加することになります。そのために必要な手順をこのセクションで紹介しています。

1. ご使用のプラットフォーム用の [Anaconda インストーラー](https://www.continuum.io/downloads)をダウンロードし、セットアップ プログラムを実行します。セットアップ ウィザードを実行する過程で、Anaconda を PATH 変数に追加するためのオプションを忘れずに選択してください。

2. 次のコマンドを実行して Jupyter をインストールします。

		conda install jupyter

	Jupyter のインストールの詳細については、[Anaconda を使用した Jupyter のインストール](http://jupyter.readthedocs.io/en/latest/install.html)に関するページを参照してください。

## カーネルと Spark マジックをインストールする

Spark マジック、PySpark カーネル、および Spark カーネルのインストール手順については、GitHub の [sparkmagic ドキュメント](https://github.com/jupyter-incubator/sparkmagic#installation)を参照してください。

## HDInsight の Spark クラスターにアクセスするように Spark マジックを構成する

このセクションでは、先ほどインストールした Spark マジックに対し、Apache Spark クラスターに接続するための構成を行います。Apache Spark クラスターは、あらかじめ Azure HDInsight に作成しておく必要があります。

1. Jupyter の構成情報は通常、ユーザーの home ディレクトリに格納されます。home ディレクトリは、任意の OS プラットフォームで以降に示したコマンドを入力して検索できます。

	Python シェルを起動します。コマンド ウィンドウで次のように入力します。

		python

	Python シェルで、次のコマンドを入力して home ディレクトリを特定します。

		import os
		print os.path.expanduser('~')

2. home ディレクトリに移動して **.sparkmagic** というフォルダーを作成します (まだ存在しない場合)。

3. このフォルダーに **config.json** というファイルを作成し、次の JSON スニペットを追加します。

		{
		  "kernel_python_credentials" : {
		    "username": "{USERNAME}",
		    "base64_password": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  },
		  "kernel_scala_credentials" : {
		    "username": "{USERNAME}",
		    "base64_password": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  }
		}

4. **{USERNAME}**、**{CLUSTERDNSNAME}**、**{BASE64ENCODEDPASSWORD}** は、それぞれ適切な値に置き換えてください。base64 エンコード パスワードは、ご利用のプログラミング言語のユーティリティまたはオンライン ユーティリティを使用して、実際のパスワードから生成してください。コマンド プロンプトから実行する単純な Python スニペットの例を以下に示します。

		python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Jupyter を起動します。コマンド プロンプトから次のコマンドを使用します。

		jupyter notebook

6. Jupyter Notebook を使用してクラスターに接続できること、カーネルに備わっている Spark マジックを使用できることを確認します。次の手順に従います。

	1. 新しい Notebook を作成します。右隅にある **[新規]** をクリックします。既定のカーネル **Python2** に加え、新たにインストールする 2 つの新しいカーネル (**PySpark** と **Spark**) が確認できます。

		![新しい Jupyter Notebook を作成します](./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "新しい Jupyter Notebook を作成します")

	
		**[PySpark]** をクリックします。


	2. 次のコード スニペットを実行します。

			%%sql
			SELECT * FROM hivesampletable LIMIT 5

		出力結果が正しく得られた場合、HDInsight クラスターへの接続テストは完了です。

	>[AZURE.TIP] 別のクラスターに接続するようにノートブックの構成を更新する必要がある場合は、前の手順 3. を参照して、config.json の一連の値を更新してください。

## Jupyter をローカル コンピューターにインストールする理由

Jupyter をローカル コンピューターにインストールし、HDInsight 上の Spark クラスターに接続するケースとしては、さまざまな理由が考えられます。

* Azure HDInsight の Spark クラスターで Jupyter Notebook が既に使用できる状態になっていても、ご使用のコンピューターに Jupyter をインストールすることで、ノートブックをローカルで作成し、稼働中のクラスターに対してアプリケーションをテストしたうえで、クラスターにノートブックをアップロードするという選択肢が得られます。ノートブックをクラスターにアップロードする手段としては、クラスターで実行されている Jupyter Notebook を使用してアップロードする方法と、クラスターに関連付けられているストレージ アカウントの /HdiNotebooks フォルダーにそれらを保存する方法とがあります。クラスターにノートブックを保存する方法の詳細については、[Jupyter Notebook の格納場所](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)に関するセクションを参照してください。
* ノートブックがローカルで利用できると、アプリケーションの要件に応じて異なる Spark クラスターに接続することができます。
* GitHub を使用してソース管理システムを導入し、ノートブックのバージョン管理を行うことができます。複数のユーザーが同じノートブックで作業するコラボレーション環境を実現することもできます。
* クラスターをセットアップしなくてもローカルでノートブックを使用できます。クラスターは、クラスターとの間でノートブックをテストする目的でのみ必要となります。ノートブックや開発環境を手動で管理するうえでクラスターは必要ありません。
* 独自のローカル開発環境の方が、クラスター上の Jupyter 環境よりも構成しやすい場合があります。リモートのクラスターを構成しなくても、ローカルにインストールされているすべてのソフトウェアを有効活用することができます。

>[AZURE.WARNING] Jupyter がローカル コンピューターにインストールされている場合、同じ Spark クラスター上で複数のユーザーが同時に同じノートブックを実行できます。そのような状況では、複数の Livy セッションが作成されます。問題が発生してデバッグが必要となった場合、それは Livy セッションとユーザーの対応関係を追跡する複雑な作業となります。




## <a name="seealso"></a>関連項目


* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### シナリオ

* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)

* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)

* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### アプリケーションの作成と実行

* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)

* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### ツールと拡張機能

* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](hdinsight-apache-spark-intellij-tool-plugin.md)

* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)

* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0928_2016-->