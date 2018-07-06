---
title: Azure Machine Learning Workbench 内での CNTK を使用した画像分類 | Microsoft Docs
description: Azure ML Workbench を使用して、カスタム画像分類モデルをトレーニング、評価、およびデプロイします。
services: machine-learning
documentationcenter: ''
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 48c21638fe5756e6527288ed0fdc73dd9e331afd
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2018
ms.locfileid: "35640642"
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench を使用した画像分類

画像分類アプローチを使用して、コンピューター ビジョンの多くの問題を解決できます。
これには、オブジェクトがたとえば*犬*、*自動車*、*船*である場合に、*画像にオブジェクトが存在するか*というような質問に答えるモデルのビルドが含まれます。 または、*この患者の網膜スキャンによって、どのクラスの眼病重症度が示されるか*というようなより複雑な質問もあります。

このチュートリアルでは、このような問題の解決に対処します。 ディープ ラーニング用の [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) を使用して、独自の画像分類モデルをトレーニング、評価、およびデプロイする方法を示します。
サンプル画像は提供していますが、読者が独自のデータセットを取り込み、独自のカスタム モデルをトレーニングすることもできます。

コンピューター ビジョン ソリューションは従来、画像内の目的の情報を強調するいわゆる*特徴*を手動で識別し、実装するために、専門知識を必要としていました。
この手動のアプローチは 2012 年に有名な [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] ディープ ラーニング論文によって変わり、現在は、ディープ ニューラル ネットワーク (DNN) を使用して、これらの特徴を自動的に検出できます。
DNN は、画像分類だけでなく、オブジェクト検出や画像の類似性などの他のコンピューター ビジョンの問題の分野でも大幅な改善につながりました。


## <a name="link-to-the-gallery-github-repository"></a>ギャラリーの GitHub リポジトリへのリンク
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>概要

このチュートリアルは、3 つの部分に分かれています。

- パート 1 では、事前トレーニング済み DNN を特徴抽出器として使用し、その出力で SVM をトレーニングして、画像分類システムをトレーニング、評価、およびデプロイする方法を示します。
- パート 2 では、たとえば DNN を固定の特徴抽出器として使用するのではなく、DNN を調整することによって、精度を高める方法を示します。
- パート 3 では、提供されたサンプル画像の代わりに独自のデータセットを使用する方法と、必要に応じて、ネットから画像をスクラップして、独自のデータセットを生成する方法を示します。

機械学習と CNTK の事前の経験は必要ありませんが、基本的な原理を理解するために役立ちます。 チュートリアルで報告されている精度の数値やトレーニング時間などは、参考目的のみであり、コードを実行したときの実際の値は、ほぼ確実に異なります。


## <a name="prerequisites"></a>前提条件

この例を実行するための前提条件は次のとおりです。

