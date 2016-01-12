<properties
	pageTitle="Azure Batch サービスの基本 |Microsoft Azure"
	description="大規模な並列ワークロードと HPC ワークロードに関する Azure Batch サービスの使用方法について説明します。"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/19/2015"
	ms.author="danlep"/>

# Azure Batch の基礎

Azure Batch を使用すると、大規模な並列コンピューティングやハイ パフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行できます。Azure Batch は、多くのコンピューティング処理を要する作業を仮想マシン (コンピューティング ノード) の管理されたコレクション上で実行するようにスケジュール設定するためのプラットフォーム サービスです。ジョブのニーズに合わせてコンピューティング リソースをスケールすることができます。Batch サービスでは、Azure のコンピューティング リソースと大規模なバッチ ジョブをオンデマンドで、またはスケジュールに従って実行するようにプログラムで定義します。HPC クラスター、個々の仮想マシン、仮想ネットワーク、またはジョブ スケジューラの構成と管理を手動で行う必要がありません。

## ユース ケース

Batch は、 *バッチ処理* または *バッチ コンピューティング* のための管理されたサービスで、期待した結果が得られるように類似のタスクを大量に実行します。バッチ コンピューティングは、スケジュールに従って、またはオンデマンドで大量のデータの処理、変換、および分析を行う組織にとっては一般的なアプローチで、金融サービスからエンジニアリングまで各種分野で実践されています。

Batch は、本質的に並列である ("驚異的並列性" とも呼ばれます) アプリケーションやワークロードに対応するため、複数のコンピューターで並列タスクを実行するのに役立ちます。図 1 を参照してください。

![並列タスク][parallel]

**図 1:複数のコンピューターで実行される並列タスク**

たとえば、次のようになります。

* 財務リスクのモデリング
* 画像のレンダリングと画像処理
* メディアのエンコードとコード変換
* 遺伝子配列の分析
* ソフトウェアのテスト

また、Batch を使用すると、最終的により少ないステップで並列計算を実行できるほか、Message Passing Interface (MPI) アプリケーションなどのより複雑な HPC ワークロードを実行できます。

>[AZURE.NOTE]現時点では、Batch は、Windows Server ベースの仮想マシンで実行されるワークロードのみをサポートしています。

Batch と Azure 内の他の HPC ソリューション オプションとの比較については、「[Batch と HPC ソリューション](batch-hpc-solutions.md)」を参照してください。

## Batch を使用した開発

Batch API を使用した開発では、コンピューティング ノードのプールの作成と管理、およびプールで実行されるジョブとタスクのスケジュール設定を行います。オンデマンドで、スケジュールに従って、または [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) などのツールによって管理される大きなワークフローの一部として、ジョブやタスクを実行するクライアント アプリまたはフロントエンドを作成します。

Baｔｃｈ の概念について詳しくは、「[Azure Batch の API の基本](batch-api-basics.md)」をご覧ください。

### 必要となるアカウント

+ **Azure アカウントとサブスクリプション** - アカウントを持っていない場合は、[MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

+ **Batch アカウント** - Batch の API 呼び出しを行う際に、Batch アカウントの名前と URL、およびアクセス キーを資格情報として使用します。コンピューティング ノード、プール、ジョブ、タスクなどの Batch リソースはすべて Batch アカウントに関連付けられています。Batch アカウントを作成してそのアカウントのアクセス キーを管理する 1 つの方法として、[Azure ポータル](batch-account-create-portal.md)を使用します。

+ **ストレージ アカウント** - ほとんどの Batch シナリオでは、データの入力と出力、およびコンピューティング ノード上で実行されるスクリプトや実行可能ファイルを格納するために、Azure ストレージ アカウントが必要になります。ストレージ アカウントの作成方法については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。

### Batch 開発ライブラリおよびツール

Azure Batch アプリケーションの開発と管理には、以下の .NET ライブラリとツールを使用します。

+ [Batch クライアント向け .NET ライブラリ](http://www.nuget.org/packages/Azure.Batch/) (NuGet) - Batch サービスを使用してジョブを実行するクライアント アプリケーションの開発
+ [Batch 管理向け .NET ライブラリ](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – Batch アカウントの管理
+ [Batch Explorer](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub) - Batch アカウント内のリソース (ジョブとタスク、コンピューティング ノードとプール、コンピューティング ノード上のファイルなど) を参照、アクセス、更新するための GUI アプリケーションの構築[ブログの投稿](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx)をご覧ください。


## シナリオ: 並列ワークロードをスケールアウトする

Batch API を使用する一般的なシナリオでは、数千個ものコンピューティング コアを提供するコンピューティング プールでのイメージ レンダリングのような、本質的に並列な作業があります。

図 2 は、Batch で並列ワークロードを実行する Batch .NET クライアント アプリケーションを使用するワークフローを示しています。


![作業項目のワークフロー][work_item_workflow]

**図 2: Batch の並列ワークロードをスケールアウトする**

1.	ジョブに必要な入力ファイル (ソース データやイメージ) を Azure ストレージ アカウントにアップロードします。ジョブ タスクが実行されると、Batch サービスはコンピューティング ノードにファイルを読み込みます。

2.	コンピューティング ノードで実行されるプログラム ファイルまたはスクリプトをストレージ アカウントにアップロードします。これらには、バイナリ ファイルとその依存アセンブリが含まれる場合があります。タスクが実行されると、Batch サービスはコンピューティング ノードにもこれらのファイルを読み込みます。

3.	プログラムによって Batch アカウントに Batch コンピューティング ノードのプールを作成し、サイズや実行する OS などのプロパティを指定します。また、ワークロードに対応してプール内のノードの数を[スケールアップまたはスケールダウン](batch-automatic-scaling.md)する方法も定義できます。タスクを実行すると、このプールからノードが割り当てられます。

4.	ノードのプールでワークロードを実行する Batch ジョブをプログラムで定義します。

5.	ジョブにタスクを追加します。各タスクは、アップロードされたプログラムを使用して、アップロードされたファイルの情報を処理します。実際のワークロードに応じて、リソースの使用効率を最大限に高めるために、各コンピューティング ノードで[複数のタスクを同時に](batch-parallel-node-tasks.md)実行できます。また、Batch では、スケジュールされたタスクを実行する前にコンピューティング ノードを準備し、完了後にクリーンアップする、[ジョブの準備と完了に特化したタスク](batch-job-prep-release.md)をサポートしています。

6.	Batch ワークロードを実行し、進行状況と結果を監視します。アプリケーションは、HTTPS 経由で Batch サービスと通信します。多数のプール、ジョブ、タスクを監視する際にアプリケーションのパフォーマンスを向上するには、Batch サービスに[クエリを実行する効率的な方法](batch-efficient-list-queries.md)を利用できます。






## 次のステップ

* [Batch クライアント向け .NET ライブラリ](batch-dotnet-get-started.md)を使用して最初のアプリケーション開発を始める
* [GitHub](https://github.com/Azure/azure-batch-samples) の Batch コード サンプルを参照する

[parallel]: ./media/batch-technical-overview/parallel.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png

<!---HONumber=AcomDC_0107_2016-->