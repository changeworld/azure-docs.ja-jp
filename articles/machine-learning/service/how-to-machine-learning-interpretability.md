---
title: Azure Machine Learning service におけるモデルの解釈可能性
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK を使用して、モデルから得られる予測の理由を説明する方法について説明します。 トレーニング中と推論中に、モデルで予測が行われる方法を理解するために使用できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 14118098e5d476dc07b21462180673b942a6224d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672548"
---
# <a name="model-interpretability-in-azure-machine-learning-service"></a>Azure Machine Learning service におけるモデルの解釈可能性
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>モデルの解釈可能性の概要

データ サイエンティストやビジネスの意思決定者にとって、解釈可能性は会社のポリシー、業界標準、政府の規制に準拠する上で、次のように重要です。
+ データ サイエンティストはモデルの結果が生み出す価値と正確性を理解するため、経営陣や利害関係者に説明する必要がある 
+ ビジネスの意思決定者は、エンド ユーザーからの信頼を獲得し、維持するための透過性を提供できるという安心感を求めている

モデル開発における次の 2 つの主なフェーズでは、機械学習モデルを説明できるようにすることが重要です。
+ 機械学習モデルにおける開発サイクルのトレーニング フェーズ。 モデルデ ザイナーと評価者はモデルの解釈可能性出力を使用して仮説を検証し、利害関係者との信頼を構築できます。 また、モデルへの洞察をデバッグに使用したり、モデルの動作が目標に一致しているか検証したり、偏りや重要度の低い機能がないか検査したりできます。
+ 推論フェーズでは、透過性があるモデルをデプロイすることで、経営者は、モデルがどのように動作しているか、およびその意思決定が実際のユーザーにどのように作用し、どのように影響を与えているかを把握することができます。 

## <a name="interpretability-with-azure-machine-learning"></a>Azure Machine Learning での解釈可能性

この記事では、モデルの解釈可能性の概念を SDK で実装する方法について説明します。

SDK のクラスとメソッドを使用して、次のことを得ることができます。
+ 未加工の特徴とエンジニアリングされた両方の特徴の重要度の値
+ トレーニングおよび推論中の実際の大規模データセットに関する解釈可能性。
+ トレーニング時にデータと説明のパターンの検出を支援するための対話型の視覚化


機械学習では、**特徴**は、ターゲット データ ポイントの予測に使用されるデータ フィールドです。 たとえば、信用リスクを予測するために、年齢、アカウント サイズ、およびアカウントの有効期間のデータ フィールドが使用されるとします。 この場合、年齢、アカウント サイズ、およびアカウントの有効期間が**特徴**です。 特徴の重要度から、各データ フィールドがモデルの予測にどのような影響を与えたかがわかります。 たとえば、年齢は予測で頻繁に使用されるが、アカウントのサイズと有効期間は予測精度に大きな影響を与えない場合があります。 このプロセスにより、データ サイエンティストは結果の予測を説明でき、利害関係者はモデルで最も重要なデータ ポイントを認識できます。

これらのツールを使用すると、使いやすくてスケーラブルな最先端のテクノロジを利用して、**すべてのデータについてグローバルに**、または**特定のデータ ポイントについてローカルに**、機械学習モデルを説明できます。

解釈可能性クラスは、複数の SDK パッケージを介して利用可能になります。 [Azure Machine Learning 用の SDK パッケージをインストールする](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)方法を確認してください。

* `azureml.interpret`。Microsoft でサポートされている機能を含むメイン パッケージ。

* `azureml.contrib.interpret`。試すことができるプレビュー機能と実験的機能。

* `azureml.train.automl.automlexplainer`。自動化された機械学習モデルを解釈するためのパッケージ。

> [!IMPORTANT]
> `contrib` 名前空間のコンテンツは完全にはサポートされていません。 実験的な機能が成熟すると、メインの名前空間に徐々に移動されます。

## <a name="how-to-interpret-your-model"></a>モデルを解釈する方法

解釈可能性クラスとメソッドを適用して、モデルのグローバルな動作または特定の予測を理解することができます。 前者はグローバル説明と呼ばれ、後者はローカル説明と呼ばれます。

