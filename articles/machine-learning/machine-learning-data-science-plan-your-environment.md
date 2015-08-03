<properties 
	pageTitle="Azure Machine Learning の高度な分析環境を計画する | Microsoft Azure" 
	description="重要事項を考慮して、高度な分析環境を計画します。" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="mohabib;bradsev" />


# Azure Machine Learning の高度な分析環境を計画する

Azure Machine Learning の高度な分析環境をセットアップするときには、いくつかの決定を行う必要があります。その決定は、データの種類やサイズによって異なるほか、データ ソースの場所とクラウド内でのデータの宛先によっても異なります。Advanced Analytics Process は、何らかのソースから元のデータを取り出してから、アプリケーションで利用できるように Azure Web サービスとしてモデルを作成し、そのモデルを発行するまでのエンド ツー エンドの一連のタスクです。

Advanced Analytics Process and Technology ワークフローは、「[Azure で高度な分析ソリューションを構築する](machine-learning-data-science-how-to-create-machine-learning-service.md)」に記載されています。Advanced Analytics Process and Technology (ADAPT) の個々の手順の詳細については、このガイドで該当する項目をクリックしてください。

この記事では、高度な分析環境をセットアップする際の考慮事項、このプロセスに役立つリソースとツール、Advanced Analytics Process and Technology ガイドの使用方法について説明します。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 考慮事項

高度な分析環境の作成を開始する前に、次の点を考慮してください。

1. **データはどこにあるのか?** (この場所を***データ ソース***と呼びます)。 次に例を示します。
	- HTTP アドレスに公開されていて、入手できるデータ。
	- ローカル/ネットワーク上のファイル位置に存在するデータ。
	- SQL Server データベースに格納されているデータ。
	- Azure ストレージ コンテナーに格納されているデータ。
2. **データはどのような形式か?** 次に例を示します。
    - コンマまたはタブ区切りファイル (圧縮されていない)。
    - コンマまたはタブ区切りファイル (圧縮されている)。
	- 圧縮されている、または圧縮されていない Azure BLOB。
	- SQL Server テーブル。
3. **データのサイズはどの程度か?**
    - 小さい: 2 GB 未満
    - 中程度: 2 GB 超、10 GB 未満
	- 大きい: 10 GB 超
	- 非常に大きい: 数百 GB
4. **データについてどの程度理解しているか?**
    - データを探索して、スキーマ、変数の分布、欠落値などを調査する必要があるか。 
	- データを表形式に変換する前に、前処理やクリーニングが必要か。 
5. **すべてのデータを Azure に移動することを計画しているか? またはそれが可能か?**
    - はい、処理のためにデータセット全体をクラウドにコピーする計画です。
	- いいえ、Azure にはデータのサブセットのみをコピーします。
6. **Azure クラウド内のデータの宛先はどこを希望しているか?** 次に例を示します。
	- Azure ストレージ BLOB にデータを移動する。
	- マウント可能な Azure 仮想ハード ディスクにデータを格納する。
	- Azure Virtual Machine 上の SQL Server データベースにデータを読み込む。
	- Azure HDInsight Hive テーブルにデータをマップする。

## Azure での高度な分析のリソース

シナリオによっては、次のリソースが必要になることもあります。

1.  Azure Tools: [Azure PowerShell SDK](../install-configure-powershell.md)、[Azure ストレージ エクスプローラー](http://azurestorageexplorer.codeplex.com/)、[AzCopy](../storage-use-azcopy.md) など
2.  SQL Server を実行する Azure Virtual Machine
3.  Azure HDInsight (Hadoop)
4.  Azure とオンプレミスのファイル共有に使用する Azure 仮想ネットワーク
5.  スケジュールされたデータ移動のための Azure データ ファクトリ


## Advanced Analytics Process and Technology (ADAPT) ガイドの使用方法

「[Azure で高度な分析ソリューションを構築する](machine-learning-data-science-how-to-create-machine-learning-service.md)」で提供されるガイドでは、データ サイエンスのさまざまな課題を提示しています。マップは、高度な分析の標準的なワークフローに含まれる主な手順を示しています。データ サイエンスのすべての課題で、これらの手順がすべて必要になるとは限りません。また、プロセスは反復的な性質をもち、手順を実行する順序が課題によって異なることもあります。上記の質問への回答は、特定のケースに関係のある手順を見極め、それらの手順がプロセス内のどのタイミングで必要になるか、手順の反復処理がどのような条件下で必要になるかを決定するために役立ちます。

元のデータのサイズ、データ ソースの場所、Azure でのターゲット リポジトリに基づくサンプル シナリオについては、「[Azure Machine Learning の Advanced Analytics Process and Technology 用のシナリオ](../machine-learning-data-science-plan-sample-scenarios.md)」をご覧ください。


 

<!---HONumber=July15_HO4-->