1. [Azure アカウント](https://azure.microsoft.com/free/) (無料試用版も使用できます)。
2. [クイック スタート インストール ガイド](../service/quickstart-installation.md)に従ってプログラムをインストールし、ワークスペースを作成するための [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)。  
3. Windows マシン。 Workbench は Windows と MacOS のみをサポートしており、Microsoft の Cognitive Toolkit (ディープ ラーニング ライブラリとして使用する) は Windows と Linux のみをサポートしているため、Windows OS が必要です。
4. 専用 GPU は、パート 1 で SVM トレーニングを実行するためには必要ありませんが、パート 2 で説明されている DNN の調整に必要です。 強力な GPU がないか、複数の GPU でトレーニングする必要があるか、または Windows マシンがない場合は、Windows オペレーティング システムで Azure のディープ ラーニング仮想マシンを使用することを検討してください。 ワンクリック デプロイ ガイドについては、[こちら](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)を参照してください。 デプロイ後、リモート デスクトップ接続経由で VM に接続し、そこで Workbench をインストールして、VM からローカルでコードを実行します。
5. OpenCV などのさまざまな Python ライブラリをインストールする必要があります。 Workbench の *[ファイル]* メニューから *[コマンド プロンプトを開く]* をクリックし、次のコマンドを実行してこれらの依存関係をインストールします。  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - http://www.lfd.uci.edu/~gohlke/pythonlibs/ から OpenCV ホイールをダウンロードした後の `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` (正確なファイル名とバージョンは変更されていることがあります)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`
    - `jupyter nbextension enable --py widgetsnbextension`

### <a name="troubleshooting--known-bugs"></a>トラブルシューティング/既知のバグ
- パート 2 では GPU が必要ですが、それが存在しない場合に、DNN を調整しようとすると、エラー "Batch normalization training on CPU is not yet implemented (CPU のバッチ正規化トレーニングはまだ実装されていません)" がスローされます。
- ミニバッチ サイズ (`PARAMETERS.py` 内の変数 `cntk_mb_size`) を減らすことで DNN トレーニング中のメモリ不足エラーを回避できます。
- このコードは、CNTK 2.2 を使用してテストされましたが、旧バージョン (最大 v2.0) および新しいバージョンでも変更なし (または小さな変更のみ) で実行できるはずです。
- 執筆時に、Azure Machine Learning Workbench には 5 MB を超えるノートブックの表示の問題がありました。 この大きなサイズのノートブックは、すべてのセル出力が表示された状態でノートブックが保存された場合に発生する可能性があります。 このエラーが発生した場合は、Workbench 内の [ファイル] メニューからコマンド プロンプトを開き、`jupyter notebook` を実行して、ノートブックを開き、すべての出力をクリアして、ノートブックを保存します。 この手順を実行すると、Azure Machine Learning Workbench 内で、再度ノートブックが正しく開きます。
- このサンプルで提供されているすべてのスクリプトは、ローカルで実行する必要があります。docker などのリモート環境では実行しないでください。 すべてのノートブックは、カーネルを "PROJECTNAME local" (例: "myImgClassUsingCNTK local") という名前のローカル プロジェクト カーネルに設定して実行する必要があります。

    
## <a name="create-a-new-workbench-project"></a>新しいワークベンチ プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成するには
1.  Azure Machine Learning Workbench を開きます。
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します。
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します。
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに「Image classification」と入力し、テンプレートを選択します。
5.  **Create** をクリックしてください。

この手順を実行すると、下に示すプロジェクト構造が作成されます。 プロジェクト ディレクトリは、Azure Machine Learning Workbench が実行のたびにこのフォルダーのコピーを作成するため (実行履歴を有効にするため)、25 MB 未満になるように制限されます。 したがって、すべての画像および一時ファイルがディレクトリ *~/Desktop/imgClassificationUsingCntk_data* (このドキュメントでは *DATA_DIR* と呼ばれる) に保存されます。

  フォルダー| 説明
  ---|---
  aml_config/|                           Azure Machine Learning Workbench 構成ファイルを格納するディレクトリ
  libraries/|                              すべての Python および Jupyter ヘルパー関数を格納するディレクトリ
  notebooks/|                              すべてのノートブックを格納するディレクトリ
  resources/|                              すべてのリソース (ファッション画像の URL など) を格納するディレクトリ
  scripts/|                              すべてのスクリプトを格納するディレクトリ
  PARAMETERS.py|                       すべてのパラメーターを指定する Python スクリプト
  readme.md|                           この readme ドキュメント


## <a name="data-description"></a>データの説明

このチュートリアルでは、現行サンプルとして、最大 428 画像から構成される上衣のテクスチャ データセットを使用しています。 各画像は、3 つの異なるテクスチャ (ドット、ストライプ、ヒョウ柄) のいずれかとして注釈が付けられています。 このチュートリアルを迅速に実行できるように、画像数を少なくしています。 ただし、コードは十分にテストされているため、数万件以上の画像でも動作します。 すべての画像は Bing Image Search を使用してスクラップされ、[パート 3](#using-a-custom-dataset) で説明するように、手動で注釈が付けられています。 各属性と共に画像の URL が、*/resources/fashionTextureUrls.tsv* ファイルに一覧表示されています。

スクリプト `0_downloadData.py` はすべての画像を *DATA_DIR/images/fashionTexture/* ディレクトリにダウンロードします。 428 個の URL の一部は、切断されている可能性があります。 これは問題ではなく、単にトレーニングとテストに使用する画像がやや少ないことを意味しています。 このサンプルで提供されているすべてのスクリプトは、ローカルで実行する必要があります。docker などのリモート環境では実行しないでください。

次の図は、ドット (左側)、ストライプ (中間 2 )、およびヒョウ柄 (右側) の属性の例を示しています。 注釈は上衣に従って付けられました。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>パート 1 - モデルのトレーニングと評価

このチュートリアルの最初の部分では、事前トレーニング済みのディープ ニューラル ネットワークを使用する (ただし変更しない) システムをトレーニングします。 この事前トレーニング済み DNN を、特徴抽出器として使用し、線形 SVM をトレーニングして、特定の画像の属性 (ドット、ストライプ、ヒョウ柄) を予測します。

このアプローチを詳細に手順を追って説明したら、実行する必要があるスクリプトを示します。 各手順の後に、どのファイルに書き込まれ、どこに書き込まれたかを調べることをお勧めします。

1 か所 (`PARAMETERS.py` ファイル) に重要なすべてのパラメーターが指定され、簡単な説明が示されています。




### <a name="step-1-data-preparation"></a>手順 1: データの準備
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

ノートブック `showImages.ipynb` を使用して、画像を視覚化し、必要に応じて、それらの注釈を修正できます。 ノートブックを実行するには、Azure Machine Learning Workbench でそれを開き、[Start Notebook Server]\(ノートブック サーバーの起動\) オプションが表示されている場合はこれをクリックして、ローカル プロジェクト カーネルを "PROJECTNAME local" (例: "myImgClassUsingCNTK local") という名前に変更した後、ノートブックのすべてのセルを実行します。 ノートブックが大きすぎて、表示できないことを示すエラーが発生した場合は、このドキュメントのトラブルシューティングのセクションを参照してください。
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

ここで、`1_prepareData.py` という名前のスクリプトを実行します。これは、すべての画像をトレーニング セットまたはテスト セットのいずれかに割り当てます。 この割り当ては相互に排他的です。トレーニング画像はテストに使用されず、その逆も同じです。 既定で、各属性クラスから、ランダムな 75% の画像がトレーニングに割り当てられ、残りの 25% がテストに割り当てられます。 スクリプトによって生成されるすべてのデータは *DATA_DIR/proc/fashionTexture/* フォルダーに保存されます。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>手順 2: ディープ ニューラル ネットワークの調整
`Script: 2_refineDNN.py`

このチュートリアルのパート 1 で説明したように、事前トレーニング済み DNN は固定されています (つまり、調整されません)。 ただし、`2_refineDNN.py` というスクリプトもパート 1 で 実行します。これは事前トレーニング済み [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2] モデルを読み込み、たとえば、入力画像の高い解像度を許可するように、それを変更します。 この手順は高速 (数秒) であるため、GPU を必要としません。

チュートリアルのパート 2 では、PARAMETERS.py ファイルを変更して、`2_refineDNN.py` スクリプトによって、事前トレーニング済み DNN を調整させます。 既定で、調整時に 45 個のトレーニング エポックを実行します。

どちらの場合も、最終的なモデルは、ファイル *DATA_DIR/proc/fashionTexture/cntk_fixed.model* に書き込まれます。

### <a name="step-3-evaluate-dnn-for-all-images"></a>手順 3: すべての画像の DNN の評価
`Script: 3_runDNN.py`

最後の手順から (なるべく調整済みの) DNN を使用して、画像の特徴を抽出できます。 DNN への入力として画像を指定すると、出力は、モデルの最後から 2 番目のレイヤーからの 512 個の浮動小数点ベクターです。 このベクターは、画像自体よりはるかにディメンションが小さくなります。 ただし、画像の属性の認識に関連する画像内のすべての情報を含む (さらに強調する) 必要があります。つまり、衣服はドット柄か、ストライプ柄か、またはヒョウ柄かどうかです。

すべての DNN 画像表現はファイル *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle* に保存されます。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>手順 4: サポート ベクター マシン トレーニング
`Script: 4_trainSVM.py`

最後の手順で計算された 512 個の浮動小数点数表現を使用して、SVM 分類器をトレーニングします。入力として画像を指定すると、SVM は存在する各属性のスコアを出力します。 このサンプル データセットでは、これは 'ストライプ'、'ドット'、'ヒョウ柄' のスコアを意味します。

スクリプト `4_trainSVM.py` はトレーニング画像を読み込み、正則化 (スラック) パラメーター C のさまざまな値で SVM をトレーニングし、最高精度で SVM を保持します。 分類精度は、コンソールに出力され、Workbench にプロットされます。 提供されたテクスチャ データの場合、これらの値はそれぞれ約 100% と 88% になるはずです。 最後に、トレーニング済み SVM がファイル *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np* に書き込まれます。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>手順 5: 評価と視覚化
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

トレーニング済み画像分類器の精度は、スクリプト `5_evaluate.py` を使用して測定できます。 スクリプトは、トレーニング済み SVM 分類器を使用してすべてのテスト 画像にスコアを付け、各画像に最高スコアの属性を割り当て、予測された属性をグラウンド トゥルース注釈と比較します。

スクリプト `5_evaluate.py` の出力を次に示します。 個々のクラスの分類精度に加えて、完全なテスト セットの精度 ('全体の精度') と個々の精度の平均 ('全体のクラス平均精度') が計算されます。 100% は可能な限り最高の精度に対応し、0% は最低です。 平均のあて推量によって、属性数全体で 1 のクラス平均精度が生成されます。ここの例では、この精度は 33.33% になります。 これらの結果は、`rf_inputResoluton = 1000` などの高い入力解像度を使用した場合に大幅に向上しますが、DNN の計算時間が長くなる犠牲を伴います。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

精度に加えて、各曲線下面積 (左側) で ROC 曲線がプロットされ、混同行列が表示されます (右側)。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

最後に、テスト 画像をスクロールし、それらの各分類スコアを視覚化するノートブック `showResults.py` が提供されます。 手順 1 で説明したように、このサンプルにあるすべてのノートブックでは、"PROJECTNAME local" という名前のローカル プロジェクト カーネルを使用する必要があります。
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>手順 6: デプロイ
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

これで、トレーニング済みシステムを REST API として公開できます。 デプロイはノートブック `deploy.ipynb` で説明されており、Azure Machine Learning Workbench 内の機能に基づいています (必ず、"PROJECTNAME local" という名前のローカル プロジェクト カーネルをカーネルとして設定してください)。 デプロイの詳細については、[IRIS チュートリアル](tutorial-classifying-iris-part-3.md)の優れたデプロイに関するセクションも参照してください。

デプロイすると、スクリプト `6_callWebservice.py` を使用して、Web サービスを呼び出すことができます。 Web サービスの IP アドレス (ローカルまたはクラウド上のいずれか) をスクリプトの最初に設定する必要があります。 ノートブック `deploy.ipynb` にこの IP アドレスを見つける方法を説明しています。








## <a name="part-2---accuracy-improvements"></a>パート 2 - 精度の向上

パート 1 で、ディープ ニューラル ネットワークの 512 個の浮動小数点数出力で線形サポート ベクター マシンをトレーニングすることによって、画像を分類する方法を示しました。 この DNN は数百万の画像で事前トレーニングされ、特徴ベクターとして最後から 2 番目のレイヤーが返されました。 このアプローチは、DNN をそのまま使用しているため高速で、それでも多くの場合に良い結果が得られます。

ここで、パート 1 のモデルの精度を向上するいくつかの方法を示します。 特に DNN を固定せずに、調整します。

### <a name="dnn-refinement"></a>DNN の調整

SVM の代わりに、ニューラル ネットワークで直接分類を行うことができます。 これは、入力として最後から 2 番目のレイヤーから 512 個の浮動小数点数値を取得する事前トレーニング済みの DNN に、新しい最後のレイヤーを追加することによって実現されます。 DNN で分類を行う利点は、誤差逆伝播法を使用して、完全なネットワークを再トレーニングできるようになったことです。 このアプローチは、事前トレーニング済み DNN をそのまま使用する場合と比較して、はるかに分類精度が向上することがありますが、トレーニング時間が大幅に長くなる (GPU を使用しても) 犠牲を伴います。

SVM の代わりのニューラル ネットワークのトレーニングは、`PARAMETERS.py` の変数 `classifier` を `svm` から `dnn` に変更して、行います。 次に、パート 1 で説明したように、データ準備 (手順 1) と SVM トレーニング (手順 4) を除くすべてのスクリプトを再度実行する必要があります。 DNN の調整には、GPU が必要です。 GPU が検出されないか、または GPU がロックされている (たとえば以前の CNTK 実行によって) 場合、スクリプト `2_refineDNN.py` はエラーをスローします。 DNN トレーニングでは一部の GPU でメモリ不足のエラーがスローされる可能性があります。これは、ミニバッチ サイズ (`PARAMETERS.py` 内の変数 `cntk_mb_size`) を小さくすることによって回避できます。

トレーニングが完了すると、調整されたモデルが *DATA_DIR/proc/fashionTexture/cntk_refined.model* に保存され、トレーニング中のトレーニングとテストの分類エラーの変化を示すプロットが描画されます。 そのプロットで、トレーニング セットのエラーは、テスト セットよりはるかに少ないことに注意してください。 このいわゆるオーバーフィット動作は、たとえばドロップアウト率 `rf_dropoutRate` に大きい値を使用することで削減できます。
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

下のプロットに示されているように、提供されたデータセットでの DNN の調整を使用した精度は以前 (パート 1) の 88.92% に対して 92.35% になります。 特に、'ドット' 画像は、ROC 曲線下面積が、調整によって 0.98 と、以前の 0.94 に対して、大幅に改善されています。 ここでは小規模のデータセットを使用しているため、コードを実行した実際の精度は異なります。 この相違は、トレーニング セットとテスト セットへの画像のランダムな分割などの確率論的な影響によるものです。
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>実行履歴の追跡

Azure Machine Learning Workbench は、Azure での実行ごとの履歴を保存しているため、数週間離れている複数の実行でも比較できます。 これについては、[Iris チュートリアル](tutorial-classifying-iris-part-2.md)で詳しく説明しています。 これについては、次のスクリーン ショットにも示しています。ここでは DNN の調整を使用した実行、つまり `classifier = "dnn"` (実行番号 148) と SVM トレーニングを使用した実行、つまり `classifier = "svm"` (実行番号 150) の 2 つの `5_evaluate.py` スクリプトの実行を比較しています。

最初のスクリーンショットで、DNN の調整により、すべてのクラスの SVM トレーニングよりも精度が向上しています。 2 番目のスクリーン ショットでは、分類器が何かなど、追跡対象のすべてのメトリックを示しています。 この追跡は、スクリプト `5_evaluate.py` で Azure Machine Learning Workbench ロガーを呼び出すことによって行われます。 さらに、スクリプトは、ROC 曲線と混同行列を *outputs* フォルダーに保存します。 この *outputs* フォルダーは、その内容も、Workbench 履歴機能によって追跡される点で特別であり、そのためにローカル コピーが上書きされているかどうかに関係なく、出力ファイルにいつでもアクセスできます。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>パラメーターのチューニング

ほとんどの機械学習プロジェクトに当てはまりますが、新しいデータセットで良い結果を得るには、パラメーターの慎重なチューニングと、さまざまな設計に関する決定を評価することが必要です。 これらのタスクに役立つように、1 か所 (`PARAMETERS.py` ファイル) に重要なすべてのパラメーターが指定され、簡単な説明が示されています。

改善に向けた最も有望な手法のいくつかを次に示します。

- データ品質: トレーニング セットとテスト セットの高品質を確保します。 つまり、画像には正しく注釈が付けられ、あいまいな画像が除去され (たとえば、ドットとストライプの両方がある衣服など)、属性が相互に排他的である (つまり、各画像が正確に 1 つの属性に属するように選択する) ようにします。

- 画像内の対象のオブジェクトが小さい場合、画像分類アプローチはうまく機能しないことが知られています。 このような場合は、この[チュートリアル](https://github.com/Azure/ObjectDetectionUsingCntk)で説明しているように、オブジェクト検出アプローチを使用することを検討してください。
- DNN の調整: おそらく最も重要なパラメーターは、学習率 `rf_lrPerMb` です。 トレーニング セットの精度 (パート 2 の最初の図) は 0 ～ 5 % にほど遠く、これは学習率が悪いことが最大の原因であると考えられます。 `rf_` から始まる他のパラメーターはあまり重要ではありません。 通常、トレーニング後にトレーニング エラーは指数関数的に減少し、0% に近づくはずです。

- 入力解像度: 既定の画像解像度は 224 x 224 ピクセルです。 448 x 448 ピクセルや 896x896 ピクセルなどの高い画像解像度 (パラメーター: `rf_inputResoluton`) を使用すると、大幅に精度が向上することがありますが、DNN の調整の速度が低下します。 **高い画像解像度を使用することは、ほぼ無料で、ほぼ常に精度が飛躍的に向上します**。

- DNN のオーバーフィット: DNN の調整時 (パート 2 の最初の図) は、トレーニングとテストの精度の大きな差を回避します。 この差を縮小するには、0.5 以上のドロップアウト率 `rf_dropoutRate` を使用し、正則化の重み `rf_l2RegWeight` を増やします。 高いドロップアウト率を使用すると、DNN 入力画像の解像度が高い場合に特に役立つことがあります。

- `rf_pretrainedModelFilename` を `ResNet_18.model` から `ResNet_34.model` または `ResNet_50.model` のいずれかに変更することで、深い DNN を使用してみます。 Resnet-50 モデルは、深いだけでなく、最後から 2 番目のレイヤーの出力が、2048 個の浮動小数点数値になります (ResNet-18 および ResNet-34 モデルの 512 個の浮動小数点数値に対して)。 この大きいディメンションは、SVM 分類器のトレーニング時に特に有益です。

## <a name="part-3---custom-dataset"></a>パート 3 - カスタム データセット

パート 1 および 2 では、提供された上衣のテクスチャ画像を使用して、画像分類モデルをトレーニングし、評価しました。 ここでは、代わりにユーザーが提供したカスタム データセットを使用する方法を示します。 または、そうしたデータセットを入手できない場合に、Bing Image Search を使用して、そのようなデータセットを生成し、注釈を付ける方法も説明します。

### <a name="using-a-custom-dataset"></a>カスタム データセットの使用

まず、衣服のテクスチャ データ用のフォルダー構造を見てみましょう。 異なる属性のすべての画像が、*DATA_DIR/images/fashionTexture/* の各サブフォルダー *dotted*、*leopard、および*striped* 内にあることに注意します。 また、画像フォルダー名も、`PARAMETERS.py` ファイル内に示されていることにも注意してください。
```python
datasetName = "fashionTexture"
```

カスタム データセットの使用は、このフォルダー構造を再現するだけで簡単です。すべての画像を、それぞれの属性に従ってサブフォルダーに入れ、これらのサブフォルダーを新しいユーザー指定のディレクトリ *DATA_DIR/images/newDataSetName/* にコピーします。 必要なコード変更は、`datasetName` 変数に *newDataSetName* を設定するだけです。 次に、スクリプト 1 ～ 5 を順番に実行すると、すべての中間ファイルが、*DATA_DIR/proc/newDataSetName/* に書き込まれます。 他のコード変更は必要ありません。

各画像は正確に 1 つの属性にしか割り当てられないことが重要です。 たとえば、'animal' の属性と 'leopard の属性を使用することは、'leopard' 画像が 'animal' にも属することになるため、誤りです。 さらに、あいまいな画像とそのために注釈付けが難しい画像を除去することをおすすめします。



### <a name="image-scraping-and-annotation"></a>画像のスクラップと注釈

トレーニングとテストのために十分な数の注釈付き画像を収集することは困難な場合があります。 この問題に対処する 1 つの方法は、インターネットから画像をスクラップすることです。 たとえば、下のクエリ *t-shirt striped* の Bing Image Search 結果をご覧ください。 予想どおりに、ほとんどの画像がストライプの T シャツです。 いくつか正しくないか、あいまいな画像 (1 列目の 1 行目または 3 列目の 2 行目など) は簡単に識別し、除去できます。
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

大規模で多様なデータセットを生成するには、多数のクエリを使用する必要があります。 たとえば、7\*3 = 21 個のクエリは、衣服 {blouse, hoodie, pullover, sweater, shirt, t-shirt, vest} と属性{striped, dotted, leopard} のすべての組み合わせを使用して、自動的に合成することができます。 クエリごとに上位 50 個の画像をダウンロードすると、最大 21*50=1050 個の画像が得られることになります。

Bing Image Search から手動で画像をダウンロードする代わりに、[Cognitive Services Bing Image Search API](https://www.microsoft.com/cognitive-services/bing-image-search-api) を使用する方がはるかに簡単です。これは、クエリ文字列を指定すると、一連の画像の URL が返されます。

ダウンロードした画像の一部が、まったく同じかほぼ同じ (たとえば、画像解像度や jpg の圧縮効果だけが異なる) ことがあります。 トレーニングとテストの分割で、同じ画像が含まれないように、これらの重複を削除する必要があります。 重複する画像を削除するにはハッシュベースのアプローチを使用します。これは、2 つの手順で動作します。(i) まず、すべての画像のハッシュ文字列を計算し、(ii) 2 回目の画像の読み取りで、まだ見たことがないハッシュ文字列を持つ画像だけを保持します。 その他のすべての画像を破棄します。 Python ライブラリ `imagehash` で、`dhash` アプローチを見つけ、この[ブログ](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html)でパラメーター `hash_size` を 16 に設定して、うまく実行する方法を説明しました。 実際に重複している画像の大部分が削除されていれば、いくつか重複していない画像が誤って削除されてもかまいません。





## <a name="conclusion"></a>まとめ

この例の主要なポイントは次のとおりです。
- 画像分類モデルをトレーニング、評価、およびデプロイするコード。
- デモ画像は提供されていますが、独自の画像データセットを使用するように簡単に適応できます (1 行の変更)。
- 転移学習に基づいた高精度モデルをトレーニングするために最先端エキスパート機能が実装されています。
- Azure Machine Learning Workbench と Jupyter Notebook を使用した対話型モデル開発。


## <a name="references"></a>参照

[1] Alex Krizhevsky、Ilya Sutskever、および Geoffrey E. Hinton、「[_ImageNet Classification with Deep Convolutional Neural Networks_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf)」 (ディープ畳み込みニューラル ネットワークによる ImageNet の分類)。 NIPS 2012。  
[2] Kaiming He、Xiangyu Zhang、Shaoqing Ren、および Jian Sun、「[_Deep Residual Learning for Image Recognition_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf)」 (画像認識のための Deep Residual Learning)。 CVPR 2016。
