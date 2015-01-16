<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Machine Learning サンプル: ネットワーク不正侵入検出 | Azure" description="分類モデルを使用して、どのネットワーク アクティビティがネットワーク侵入の一部であるかを判断する Azure Machine Learning のサンプル実験。" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />



# Azure Machine Learning サンプル: ネットワーク不正侵入検出

>[AZURE.NOTE]
>ML Studio には、このモデルに対応した[サンプル実験]と[サンプル データセット]が用意されています。詳細については、以下を参照してください。
[サンプル実験]: #sample-experiment
[サンプル データセット]: #sample-dataset


## 問題の説明 ##

このデモでは、さまざまなネットワークの特徴を使用して、どのネットワーク活動が侵入/攻撃の一部であるかを検出します。この二項分類問題はそれほど難しくはありません。特徴からクラスを予想しやすいうえ、クラスのバランスも取れているためです。一部の特徴はラベルに含まれているので、分類器をトレーニングする前にラベルを部分的に削除することが重要です。 

## データ ##

1. KDD Cup 1999 のデータにはトレーニング データセットとテスト データセットの両方が含まれています。トレーニング データセットの分類の正確度はテスト データセットよりも高いのが普通です。テスト セットには新しいネットワーク侵入が追加されるためです。トレーニング データセットには、ラベルも含めておよそ 126,000 個の行と 43 個の列があります。ラベル情報の列が 3 つあるため、モデルのトレーニングに使用できるのは 40 列です。この 40 列はほとんどが数値ですが、一部は文字列/カテゴリの特徴です。テスト データには約 22,500 個のテスト例があります (トレーニング データと同じ 43 列)。 
1. データは、アクセスしやすいようにパブリック BLOB にアップロードされています。Azure ML には、コードを記述しなくても数回のクリックだけで BLOB などのさまざまなストレージからデータを読み取ることのできるリーダー モジュールが用意されています。 

## モデル ##

1. モデルの最初のステップは、後の分類に備えてデータを調整することです。"Class" 列には、予測するラベルが含まれます。  このラベルは、"normal" (侵入なし) または攻撃の名前です。  一部の攻撃については、トレーニング データの中に例があまり多くありません。新しい攻撃はテスト データセットに含まれています。そのため、これらのマルチクラス ラベルを二項クラス ラベルに変換して、問題を実行可能にします。Studio にはこの前処理のステップを容易にするモジュールが組み込まれています。クラス ラベルの二項化にはインジケーター値モジュールを使用し、この二項化されたクラス ラベル列 (元のクラス ラベル列は除く) の選択にはプロジェクト列モジュールを使用します。  
1. この実験の作成過程で、多くの特徴がラベルと密接に関係している特徴選択/相関関係を確認します。これは合成データセットでは珍しいことではありません。Cloud ML では、数回のクリックだけでこれを視覚化できます。具体的には、プロジェクト モジュールの出力で視覚化オプションを選択し、いずれかの特徴のヘッダーをクリックして、ビジュアライザーを起動します。そのうえで、比較対象のドロップダウンでクラス ラベルを選択します。
1. 特徴のごく一部がラベルと密接に関係しているため、特徴選択ありと特徴選択なしの両方で線形および非線形分類器を組み合わせてテストして、最適なモデルを特定します。この比較は、モデル評価モジュールを通じて行います。    

## 結果 ##

1. 非線形分類器 (ブースト デシジョン ツリー) の方が線形分類器 (ロジスティック回帰) よりも若干パフォーマンスが高くなっています。 

![][1]


次に、特徴選択ありでブースト デシジョン ツリーと比較してみます。分類のパフォーマンスは非常によく似ているものの、すべての特徴でブースト デシジョン ツリーの方がパフォーマンスが若干高くなっているため、スコア付けワークフローにはこちらを使用します。この高い分類 AUC は、このデータセットの典型です。 


![][2]

<!-- Removed until this part is fixed
## Operationalization ##


We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio: 

1. First step is to save the learned model (by right clicking on the classifier module output) 
1. Now create a new experiment and search for saved model and drop it in the panel for new experiment 
1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment 
1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service 
1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio   
-->


## サンプル実験

ML Studio には、このモデルに対応した次のサンプル実験が用意されています。**[サンプル]** タブの **[実験]** セクションにあります。

> **サンプル実験 - ネットワーク不正侵入検出 - 開発**


## サンプル データセット

ML Studio には、この実験で使用される次のサンプル データセットが用意されています。モジュール パレットの **[保存されたデータセット]** にあります。

###network_intrusion_detection.csv
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../includes/machine-learning-sample-dataset-network-intrusion.md)]




[1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
[2]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
