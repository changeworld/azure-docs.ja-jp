<properties 
	pageTitle="クラウド データ サイエンスの環境を計画する | Azure" 
	description="クラウド データ サイエンスの環境を計画する" 
	metaKeywords="" 
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
	ms.date="03/24/2015" 
	ms.author="mohabib;bradsev" /> 


# Azure Machine Learning のデータ サイエンス環境を計画する

Azure Machine Learning でデータ サイエンス環境をセットアップするときには、いくつかの決定をする必要があります。その決定は、データの種類やサイズによって異なるほか、データ ソースの場所とクラウド内でのデータの宛先によっても異なります。クラウド データ サイエンスのプロセスとは、何らかのソースからオリジナル データを取り出してから、Azure の Web サービスとしてモデルを作成し、発行し、アプリケーションで利用するまでのエンド ツー エンドの一連のタスクのことです。

クラウド データ サイエンスのプロセスのワークフローは、「**[データ サイエンスのプロセス マップ](machine-learning-data-science-how-to-create-machine-learning-service.md)**」に記載されています。データ サイエンスのプロセスの個々のステップについて参照するには、[マップ](machine-learning-data-science-how-to-create-machine-learning-service.md)内の該当する項目をクリックしてください。

この記事では、クラウド データ サイエンス環境をセットアップする際に検討するべき事項、このプロセスに役立つリソースやツール、クラウド データ サイエンスのプロセス マップの使用方法について説明します。

## 考慮事項

クラウド データ サイエンス環境の作成を開始する前に、次の点を検討してください。

1. **データはどこにあるか?** (この場所のことを***データ ソース***といいます。) たとえば、次のような場所があります。
	- HTTP アドレスに公開されていて、入手できるデータ。
	- ローカル/ネットワーク上のファイル位置に存在するデータ。
	- SQL Server データベースに格納されているデータ。
	- Azure ストレージ コンテナーに格納されているデータ。
2. **データはどのような形式か?** たとえば、次のような形式があります。
    - コンマまたはタブ区切りファイル (圧縮されていない)。
    - コンマまたはタブ区切りファイル (圧縮されている)。
	- 圧縮されている、または圧縮されていない Azure BLOB。
	- SQL Server テーブル。
3. **データのサイズはどの程度か?**
    - 小さい:2 GB 未満
    - 中程度:2GB 超、10GB 未満
	- 大きい:10GB 超
	- 非常に大きい:数百 GB
4. **データについてどの程度理解しているか?**
    - データを探索して、スキーマ、変数の分布、欠落値などを調査する必要があるか。 
	- データを表形式に変換する前に、前処理やクリーニングが必要か。 
5. **すべてのデータを Azure に移動することを計画しているか? またはそれが可能か?**
    - はい、処理のためにデータセット全体をクラウドにコピーする計画です。
	- いいえ、Azure にはデータのサブセットのみをコピーします。
6. **Azure クラウド内のデータの宛先はどこを希望しているか?** たとえば、次のような宛先があります。
	- Azure ストレージ BLOB にデータを移動する。
	- マウント可能な Azure 仮想ハード ディスクにデータを格納する。
	- Azure Virtual Machine 上の SQL Server データベースにデータを読み込む。
	- Azure HDInsight Hive テーブルにデータをマップする。

## クラウド データ サイエンスのための Azure リソース

シナリオによっては、次のリソースが必要になることもあります。

1.  Azure ツール: [Azure PowerShell SDK](install-configure-powershell.md)、[Azure ストレージ エクスプローラー](http://azurestorageexplorer.codeplex.com/)、[AzCopy](storage-use-azcopy.md)、その他
2.  SQL Server を実行する Azure Virtual Machine
3.  Azure HDInsight (Hadoop)
4.  Azure とオンプレミスのファイル共有に使用する Azure 仮想ネットワーク
5.  スケジュールされたデータ移動のための Azure データ ファクトリ


## クラウド データ サイエンスのプロセス マップを使用する方法

[クラウド データ サイエンスのプロセス マップ](machine-learning-data-science-how-to-create-machine-learning-service.md)は、データ サイエンスのさまざまな課題を提示しています。このマップは、典型的なデータ サイエンスのワークフローに含まれる主な手順を示しています。データ サイエンスのすべての課題で、これらの手順がすべて必要になるとは限りません。また、プロセスは反復的な性質をもち、手順を実行する順序が課題によって異なることもあります。上記の質問への回答は、特定のケースに関係のある手順を見極め、それらの手順がプロセス内のどのタイミングで必要になるか、手順の反復処理がどのような条件下で必要になるかを決定するために役立ちます。

元のデータのサイズ、データ ソースの場所、および Azure でのターゲット リポジトリに基づくサンプルのシナリオについては、「[Azure Machine Learning におけるクラウド データ サイエンスのプロセス](hmachine-learning-data-science-plan-sample-scenarios.md)」を参照してください。



<!--HONumber=49-->