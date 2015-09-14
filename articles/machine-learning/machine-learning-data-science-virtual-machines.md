<properties
	pageTitle="Azure のデータ サイエンス用仮想マシン | Microsoft Azure"
	description="データ サイエンス用の Virtual Machinee のセットアップ"
	services="machine-learning"
	documentationCenter=""
	authors="msolhab"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="mohabib;xibingao;bradsev"/>

# Azure のデータ サイエンス用仮想マシン

クラウド ベースのデータ サイエンス環境の一部として使用するように、いくつかの種類の Azure Virtual Machine をプロビジョニングし、構成できます。使用する Virtual Machine の種類を決定する方法は、Machine Learning によってモデル化するデータの種類と量、さらにクラウド内でのデータの宛先によって異なります。


* この決定を行う際の考慮事項については、「[Azure Machine Learning のデータ サイエンス環境を計画する](machine-learning-data-science-plan-your-environment.md)」をご覧ください。 
* 高度な分析を実施する際に発生する可能性があるシナリオのカタログについては、「[Azure Machine Learning に関するクラウド データ サイエンス プロセス](../machine-learning-data-science-plan-sample-scenarios.md)」をご覧ください。

ここでは、Azure VM と、SQL サービス付きの Azure VM を IPython の Notebook サーバーとして設定する方法について説明します。IPython Notebook、Azure Storage Explorer および AzCopy などのサポート ツールに加えて、データ サイエンス プロジェクトに役立つ他のユーティリティのある Windows 仮想マシンを構成します。Azure ストレージ エクスプローラーと AzCopy は、たとえば、ローカル コンピューターから Azure ストレージにデータをアップロードしたり、Azure ストレージからローカル コンピューターにデータをダウンロードしたりする便利な手段を提供します。次の 2 つの指示について説明します。

* 「[高度な分析のために Azure 仮想マシンを IPython Notebook サーバーとしてセットアップする](machine-learning-data-science-setup-virtual-machine.md)」では、SQL 以外の Azure ストレージ形式でデータを格納できるようにする場合の、IPython Notebook を使用する Azure 仮想マシンと、データ サイエンスに使用するその他のツールのプロビジョニングの実行方法を示しています。

* 「[高度な分析のために Azure SQL Server 仮想マシンを IPython Notebook サーバーとして設定する](machine-learning-data-science-setup-sql-server-virtual-machine.md)」では、SQL Database の形式でデータを格納できるようにする場合の、IPython Notebook を使用する Azure SQL Server 仮想マシンと、データ サイエンスに使用するその他のツールのプロビジョニングの実行方法を示しています。

プロビジョニングと構成が完了すると、これらの仮想マシンは IPython Notebook サーバーとして使用する準備が整い、Azure Machine Learning やクラウド データ サイエンス プロセスと連携して、必要なデータの探索と処理、その他のタスクを実行できるようになります。データ サイエンス プロセスにおける次のステップは、「[学習ガイド: Azure での高度なデータ処理](machine-learning-data-science-advanced-data-processing.md)」に示されています。これには、SQL Server か HDInsight にデータを移動し、Azure Machine Learning でデータから知見を得るための準備としてデータを処理してサンプリングする手順が含まれています。


> [AZURE.NOTE]Azure Virtual Machines の料金は**従量課金制**です。仮想マシンを使用していないときに課金されないようにするには、[Microsoft Azure 管理ポータル](http://manage.windowsazure.com/)から、その仮想マシンを **[停止 (割り当て解除)]** 状態にする必要があります。仮想マシンの割り当てを解除する方法については、「[未使用時に仮想マシンをシャットダウンして割り当てを解除する](machine-learning-data-science-setup-virtual-machine.md#shutdown)」をご覧ください。
 

<!---HONumber=September15_HO1-->