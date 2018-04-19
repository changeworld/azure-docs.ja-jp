---
title: "Azure Machine Learning データ準備の運用化の使用方法に関する詳細ガイド | Microsoft Docs"
description: "このドキュメントでは、以前に設計されたデータ ソースおよびデータ準備パッケージの実行について詳しく説明しています"
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 0849747fe6d66d55d11c131b51b07d8f689774e1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
# <a name="data-preparation-operationalization"></a>データ準備の運用化 

## <a name="operationalization-scenario"></a>運用化のシナリオ

ユーザーが遭遇する可能性のあるさまざまなデータ準備の運用化のシナリオ例を次に示します。

### <a name="develop-your-model-based-on-training-data"></a>トレーニング データに基づいてモデルを構築する

リアルタイム スコアリング API サービスを作成し、デプロイする場合について説明します。 最初の手順は、トレーニング データの一部に基づいてスコアリングのモデルを構築することです。 データ準備で、トレーニング データのサンプルを新しいデータ ソースとしてインポートします。 データの準備が完了すると、DataPrep パッケージに準備手順が含まれます。 AzureML 実験アカウントを使用すると、機械学習モデルに対してトレーニング データの各入力にラベルを生成する繰り返しプロセスを実行できます。

データの機能を更新する必要があるため、ユーザーは DataPrep パッケージに戻り、新しい方法でデータを準備し、その手順を保存します。 新しい機能を生成し、機械学習モデルを調整するこの繰り返しプロセスは、モデルがテスト データを正確に評価するまで継続されます。 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>モデルを Azure モデル管理サービスにデプロイする

これまでの操作で、リアルタイムで評価したい顧客データを入手できました。 Azure モデル管理サービスを使用して、AzureML Python CLI からこのモデルをサービスとしてデプロイできます。 デプロイされたサービスは、顧客データをリアルタイムで受け取り、トレーニングされたモデルから対応する出力ラベルを返す REST エンドポイントを公開しています。

使いやすくするために、このモデル エンドポイントは JSON 形式のデータを受け入れています。 2 次元のデータ配列を表す JSON 文字列を入力する必要があります。この文字列は ReadJSONLiteral 変換を介して渡され、DataPrep データ ソースに変換されます。 実験段階で作成されたデータ準備パッケージは、ストリーミングされた JSON データに対して実行できます。 結果のデータフレームは、スコアリングのためにトレーニングされたモデルに渡すことができます。

## <a name="read-json-literal-transformation"></a>JSON リテラル変換を読み取る

### <a name="description"></a>[説明]

運用化の目的から、データ準備には、アクティビティを実行し、Pandas または Spark Dataframe を生成するための  **ReadJsonLiteral**  変換が含まれています。 この変換は、既存のデータ準備パッケージと JSON データ ソースを入力として一意に受け取ります。 この変換は、DataPrep Python CLI を介して公開されます。

### <a name="instructions"></a>このサンプルについての指示

#### <a name="pythoncli"></a>PythonCLI

Azure Machine Learning Workbench からコマンドライン インターフェイスを開きます ([ファイル]、[コマンド プロンプトを開く] の順に選択します)。

この例では、TrainingPreparationSteps データ準備パッケージを使用してデータを準備し、ボリューム機能を各入力に追加します。

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()` には次の省略可能なパラメーターがあります
 - `dataflow_idx`: パッケージで実行する目的のデータフローのインデックスを指定します
 - `secrets`: シークレット ストア ディクショナリ (キー: secretId、値: 格納されているシークレット)
 - `spark`: Spark 実行のための Spark セッションを指定します

#### <a name="input"></a>入力

2 次元配列入力 ("配列の配列")。 Python では、入力はリストのリストにする必要があります。 JSON にはネイティブの日付型がないため、Python の datetime.datetime オブジェクトはすべて ISO 形式の日付文字列に変換されます。 REST エンドポイントの場合、入力は 2 次元 JSON 配列を表す JSON リテラル文字列にする必要があります。

#### <a name="output"></a>出力

Pandas または Spark のデータフレーム。 データフレームの型は、実行構成 (`.runconfig`) で選択したフレームワークによって決まります。 結果のデータフレームは、スコアリングのためにトレーニングされたモデルに入力として渡すことができます。
