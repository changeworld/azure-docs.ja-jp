<properties 
	pageTitle="データ サイエンス用の Virtual Machine または SQL Server Virtual Machine を Azure でセットアップする" 
	description="データ サイエンス用の Virtual Machinee のセットアップ" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# データ サイエンス用の Virtual Machine または SQL Server Virtual Machine を Azure でセットアップする

クラウド ベースのデータ サイエンス環境の一部として使用するように、いくつかの種類の Azure Virtual Machine をプロビジョニングし、構成できます。使用する Virtual Machine の種類を決定する方法は、Machine Learning によってモデル化するデータの種類と量、さらにクラウド内でのデータの宛先によって異なります。この決定を行う際の考慮点については、「[Azure Machine Learning のデータ サイエンス環境を計画する](machine-learning-data-science-plan-your-environment.md)」を参照してください。ここで取り上げる 2 つのシナリオは次のとおりです。

ここでは、Azure VM と、SQL サービス付きの Azure VM を IPython の Notebook サーバーとして設定する方法について説明します。Virtual Machine は Windows で実行し、サポート ツールとして Azure ストレージ エクスプローラーと AzCopy、およびデータ サイエンスのプロジェクトに役立つその他のパッケージを構成します。Azure ストレージ エクスプローラーと AzCopy は、たとえば、ローカル コンピューターから Azure ストレージにデータをアップロードしたり、Azure ストレージからローカル コンピューターにデータをダウンロードしたりする便利な手段を提供します。次の 2 つの指示について説明します。

* 「[データ サイエンス用に Azure Virtual Machine をセットアップする](machine-learning-data-science-setup-virtual-machine.md)」では、SQL 以外の Azure ストレージ形式でデータを格納できるようにする場合の、IPython Notebook を使用する Azure Virtual Machine と、データ サイエンスに使用するその他のツールのプロビジョニングの実行方法について説明します。 

* 「[データ サイエンス用に Azure SQL Server Virtual Machine をセットアップする](machine-learning-data-science-setup-sql-server-virtual-machine.md)」では、SQL データベースにデータを格納する場合の、IPython Notebook を使用する Azure SQL Server Virtual Machine と、データ サイエンスに使用するその他のツールのプロビジョニングの実行方法について説明します。

プロビジョニングと構成が完了すると、これらの Virtual Machine は IPython Notebook サーバーとして使用する準備が整い、Azure Machine Learning およびクラウド データ サイエンスのプロセスと連携して、データの探索と処理、およびその他のタスクを実行できるようになります。このプロセスには、Azure Machine Learning を使ってデータから学習する準備として、HDInsight にデータを移動して処理とサンプリングを行う手順を組み込むことができます。

* Azure BLOB ストレージからデータを HDInsight に移動する方法の詳細については、「[Azure BLOB ストレージから HIVE テーブルを作成し、データを読み込む](machine-learning-data-science-hive-tables.md)」を参照してください。
* HDInsight で Hive クエリを使用してデータを処理する方法の詳細については、「[クラウド データ サイエンスのプロセスで HDInsight の Hadoop クラスターに Hive クエリを送信する](machine-learning-data-science-hive-queries.md)」を参照してください。
* HDInsight でデータをサンプリングする方法の詳細については、「[Azure HDInsight Hive テーブルでのデータのサンプリング](machine-learning-data-science-sample-data-hive.md)」を参照してください。


> [AZURE.NOTE] Azure Virtual Machines の料金は、**従量課金制**です。Virtual Machine を使用していない間に課金されていないことを確認するには、[Azure 管理ポータル](http://manage.windowsazure.com/)からその Virtual Machine が **[停止 (割り当て解除)]** 状態になっていることを確認します。Virtual Machine の割り当てを解除する方法とステップごとの指示については、「[使用していないときに Virtual Machine をシャットダウンして割り当て解除する](machine-learning-data-science-setup-virtual-machine.md#shutdown)」を参照してください。 




<!--HONumber=49-->