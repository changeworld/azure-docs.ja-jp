<properties
	pageTitle="Azure Batch サービスの基本 |Microsoft Azure"
	description="大規模な並列ワークロードと HPC ワークロードに関する Azure Batch サービスの使用方法について説明します。"
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/16/2016"
	ms.author="marsma"/>

# Azure Batch の基礎

Azure Batch を使用すると、大規模な並列コンピューティングやハイ パフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行できます。Azure Batch は、多くのコンピューティング処理を要する作業を管理された仮想マシンの集合で実行するようにスケジュール設定するためのプラットフォーム サービスです。ジョブのニーズに合わせてコンピューティング リソースを自動的に拡大/縮小できます。

Batch サービスでは、複数のアプリケーションを並列で大規模に実行するための Azure コンピューティング リソースを定義します。オンデマンドのジョブやスケジュールされたジョブを実行することができ、HPC クラスター、個々の仮想マシン、仮想ネットワークや、複雑なジョブとタスクのスケジュール インフラストラクチャを手動で作成、構成、管理する必要がありません。

## Batch の使用例

Batch は、*バッチ処理*または*バッチ コンピューティング*のために使用される管理された Azure サービスであり、期待した結果が得られるように類似のタスクを大量に実行します。バッチ コンピューティングは主に、大量のデータを定期的に処理し、変換し、分析する組織で使用されます。

Batch は本質的に並列 (「驚異的並列」とも呼ばれています) のアプリケーションとワークロードと効果的に連動します。本質的に並列のワークロードは複数のタスクに簡単に分割されます。複数に分割されたタスクは複数のコンピューターで同時に実行されます。

![並列タスク][1]<br/>

この手法で一般的に処理されるワークロードの例には次のものがあります。

* 財務リスクのモデリング
* 気候や水文学データの分析
* イメージの表示、分析、処理
* メディアのエンコードとコード変換
* 遺伝子配列の分析
* 工学応力の分析
* ソフトウェアのテスト

また、Batch を使用すると、最終的により少ないステップで並列計算を実行できるほか、[Message Passing Interface (MPI)](batch-mpi.md) アプリケーションなどのより複雑な HPC ワークロードを実行できます。

Batch と Azure 内の他の HPC ソリューション オプションとの比較については、「[Batch と HPC ソリューション](batch-hpc-solutions.md)」を参照してください。

## Batch を使用した開発

並列ワークロード処理に Azure Batch を使用するソリューションを構築するとき、[Batch API](#batch-development-apis) を利用し、プログラミングでそれを実行できます。Batch API を使用すると、コンピューティング ノード (仮想マシン) のプールを作成して管理し、そのノードで実行するジョブとタスクのスケジュールを設定できます。作成したクライアント アプリケーションまたはサービスで Batch API を使用し、Batch サービスと通信します。

組織の大規模なワークロードを効率的に処理したり、顧客が 1 つのノード、数百のノード、数千のノードでも、必要なときに、またはスケジュールに基づきジョブやタスクを実行できるように、顧客にサービス フロントエンドを提供したりできます。また、Batch を大規模なワークフローの一部として使用し、[Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md) などのツールで管理できます。

> [AZURE.TIP] Batch API の機能をもっと詳しく理解するには、「[開発者向け Batch 機能の概要](batch-api-basics.md)」をご覧ください。

### 必要となる Azure アカウント

Batch ソリューションを開発するとき、Microsoft Azure で次のアカウントが必要になります。

- **Azure アカウントとサブスクリプション** - Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典][msdn_benefits]を有効にするか、[無料 Azure アカウント][free_account]にサインアップしてください。アカウントを作成すると、既定のサブスクリプションが自動的に作成されます。

- **Batch アカウント** - アプリケーションが Batch サービスと連動するとき、アカウント名、アカウントの URL、アクセス キーが資格情報として使用されます。プール、コンピューティング ノード、ジョブ、タスクなどの Batch リソースはすべて Batch アカウントに関連付けられています。Azure ポータルで [Batch アカウントを作成して管理](batch-account-create-portal.md)できます。

- **ストレージ アカウント** - Batch には、[Azure Storage][azure_storage] のファイルを操作するためのサポートが組み込まれています。ほぼすべての Batch シナリオで、Azure Storage が使用されます。タスクで実行されるプログラムや、プログラムで処理されるデータのステージングに使用されたり、プログラムで生成される出力データの格納に使用されたりします。ストレージ アカウントの作成方法については、「[Azure ストレージ アカウントについて](./../storage/storage-create-storage-account.md)」を参照してください。

### Batch 開発 API

アプリケーションとサービスは、直接 REST API 呼び出しを発行し、以下のクライアント ライブラリのいずれかを使用するか、両方を組み合わせて、コンピューティング リソースを管理したり、Batch サービスを使用して大規模な並列ワークロードを実行したりすることができます。

