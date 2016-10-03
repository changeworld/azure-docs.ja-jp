<properties
	pageTitle="Azure のデータ サイエンス用仮想マシン | Microsoft Azure"
	description="データ サイエンス用仮想マシンのセットアップ"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="xibingao;bradsev" />

# Azure のデータ サイエンス用仮想マシン

ここでは、Azure VM と、SQL サービスを使用する Azure VM を IPython Notebook サーバーとして設定する方法について説明します。IPython Notebook、Azure ストレージ エクスプローラーおよび AzCopy などのサポート ツールに加えて、データ サイエンス プロジェクトに役立つ他のユーティリティのある Windows 仮想マシンを構成します。Azure ストレージ エクスプローラーと AzCopy は、たとえば、ローカル コンピューターから Azure Storage にデータをアップロードしたり、Azure Storage からローカル コンピューターにデータをダウンロードしたりする便利な手段を提供します。

このメニューは、[Team Data Science Process (TDSP)](data-science-process-overview.md) で使用されるさまざまなデータ サイエンス環境の設定方法を説明するトピックにリンクしています。

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

クラウド ベースのデータ サイエンス環境の一部として使用するように、いくつかの種類の Azure Virtual Machine をプロビジョニングし、構成できます。使用する仮想マシンの種類を決定する方法は、Machine Learning によってモデル化するデータの種類と量、さらにクラウド内でのデータの宛先によって異なります。

* この決定を行う際の考慮事項については、「[Azure Machine Learning のデータ サイエンス環境を計画する](machine-learning-data-science-plan-your-environment.md)」をご覧ください。
* 高度な分析を実施する際に発生する可能性があるシナリオのカタログについては、「[Azure Machine Learning に関するクラウド データ サイエンス プロセス](machine-learning-data-science-plan-sample-scenarios.md)」をご覧ください。

次の 2 つの指示について説明します。

* 「[高度な分析のために Azure 仮想マシンを IPython Notebook サーバーとしてセットアップする](machine-learning-data-science-setup-virtual-machine.md)」では、SQL 以外の Azure Storage 形式でデータを格納できるようにする場合の、IPython Notebook を使用する Azure 仮想マシンと、データ サイエンスに使用するその他のツールのプロビジョニングの実行方法を示しています。

* 「[高度な分析のために Azure SQL Server 仮想マシンを IPython Notebook サーバーとして設定する](machine-learning-data-science-setup-sql-server-virtual-machine.md)」では、SQL Database の形式でデータを格納できるようにする場合の、IPython Notebook を使用する Azure SQL Server 仮想マシンと、データ サイエンスに使用するその他のツールのプロビジョニングの実行方法を示しています。

プロビジョニングと構成が完了すると、これらの仮想マシンは IPython Notebook サーバーとして使用する準備が整い、Azure Machine Learning や Team Data Science Process (TDSP) と連携して、必要なデータの探索と処理、その他のタスクを実行できるようになります。データ サイエンス プロセスの次のステップは、[TDSP ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)に示されています。SQL Server または HDInsight にデータを移動し、Azure Machine Learning でデータの情報を取得する準備としてデータを処理してサンプリングする手順などがあります。


> [AZURE.NOTE] Azure Virtual Machines の料金は**従量課金制**です。仮想マシンを使用していないときに課金されないようにするには、[Azure クラシック ポータル](http://manage.windowsazure.com/)から、その仮想マシンを **[停止 (割り当て解除)]** 状態にする必要があります。仮想マシンの割り当てを解除する方法については、「[未使用時に仮想マシンをシャットダウンして割り当てを解除する](machine-learning-data-science-setup-virtual-machine.md#shutdown)」をご覧ください。
 

<!---HONumber=AcomDC_0921_2016-->