---
title: "Azure ML の ALM | Microsoft Docs"
description: "Azure Machine Learning Studio でのアプリケーション ライフサイクル管理のベスト プラクティスの適用"
keywords: "ALM、AML、Azure ML、アプリケーション ライフ サイクル管理、バージョン管理"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1be6577d-f2c7-425b-b6b9-d5038e52b395
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: haining
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 614aa45b365abe90fcc2e75fcde50f1c62a95ed8


---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio でのアプリケーション ライフサイクル管理
Azure Machine Learning Studio は、機械学習の実験を開発し、それを Azure クラウドで運用可能にするためのツールです。 これは Visual Studio IDE に似ており、1 つのプラットフォームにマージされているスケーラブルな Web サービス ホスティング サービスです。 そのため、さまざまな資産のバージョン管理から自動化された実行とデプロイまで、標準的な ALM (アプリケーション ライフサイクル管理) プラクティスの Azure Machine Learning Studio への 組み込み対してのみ論理的/自然になります。 この記事では、一部のオプションとアプローチについて説明します。 

## <a name="versioning-experiment"></a>実験のバージョン管理
実験のバージョン管理に推奨される方法は 2 つあります。 組み込みの実行履歴に依存するか、JSON 形式で実験をエクスポートし、外部で管理します。 それぞれのアプローチには長所と短所があります。

### <a name="experiment-snapshots-using-run-history"></a>実行履歴を使用した実験のスナップショット
Azure Machine Learning の実験の実行モデルでは、実験エディターで [実行] ボタンをクリックするたびに、実験の変更不可スナップショットがジョブ スケジューラに送信されます。 このスナップショットの一覧は、実験エディター ビューのコマンド バーで、[実行履歴] ボタンをクリックして表示できます。

![[実行履歴] ボタン](media/machine-learning-version-control/runhistory.png)

実験が実行のために送信されてスナップショットが取得された時点の実験の名前をクリックすると、スナップショットをロック モードで開くことができます。 現在の実験を表す、一覧の最初の項目のみが編集可能な状態になります。 また、各スナップショットは、[Finished (Partial run) (完了 (部分的に実行))]、[失敗]、[Failed (Partial run) (失敗 (部分的に実行))]、[ドラフト] などのさまざまな状態になります。

![[実行履歴] リスト](media/machine-learning-version-control/runhistorylist.png)

開いたら、スナップショットの実験を新しい実験として保存し、それを変更できます。 1 つの注意点として、実験のスナップショットにトレーニング済みのモデル、変換、データセットなど、更新バージョンがある資産が含まれている場合、スナップショットにはスナップショット取得時の元のバージョンへの参照が保持されます。 しかし、ロックされているスナップショットを新しい実験として保存する場合は、ML Studio によってこれらの資産の新しいバージョンの存在が検出され、最新バージョンに自動的に更新されます。 

また、実験を削除した場合、その実験のスナップショットもすべて削除されます。

