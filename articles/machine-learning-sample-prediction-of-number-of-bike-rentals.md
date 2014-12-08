<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Machine Learning サンプル: 自転車のレンタルの予測 | Azure" description="A sample Azure Machine Learning experiment to develop a regression model that predicts the number of bike rentals hourly." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning サンプル: 自転車のレンタル数の予測

>[AZURE.NOTE]
>ML Studio には、このモデルに対応した[サンプル実験]と[サンプル データセット]が用意されています。詳細については、以下を参照してください。
[サンプル実験]: #sample-experiment
[サンプル データセット]: #sample-dataset


## 問題の説明 ##
これまでのレンタルの履歴、1 時間ごとに観測される天候、祝日、平日、土日の区別を利用して、本日、1 時間ごとにレンタルされる自転車の数を予測します。予測は時間ごとに異なります (たとえば、
 朝にはレンタル数が多くなり、夜はほとんどレンタルはありません)。 

## データ ##
UCI Bike Rental データセットを使用します。このデータセットは、ワシントン D.C. で自転車のレンタル ネットワークを管理している Capital Bikeshare 社の実際のデータに基づいています。このデータセットは、2011 年と 2012 年の各日の 1 時間ごとに 1 行という構成になっており、全体で 17,379 行あります。毎時間の自転車レンタル数の範囲は 1 ～ 977 です。

## モデル ##
2011 年のデータをトレーニング セットとして使用し、2012 年のデータをテスト セットとして使用しました。4 セットの特徴を比較しました。

1. 予測日の "天候" + "祝日" + "平日" + "週末" の各特徴
1. 過去 12 時間ごとにレンタルされたバイクの数
1. 過去 12 日ごとにレンタルされたバイクの数 (同じ時間帯)
1. 過去 12 週ごとにレンタルされたバイクの数 (同じ時間帯、同じ日)

特徴 B では、直近のバイクの需要を収集します。特徴 C では、特定の時間帯のバイクの需要を収集します。特徴 D では、特定の日の特定の時間帯のバイクの需要を収集します。

ラベル (レンタルの数) は実際の値であるため、回帰設定があります。また、特徴の数は比較的小さく (100 未満)、疎ではないため、デシジョンの境界はおそらく非線形です。この点に基づき、ブースト デシジョン ツリーの回帰アルゴリズムを使用することにしました。

## 結果 ##

<table border="1">
<tr><th>特徴</th><th>平均絶対誤差</th> <th>二乗平均平方根誤差</th> </tr>
<tr style="background-color: #fff"><td>A</td> <td> 89.7</td> <td>124.9 </td> </tr>
<tr style="background-color: #fff"><td>A+B</td><td>51.2 </td> <td>86.7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C</td><td> 48.5</td> <td> 83.7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C+D</td><td> 48.8 </td> <td>83.2 </td></tr>
</table>

</table>

特徴 A+B+C および A+B+C+D のときに最適な結果が示されています。驚いたことに、特徴 D を加えても、A+B+C と比べて大幅な改善は見られません。 

## サンプル実験

ML Studio には、このモデルに対応した次のサンプル実験が用意されています。**[サンプル]** タブの **[実験]** セクションにあります。

> **サンプル実験 - 自転車の需要予測**


## サンプル データセット

ML Studio には、この実験で使用される次のサンプル データセットが用意されています。モジュール パレットの **[保存されたデータセット]** にあります。

###自転車レンタルの UCI データセット
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]


