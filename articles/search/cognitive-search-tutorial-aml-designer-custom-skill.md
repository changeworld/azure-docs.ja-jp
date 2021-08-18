---
title: '例: Azure Machine Learning デザイナーを使用してカスタム スキルを作成およびデプロイする'
titleSuffix: Azure Cognitive Search
description: この例では、Azure Machine Learning デザイナーを使用して、Azure Cognitive Search の AI エンリッチメント パイプラインのカスタム AML スキルを作成してデプロイする方法について説明します。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 5af4110d1092c2e0e916d1404e9177b64e650e0b
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2021
ms.locfileid: "114728112"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning-designer"></a>例: Azure Machine Learning デザイナーを使用してカスタム スキルをビルドおよびデプロイする

[Azure Machine Learning デザイナー](../machine-learning/concept-designer.md)は、回帰や分類などのタスクの機械学習モデルを作成するための使いやすい対話型キャンバスです。 Cognitive Search エンリッチメント パイプラインでデザイナーによって作成されたモデルを呼び出すには、いくつかの追加の手順が必要です。 この例では、自動車の価格を予測し、推論エンドポイントを AML スキルとして呼び出すシンプルな回帰モデルを作成します。 

[パイプラインとデータセットのサンプル](../machine-learning/concept-designer.md)に関するドキュメント ページの[回帰 - 自動車価格の予測 (詳細)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-compare-algorithms.md) についてのチュートリアルに従って、さまざまな機能を指定して自動車の価格を予測するモデルを作成します。

> [!IMPORTANT] 
> リアルタイムの推論プロセスに従ってモデルをデプロイすると、有効なエンドポイントが得られますが、Cognitive Search の AML スキルで使用できるものではありません。 

## <a name="register-model-and-download-assets"></a>モデルを登録して資産をダウンロードする

モデルをトレーニングしたら、[そのトレーニング済みのモデルを登録](../machine-learning/how-to-deploy-model-designer.md)し、手順に従って `trained_model_outputs` フォルダー内のすべてのファイルをダウンロードするか、モデル成果物ページから `score.py` および `conda_env.yml` ファイルのみをダウンロードします。 スコア付けスクリプトは、モデルがリアルタイムの推論エンドポイントとしてデプロイされる前に編集します。


## <a name="edit-the-scoring-script-for-use-with-cognitive-search"></a>Cognitive Search で使用するスコア付けスクリプトを編集する 

Cognitive Search エンリッチメント パイプラインは単一のドキュメントに対して機能し、単一の予測の入力を含む要求が生成されます。 ダウンロードした `score.py` ではレコードのリストを受け入れ、シリアル化された JSON 文字列として予測のリストを返します。 `score.py` に対して、次の 2 つの変更を行います

* リストではなく、単一の入力レコードを操作するようにスクリプトを編集する
* 単一のプロパティ (予測価格) を含む JSON オブジェクトを返すようにスクリプトを編集する。

ダウンロードした `score.py` を開き、`run(data)` 関数を編集します。 この関数は現在、モデルの `_samples.json` ファイルで説明されているように、次の入力を予測するように設定されています。

```json
[
  {
    "symboling": 2,
    "make": "mitsubishi",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "hatchback",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 93.7,
    "length": 157.3,
    "width": 64.4,
    "height": 50.8,
    "curb-weight": 1944,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 2.97,
    "stroke": 3.23,
    "compression-ratio": 9.4,
    "horsepower": 68.0,
    "peak-rpm": 5500.0,
    "city-mpg": 31,
    "highway-mpg": 38,
    "price": 6189.0
  },
  {
    "symboling": 0,
    "make": "toyota",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "four",
    "body-style": "wagon",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 95.7,
    "length": 169.7,
    "width": 63.6,
    "height": 59.1,
    "curb-weight": 2280,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 3.05,
    "stroke": 3.03,
    "compression-ratio": 9.0,
    "horsepower": 62.0,
    "peak-rpm": 4800.0,
    "city-mpg": 31,
    "highway-mpg": 37,
    "price": 6918.0
  },
  {
    "symboling": 1,
    "make": "honda",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "sedan",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 96.5,
    "length": 169.1,
    "width": 66.0,
    "height": 51.0,
    "curb-weight": 2293,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 110,
    "fuel-system": "2bbl",
    "bore": 3.15,
    "stroke": 3.58,
    "compression-ratio": 9.1,
    "horsepower": 100.0,
    "peak-rpm": 5500.0,
    "city-mpg": 25,
    "highway-mpg": 31,
    "price": 10345.0
  }
]
```

