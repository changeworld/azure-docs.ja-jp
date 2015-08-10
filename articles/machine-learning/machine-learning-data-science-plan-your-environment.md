<properties 
	pageTitle="Azure Machine Learning の高度な分析環境を計画する | Microsoft Azure" 
	description="重要事項を考慮して、高度な分析環境を計画します。" 
	services="machine-learning" 
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
	ms.date="07/22/2015" 
	ms.author="mohabib;bradsev" />


# Azure Machine Learning の高度な分析環境を計画する

Azure Machine Learning で高度な分析を実行する環境をセットアップするための準備を進めている場合、現在の分析の問題に適合するのはどのようなシナリオでしょうか。 必要なリソースに関する選択は、データの種類、サイズ、およびソースの場所と、そのデータの宛先に基づきます。この記事では、シナリオの特定に役立つこれらの考慮事項について説明します。

該当するシナリオを特定したら、「[Azure で高度な分析ソリューションを構築する](machine-learning-data-science-how-to-create-machine-learning-service.md)」に記載されている Advanced Analytics Process and Technology (ADAPT) ワークフローに従います。このワークフローでは、データセットを取得してから、アプリケーションで利用できるように Azure Web サービスとしてモデルを作成し、そのモデルを発行するまでの一連のタスクを示しています。

このトピックでは、この高度な分析プロセスで使用するリソースとツールも紹介します。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 考慮事項
高度な分析環境を作成する前に、使用するシナリオを決定するために次の点を考慮してください。

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
5. **すべてのデータを Azure ストレージに移動することを計画しているか? またはそれが可能か?**
    - はい、処理のためにデータセット全体をクラウドにコピーする計画です。
	- いいえ、Azure にはデータのサブセットのみをコピーします。
6. **Azure クラウド内のデータの宛先はどこを希望しているか?** 次に例を示します。
	- Azure ストレージ BLOB にデータを移動する。
	- マウント可能な Azure 仮想ハード ディスクにデータを格納する。
	- Azure Virtual Machine 上の SQL Server データベースにデータを読み込む。
	- Azure HDInsight Hive テーブルにデータをマップする。

## シナリオの決定
前のセクションの質問に答えたら、現在の状況に最適なシナリオを決定する準備が整います。サンプル シナリオは、「[Azure Machine Learning の Advanced Analytics Process and Technology 用のシナリオ](../machine-learning-data-science-plan-sample-scenarios.md)」に記載されています。

## Azure での高度な分析のリソース
シナリオによっては、次のツールとリソースが必要になる場合があります。

1.  Azure ツール: 
	* 	[Azure PowerShell SDK](../install-configure-powershell.md) 
	* 	[Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/)
	* 	[AzCopy](../storage-use-azcopy.md)
2.  SQL Server を実行する Azure Virtual Machine
3.  Azure HDInsight (Hadoop)
4.  Azure とオンプレミスのファイル共有に使用する Azure 仮想ネットワーク
5.  スケジュールされたデータ移動のための Azure データ ファクトリ






 

<!---HONumber=July15_HO5-->