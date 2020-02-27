---
title: データセットのデータ ドリフトを分析および監視する (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning データセット モニター (プレビュー) の作成、データセットでのデータ ドリフトの監視、およびアラートの設定を行います。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 401019c537cb0eb51fa6002637e170a79210f7d2
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617631"
---
# <a name="detect-data-drift-preview-on-datasets"></a>データセットでデータ ドリフトを検出する (プレビュー)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning データセット モニター (プレビュー) を作成する方法、データセットのデータ ドリフトと統計的変化を監視する方法、およびアラートを設定する方法について説明します。

Azure Machine Learning データセット モニターを使用すると、次のことを実行できます。
* **データのドリフトを分析**して、時間の経過と共にどのように変化するかを把握する。
* **モデル データを監視**して、トレーニング用データセットと供給データセットの違いを確認する。
* **新しいデータを監視**して、ベースライン データセットとターゲット データセットの違いを確認する。
* **データの特徴をプロファイリング**して、時間の経過と共に統計的な特性がどのように変化するかを追跡する。
* **データ ドリフトに関するアラートを設定**して、潜在的な問題を早期に警告する。 

メトリックと分析情報は、Azure Machine Learning ワークスペースに関連付けられている [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) リソースを通じて利用できます。

> [!Important]
> SDK を使用したデータ ドリフトの監視は、すべてのエディションで利用できることに注意してください。一方、Web で Studio を介してデータ ドリフトを監視できるのは Enterprise エディションだけです。

## <a name="prerequisites"></a>前提条件

データセット モニターを作成して使用するには、以下が必要です。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。
* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。
* [Azure Machine Learning SDK for Python がインストール済み](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) (これには azureml-datasets パッケージが含まれています)。
* データのファイル パス、ファイル名、または列にタイムスタンプが指定された構造化 (表形式) データ。

## <a name="what-is-data-drift"></a>データの誤差とは

機械学習においてデータの誤差とは、モデルのパフォーマンスの低下につながるモデルの入力データの変更のことです。 これはモデルの精度が時間の経過と共に低下する主な理由の 1 つであるため、データ ドリフトの監視はモデルのパフォーマンスに関する問題の検出に役立ちます。

データ ドリフトは、次のようなことが原因で発生します。 

- 上流プロセスの変更 (センサーを交換したため測定単位がインチからセンチメートルに変更された場合など)。 
- データ品質の問題 (センサーが破損しているため読み取り値が常に 0 になっている場合など)。
- データの自然なドリフト (平均気温が季節と共に変化する場合など)。
- 特徴間の関係の変化 (共変量シフト)。 

Azure Machine Learning データセット モニターを使用すると、時間の経過と共にデータセット内に発生するデータ ドリフトの検出に役立つアラートを設定することができます。 

### <a name="dataset-monitors"></a>データセット モニター 

データセット モニターを作成すると、データセット内の新しいデータのデータ ドリフトを検出してアラートを生成したり、履歴データからドリフトを分析したり、時間の経過に沿って新しいデータをプロファイリングしたりすることが可能になります。 データ ドリフトに関するアルゴリズムは、データの変化を総合的に測定するだけでなく、どの特徴に詳細な調査が必要であるかがわかるようになっています。 データセット モニターでは、`timeseries` データセット内の新しいデータをプロファイリングすることによって、ほかにも多数のメトリックが生成されます。 [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) を使用することで、モニターによって生成されるすべてのメトリックについてカスタム アラートを設定できます。 データセット モニターを使用すると、データの問題を迅速に検出し、考えられる原因を特定することによって問題のデバッグ時間を短縮できます。  

概念的には、Azure Machine Learning でデータセット モニターを設定するシナリオは主に 3 つあります。

シナリオ | 説明
---|---
モデルの供給データを監視して、モデルのトレーニング データからのドリフトを調べる | 供給データがトレーニング データからドリフトする場合にモデルの精度が低下すると考えた場合、このシナリオの結果は、代用品を監視してモデルの精度を調べたものと解釈できます。
時系列データセットを監視して、以前の期間からのドリフトを調べる。 | このシナリオはより一般的なものであり、モデル構築の上流または下流に関係するデータセットを監視するために使用できます。  ターゲット データセットにはタイムスタンプ列が必要ですが、ベースライン データセットにはターゲット データセットと共通の特徴が含まれる任意の表形式データセットを使用できます。
過去のデータに基づいて分析を実行する。 | このシナリオは、履歴データを解釈し、データセット モニターを設定する際の意思決定に反映させるために使用できます。

## <a name="how-dataset-can-monitor-data"></a>データセットによるデータの監視

Azure Machine Learning を使用すると、データセットを通じてデータ ドリフトが監視されます。 データの誤差を監視するには、基準データセット (通常は、モデルのトレーニング データセット) を指定します。 ターゲット データセット (通常はモデルの入力データ) は、時間の経過に沿ってベースライン データセットと比較されます。 この比較は、ターゲット データセットにはタイムスタンプ列が指定されている必要があるということを意味します。

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>ターゲット データセットでの `timeseries` 特性の設定

ターゲット データセットには、データ内の列またはファイルのパス パターンから派生した仮想列のいずれかにタイムスタンプ列を指定することにより、`timeseries` 特性が設定されている必要があります。 これは、Python SDK または Azure Machine Learning Studio を使用して行うことができます。 `timeseries` 特性をデータセットに追加するには、"狭間隔" のタイムスタンプを表す列を指定する必要があります。 データが "{yyyy/MM/dd}" などの時刻情報を含むフォルダー構造に分割されている場合は、パス パターン設定を使用して仮想列を作成して、"広間隔" のタイムスタンプとして設定し、時系列機能の重要度を向上させることができます。 

#### <a name="python-sdk"></a>Python SDK

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) クラスの [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) メソッドによって、データセットのタイムスタンプ列が定義されます。 

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

