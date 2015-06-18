<properties
	pageTitle="クラウド データ サイエンス プロセスのチュートリアル | Azure" 
	description="クラウド データ サイエンス プロセスのチュートリアル" 
	metaKeywords="" 
	services="data-science-process" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="" />

<tags 
	ms.service="data-science-process" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="msolhab" />


# Azure Machine Learning に関するクラウド データ サイエンス プロセス

この記事では、クラウド データ サイエンス プロセス マップといくつかのサンプル データ ソースとターゲットのシナリオについて説明します。これは、Azure でのデータの特性、ソースの場所、ターゲットのリポジトリに基づいた、いくつかのオプションの選択とシーケンスの処理を示しています。最後のセクションでは、サンプル シナリオのクラウド データ サイエンス プロセスのフローをまとめます。

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)]

**すべてのシナリオでは、次の操作が必要です。**

   [ストレージ アカウントの作成](storage-whatis-account.md)

  [Azure ML ワークスペースを作成する](machine-learning/machine-learning-create-workspace.md)

**次のセクションでは、いくつかのサンプル シナリオを示します。各シナリオでは、考えられるデータ サイエンスのフローとサポートする Azure リソースが一覧されています。**

- [シナリオ 1: ローカル ファイルの小規模から中規模の表形式のデータセット](#smalllocal)
- [シナリオ 2: 処理が必要なローカル ファイルの小規模から中規模のデータセット](#smalllocalprocess)
- [シナリオ 3: ローカル ファイルの大規模データセット (Azure BLOB を対象)](#largelocal)
- [シナリオ 4: ローカル ファイルの小規模から中規模のデータセット (Azure VM の SQL Server を対象)](#smalllocaltodb)
- [シナリオ 5: ローカル ファイルの大規模データセット (Azure VM の SQL Server を対象)](#largelocaltodb)
- [シナリオ 6: 内部設置型の SQL Server データベースの大規模データセット (Azure VM の SQL Server を対象)](#largedbtodb)
- [シナリオ #7: ローカル ファイルのビッグ データ (Azure HDInsight Hadoop クラスターの Hive データベースを対象)](#largedbtohive)

## <a name="smalllocal"></a>シナリオ 1: ローカル ファイルの小規模から中規模の表形式のデータセット

![小規模から中規模のローカル ファイル][1]

#### その他の Azure リソース: なし

1.  [Azure Machine Learning Studio](https://studio.azureml.net/) にサインインします。

2.  データセットをアップロードします。

3.  アップロードされたデータセットで始まる Azure Machine Learning の実験フローを構築します。

## <a name="smalllocalprocess"></a>シナリオ 2: 処理が必要なローカル ファイルの小規模から中規模のデータセット

![処理中の小規模から中規模のローカル ファイル][2]

#### その他の Azure リソース: Azure 仮想マシン (IPython Notebook サーバー)

1.  IPython Notebook を実行する Azure 仮想マシンを作成します。

2.  Azure ストレージ コンテナーにデータをアップロードします。

3.  IPython Notebook でデータの前処理とクリーニングを行い、Azure ストレージ コンテナーからデータにアクセスします。

4.  クリーニングされたデータを表形式に変換します。

5.  変換されたデータを Azure BLOB に保存します。

6.  [Azure Machine Learning Studio](https://studio.azureml.net/) にサインインします。

7.  [リーダー][reader] モジュールを使用して、Azure BLOB からデータを読み取ります。

8. 統合されたデータセットで始まる Azure Machine Learning の実験フローを構築します。

## <a name="largelocal"></a>シナリオ 3: ローカル ファイルの大規模データセット (Azure BLOB を対象)

![大規模なローカル ファイル][3]

#### その他の Azure リソース: Azure 仮想マシン (IPython Notebook サーバー)

1.  IPython Notebook を実行する Azure 仮想マシンを作成します。

2.  Azure ストレージ コンテナーにデータをアップロードします。

3.  IPython Notebook でデータの前処理とクリーニングを行い、Azure BLOB からデータにアクセスします。

4.  必要に応じて、クリーニングされたデータを表形式に変換します。

5.  データを探索し、必要に応じて機能を作成します。

6.  小規模から中規模のデータ サンプルを抽出します。

7.  サンプリングされたデータを Azure BLOB に保存します。

8. [Azure Machine Learning Studio](https://studio.azureml.net/) にサインインします。

9. [リーダー][reader] モジュールを使用して、Azure BLOB からデータを読み取ります。

10. 統合されたデータセットで始まる Azure ML の実験フローを構築します。


## <a name="smalllocaltodb"></a>シナリオ 4: ローカル ファイルの小規模から中規模のデータセット (Azure VM の SQL Server を対象)

![Azure の SQL DB への小規模から中規模のローカル ファイル][4]

#### その他の Azure リソース: Azure 仮想マシン (SQL Server / IPython Notebook サーバー)

1.  SQL Server と IPython Notebook を実行する Azure 仮想マシンを作成します。

2.  Azure ストレージ コンテナーにデータをアップロードします。

3.  IPython Notebook を使用して、Azure ストレージ コンテナーでデータの前処理とクリーニングを行います。

4.  必要に応じて、クリーニングされたデータを表形式に変換します。

5.  データをローカルの VM ファイルに保存します (IPython Notebook は VM で実行し、ローカル ドライブは VM ドライブを参照します)。

6.  Azure VM で実行している SQL Server データベースにデータを読み込みます。

    a.オプション 1: SQL Server Management Studio を使用する

		i.  SQL Server VM にログインします。
        ii. SQL Server Management Studio を実行します。
        iii. データベースとターゲット テーブルを作成します。
        iv. いずれかの一括インポート方法を使用して、ローカルの VM ファイルからデータを読み込みます。

    b.オプション 2: IPython Notebook を使用する (中規模以上のデータセットには不向き)

        i.  ODBC 接続文字列を使用して、VM 上の SQL Server にアクセスします。
        ii. データベースとターゲット テーブルを作成します。
        iii. いずれかの一括インポート方法を使用して、ローカルの VM ファイルからデータを読み込みます。

7.  データを探索し、必要に応じて機能を作成します。機能をデータベース テーブルで具体化する必要はありません。作成に必要なクエリに注意してください。

8. 必要に応じて、データのサンプル サイズを決定します。

9. [Azure Machine Learning Studio](https://studio.azureml.net/) にサインインします。

10. [リーダー][reader] モジュールを使用して、SQL Server から直接データを読み取ります。フィールドの抽出、機能の作成、データのサンプリングを行うのに必要なクエリを、必要に応じて、直接[リーダー][reader] クエリに貼り付けます。

11. 統合されたデータセットで始まる Azure ML の実験フローを構築します。

## <a name="largelocaltodb"></a>シナリオ 5: ローカル ファイルの大規模データセット (Azure VM の SQL Server を対象)

![Azure の SQL DB への大規模なローカル ファイル][5]

#### その他の Azure リソース: Azure 仮想マシン (SQL Server / IPython Notebook サーバー)

1.  SQL Server と IPython Notebook サーバーを実行する Azure 仮想マシンを作成します。

2.  Azure ストレージ コンテナーにデータをアップロードします。

3.  (省略可能) データの前処理とクリーニングを行います。

    a.IPython Notebook でデータの前処理とクリーニングを行い、Azure BLOB からデータにアクセスします。

    b.必要に応じて、クリーニングされたデータを表形式に変換します。

    c.データをローカルの VM ファイルに保存します (IPython Notebook は VM で実行し、ローカル ドライブは VM ドライブを参照します)。

4.  Azure VM で実行している SQL Server データベースにデータを読み込みます。

    a.SQL Server VM にログインします。

    b.データが保存されていない場合は、データ ファイルを Azure ストレージ コンテナーからローカルの VM フォルダーにダウンロードします。

    c.SQL Server Management Studio を実行します。

    d.データベースとターゲット テーブルを作成します。

	e.いずれかの一括インポート方法を使用してデータを読み込みます。

    f.テーブルの結合が必要な場合は、インデックスを作成して処理時間を短縮します。

 >[AZURE.NOTE]大きなサイズのデータの読み込みを高速にするため、パーティション分割されたテーブルを作成し、並列処理でデータを一括インポートすることをお勧めします。詳細については、「[Parallel Data Import to SQL Partitioned Tables (SQL パーティション テーブルへのデータの並列インポート](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md)」をご覧ください。

5.  データを探索し、必要に応じて機能を作成します。機能をデータベース テーブルで具体化する必要はありません。作成に必要なクエリに注意してください。

6.  必要に応じて、データのサンプル サイズを決定します。

7.  [Azure Machine Learning Studio](https://studio.azureml.net/) にサインインします。

8. [リーダー][reader] モジュールを使用して、SQL Server から直接データを読み取ります。フィールドの抽出、機能の作成、データのサンプリングを行うのに必要なクエリを、必要に応じて、直接[リーダー][reader] クエリに貼り付けます。

9. アップロードされたデータセットで始まる Azure ML の実験フローを構築します。

## <a name="largedbtodb"></a>シナリオ 6: 内部設置型の SQL Server データベースの大規模データセット (Azure 仮想マシンの SQL Server を対象)

![Azure の SQL DB への内部設置型の大規模な SQL DB][6]

#### その他の Azure リソース: Azure 仮想マシン (SQL Server / IPython Notebook サーバー)

1.  SQL Server と IPython Notebook サーバーを実行する Azure 仮想マシンを作成します。

2.  いずれかのデータ エクスポート方法を使用して、SQL Server からダンプ ファイルにデータをエクスポートします。

    a.注: 内部設置型のデータベースからすべてのデータを移動する場合、より速い方法は、完全なデータベースを Azure の SQL Server インスタンスに移動することです。データのエクスポート、データベースの作成、ターゲット データベースへのデータの読み込み/インポートの手順を省略し、別の方法を続行します。

3.  ダンプ ファイルを Azure ストレージ コンテナーにアップロードします。

4.  Azure 仮想マシンで実行している SQL Server データベースにデータを読み込みます。

    a.SQL Server VM にログインします。

    b.データ ファイルを Azure ストレージ コンテナーからローカルの VM フォルダーにダウンロードします。

    c.SQL Server Management Studio を実行します。

    d.データベースとターゲット テーブルを作成します。

	e.いずれかの一括インポート方法を使用してデータを読み込みます。

	f.テーブルの結合が必要な場合は、インデックスを作成して処理時間を短縮します。

> [AZURE.NOTE]大きなサイズのデータの読み込みを高速にするため、パーティション分割されたテーブルを作成し、並列処理でデータを一括インポートします。詳細については、「[Parallel Data Import to SQL Partitioned Tables (SQL パーティション テーブルへのデータの並列インポート](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md)」をご覧ください。

5.  データを探索し、必要に応じて機能を作成します。機能をデータベース テーブルで具体化する必要はありません。作成に必要なクエリに注意してください。

6.  必要に応じて、データのサンプル サイズを決定します。

7.  [Azure Machine Learning Studio](https://studio.azureml.net/) にサインインします。

8. [リーダー][reader] モジュールを使用して、SQL Server から直接データを読み取ります。フィールドの抽出、機能の作成、データのサンプリングを行うのに必要なクエリを、必要に応じて、直接[リーダー][reader] クエリに貼り付けます。

9. アップロードされたデータセットで始まる Azure ML の実験フローを構築します。

### 内部設置型 SQL Server から Azure SQL データベースに完全なデータベースをコピーする別の方法

![ローカル DB をデタッチし、Azure の SQL DB にアタッチする][7]

#### その他の Azure リソース: Azure 仮想マシン (SQL Server / IPython Notebook サーバー)

SQL Server VM で SQL Server データベース全体をレプリケートするには、データベースを 1 つの場所/サーバーから別の場所にコピーする必要があり、データベースが一時的にオフラインになることを想定しています。これは、SQL Server Management Studio のオブジェクト エクスプローラー GUI か、同等の TRANSACT-SQL コマンドを使用して行います。

1. ソースの場所にあるデータベースをデタッチします。詳細については、「[Detach a database (データベースのデタッチ)](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx)」をご覧ください。
2. Windows エクスプローラーや Windows コマンド プロンプト ウィンドウで、デタッチされたデータベース ファイルとログ ファイルを Azure の SQL Server VM 上のターゲットの場所にコピーします。
3. コピーしたファイルを対象の SQL Server インスタンスにアタッチします。詳細については、「[Attach a Database (データベースのアタッチ)](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx)」をご覧ください。 

[デタッチとアタッチを使用したデータベースの移動 (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>シナリオ #7: ローカル ファイルのビッグ データ (Azure HDInsight Hadoop クラスターの Hive データベースを対象)

![ローカル ターゲット Hive のビッグ データ][9]

#### その他の Azure リソース: Azure HDInsight Hadoop クラスターと Azure 仮想マシン (IPython Notebook サーバー)

1.  IPython Notebook サーバーを実行する Azure 仮想マシンを作成します。

2.  Azure HDInsight Hadoop クラスターを作成します。

3.  (省略可能) データの前処理とクリーニングを行います。

    a.IPython Notebook でデータの前処理とクリーニングを行い、Azure BLOB からデータにアクセスします。

    b.必要に応じて、クリーニングされたデータを表形式に変換します。

    c.データをローカルの VM ファイルに保存します (IPython Notebook は VM で実行し、ローカル ドライブは VM ドライブを参照します)。

4.  手順 2 で選択されている Hadoop クラスターの既定のコンテナーにデータをアップロードします。

5.  Azure HDInsight Hadoop クラスターの Hive データベースにデータを読み込みます。

    a.Hadoop クラスターのヘッド ノードにログインします。

    b.Hadoop コマンド ラインを開きます。

    c.Hadoop コマンド ラインで、コマンド `cd %hive_home%\bin` を使用して Hive ルート ディレクトリを入力します。

    d.Hive クエリを実行してデータベースとテーブルを作成し、BLOB ストレージから Hive テーブルにデータを読み込みます。

 	> [AZURE.NOTE]データが大きい場合、ユーザーはパーティションを使用して Hive テーブルを作成できます。次に、ヘッド ノードの Hadoop コマンド ラインで `for` ループを使用し、パーティション分割された Hive テーブルにデータを読み込むことができます。

6.  Hadoop コマンド ラインでデータを探索し、必要に応じて機能を作成します。機能をデータベース テーブルで具体化する必要はありません。作成に必要なクエリに注意してください。

	a.Hadoop クラスターのヘッド ノードにログインします。

    b.Hadoop コマンド ラインを開きます。

    c.Hadoop コマンド ラインで、コマンド `cd %hive_home%\bin` を使用して Hive ルート ディレクトリを入力します。

	d.Hadoop クラスターのヘッド ノードの Hadoop コマンド ラインで Hive クエリを実行してデータを探索し、必要に応じて機能を作成します。

7.  必要に応じて、Azure Machine Learning Studio に適したデータをサンプリングします。

8.  [Azure Machine Learning Studio](https://studio.azureml.net/) にサインインします。

9. [リーダー][reader] モジュールを使用して、`Hive Queries` から直接データを読み取ります。フィールドの抽出、機能の作成、データのサンプリングを行うのに必要なクエリを、必要に応じて、直接[リーダー][reader] クエリに貼り付けます。

10. アップロードされたデータセットで始まる Azure ML の実験フローを構築します。

サンプル シナリオの概要
------------------------

次の図は、上記で説明したシナリオと各シナリオで行われたクラウド データ サイエンス プロセスの内容をまとめたものです。データの処理、探索、特徴エンジニアリング、サンプリングは、1 つ以上のメソッドや環境 (ソース、中間、ターゲット環境) で生じる可能性があり、必要に応じて繰り返し実行できます。この図は、いくつかの起こり得るフローの例としてのみ機能します。

![サンプル DS プロセスのチュートリアル シナリオ][8]

### 実行中の Azure データ サイエンスの例

公開されているデータセットを使用した Azure のデータ サイエンス プロセスのエンドツーエンドのチュートリアルの例については、「[実行中の Azure データ サイエンス プロセス](machine-learning/machine-learning-data-science-process-sql-walkthrough.md)」をご覧ください。

[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!--HONumber=54-->