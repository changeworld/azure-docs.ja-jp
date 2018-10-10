---
title: データ サイエンス仮想マシンのサンプルとチュートリアル - Azure | Microsoft Docs
description: データ サイエンス仮想マシンのサンプルとチュートリアル。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: a1f15b805d2f27152d9ba85608ce0dc1d1aac21e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392570"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>データ サイエンス仮想マシン (DSVM) に関するサンプル

DSVM には、Jupyter Notebooks の形式の包括的なサンプル コード セットと、Python や R などの言語のスクリプトが含まれています。    
> [!NOTE]
> DSVM で Jupyter Notebooks を実行する方法の詳細については、[Jupyter へのアクセス](#access-jupyter)に関するセクションを参照してください。

## <a name="quick-reference-of-samples"></a>サンプルのクイックリファレンス
| サンプル カテゴリ | 説明 | 場所 |
| ------------- | ------------- | ------------- |
| **R** 言語  | **R** のサンプルでは、Azure クラウド データ ストアとの接続、オープン ソースの R と Microsoft R の比較、Microsoft R Server または SQL Server での操作可能化モデルなどのシナリオを説明します。 <br/> [スクリーンショット](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** 言語  | **Python** のサンプルで、Azure クラウド データ ストアとの接続や、**Azure Machine Learning** の操作などのシナリオを説明します。  <br/> [スクリーンショット](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Julia** 言語  | **Julia** 内のサンプルで、Julia でのプロット、Julia でのディープ ラーニング、Julia からの C や Python の呼び出しなどを詳しく説明します。 <br/> [スクリーンショット](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **Azure Machine Learning** AzureML  | **Azure Machine Learning** サービスと任意の場所へのモデル展開を使用して ML およびディープ ラーニング モデルを構築します。 Automated ML、インテリジェント ハイパー パラメーターの調整、モデル管理、分布トレーニングなどの機能を利用します。 <br/> [スクリーンショット](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| **PyTorch** Notebooks  | **PyTorch** ベースのニューラル ネットワークを利用したディープ ラーニング サンプル。 初心者から高度なシナリオまでのさまざまなノートブックがあります。  <br/> [スクリーンショット](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| **TensorFlow**  | **TensorFlow** フレームワークを使用して実装された、ニューラル ネットワークの各種サンプルおよび技法。 <br/> [スクリーンショット](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive Toolkit)  | Microsoft の Cognitive Toolkit チームによって公開されているディープ ラーニング サンプル。  <br/> [スクリーンショット](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **caffe2** | ニューラル ネットワークに基づいて **caffe2** を利用したディープ ラーニング サンプル。 イメージの前処理、データセット作成、回帰、事前トレーニング済みモデルの使用などの例を含め、caffe2 とその効果的な使用方法をユーザーに理解してもらうために設計された、複数のノートブックがあります。 <br/> [スクリーンショット](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| **H2O**   | さまざまな現実世界のシナリオの問題に対して **H2O** を利用する、Python ベースのサンプル。 <br/> [スクリーンショット](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** 言語  | **Apache Spark 2.x** 上で **pySpark** と **MMLSpark - Microsoft Machine Learning for Apache Spark** を介して Spark の **MLlib** ツールキットの特性と機能を利用するサンプル。  <br/> [スクリーンショット](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| **XGBoost** | 分類、回帰などのシナリオ向けの、**XGBoost** における標準的な機械学習サンプル。 <br/> [スクリーンショット](#xgboost) | <br/>**Windows**:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Jupyter にアクセスする 

デスクトップまたはアプリケーション メニューの `Jupyter` アイコンをクリックすると Jupyter にアクセスできます。 Ubuntu 上で **`https://<Full Domain Name or IP Address of the DSVM>:8000`** にアクセスして、Web ブラウザーからリモートで DSVM の Linux エディションの Jupyter にアクセスすることもできます。

スクリーンショットを参考に、例外を追加してブラウザー経由の Jupyter アクセスを有効にします。


![Jupyter の例外を有効にする](./media/ubuntu-jupyter-exception.png)


DSVM のログインと同じパスワードでサインインします。
<br/>

**Jupyter ホーム**
<br/>![Jupyter ホーム](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 言語 
<br/>![R サンプル](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 言語
<br/>![Python サンプル](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 言語 
<br/>![Julia サンプル](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML サンプル](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch サンプル](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow サンプル](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK サンプル](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>caffe2 
<br/>![caffe2 サンプル](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O サンプル](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML サンプル](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost サンプル](./media/xgboost-samples.png)<br/>

