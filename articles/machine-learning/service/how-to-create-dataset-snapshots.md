---
title: スナップショットを使用して一定期間にわたるデータを比較、再現する
titleSuffix: Azure Machine Learning service
description: データセットのスナップショットを使用して、一定期間にわたるデータを比較したり再現性を確保したりする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/02/2019
ms.openlocfilehash: 51d0dcfc543834e9a8725d11fa82b566a5132a6b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204987"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>スナップショット (プレビュー) を使用したデータの比較と再現性の確保

この記事では、一定期間にわたるデータをキャプチャまたは比較できるよう、[Azure Machine Learning Datasets](how-to-create-register-datasets.md) (データセット) のスナップショットを作成して管理する方法について説明します。 データセットを使用すると、クラウドのデータにアクセスして操作することが、さまざまなシナリオで容易になります。

**データセットのスナップショット**には、データの作成時点でのプロファイル (サマリー統計) が格納されます。 また、再現性を確保する目的でスナップショットにデータのコピーを格納することもできます。

>[!Important]
> スナップショットには、ストレージ コストがかかります。 データのコピーをスナップショットに格納することで、さらに多くの保存スペースが必要となります。 不要になったら [`dataset.delete_snapshot()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) を使用してください。

## <a name="when-to-use-snapshots"></a>スナップショットを使用する目的

スナップショットの主な目的は次の 3 つです。

+ **モデルの検証**: トレーニングの実行間で、またはトレーニング データと本番データ間で、異なるスナップショットのデータ プロファイルを比較します。

+ **モデルの再現性**: トレーニング時のデータを含んだスナップショットを呼び出すことによって結果を再現します。

+ **一定期間にわたるデータの追跡**: [プロファイルを比較](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)することによってデータセットの変化を確認します。
  
## <a name="prerequisites"></a>前提条件

データセットのスナップショットを作成するには、登録済みの Azure Machine Learning Datasets が必要です。 まだデータセットがない場合は、[データセットの作成と登録](how-to-create-register-datasets.md)に関するページを参照してください。

## <a name="create-dataset-snapshots"></a>データセットのスナップショットを作成する

データセットのスナップショットを作成するには、Azure Machine Learning SDK の [`dataset.create_snapshot()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) を使用します。

既定では、最新の[データセット定義](how-to-manage-dataset-definitions.md)が適用されたデータのプロファイル (サマリー統計) がスナップショットに格納されます。 データの変換ステップが定義されている場合は、データセット定義にその記録が含まれます。 このしくみを利用して、データの準備作業を再現することができます。

`create_data_snapshot = True` を追加すれば、必要に応じてデータのコピーをスナップショットに含めることができます。  このデータを再現性の確保に役立てることができます。

この例では、[データセットの作成と登録](how-to-create-register-datasets.md)に関する記事で作成したデータセット (`dataset_crime`) と[サンプルの犯罪データ](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv)を使用しています。

```Python
from azureml.core.workspace import Workspace
from azureml.core.dataset import Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.datasets['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```

スナップショットは非同期的に作成されるため、そのプロセスは [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-) メソッドを使用して監視します。

```Python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

出力:

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

不要になったら [`dataset.delete_snapshot()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) を使用してください。

## <a name="find-snapshots"></a>スナップショットを検索する

既存のスナップショットを取得するには、[`get_snapshot()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-) を使用します。

特定のデータセットについて保存したスナップショットのリストを取得するには、[`get_all_snapshots()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--) を使用します。

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>スナップショットからデータとプロファイルを取得する

いずれのスナップショットも、サマリー統計を含んだデータセットのプロファイルが生成され、また、データのコピーを保存するためのオプションが用意されています。

### <a name="get-the-data-profile"></a>データ プロファイルを取得する

データのプロファイルにアクセスするには、[`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) を使用します。  このプロファイルを使用すれば、たとえばトレーニング データと本番データを比較することができます。

```Python
snapshot.get_profile()
```

||Type|Min|max|Count|Missing Count|Not Missing Count|Percent missing|Error Count|Empty count|0.1% Quantile|1% Quantile|5% Quantile|25% Quantile|50% Quantile|75% Quantile|95% Quantile|99% Quantile|99.9% Quantile|平均|標準偏差|variance|傾斜|尖度
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
事件番号|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
ブロック|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
プライマリ タイプ|FieldType.STRING|DECEPTIVE PRACTICE|THEFT|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
説明|FieldType.STRING|BOGUS CHECK|OVER $500|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
場所の説明|FieldType.STRING||SCHOOL, PUBLIC, BUILDING|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
逮捕|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
国内|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Beat|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
District|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
区|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
コミュニティ エリア|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379

### <a name="get-the-data-from-the-snapshot"></a>スナップショットからデータを取得する

データセットのスナップショットに保存されたデータのコピーを取得するには、[`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--) メソッドで pandas DataFrame を生成します。

スナップショットの作成時にデータのコピーが要求されなかった場合、このメソッドは失敗します。

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID|事件番号|Date|ブロック|IUCR|プライマリ タイプ|説明|場所の説明|逮捕|国内|...|区|コミュニティ エリア|FBI コード|X 座標|Y 座標|年|更新日|Latitude|Longitude|Location
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|DECEPTIVE PRACTICE|FINANCIAL IDENTITY THEFT OVER $ 300|OTHER|False|False|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|890|THEFT|FROM BUILDING|RESIDENCE|False|False|...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|1154|DECEPTIVE PRACTICE|FINANCIAL IDENTITY THEFT $300 AND UNDER|RESIDENCE|False|False|...|19|74|11|(NaN)|(NaN)|2016|2016-05-12 15:50:00|(NaN)|(NaN)|

## <a name="next-steps"></a>次の手順

* 設計パターンと使用例については、SDK の [概要](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を参照してください

* データセットのスナップショットの使用例については、[サンプル ノートブック](https://aka.ms/dataset-tutorial)を参照してください。