変更により、インデックス作成時に Cognitive Search によって生成された入力 (単一のレコード) が確実にモデルで受け入れられるようになります。

```json
{
    "symboling": 2,
    "make": "mitsubishi",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "hatchback",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 93.7,
    "length": 157.3,
    "width": 64.4,
    "height": 50.8,
    "curb-weight": 1944,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 2.97,
    "stroke": 3.23,
    "compression-ratio": 9.4,
    "horsepower": 68.0,
    "peak-rpm": 5500.0,
    "city-mpg": 31,
    "highway-mpg": 38,
    "price": 6189.0
}
```

27 行目から 30 行目までを以下に置き換えます
```python

    for key, val in data.items():
        input_entry[key].append(decode_nan(val))
```
また、スクリプトで生成された出力を編集し、文字列から JSON オブジェクトにする必要があります。 元のファイルの return ステートメント (37 行目) を次のように編集します。
```python
    output = result.data_frame.values.tolist()
    return {
        "predicted_price": output[0][-1]
    }
```

これは、入力形式と予測される出力が変更された更新済みの `run` 関数であり、単一のレコードを入力として受け入れ、予測される価格を含む JSON オブジェクトが返されます。

```python
def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    # data is now a JSON object not a list of JSON objects
    for key, val in data.items():
        input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)
    score_module = ScoreModelModule()
    result, = score_module.run(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        append_or_result_only=True)
    #return json.dumps({"result": result.data_frame.values.tolist()})
    output = result.data_frame.values.tolist()
    # return the last column of the the first row of the dataframe
    return  {
        "predicted_price": output[0][-1]
    }
```
## <a name="register-and-deploy-the-model"></a>モデルを登録してデプロイする

変更が保存されたので、ポータルでモデルを登録できるようになりました。 [モデルの登録] を選択し、有効な名前を指定します。 モデル フレームワークには `Other`、フレームワーク名には `Custom`、フレーム ワークバージョンには `1.0` を選びます。 `Upload folder` オプションを選択し、`score.py` と `conda_env.yaml` が更新されているフォルダーを選びます。

モデルを選び、`Deploy` アクションを選択します。 デプロイ手順は、AKS 推論クラスターがプロビジョニングされていることを前提としています。 現在、Cognitive Search では、コンテナー インスタンスはサポートされていません。
 1. 有効なエンドポイント名を指定します
2. コンピューティングの種類として `Azure Kubernetes Service` を選択します
3. 推論クラスターのコンピューティング名を選択します
4. `enable authentication` をオンに切り替えます
5. その種類には `Key-based authentication` を選択します
6. `entry script file` には更新された `score.py` を選択します
7. `conda dependencies file` には `conda_env.yaml` を選択します
8. デプロイ ボタンを選択して、新しいエンドポイントをデプロイします。

## <a name="integrate-with-cognitive-search"></a>Cognitive Search と統合する

新しく作成されたエンドポイントを Cognitive Search と統合するには、次のようにします
1. 単一の自動車レコードを含む JSON ファイルを BLOB コンテナーに追加します
2. [データのインポート ワークフロー](cognitive-search-quickstart-blob.md)を使用して、AI エンリッチメント パイプラインを構成します。 必ず、`parsing mode` として `JSON` を選択してください
3. `Add Enrichments` タブで、プレースホルダーとして単一のスキル `Extract people names` を選択します。
4. 種類が `Edm.Double` の `predicted_price` というインデックスに新しいフィールドを追加し、Retrievable プロパティを true に設定します。
5. データのインポート プロセスを完了します

### <a name="add-the-aml-skill-to-the-skillset"></a>AML スキルをスキルセットに追加する

