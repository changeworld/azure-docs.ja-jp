<properties 
	pageTitle="Team Data Science Process で使用するためのデータ サイエンス環境の設定 | Azure" 
	description="Team Data Science Process で使用するためのデータ サイエンス環境の設定" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/23/2016" 
	ms.author="bradsev" />

# Team Data Science Process で使用するためのデータ サイエンス環境の設定

Team Data Science Process は、データの格納、処理、および分析のために、さまざまなデータ サイエンス環境を使用します。たとえば、Azure BLOB ストレージ、いくつかの種類の Azure 仮想マシン、HDInsight (Hadoop) クラスター、Azure Machine Learning ワークスペースなどです。使用する環境を決定する方法は、Machine Learning によってモデル化するデータの種類と量、さらにクラウド内でのデータの宛先によって異なります。

* この決定を行う際の考慮事項については、「[Azure Machine Learning のデータ サイエンス環境を計画する](machine-learning-data-science-plan-your-environment.md)」をご覧ください。
* 高度な分析を実施する際に発生する可能性があるシナリオのカタログについては、[Team Data Science Process のシナリオ](machine-learning-data-science-plan-sample-scenarios.md)に関するページをご覧ください

このメニューは、Team Data Science Process によって使用されるさまざまなデータ サイエンス環境の設定方法を説明するトピックにリンクしています。

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

**Microsoft データ サイエンス用仮想マシン**を利用することもできます。Microsoft データ サイエンス用仮想マシンは、プレインストールの Azure 仮想マシン (VM) イメージです。データ分析と機械学習用に一般的に使用されているいくつかのツールで構成されています。この VM の使用方法については、「[Microsoft データ サイエンス仮想マシンのプロビジョニング](machine-learning-data-science-provision-vm.md)」を参照してください。

<!---HONumber=AcomDC_0914_2016-->