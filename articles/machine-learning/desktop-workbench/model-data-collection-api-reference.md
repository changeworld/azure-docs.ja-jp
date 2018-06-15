---
title: Azure Machine Learning モデル データ収集 API リファレンス | Microsoft Docs
description: Azure Machine Learning モデル データ収集 API のリファレンスです。
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: ff80130ebb9c4095d4a20202cdfabd9aaf1b1992
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832008"
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Azure Machine Learning モデル データ収集 API リファレンス

モデル データ収集機能を使うと、モデルの入力と Machine Learning Web サービスからの予測をアーカイブできます。 Windows および Linux コンピューターに `azureml.datacollector` をインストールする方法については、[モデル データ収集の方法に関するガイド](how-to-use-model-data-collection.md)をご覧ください。

この API リファレンス ガイドでは、Machine Learning Web サービスからモデルの入力と予測を収集する手順を説明します。

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Azure ML Workbench 環境でモデル データの収集を有効にする

 プロジェクトの aml_config フォルダーで conda\_dependencies.yml ファイルを探し、次に示すように、conda\_dependencies.yml ファイルの pip セクションに azureml.datacollector モジュールを追加します。 次に示すのは conda\_dependencies.yml ファイルの全体ではなく一部分だけであることに注意してください。

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>現時点では、データ コレクター モジュールを使うには Azure ML Workbench から Docker モードで実行する必要があります。 ローカル モードはすべての環境で動作しない可能性があります。




## <a name="enable-model-data-collection-in-the-scoring-file"></a>スコア付けファイルでモデル データ収集を有効にする

運用化に使われるスコア付けファイルで、データ コレクター モジュールと ModelDataCollector クラスをインポートします。

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>モデル データ コレクターのインスタンス化
ModelDataCollector の新しいインスタンスをインスタンス化します。

    dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

クラスとパラメーターの詳細は以下のとおりです。

### <a name="class"></a>クラス
| Name | 説明 |
|--------------------|--------------------|
| ModelDataCollector | azureml.datacollector 名前空間のクラスです。 このクラスのインスタンスが、モデル データの収集に使われます。 1 つのスコア付けファイルに、複数の ModelDataCollector を含めることができます。 収集されるデータのスキーマの一貫性が保たれるように (つまり、入力と予測)、スコア付けファイルの 1 つの独立した場所でのデータ収集に、各インスタンスを使う必要があります。|


### <a name="parameters"></a>parameters

| Name | type | 説明 |
|-------------|------------|-------------------------|
| model_name | 文字列 | データが収集対象であるモデルの名前です。 |
| identifier | 文字列 | このデータを示すコード内の場所です。つまり、"RawInput" または "Prediction" です。 |
| feature_names | string のリスト | 指定すると CSV のヘッダーになる機能の一覧です。 |
| model_management_account_id | 文字列 | このモデルが格納されるモデル管理アカウントの識別子です。 これは、AML によりモデルが運用可能化されるときに自動的に設定されます。 |
| webservice_name | 文字列 | このモデルが現在デプロイされている Web サービスの名前です。 これは、AML によりモデルが運用可能化されるときに自動的に設定されます。 |
| model_id | 文字列 | モデル管理アカウントのコンテキストにおけるこのモデルの一意識別子です。 これは、AML によりモデルが運用可能化されるときに自動的に設定されます。 |
| model_version | 文字列 | モデル管理アカウントのコンテキストにおけるこのモデルのバージョン番号です。 これは、AML によりモデルが運用可能化されるときに自動的に設定されます。 |



 

## <a name="collecting-the-model-data"></a>モデル データの収集

上で作成した ModelDataCollector のインスタンスを使って、モデル データを収集することができます。

    dc.collect(input_data, user_correlation_id="")

メソッドとパラメーターの詳細は以下のとおりです。

### <a name="method"></a>方法
| Name | 説明 |
|--------------------|--------------------|
| collect | モデルの入力または予測のデータを収集するために使います。|


### <a name="parameters"></a>parameters

| Name | type | 説明 |
|-------------|------------|-------------------------|
| input_data | 複数の型 | 収集対象のデータです (現在受け付けられる型: list、numpy.array、pandas.DataFrame、pyspark.sql.DataFrame)。 DataFrame 型の場合、機能名のヘッダーが存在すると、その情報が対象のデータに組み込まれます (ModelDataCollector コンストラクターで機能名を明示的に渡す必要はありません)。 |
| user_correlation_id | 文字列 | オプションの相関 ID です。これは、この予測を関連付けるためにユーザーが提供できます。 |