データセットの `timeseries` 特性を使用する完全な例については、[ノートブックの例](https://aka.ms/azureml-tsd-notebook)または [Datasets SDK のドキュメント](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)を参照してください。

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Azure Machine Learning Studio を使用してデータセットを作成する場合は、データへのパスにタイムスタンプ情報が含まれていることを確認し、データが存在するすべてのサブフォルダーを含め、パーティション形式を設定します。 

次の例では、サブフォルダー *NoaaIsdFlorida/2019* のすべてのデータが取得され、パーティション形式でタイムスタンプの年、月、日が指定されています。 

[![パーティション形式](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

**スキーマ**設定では、指定されたデータセットの仮想または実際の列からタイムスタンプ列を指定します。

![Timestamp](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>データセット モニターの設定

指定されたタイムスタンプ設定を使用してデータセットを作成したら、データセット モニターの構成に進むことができまあす。

データセット モニターの各種設定は、次の 3 つのグループに分類されます: **基本情報、モニターの設定、** および**バックフィルの設定**。

### <a name="basic-info"></a>基本情報

次の表に、データセット モニターに使用される基本的な設定を示します。

| 設定 | 説明 | ヒント | 変更可能 | 
| ------- | ----------- | ---- | ------- | 
| 名前 | データセット モニターの名前。 | | いいえ |
| ベースライン データセット | 時間の経過に沿ってターゲット データセットと比較するベースラインとして使用される表形式のデータセット。 | ベースライン データセットには、ターゲット データセットと共通の特徴が含まれている必要があります。 一般に、ベースラインは、モデルのトレーニング データセットか、ターゲット データセットのスライスに設定する必要があります。 | いいえ |
| ターゲット データセット | データ ドリフトの分析対象となる、タイムライン列が指定された表形式のデータセット。 | ターゲット データセットは、ベースライン データセットと共通の特徴を備え、かつ、新しいデータが追加される `timeseries` データセットである必要があります。 ターゲット データセット内の履歴データを分析することも、新しいデータを監視することもできます。 | いいえ | 
| 頻度 | バックフィルを実行する場合にパイプライン ジョブをスケジュールし、履歴データを分析するために使用される頻度。 オプションには、毎日、毎週、毎月があります。 | この設定を調整して、比較可能なデータ サイズをベースラインに含めます。 | いいえ | 
| [機能] | 時間の経過に沿ってデータ ドリフトが分析される特徴の一覧。 | 概念ドリフトが測定されるように、モデルで出力される特徴に設定します。 時間の経過と共に自然にドリフトする特徴 (月、年、インデックスなど) は含めないでください。 特徴の一覧を調整したら、既存のデータ ドリフト モニターをバックフィルできます。 | はい | 
| コンピューティング ターゲット | データセット モニター ジョブを実行する Azure Machine Learning コンピューティング ターゲット。 | | はい | 

### <a name="monitor-settings"></a>モニターの設定

以下は、スケジュールされたデータセット モニター パイプラインを作成する場合の設定です。 

| 設定 | 説明 | ヒント | 変更可能 | 
| ------- | ----------- | ---- | ------- |
| [有効化] | データセット モニター パイプラインのスケジュールを有効または無効にします | バックフィル設定を使用して履歴データを分析する場合は、スケジュールを無効にします。 これは、データセット モニターの作成後に有効にできます。 | はい | 
| Latency | データがデータセットに到達するのにかかる時間 (時間単位)。 たとえば、データが、データセットをカプセル化している SQL DB に到達するのに 3 日かかる場合は、待ち時間を 72 に設定します。 | データセット モニターの作成後に変更することはできません | いいえ | 
| メール アドレス | データ ドリフトのしきい値違反 (パーセンテージ単位) に基づいてアラートを送信するメール アドレス。 | メールは Azure Monitor 経由で送信されます。 | はい | 
| Threshold | メール アラートが送信される、データ ドリフトのしきい値 (パーセンテージ単位)。 | さらに、Application Insights リソースに関連付けられているワークスペース内の他のさまざまなメトリックについても、アラートやイベントを設定できます。 | はい | 

### <a name="backfill-settings"></a>バックフィルの設定

ここに挙げた設定は、データ ドリフトの各種メトリックについて過去のデータに対してバックフィルを実行するためのものです。

| 設定 | 説明 | ヒント |
| ------- | ----------- | ---- |
| 開始日 | バックフィル ジョブの開始日。 | | 
| 終了日 | バックフィル ジョブの終了日。 | 終了日は、開始日から 31* を超える頻度の時間単位には設定できません。 既存のデータセット モニターでメトリックをバックフィルすることで、履歴データを分析したり、更新した設定でメトリックを置き換えたりできます。 |

## <a name="create-dataset-monitors"></a>データセット モニターを作成する 

新しいデータセットでデータ ドリフトを検出してアラートを生成できるように、Azure Machine Learning Studio または Python SDK を使用してデータセット モニターを作成します。 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

データセット モニターにアラートを設定するには、モニターを作成するデータセットを含むワークスペースに Enterprise Edition の機能が必要です。 

ワークスペースの機能を確認したら、Studio のホームページに移動し、左側の [データセット] タブを選択します。 データセット モニターを選択します。

![モニターの一覧](./media/how-to-monitor-datasets/monitor-list.png)

**[+ モニターの作成]** をクリックし、 **[次へ]** をクリックしてウィザードの手順を続行します。

![ウィザード](./media/how-to-monitor-datasets/wizard.png)

結果のデータセット モニターが一覧に表示されます。 それを選択して、このモニターの詳細ページに移動します。

### <a name="from-python-sdk"></a>Python SDK

詳細については、[データ ドリフトに関する Python SDK リファレンス ドキュメント](/python/api/azureml-datadrift/azureml.datadrift)を参照してください。 

次の例では、Python SDK を使用してデータセット モニターを作成する方法を示します。

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

`timeseries` データセットとデータ ドリフト検出機能を設定する完全な例については、[サンプルのノートブック](https://aka.ms/datadrift-notebook)を参照してください。

## <a name="understanding-data-drift-results"></a>データ ドリフトの結果の解釈

データ モニターでは、次の 2 つのグループの結果が生成されます。ドリフトの概要と特徴の詳細。 次のアニメーションは、選択した特徴とメトリックに基づいて利用可能なドリフト モニターのグラフを示しています。 

![デモ ビデオ](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>ドリフトの概要

**[ドリフトの概要]** セクションには、データ ドリフトの大きさに関する概略的な分析情報と、より詳細な調査が必要な特徴が記載されています。 

| メトリック | 説明 | ヒント | 
| ------ | ----------- | ---- | 
| データ ドリフトの大きさ | 時間の経過と共に生じるベースラインとターゲットのデータセット間のドリフトがパーセンテージとして示されます。 0 から 100 までの範囲で示され、0 はデータセットが同一であることを表し、100 は Azure Machine Learning データ ドリフト機能によって 2 つのデータセットが完全に識別可能であることを表します。 | この大きさを生成するために使用されている機械学習の手法が原因で、測定されたパーセンテージの数値にはノイズの混入が想定されます。 | 
| 特徴ごとのドリフトへの影響 | ターゲット データセット内の各特徴が、測定されたドリフトの大きさに与える影響。 |  共変量シフトがあるため、基盤となる特徴の分布が特徴として比較的高い重要度になるように変更する必要は必ずしもありません。 | 

次の図は、Azure Machine Learning Studio の **[ドリフトの概要]** の結果に表示されたグラフの例です。これは、[NOAA Integrated Surface Data](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) のバックフィルに起因するものです。 データは `stationName contains 'FLORIDA'` に対してサンプリングされました。2019 年 1 月をベースライン データセットとして使用し、2019 年の全データをターゲットとして使用しています。
 
![ドリフトの概要](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>特徴の詳細

**[Feature details]\(特徴の詳細\)** セクションには、選択した特徴の分布の経時的変化に対する特徴レベルの分析情報と、その他の統計情報が含まれています。 

ターゲット データセットも時間の経過に沿ってプロファイリングされます。 各特徴のベースラインの分布間の統計的距離が、時間の経過と共にターゲット データセットと比較されます。これは、この統計的距離が個々の特徴を対象としているという点を除いて、データ ドリフトの大きさと概念的に似ています。 最小値、最大値、平均値も取得できます。 

Azure Machine Learning Studio では、グラフ内のデータ ポイントをクリックすると、表示されている特徴の分布がそれに応じて調整されます。 既定では、同じ特徴について、ベースライン データセットの分布と最近の実行時の分布が表示されます。 

これらのメトリックは、Python SDK で、`DataDriftDetector` オブジェクトに `get_metrics()` メソッドを実行して取得することもできます。 

#### <a name="numeric-features"></a>数値の特徴 

数値の特徴は、データセット モニターの実行ごとにプロファイリングされます。 Azure Machine Learning Studio では、次のものが公開されています。 分布の確率密度が表示されます。

| メトリック | 説明 |  
| ------ | ----------- |  
| ワッサースタイン距離 | ベースライン分布をターゲット分布に変換するための最小限の作業量。 |
| 平均値 | 特徴量の平均値。 |
| 最小値 | 特徴量の最小値。 |
| 最大値 | 特徴量の最大値。 |

![特徴の詳細の数値](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>カテゴリ別の特徴 

数値の特徴は、データセット モニターの実行ごとにプロファイリングされます。 Azure Machine Learning Studio では、次のものが公開されています。 分布のヒストグラムが表示されます。

| メトリック | 説明 |  
| ------ | ----------- |  
| ユークリッド距離 | ベースラインとターゲットの分布間の幾何学的距離。 |
| 一意の値の数 | 特徴の一意の値 (カーディナリティ) の数。 |


![特徴の詳細のカテゴリ](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>メトリック、アラート、イベント

メトリックは、Machine Learning ワークスペースに関連付けられている [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) リソースで照会できます。 そこから、カスタム アラート ルールの設定や、アクション (メール、SMS、プッシュ、音声、Azure 関数など) をトリガーするためのアクション グループの設定など、Application Insights のすべての機能にアクセスすることができます。 詳細については、Application Insights の包括的ドキュメントを参照してください。 

最初に、Azure portal に移動し、ワークスペースの **[概要]** ページを選択します。  関連付けられている Application Insights リソースが右端に表示されます。

[![Azure portal の概要](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

左側のペインの [監査] から [ログ (Analytics)] を選択します。

![Application Insights の概要](./media/how-to-monitor-datasets/ai-overview.png)

データセット モニターのメトリックは、`customMetrics` として格納されます。 データセット モニターの設定後、クエリを記述して実行すれば、それらを表示できます。

[![Log Analytics のクエリ](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

アラート ルールを設定するためのメトリックを確認したら、新しいアラート ルールを作成できます。

![新しいアラート ルール](./media/how-to-monitor-datasets/alert-rule.png)

既存のアクション グループを使用するか、または、新しいアクション グループを作成して、設定した条件が満たされたときに実行されるアクションを定義することができます。

![新しいアクション グループ](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>トラブルシューティング

制限事項と既知の問題:

* バックフィル ジョブの時間の範囲は、モニターの頻度設定である 31 サイクル間隔に制限されます。 
* 特徴一覧が指定されていない (すべての特徴を使用する) 場合、200 の特徴に制限されます。
* コンピューティング サイズは、データを処理できる十分な大きさにする必要があります。 
* 特定のモニターの実行について、開始日と終了日の範囲に該当するデータがデータセットに存在することを確認します。
* データセット モニターは、50 行以上を含むデータセットでのみ機能します。 

データセット内の列、つまり特徴は、次の表の条件に基づいてカテゴリまたは数値として分類されます。 特徴がこれらの条件を満たしていない場合 (たとえば、string 型の列に一意の値が 100 個以上含まれる場合)、その特徴はデータ ドリフト アルゴリズムから削除されますが、プロファイリングは引き続き行われます。 

| 特徴の種類 | データ型 | 条件 | 制限事項 | 
| ------------ | --------- | --------- | ----------- |
| Categorical | string、bool、int、float | 特徴内の一意の値の数は、100 個未満であり、かつ行数の 5% 未満であること。 | null 値は独自のカテゴリとして扱われます。 | 
| 数値 | int、float | 特徴内の値は数値データ型で、カテゴリの特徴の条件を満たしていません。 | 値の数の 15% を超える null が含まれる場合、その特徴は削除されます。 | 

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning Studio](https://ml.azure.com) または [Python ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb)に移動して、データセット モニターを設定する。
* [Azure Kubernetes Service にデプロイされたモデル](how-to-monitor-data-drift.md)でデータ ドリフトを設定する方法を確認する。
* [Event Grid](how-to-use-event-grid.md) を使用してデータセット ドリフト モニターを設定する。 