スキルセットのリストから、作成したスキルセットを選択します。 次に、スキルセットを編集してユーザー識別スキルを AML スキルに置き換え、価格を予測します。 [スキルセットの定義 (JSON)] タブで、スキル ドロップダウンから [`Azure Machine Learning (AML)`] を選択します。 ワークスペースを選択します。AML スキルでエンドポイントを検出するには、ワークスペースと検索サービスが同じ Azure サブスクリプションにある必要があります。
チュートリアルの前の手順で作成したエンドポイントを選択します。
エンドポイントをデプロイしたときに構成された URI と認証情報がスキルに設定されていることを確認します。 スキル テンプレートをコピーし、スキルセットのスキルを置き換えます。
スキルを編集して次のようにします。
1. 名前を有効な名前に設定します
2. 説明を追加します
3. degreesOfParallelism を 1 に設定します
4. コンテキストを `/document` に設定します
5. 入力をすべての必須入力に設定します。以下のスキル定義のサンプルを参照してください
6. 返される予測価格を取り込むように出力を設定します。

```json
{
      "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
      "name": "AMLdemo",
      "description": "AML Designer demo",
      "context": "/document",
      "uri": "Your AML endpoint",
      "key": "Your AML endpoint key",
      "resourceId": null,
      "region": null,
      "timeout": "PT30S",
      "degreeOfParallelism": 1,
      "inputs": [
        {
          "name": "symboling",
          "source": "/document/symboling"
        },
        {
          "name": "make",
          "source": "/document/make"
        },
        {
          "name": "fuel-type",
          "source": "/document/fuel-type"
        },
        {
          "name": "aspiration",
          "source": "/document/aspiration"
        },
        {
          "name": "num-of-doors",
          "source": "/document/num-of-doors"
        },
        {
          "name": "body-style",
          "source": "/document/body-style"
        },
        {
          "name": "drive-wheels",
          "source": "/document/drive-wheels"
        },
        {
          "name": "engine-location",
          "source": "/document/engine-location"
        },
        {
          "name": "wheel-base",
          "source": "/document/wheel-base"
        },
        {
          "name": "length",
          "source": "/document/length"
        },
        {
          "name": "width",
          "source": "/document/width"
        },
        {
          "name": "height",
          "source": "/document/height"
        },
        {
          "name": "curb-weight",
          "source": "/document/curb-weight"
        },
        {
          "name": "engine-type",
          "source": "/document/engine-type"
        },
        {
          "name": "num-of-cylinders",
          "source": "/document/num-of-cylinders"
        },
        {
          "name": "engine-size",
          "source": "/document/engine-size"
        },
        {
          "name": "fuel-system",
          "source": "/document/fuel-system"
        },
        {
          "name": "bore",
          "source": "/document/bore"
        },
        {
          "name": "stroke",
          "source": "/document/stroke"
        },
        {
          "name": "compression-ratio",
          "source": "/document/compression-ratio"
        },
        {
          "name": "horsepower",
          "source": "/document/horsepower"
        },
        {
          "name": "peak-rpm",
          "source": "/document/peak-rpm"
        },
        {
          "name": "city-mpg",
          "source": "/document/city-mpg"
        },
        {
          "name": "highway-mpg",
          "source": "/document/highway-mpg"
        },
        {
          "name": "price",
          "source": "/document/price"
        }
      ],
      "outputs": [
        {
          "name": "predicted_price",
          "targetName": "predicted_price"
        }
      ]
    }
```
### <a name="update-the-indexer-output-field-mappings"></a>インデクサーの出力フィールド マッピングを更新する

インデクサーの出力フィールド マッピングによって、インデックスに保存されるエンリッチメントが決まります。 インデクサーの出力フィールド マッピング セクションを以下のスニペットに置き換えます。

```json
"outputFieldMappings": [
    {
      "sourceFieldName": "/document/predicted_price",
      "targetFieldName": "predicted_price"
    }
  ]
```

これでインデクサーを実行し、AML スキル出力からの結果を使用して、インデックスに `predicted_price` プロパティが設定されていることを確認できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム スキルの Web API を確認する](./cognitive-search-custom-skill-web-api.md)

> [エンリッチメント パイプラインへのカスタム スキルの追加について詳しく学習する](./cognitive-search-custom-skill-interface.md)

> [AML スキルについて詳しく学習する](./cognitive-search-tutorial-aml-custom-skill.md)