### <a name="exportimport-experiment-in-json-format"></a>JSON 形式で実験のエクスポートとインポート
実行履歴のスナップショットは、実行のために送信されるたびに実験の変更不可バージョンを ML Studio に保持しますが、実験のローカル コピーを保存し、Team Foundation Server などのお気に入りのソース管理システムにチェックインして、後でそのローカル ファイルから実験を再作成したい場合があります。 [Azure ML PowerShell](http://aka.ms/amlps) コマンドレット [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) と [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) を使用して、これを実現できます。

JSON ファイルは実験グラフのテキスト表現ですが、データセットやトレーニング済みのモデルなど、ワークスペース内の資産への参照を含む場合があります。 ただし、このような資産のシリアル化されたバージョンは含まれていません。 このため、JSON ドキュメントをワークスペースにもう一度インポートしようとする場合、これらの参照先資産は実験で参照されているのと同じ資産 ID で既に存在する必要があり、そうでないとインポートされた実験にアクセスできません。

## <a name="versioning-trained-model"></a>トレーニング済みのモデルのバージョン管理
Azure ML のトレーニング済みのモデルは .iLearner ファイルと呼ばれる形式にシリアル化され、ワークスペースに関連付けられている Azure Blob Storage アカウントに格納されます。 ILearner ファイルのコピーを保持する 1 つの方法は、再トレーニング API を使用することです。 この[記事](machine-learning-retrain-models-programmatically.md)では、再トレーニング API の動作について詳しく説明します。 手順の概要は次のとおりです。

1. トレーニング実験を設定します。
2. モデルのトレーニング モジュール、またはモデル ハイパーパラメーターの調整モジュールや R モデルの作成モジュールなど、トレーニング済みのモデルを生成するモジュールに、Web サービス出力ポートを追加します。
3. トレーニング実験を実行し、モデルのトレーニング Web サービスとしてデプロイします。 
4. トレーニング Web サービスの BES エンドポイントを呼び出し、必要な .iLearner ファイル名とそれを格納する Azure Blob Storage アカウントの場所を指定します。
5. BES の呼び出しが終了した後で、生成された .iLearner ファイルを収集します。

.ILearner ファイルを取得するもう 1 つの方法は、PowerShell コマンドレット [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput) を使用することです。 これはモデルをプログラムによって再トレーニングする必要なしに iLearner ファイルのコピーを取得する場合に簡単になります。

トレーニング済みのモデルを含む .iLearner ファイルを作成したら、名前付け規則としての接頭辞/接尾辞の適用や Azure Blob Storage に .iLearner ファイルを残すだけの単純な戦略から、バージョン管理システムへのコピー/インポートまで、独自のバージョン管理戦略を使用できます。

保存されている .iLearner ファイルは、デプロイされた Web サービスを通じてスコアリングに使用できます。

## <a name="versioning-web-service"></a>バージョン管理 Web サービス
Azure ML の実験から、2 種類の Web サービスをデプロイできます。 従来の Web サービスは、実験およびワークスペースと緊密に結合されています。 新しい Web サービスは Azure リソース管理フレームワークを活用しており、元の実験とワークスペースのどちらとも結合されなくなりました。 

### <a name="classic-web-service"></a>従来の Web サービス
従来の Web サービスをバージョン管理するには、Web サービス エンドポイントのコンストラクトを利用できます。 典型的なフローを次に示します。

1. 予測実験から、既定のエンドポイントを含む新しい従来の Web サービスをデプロイします。
2. 次に、現在のバージョンの実験/トレーニング済みのモデルを公開する ep2 という名前の新しいエンドポイントを作成します。
3. 戻って予測実験およびトレーニング済みのモデルを更新します。
4. 予測実験を再デプロイでき、それによって既定のエンドポイントが更新されます。 しかし、ep2 は変更されません。
5. ここで、新しいバージョンの実験とトレーニング済みのモデルを公開する追加のエンドポイント ep3 を作成できます。 
6. 必要に応じて手順 3 に戻ります。

時間の経過と共に多くのエンドポイントが同じ Web サービスで作成される場合があり、それぞれが特定の時点のバージョンのトレーニング済みのモデルを含む特定の時点の実験のコピーを表します。 次に、外部のロジックを使用して、呼び出すエンドポイントを決定します。これは事実上、スコア付けの実行用のトレーニング済みのモデルのバージョンを選択することを意味します。

また、多くの同じ Web サービス エンドポイントを作成し、異なるバージョンの .iLearner ファイルのパッチをエンドポイントに適用して、同様の効果を実現することもできます。 この[記事](machine-learning-create-models-and-endpoints-with-powershell.md)では、これを実現する方法について詳しく説明します。

### <a name="new-web-service"></a>新しい Web サービス
新しい Azure Resource Manager ベースの Web サービスを作成する場合は、エンドポイント コンストラクトを使用できなくなります。 代わりに、[Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell コマンドレットを使用するか、既にデプロイされた Resource Manager ベースの Web サービスから [*Export-AzureRmMlWebservice*](https://msdn.microsoft.com/library/azure/mt767935.aspx) PowerShell コマンドレットを使用して、予測実験から WSD (Web サービス定義) ファイルを JSON 形式で生成できます。 

WSD ファイルをエクスポートしたら、それをバージョン管理します。 別の Azure リージョンで別の Web サービス プランの新しい Web サービスとして WSD をデプロイすることもできます。 適切なストレージ アカウント構成だけではなく、新しい Web サービス プラン ID を指定してください。 異なる .iLearner ファイルにパッチを適用するには、WSD ファイルを変更し、トレーニング済みのモデルの場所の参照を更新し、新しい Web サービスとしてデプロイします。

## <a name="automate-experiment-execution-and-deployment"></a>実験の実行とデプロイの自動化
ALM の重要な側面は、アプリケーションの実行とデプロイのプロセスを自動化できるようにすることです。 Azure ML では、[PowerShell モジュール](http://aka.ms/amlps)を使用してこれを実現できます。 ここでは、[Azure ML Studio PowerShell モジュール](http://aka.ms/amlps)を使用して標準的な ALM 自動化実行/デプロイ プロセスに関連するエンド ツー エンドの手順の例を示します。 各手順は、その手順の実行に利用できる 1 つまたは複数の PowerShell コマンドレットにリンクされています。

1. [データセットをアップロード](https://github.com/hning86/azuremlps#upload-amldataset)します。 
2. [ワークスペース](https://github.com/hning86/azuremlps#copy-amlexperiment)または[ギャラリー](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)からワークスペースにトレーニング実験をコピーするか、[エクスポート](https://github.com/hning86/azuremlps#export-amlexperimentgraph)された実験をローカル ディスクから[インポート](https://github.com/hning86/azuremlps#import-amlexperimentgraph)します。
3. トレーニング実験で[データセットを更新](https://github.com/hning86/azuremlps#update-amlexperimentuserasset)します。
4. [トレーニング実験を実行](https://github.com/hning86/azuremlps#start-amlexperiment)します。
5. [トレーニング済みのモデルを昇格](https://github.com/hning86/azuremlps#promote-amltrainedmodel)させます。
6. ワークスペースに[予測実験をコピー](https://github.com/hning86/azuremlps#copy-amlexperiment)します。
7. 予測実験で[トレーニング済みのモデルを更新](https://github.com/hning86/azuremlps#update-amlexperimentuserasset)します。
8. [予測実験を実行](https://github.com/hning86/azuremlps#start-amlexperiment)します。
9. 予測実験から [Web サービスをデプロイ](https://github.com/hning86/azuremlps#new-amlwebservice)します。
10. Web サービス [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) または [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) エンドポイントをテストします。 

## <a name="next-steps"></a>次のステップ
* [Azure ML Studio PowerShell](http://aka.ms/amlps) モジュールをダウンロードし、ALM タスクの自動化を開始します。
* PowerShell および再トレーニング API を通じて、[1 つの実験を使用して多数の ML モデルを作成および管理](machine-learning-create-models-and-endpoints-with-powershell.md)する方法を説明します。
* [Azure ML Web サービスのデプロイ](machine-learning-publish-a-machine-learning-web-service.md)の詳細について説明します。




<!--HONumber=Nov16_HO3-->