| API | API リファレンス | ダウンロード | コード サンプル |
| ----------------- | ------------- | -------- | ------------ |
| **Batch REST** | [MSDN][batch_rest] | 該当なし | [MSDN][batch_rest] |
| **Batch .NET** | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **Batch Python** | [readthedocs.io][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Batch Node.js** | [github.io][api_nodejs] | [npm][api_nodejs_npm] | - |
| **Batch Java** (プレビュー) | [github.io][api_java] | [Maven スナップショット リポジトリ][api_java_jar] | - |

### Batch リソース管理

クライアント API の他に、以下のものを使用して、Batch アカウント内のリソースを管理することもできます。

- [Batch PowerShell コマンドレット][batch_ps]\: [Azure PowerShell](../powershell-install-configure.md) モジュールの Azure Batch コマンドレットを利用すると、PowerShell で Batch リソースを管理できます。

- [Azure CLI](../xplat-cli-install.md): Azure コマンド ライン インターフェイス (Azure CLI) は、Batch などの多くの Azure サービスを操作するためのシェル コマンドを提供するクロスプラットフォーム ツールセットです。

- [Batch Management .NET](batch-management-dotnet.md) クライアント ライブラリ: これも [NuGet][api_net_mgmt_nuget] から入手できます。Batch Management .NET クライアント ライブラリを利用して、Batch アカウント、クォータ、およびアプリケーション パッケージをプログラミングで管理できます。管理ライブラリのリファレンスは、[MSDN][api_net_mgmt] にあります。

## シナリオ: 並列ワークロードをスケールアウトする

Batch API を利用し、Batch サービスとやりとりする一般的なソリューションには、コンピューティング ノードのプールでの本質的に並列な作業の拡張があります。3D シーンのイメージのレンダリングなどです。このようなコンピューティング ノードのプールは「レンダー ファーム」となり、たとえば、数十、数百、さらには数千のコアをレンダリング ジョブに提供します。

次の図は一般的な Batch ワークフローを示しています。クライアント アプリケーションまたはホステッド サービスが Batch を利用し、並列ワークロードを実行します。

![Batch ソリューション ワークフロー][2]

この一般的なシナリオで、アプリケーションまたはサービスは、次の手順を実行することで Azure Batch のコンピューティング ワークロードを処理します。

1. **入力ファイル**とそのファイルを処理する**アプリケーション**を Azure ストレージ アカウントにアップロードします。アプリケーションが処理するあらゆるデータが入力ファイルになります。財務モデリング データや変換する動画ファイルなどです。データを処理するためのあらゆるアプリケーションがアプリケーション ファイルになります。3D レンダリング アプリケーションやメディア トランスコーダーなどです。

2. Batch アカウントでコンピューティング ノードの Batch **プール**を作成します。これらのノードは、タスクを実行する仮想マシンです。[ノード サイズ](./../cloud-services/cloud-services-sizes-specs.md)、オペレーティング システム、ノードがプールに参加するときにインストールするアプリケーション (手順 1. でアップロードしたアプリケーション) の Azure Storage における場所などのプロパティを指定します。[自動的に拡張](batch-automatic-scaling.md)するようにプールを構成することもできます。タスクによって生成されるワークロードに応じて、プール内のコンピューティング ノードの数が動的に調整されます。

3. コンピューティング ノードのプールでワークロードを実行する Batch **ジョブ**を作成します。ジョブを作成するとき、ジョブを Batch プールに関連付けます。

4. ジョブに**タスク**を追加します。ジョブにタスクを追加すると、Batch サービスは、プールのコンピューティング ノードでタスクを実行するように自動的にスケジュールします。各タスクでアップロードしたアプリケーションが使用され、入力ファイルが処理されます。

    - 4a.タスクが実行される前に、割り当てられているコンピューティング ノードに処理するデータ (入力ファイル) をダウンロードできます。アプリケーションがノードにインストールされていない場合 (手順 2 参照)、代わりにここでダウンロードできます。ダウンロードが完了すると、割り当てられているノードでタスクが実行されます。

5. タスクが実行されたら、Batch にクエリを実行し、ジョブとそのタスクの進捗状況を監視できます。クライアント アプリケーションまたはサービスは HTTPS 経由で Batch サービスと通信します。数千のコンピューティング ノードで実行されている数千のタスクを監視することがあるので、[Batch サービスには効率的にクエリを実行](batch-efficient-list-queries.md)してください。

6. タスクが完了すると、結果データを Azure Storage にアップロードできます。コンピューティング ノードからファイルを直接取得することもできます。

7. ジョブのタスクが完了したことが監視により検出された場合、クライアント アプリケーションまたはサービスで出力データをダウンロードし、さらに処理したり、評価したりできます。

これは Batch の利用方法の 1 つに過ぎません。このシナリオでは、利用できる機能のほんの一部のみを紹介しました。たとえば、各コンピューティング ノードで[複数のタスクを並列に](batch-parallel-node-tasks.md)実行できます。また、[ジョブの準備と完了のタスク](batch-job-prep-release.md)を利用し、ジョブのノードを準備し、後で消去できます。

## 次のステップ

これで Batch サービスの概要を確認できました。次は、深く掘り下げ、コンピューティング リソースが大量に使われる並列ワークロードをサービスで処理する方法を学習してください。

- 「[開発者向け Batch 機能の概要](batch-api-basics.md)」をお読みください。ワークロードを処理するための Batch の API 機能について詳しく説明しています。Batch の使用を準備しているユーザーはぜひご確認ください。

- 「[.NET 向け Azure Batch ライブラリの概要](batch-dotnet-get-started.md)」では、C# と Batch .NET ライブラリを利用し、一般的な Batch ワークフローを使用して簡単なワークロードを実行する方法を学習できます。これは Batch サービスの使用方法を学習するときに最初に参考にするページの 1 つです。また、[Python 向け](batch-python-tutorial.md)のチュートリアルも用意されています。

- [GitHub のサンプル コード][github_samples]をダウンロードし、C# と Python の両方について、Batch とやり取りしてサンプル ワークロードのスケジュール設定と処理を実行する方法を確認してください。

- [Batch ラーニング パス][learning_path]で、Batch の使用方法を学習する際に利用できるリソースを確認してください。

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://adxsnapshots.azurewebsites.net/?dir=com%5cmicrosoft%5cazure%5cazure-batch
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

<!---HONumber=AcomDC_0622_2016-->