<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="Machine Learning サンプル: 類似した会社の特定 | Azure" description="A sample Azure Machine Learning experiment that uses a clustering model to find similar companies." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning サンプル: 類似した会社の特定

>[AZURE.NOTE]
>ML Studio には、このモデルに対応した[サンプル実験]と[サンプル データセット]が用意されています。詳細については、以下を参照してください。
[サンプル実験]: #sample-experiment
[サンプル データセット]: #sample-dataset


## 問題の説明 ##
各会社の Wikipedia の記事に基づいて、S&P 500 インデックスの会社をクラスター化します。このモデルは、既存の S&P 500 の会社との類似度に基づいて有望な会社を特定する場合などに使用できます。

## データ ##
データは Wikipedia XML ダンプから取得します。前処理として、ML Studio の外部で、会社ごとにテキスト コンテンツを抽出し、Wiki の形式や英数字でない文字を取り除き、すべてのテキストを小文字に変換しました。また、既知の会社のカテゴリを追加しました。一部の会社の記事は見つかりませんでした。そのため、レコードの数は 500 件未満です。

## モデル ##
まず、記事のコンテンツは "特徴のハッシュ化" モジュールに渡され、このモジュールによって、各トークンのハッシュ値に基づいてテキスト データから数値形式に変換されます。K 平均法クラスタリングで直接使用するには、このデータは高次元すぎて疎になっています。したがって、PCA を "R スクリプトの実行" モジュール内で使用して、次元を 10 変数まで減らします。R モジュールの右側の出力内容を開いて、PCA の結果を表示することができます。 

試行錯誤によって、PCA の変換済みデータの 1 番目の変数 (最も分散が高い変数) がクラスタリングに有害な効果を及ぼすと見られることが明らかになりました。そのため、"プロジェクト列" モジュールを使用して特徴セットから取り除かれました。

データは、K 平均法アルゴリズムを使用して、3 つにクラスター化されました。試行錯誤の結果、この数が理にかなった結果であることがわかりました。4 つと 5 つのクラスター化も試行しました。

最後に、メタデータ エディターを使用して、クラスターのラベルの変数をカテゴリに変換します。また、結果は、ダウンロード用に CSV ファイルに変換します。

## 結果 ##
メタデータ エディターの出力内容を視覚化し、"割り当て" 列群に対して "カテゴリ" 列 (Wikipedia データにある) をプロットすることで、結果を表示できます。3 つのクラスターは、次の既知のカテゴリにほぼ対応しています。

クラスター 0: 一般消費財、生活必需品、金融、ヘルスケア

クラスター 1: 情報技術

クラスター 2: エネルギー、公益企業、電気通信サービス

**注** クラスタリングの境界は明快ではありません。クラスター 0 には情報技術やエネルギー分野の会社がいくつか含まれ、工業や素材産業が、クラスター 0 とクラスター 1 に分類されています。

**注** クラスタリングは実行するたびに変わる可能性があります。 



## サンプル実験

ML Studio には、このモデルに対応した次のサンプル実験が用意されています。**[サンプル]** タブの **[実験]** セクションにあります。

> **サンプル実験 - S & P 500 会社のクラスタリング - 開発**


## サンプル データセット

ML Studio には、この実験で使用される次のサンプル データセットが用意されています。モジュール パレットの **[保存されたデータセット]** にあります。

###Wikipedia SP 500 データセット
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
