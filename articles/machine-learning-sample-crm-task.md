<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Machine Learning Sample: CRM task | Azure" description="A sample Azure Machine Learning experiment to develop multiple models that predict customer churn, upsell, and propensity to buy a new product." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning サンプル: CRM タスク

*ML Studio には、このモデルに対応したサンプル実験が用意されています。**[サンプル]** タブの **[実験]** セクションにあります。実験名は次のとおりです。*

    Sample Experiments - CRM - Development

## 問題の説明

顧客離れ (プロバイダーの乗り換え)、アップセル (上位サービスの購入または追加購入)、新製品を購入する傾向 (強い欲求) の予測

## データ

フランスの French Telecom Company Orange の 50,000 人の顧客のデータセット。顧客ごとに匿名の 230 の特徴があります。このデータは KDD Cup 2009 から取得しています。

このデータには数値特徴と文字列特徴の両方があります。特徴はきわめて疎です。

## モデル

データの処理は、不足値の対処のみが実行されました。文字列特徴の不足値を "0" に置き換えました。ほとんどすべての数値特徴が負でない値です。数値特徴の既存の値に 1 を加算し、不足値のエントリを 0 で置き換えました。この方法で、実際に元からの 0 と不足値を示す 0 を区別します。数値特徴のみに最初に数値演算 (+1) を適用して、区別しました。この後に、すべての不足値を 0 (文字列の場合は "0") に置き換えました。

数値特徴とカテゴリ特徴の両方があるため、ブースト デシジョン ツリー分類器を使用しました。3 つの問題はすべて不均衡で、陽性のサンプルが少数派になっていました。場合によっては、このことが原因で、少数の陽性のサンプルがわずかに存在する特徴空間の領域を分類器が無視する可能性があります。これをテストするために、問題ごとに 2 つのモデルをトレーニングしました。1 つには生データを使用し、もう 1 つには、新しい陽性のサンプルの数が陰性のテスト サンプルの数にほぼ等しくなるように、陽性のサンプルをレプリケートしました。これは、陽性のサンプルと陰性のサンプルを分離する単純な R スクリプトを使用して実現し、13 件の陽性のサンプルが陰性のセットに追加されました。

## 結果

このモデルは、各タスクについて陽性の結果が出る確率に関連するスコアを出力します。観察を陽性として分類するようにカットオフのしきい値を任意に選択できるため、受信者動作特性 (ROC) 曲線 (AUC) の下の面積によってパフォーマンスを測定します。顧客離れとアップセルの両方で、セットのバランスを取るために陽性のサンプルをレプリケーションしたことにより、モデルのパフォーマンスがわずかに改善しました。

<table border="1">
<tr><td>顧客離れ</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>レプリケーションなし</td><td>0.711</td></tr>
<tr><td>陽性をレプリケーション</td><td>0.728</td></tr>
</table>
<table border="0">
<tr><td>アップセル</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>レプリケーションなし</td><td>0.853</td></tr>
<tr><td>陽性をレプリケーション</td><td>0.865</td></tr>
</table>
<table border="0">
<tr><td>強い欲求</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>レプリケーションなし</td><td>0.805</td></tr>
<tr><td>陽性をレプリケーション</td><td>0.8</td></tr>
</table>

## API

匿名の特徴を使用しているため、このモデルは運用に対応していません。