メソッドは、メソッドがモデルに依存しないか、またはモデルに固有かに基づいても分類できます。 一部のメソッドは、特定の種類のモデルを対象にします。 たとえば、SHAP の tree explainer は、ツリー ベースのモデルにのみ適用されます。 mimic explainer や SHAP の kernel explainer などの一部のメソッドでは、モデルはブラック ボックスとして処理されます。 `interpret` パッケージでは、データ セット、モデルの種類、およびユース ケースに基づいて、これらのさまざまなアプローチが使用されます。

出力は、特定のモデルが予測を行うときの方法に関する情報セットであり、例えば次のものがあります。
* グローバル/ローカルな特徴の相対的重要度
* グローバル/ローカルな特徴と予測の関係

### <a name="explainers"></a>Explainer

このパッケージでは、解釈可能なモデルをトレーニングし、ブラックボックス化した AI システムを説明するオープンソースの python パッケージである [Interpret-Community](https://github.com/interpretml/interpret-community/) で開発された解釈能力の手法を使用します。 [Interpret-Community](https://github.com/interpretml/interpret-community/) は、この SDK でサポートされている Explainer のホストとして機能し、現在、次の解釈可能性の手法をサポートしています。

* **SHAP Tree Explainer**:[SHAP](https://github.com/slundberg/shap) の Tree Explainer は、ツリーおよびツリーのアンサンブルに固有の多項式時間高速 SHAP 値推定アルゴリズムに注目します。
* **SHAP Deep Explainer**:[SHAP](https://github.com/slundberg/shap) による説明に基づくと、Deep Explainer は "[SHAP NIPS の論文](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)で説明されている DeepLIFT との接続上に構築されているディープ ラーニング モデルでの SHAP 値に対する高速近似アルゴリズムです。 TensorFlow モデルおよび TensorFlow バックエンドを使用する Keras モデルがサポートされています (PyTorch の暫定サポートもあります)"。
* **SHAP Linear Explainer**:[SHAP](https://github.com/slundberg/shap) の Linear Explainer は、線形モデルの SHAP 値を計算し、必要に応じて機能間の相関関係を考慮します。

* **SHAP Kernel Explainer**:[SHAP](https://github.com/slundberg/shap) の Kernel Explainer は、特別に重み付けされたローカル線形回帰を使用して任意のモデルの SHAP 値を推定します。
* **Mimic Explainer**: Mimic Explainer は、[グローバル サロゲート モデル](https://christophm.github.io/interpretable-ml-book/global.html)をトレーニングしてブラックボックス モデルを模倣するアイデアに基づいています。 グローバル サロゲート モデルは、ブラック ボックス モデルの予測をできる限り正確に近似するためにトレーニングされた本質的に解釈可能なモデルです。 データ サイエンティストは、サロゲート モデルを解釈してブラック ボックス モデルについての結論を導き出すことができます。 サロゲート モデルとして、次のいずれかの解釈可能なモデルを使用できます。LightGBM (LGBMExplainableModel)、線形回帰 (LinearExplainableModel)、確率勾配降下説明可能モデル (SGDExplainableModel)、およびデシジョン ツリー (DecisionTreeExplainableModel)。


* **Permutation Feature Importance Explainer**:Permutation Feature Importance は、[Breiman のランダム フォレスト論文](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (セクション 10 を参照してください) から着想を得た、分類および回帰モデルの説明に使用される手法です。 概要を説明すると、データセット全体に対して一度に 1 つの特徴のデータをランダムにシャッフルし、対象のパフォーマンス メトリックがどのくらい変化するかを計算するしくみです。 変化が大きいほど、その特徴の重要度は高くなります。

* **LIME Explainer** (`contrib`):[LIME](https://github.com/marcotcr/lime) に基づく LIME Explainer は、最新の LIME (Local interpretable model-agnostic explanations) アルゴリズムを使用して、ローカル サロゲート モデルを作成します。 グローバル サロゲート モデルとは異なり、LIME はローカル サロゲート モデルをトレーニングして個別の予測を説明することに着目します。
* **HAN Text Explainer** (`contrib`):HAN Text Explainer は、特定のブラック ボックス テキスト モデルに対するテキスト データからモデルの説明を取得するために Hierarchical Attention Network を使用します。 特定のブラック ボックス モデルの予測された出力で HAN サロゲート モデルをトレーニングします。 テキスト コーパス全体でグローバルにトレーニングした後、説明の正確性を向上させるため、特定のドキュメントに対する微調整ステップを追加します。 HAN は、文と単語のアテンションに対する 2 つのアテンション レイヤーで双方向 RNN を使用します。 DNN がブラック ボックス モデルでトレーニングされて、特定のドキュメントで微調整された後、ユーザーはアテンション レイヤーから単語の重要性を抽出できます。 テキスト データについては HAN は LIME や SHAP より正確であることが分かっていますが、トレーニング時間に関してはいっそうコストがかかります。 トレーニング時間を短縮するため、ユーザーは GloVe 単語埋め込みを使用してネットワークを初期化するオプションを使用できるようになりました。 HAN をリモート Azure GPU VM で実行することで、トレーニング時間を大幅に改善できます。 HAN の実装については、「[Hierarchical Attention Networks for Document Classification (ドキュメントの分類に対する階層型アテンション ネットワーク)」 (Yang 他、2016)](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification) で説明されています。


* **Tabular Explainer**: `TabularExplainer` では次のロジックを使用して Direct [SHAP](https://github.com/slundberg/shap) Explainer が呼び出されています。

    1. ツリー ベースのモデルの場合は、SHAP `TreeExplainer` を適用し、それ以外の場合は、
    2. DNN モデルの場合は、SHAP `DeepExplainer` を適用し、それ以外の場合は、
    3. 線形モデルの場合は、SHAP `LinearExplainer` を適用し、それ以外の場合は、
    3. ブラックボックス モデルとして扱い、SHAP `KernelExplainer` を適用します


`TabularExplainer` ではまた、Direct SHAP Explainer より大きな機能とパフォーマンスの強化も行われています。

* **初期化データセットの要約作成**。 説明の速度が最も重要な場合、初期化データセットを要約し、代表的なサンプルの小さなセットを生成します。これにより、グローバルとローカル両方の説明が高速化されます。
* **評価データ セットのサンプリング**。 ユーザーが評価サンプルの大きなセットを渡しても、実際にはそれらのすべてを評価する必要がない場合は、サンプリング パラメーターを true に設定し、グローバルな説明を高速化できます。

次の図は、Direct Explainer と Meta Explainer の現在の構造を示しています。

[![機械学習解釈可能性のアーキテクチャ](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>サポートされているモデル

Python の `numpy.array`、`pandas.DataFrame`、`iml.datatypes.DenseData`、または `scipy.sparse.csr_matrix` 形式のデータセットでトレーニングされたすべてのモデルが、SDK の解釈可能性 `explain` パッケージによってサポートされます。

説明関数では、モデルとパイプラインの両方が入力として受け付けられます。 モデルを提供する場合、モデルでは Scikit 規則に準拠する予測関数 `predict` または `predict_proba` が実装されている必要があります。 パイプライン (パイプライン スクリプトの名前) が提供された場合、説明関数では、パイプライン スクリプトの実行から予測が返るものと想定します。 PyTorch、TensorFlow、Keras のディープ ラーニング フレームワークでトレーニングされたモデルをサポートしています。

### <a name="local-and-remote-compute-target"></a>ローカルとリモートのコンピューティング先

`explain` パッケージは、ローカルおよびリモート両方のコンピューティング先で動作するように設計されています。 ローカルで実行した場合、SDK 関数はどの Azure サービスにも接続されません。 Azure Machine Learning コンピューティング上で説明をリモートで実行し、説明情報を Azure Machine Learning 実行履歴サービスに記録できます。 この情報が記録されると、ユーザーによる分析のため、説明からのレポートや視覚化を、Azure Machine Learning ワークスペース ポータルですぐに使用できます。


## <a name="next-steps"></a>次の手順

ローカルと Azure Machine Learning リモート コンピューティング リソースの両方で解釈可能性を有効にし、モデルのトレーニングを行う[方法](how-to-machine-learning-interpretability-aml.md)を参照してください。 その他のシナリオについては、[ノートブックのサンプル](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)を参照してください。
