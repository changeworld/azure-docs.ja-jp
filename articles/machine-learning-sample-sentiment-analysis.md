<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Machine Learning サンプル: センチメント分析 | Azure" description="製品のレビューを予測するセンチメント分類を使用する Azure Machine Learning のサンプル実験。" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />
 

# Azure Machine Learning サンプル: センチメント分析

>[AZURE.NOTE]
>ML Studio には、このモデルに対応した[サンプル実験]と[サンプル データセット]が用意されています。詳細については、以下を参照してください。
[サンプル実験]: #sample-experiment
[サンプル データセット]: #sample-dataset


##問題の説明
製品レビューの評価を予測します。評価は 1、2、3、4、5 で決定されます。これは順序回帰の問題ですが、回帰の問題としても、マルチクラス分類の問題としても解決できます。
 
##データ
ペンシルバニア大学の研究者が Amazon サイトから抜き出した、Amazon の書評データです ([http://www.cs.jhu.edu/~mdredze/datasets/sentiment/](http://www.cs.jhu.edu/~mdredze/datasets/sentiment/))。元のデータセットは、1、2、3、4、5 のいずれかの評価が付けられた、975,000 件のレビューです。実験時間の短縮のために、データセットはレビュー 10,000 件にダウンサンプリングされています。レビューはすべて英語で、1997 年から 2007 年の間に投稿されています。 
 
##モデル
特徴ハッシュ モジュールを使用して、英語の文字列を整数値の特徴に変換しました。以下の 3 つのモデルが比較されました。  
 
1. 線形回帰   
2. 2 クラス ロジスティック回帰を基底分類器として使用した順序回帰
3. マルチクラス ロジスティック回帰分類器を使用したマルチクラス分類
 
##結果

アルゴリズム                 | 平均絶対誤差 | 二乗平均平方根誤差
:---------                | :-----------------: | :--------------------:
順序回帰        | 0.82                | 1.41
線形回帰         | 1.04                | 1.36
マルチクラス分類 | 0.85                | 1.57
 
これらの結果に基づき、順序回帰モデルを使用して、このモデルに基づいて Web サービスを構築しました。
 
<!-- Removed until this part is fixed
##API
We have built a web service that takes a plain text review and predicts its rating.
-->


## サンプル実験

ML Studio には、このモデルに対応した次のサンプル実験が用意されています。**[サンプル]** タブの **[実験]** セクションにあります。

> **サンプル実験 - センチメント分類 - 開発**


## サンプル データセット

ML Studio には、この実験で使用される次のサンプル データセットが用意されています。モジュール パレットの **[保存されたデータセット]** にあります。

###Amazon の書評
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
