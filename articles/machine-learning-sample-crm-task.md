<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Machine Learning サンプル: CRM タスク | Azure" description="顧客離れ、アップセル、新製品を購入する傾向を予測する複数のモデルを作成するための Azure Machine Learning のサンプル実験。" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning サンプル: CRM タスク

>[AZURE.NOTE]
>ML Studio には、このモデルに対応した[サンプル実験]と[サンプル データセット]が用意されています。詳細については、以下を参照してください。
[サンプル実験]: #sample-experiment
[サンプル データセット]: #sample-datasets

<!--
- [Problem Description]
- [Data]
- [Model]
- [Results]
- [API]
- [Sample Experiment]
-->
[問題の説明]: #problem-description
[データ]: #data
[モデル]: #model
[結果]: #results
[API]: #api

## 問題の説明 ##

顧客離れ (プロバイダーの乗り換え)、アップセル (上位サービスの購入または追加購入)、新製品を購入する傾向 (強い欲求) の予測 

## データ ##

フランスの French Telecom Company Orange の 50,000 人の顧客のデータセット。顧客ごとに匿名の 230 の特徴があります。このデータは KDD Cup 2009 から取得しています。 

このデータには数値特徴と文字列特徴の両方があります。特徴はきわめて疎です。
 
## モデル ##

データの処理は、不足値の対処のみが実行されました。文字列特徴の不足値を "0" に置き換えました。ほとんどすべての数値特徴が負でない値です。数値特徴の既存の値に 1 を加算し、不足値のエントリを 0 で置き換えました。この方法で、実際に元からの 0 と不足値を示す 0 を区別します。数値特徴のみに最初に数値演算 (+1) を適用して、区別しました。この後に、すべての不足値を 0 (文字列の場合は "0") に置き換えました。 

数値特徴とカテゴリ特徴の両方があるため、ブースト デシジョン ツリー分類器を使用しました。3 つの問題はすべて不均衡で、陽性のサンプルが少数派になっていました。場合によっては、このことが原因で、少数の陽性のサンプルがわずかに存在する特徴空間の領域を分類器が無視する可能性があります。これをテストするために、問題ごとに 2 つのモデルをトレーニングしました。1 つには生データを使用し、もう 1 つには、新しい陽性のサンプルの数が陰性のテスト サンプルの数にほぼ等しくなるように、陽性のサンプルをレプリケートしました。これは、陽性のサンプルと陰性のサンプルを分離する単純な R スクリプトを使用して実現し、13 件の陽性のサンプルが陰性のセットに追加されました。 

## 結果 ##

このモデルは、各タスクについて陽性の結果が出る確率に関連するスコアを出力します。観察を陽性として分類するようにカットオフのしきい値を任意に選択できるため、受信者動作特性 (ROC) 曲線 (AUC) の下の面積によってパフォーマンスを測定します。顧客離れとアップセルの両方で、セットのバランスを取るために陽性のサンプルをレプリケーションしたことにより、モデルのパフォーマンスがわずかに改善しました。  

<table border="1">
<tr><td>顧客離れ</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.711</td></tr>
<tr><td>レプリケートされた陽性</td><td>0.7280</td></tr>
</table>


<table border="0">
<tr><td>アップセル</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.853</td></tr>
<tr><td>レプリケートされた陽性</td><td>0.865</td></tr>
</table>


<table border="0">
<tr><td>強い欲求</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.805</td></tr>
<tr><td>レプリケートされた陽性</td><td>0.8</td></tr>
</table>

## API ##

匿名の特徴を使用しているため、このモデルは運用に対応していません。



## サンプル実験

ML Studio には、このモデルに対応した次のサンプル実験が用意されています。**[サンプル]** タブの **[実験]** セクションにあります。

> **サンプル実験 - CRM - 開発**

## サンプル データセット

ML Studio には、この実験で使用される次のサンプル データセットが用意されています。モジュール パレットの **[保存されたデータセット]** にあります。

###CRM データセットの共有
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]

###CRM 強い欲求ラベルの共有
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]

###CRM 離反ラベルの共有
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]

###CRM アップセリング ラベルの共有
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
