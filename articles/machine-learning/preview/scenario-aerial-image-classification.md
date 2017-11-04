---
title: "航空画像の分類 | Microsoft Docs"
description: "航空画像の分類に関する実際のシナリオの手順について説明します。"
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 09/15/2017
ms.openlocfilehash: e6b673527d77550d4213e6d742156ccc525f6b44
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2017
---
# <a name="aerial-image-classification"></a>航空画像の分類

この例は、Azure Machine Learning Workbench を使用して、画像分類モデルの分散トレーニングと運用化を調整する方法を示しています。 ここでは [Microsoft Machine Learning for Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) パッケージを使用し、トレーニング済みの CNTK モデルを使用して画像を特徴付けし、生成された特徴を使用して分類をトレーニングします。 次に、トレーニングしたモデルを並行してクラウド内の大規模な画像セットに適用します。 これらの手順は [Azure HDInsight Spark](https://azure.microsoft.com/en-us/services/hdinsight/apache-spark/) クラスターに対して実行されるので、worker ノードを増減することで、トレーニングと運用化の速度を上下できます。

ここで説明する転移学習の形式は、深層ニューラル ネットワークの再トレーニングや微調整よりも大きな利点があります。具体的には、GPU コンピューティングが不要、本質的に高速で任意の拡大縮小が可能、少ないパラメーターに適合するという利点があります。 そのため、この方法は、少ないトレーニング サンプルを使用可能な場合 (カスタム ユース ケースでよく見られる例) に最適です。 多くのユーザーは、転移学習によって高パフォーマンスのモデルが生成されるので、はるかに高いコストでゼロから深層ニューラル ネットワークをトレーニングしなくても済むと報告しています。

この例で使用される Spark クラスターには 40 worker ノードがあり、運用コストは 1 時間あたり 40 ドル以下です。 このチュートリアルの完了には約 2 時間かかります。 チュートリアルを完了したら、クリーンアップ手順に従って、作成したリソースを削除し、課金を停止してください。

## <a name="link-to-the-gallery-github-repository"></a>ギャラリーの GitHub リポジトリへのリンク

この実際のシナリオに対応するパブリック GitHub リポジトリには、コード サンプルなど、この例に必要なすべての素材が含まれています。

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>ユース ケースの説明

このシナリオでは、機械学習モデルをトレーニングして、224 m × 224 m プロットの航空画像に表示される土地の種類を分類します。 土地利用の分類モデルでは、定期的に収集した航空画像を使用して、都会化、森林伐採、湿地帯の消失などの主要な環境傾向を追跡することができます。 ここでは、米国National Agriculture Imagery Program の画像と、米国National Land Cover Database が公開している土地利用ラベルに基づいて設定したトレーニングと検証画像を用意しました。 各土地利用クラスの画像例を次に示します。

![各土地利用ラベルの地域例](media/scenario-aerial-image-classification/example-labels.PNG)

分類モデルのトレーニングと検証の後に、マサチューセッツ州ミドルセックス郡 (Microsoft の New England Research & Development (NERD) Center の拠点) 全体を撮影した航空画像に適用し、これらのモデルを使用して都市開発の傾向を研究することができます。

多くの場合、データ サイエンティストは、転移学習を使用して画像分類を生成するために、方法の範囲を指定して複数のモデルを構築し、最も効率的なモデルを選択します。 データ サイエンティストは Azure Machine Learning Workbench を使用して、さまざまなコンピューティング環境のトレーニングを調整し、複数のモデルのパフォーマンスを追跡して比較し、選択したモデルをクラウドの大規模なデータセットに適用することができます。

## <a name="scenario-structure"></a>シナリオの構造

この例では、画像データとトレーニング済みのモデルは Azure ストレージ アカウントに保存されます。 Azure HDInsight Spark クラスターはこれらのファイルを読み取り、MMLSpark を使用して画像分類モデルを構築します。 トレーニング済みモデルとその予測はストレージ アカウントに書き込まれます。書き込まれたデータは、ローカルで実行されている Jupyter ノートブックで分析し、視覚化することができます。 Azure Machine Learning Workbench は、Spark クラスター上のスクリプトのリモート実行を調整します。 また、異なる方法を使用してトレーニングされた複数のモデルについて精度メトリックも追跡し、最も効率的なモデルをユーザーが選択できるようにします。

![航空画像分類の実際のシナリオに関する概略図](media/scenario-aerial-image-classification/scenario-schematic.PNG)

この手順では、データ転送と依存関係のインストールなど、Azure ストレージ アカウントと Spark クラスターの作成と準備について説明します。 次に、トレーニング ジョブの開始方法と、結果のモデルのパフォーマンスを比較する方法について説明します。 最後に、Spark クラスターに設定されている大規模な画像に選択したモデルを適用し、ローカルで予測結果を分析する方法について説明します。


## <a name="set-up-the-execution-environment"></a>実行環境を設定する

次の手順では、この例の実行環境を設定するプロセスを説明します。

### <a name="prerequisites"></a>前提条件
- [Azure アカウント](https://azure.microsoft.com/en-us/free/) (無料試用版もご利用いただけます)
    - このサンプルでは、worker ノード数が 40 個の HDInsight Spark クラスターを作成します (合計 168 コア)。 Azure Portal のサブスクリプションの [使用量 + クォータ] タブで、アカウントで使用できるコアが十分にあることを確認します。
    - 使用可能なコアがあまりない場合は、HDInsight クラスター テンプレートを変更して、プロビジョニングする worker 数を減らすことができます。 この手順については、「HDInsight Spark クラスターの作成」セクションを参照してください。
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - [インストールと作成のクイックスタート](quickstart-installation.md)に関するページを参照して Azure Machine Learning Workbench をインストールし、実験用アカウントとモデル管理アカウントを作成します。
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy)。Azure ストレージ アカウント間のファイル転送を調整する無料のユーティリティです。
    - AzCopy の実行可能ファイルを含むフォルダーが、システム PATH 環境変数にあることを確認します (環境変数の変更手順については、[こちら](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp)を参照してください)。この例は、Windows 10 PC でテストされましたが、Azure Data Science Virtual Machines など、任意の Windows マシンから実行することができます。 この例を macOS 上で実行するには、若干の変更が必要になる可能性があります (ファイルパスの変更など)。

### <a name="set-up-azure-resources"></a>Azure リソースの設定

この例では、関連するファイルをホストするために HDInsight Spark クラスターと Azure ストレージ アカウントが必要です。 これらの手順に従って、新しい Azure リソース グループに以下のリソースを作成します。

#### <a name="create-a-new-workbench-project"></a>新しいワークベンチ プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Azure Machine Learning Workbench を開きます
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに「Aerial Image Classification」と入力し、テンプレートを選択します
5.  **[作成]**
 
#### <a name="create-the-resource-group"></a>リソース グループの作成

1. Azure Machine Learning Workbench プロジェクトから、[ファイル]、[コマンド プロンプトを開く] の順にクリックしてコマンドライン インターフェイス (CLI) を開きます。
1. コマンド ライン インターフェイスから、次のコマンドを実行して Azure アカウントにログインします。

    ```
    az login
    ```

    URL にアクセスして指定された一時コードを入力するよう求められます。Web サイトから、お使いの Azure アカウント資格情報が要求されます。
    
1. ログインが完了したら、CLI に戻り、次のコマンドを実行して、どの Azure サブスクリプションを Azure アカウントに利用できるかを判定します。

    ```
    az account list
    ```

    このコマンドは、Azure アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。 使用するサブスクリプションの ID を見つけます。 次のコマンドの指示された場所にサブスクリプション ID を記述し、コマンドを実行してアクティブなサブスクリプションを設定します。

    ```
    az account set --subscription [subscription ID]
    ```

1. この例で作成した Azure リソースは、Azure リソース グループにまとめて保存されます。 一意のリソース グループ名を選択し、指示された場所に記述します。次に、両方のコマンドを実行して、Azure リソース グループを作成します。

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>ストレージ アカウントの作成

ここでは、常に HDInsight Spark からアクセスされるプロジェクト ファイルをホストするストレージ アカウントを作成します。

1. 一意のストレージ アカウント名を選択し、次の `set` コマンドの指示された場所に記述します。次に、両方のコマンドを実行して、Azure ストレージ アカウントを作成します。

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. 次のコマンドを発行して、ストレージ アカウント キーを一覧表示します。

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    `key1` の値を次のコマンドのストレージ キーとして記録し、コマンドを実行して値を格納します。
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. 使い慣れたテキスト エディターで、Azure Machine Learning Workbench プロジェクトの "Code" サブディレクトリから `settings.cfg` ファイルを読み込み、指示されたストレージ アカウント名とキーを挿入します。 ファイル内の変更された行は、次のようになります。
    ```
    [Settings]
        # Credentials for the Azure Storage account
        storage_account_name = storacctname
        storage_account_key = kpI...88 chars total...Q==
    ```
    `settings.cfg` ファイルを保存して閉じます。
1. まだ完了していない場合は、[AzCopy](http://aka.ms/downloadazcopy) ユーティリティをダウンロードしてインストールします。 「AzCopy」と入力し、Enter キーを押してドキュメントを表示して、AzCopy の実行可能ファイルがシステム パス上にあることを確認します。
1. 次のコマンドを発行して、すべてのサンプル データ、事前トレーニング済みモデル、およびモデル トレーニング スクリプトをストレージ アカウントの適切な場所にコピーします。

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    ファイル転送には、最大で 20 分かかります。 待機している間に、以下のセクションに進んでもかまいません。 Workbench から別のコマンド ライン インターフェイスを開き、そこで一時変数を再定義する必要が生じる場合があります。

#### <a name="create-the-hdinsight-spark-cluster"></a>HDInsight Spark クラスターの作成

推奨される HDInsight クラスターの作成方法では、このプロジェクトの "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" サブフォルダーに含まれている HDInsight Spark クラスター Resource Manager テンプレートを使用します。

1. HDInsight Spark クラスター テンプレートは、このプロジェクトの "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" サブフォルダーにある "template.json" ファイルです。 テンプレートでは既定で、40 ワーカー ノードの Spark クラスターを作成します。 この数値を調整する必要がある場合は、使い慣れたテキスト エディターでテンプレートを開き、すべてのインスタンスの "40" を指定したいワーカー ノード数に置き換えます。
    - 指定したワーカー ノード数が少ないと、メモリ不足エラーが発生する可能性があります。 メモリ エラーに対処するために、このドキュメントで後から説明する手順に従って、利用可能なデータのサブセット上でトレーニング スクリプトおよび運用化スクリプトを実行できます。
2. HDInsight クラスターの一意の名前とパスワードを選択し、次のコマンドの指示された場所に記述します。 その後、次のコマンドを実行してクラスターを作成します。

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

クラスターのデプロイには、最大 30 分かかる場合があります (プロビジョニングとスクリプト操作の実行を含む)。

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Azure Machine Learning Workbench 実行環境の準備

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>HDInsight クラスターを Azure Machine Learning Workbench コンピューティング ターゲットとして登録する

HDInsight クラスターの作成が完了したら、次の手順に従って、クラスターをお使いのプロジェクトのコンピューティング ターゲットとして登録します。

1.  Azure Machine Learning コマンド ライン インターフェイスから次のコマンドを発行します。

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    このコマンドでは、`myhdi.runconfig` および `myhdi.compute` という 2 つのファイルをお使いのプロジェクトの `aml_config` フォルダーに追加します。

1. 使い慣れたテキスト エディターで `myhdi.compute` ファイルを開きます。 `yarnDeployMode: client` を読み取るように `yarnDeployMode: cluster` 行を変更してから、ファイルを保存して閉じます。
1. 次のコマンドを実行して、環境を使用できるように準備します。
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>ローカルの依存関係のインストール

Azure Machine Learning Workbench から CLI を開き、次のコマンドを発行して、ローカル実行に必要な依存関係をインストールします。

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn
```

## <a name="data-acquisition-and-understanding"></a>データの取得と理解

このシナリオでは、[National Agriculture Imagery Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) (米国農業用地画像プログラム) で一般公開されている、解像度 1 メートルの航空画像データを使用します。 ここでは、224 ピクセル x 224 ピクセルの PNG ファイルのセットを元の NAIP データから生成し、[National Land Cover Database](https://www.mrlc.gov/nlcd2011.php) (米国土地被覆に関するデータベース) の土地用途ラベルに従って並べ替えました。 以下に "Developed" (開発済み) というラベルのサンプル画像をフル サイズで表示します。

![開発地のサンプル タイル](media/scenario-aerial-image-classification/sample-tile-developed.png)

~ 44 k および11 k 画像のクラス均衡セットはそれぞれ、モデルのトレーニングと検証に使用されます。 ここでは、マサチューセッツ州ミドルセックス群にあるマイクロソフトの New England Research and Development (NERD : ニューイングランド研究開発センター) センター拠点の、タイル状になった ~67k 画像 セットのモデル デプロイを示します。 これらの画像セットの構築方法の詳細については、git リポジトリの「[Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)」(驚異的な並行画像分類) をご覧ください。

![マサチューセッツ州ミドルセックス郡の場所](media/scenario-aerial-image-classification/middlesex-ma.png)

セットアップ中に、この例で使用される航空画像セットが、作成したストレージ アカウントに転送されました。 トレーニング、検証、および運用化の画像はすべて、1 平方メートルあたり 1 ピクセルの解像度の、224 ピクセル x 224 ピクセルの PNG ファイルです。 トレーニングと検証の画像は、土地用途ラベルに基づくサブフォルダーに編成済みです  (運用化の画像の土地用途ラベルは不確定であり、多くの場合はあいまいです。これらの画像の一部は、複数の土地種類を含みます)。これらの画像セットの構築方法の詳細については、git リポジトリの「[Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)」(驚異的な並行画像分類) をご覧ください。

Azure ストレージ アカウントでサンプル 画像を表示するには、次の手順を実行します (省略可能)。
1. [Azure Portal](https://portal.azure.com) にログインします。
1. 画面の上部にある検索バーで、ストレージ アカウントの名前を検索します。 検索結果で、該当のストレージ アカウントをクリックします。
2. ストレージ アカウントのメイン ウィンドウにある [BLOB] リンクをクリックします。
3. "train" という名前のコンテナーをクリックします。 土地用途に従って名前付けされたディレクトリの一覧が表示されます。
4. これらのディレクトリのいずれかをクリックして、含まれる画像の一覧を読み込みます。
5. 任意の画像をクリックし、ダウンロードして画像を表示します。
6. 必要な場合は、"test" および "middlesexma2016" という名前のコンテナーをクリックして、それらのコンテンツも一緒に表示します。

## <a name="modeling"></a>モデリング

### <a name="training-models-with-mmlspark"></a>MMLSpark によるモデルのトレーニング
Workbench プロジェクトの "Code\02_Modeling" サブフォルダーにある `run_mmlspark.py` スクリプトは、画像分類用の [MMLSpark](https://github.com/Azure/mmlspark) モデルのトレーニングに使用されます。 スクリプトでは最初に、ImageNet データセット (AlexNet または 18 レイヤーの ResNet) 上で事前にトレーニングされた画像分類子 DNN を使用して、トレーニング セットの画像の特徴付けを行います。 次に、スクリプトは、特徴付けした画像を使って MMLSpark モデル (ランダム フォレストまたはロジスティック回帰モデルのいずれか) をトレーニングし、画像を分類します。 その後、テスト画像セットは、トレーニング済みのモデルを使って特徴付けおよびスコア付けされます。 テスト セット上のモデルの予測精度が計算され、Azure Machine Learning Workbench の実行履歴機能に記録されます。 最後に、テスト セット上のトレーニング済みの MMLSpark モデルと予測が、BLOB ストレージに保存されます。

トレーニング済みモデルの一意の出力モデル名、事前トレーニング済みモデルの種類、および MMLSpark モデルの種類を選択します。 次のコマンド テンプレートの指示された場所に選択内容を記述して、Azure ML コマンド ライン インターフェイスからコマンドを実行して、再トレーニングを開始します。

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

追加の `--sample_frac` パラメーターは、利用可能なデータのサブセットを使って、モデルのトレーニングおよびテストに使用できます。 小さなサンプルの一部を使用すると、トレーニング済みモデルの精度が低下する代わりに、ランタイムとメモリの要件が下がります。 この件および他のパラメーターに関する詳細については、`python Code\02_Modeling\run_mmlspark.py -h` を実行してください。

ユーザーは別の入力パラメーターを使用してこのスクリプトを複数回実行することが、推奨されています。 結果として得られるモデルのパフォーマンスは、Azure Machine Learning Workbench の実行履歴機能で比較できます。

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Workbench の実行履歴機能を使用したモデル パフォーマンスの比較

いずれかの種類のトレーニング実行を 2 回以上終えたら、左側のメニュー バーにある時計のアイコンをクリックして、Workbench の実行履歴機能に移動します。 左側のスクリプト一覧から `run_mmlspark.py` を選びます ウィンドウに、すべての実行に対するテスト セットの精度比較が読み込まれます。 詳細を表示するには、下へスクロールして個々 の実行の名前をクリックします。

## <a name="deployment"></a>デプロイ

HDInsight のリモート実行を使用して、トレーニング済みモデルの 1 つをマサチューセッツ州ミドルセックス郡のタイル状の航空画像に適用するには、目的のモデルの名前を次のコマンドに挿入して、コマンドを実行します。

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

追加の `--sample_frac` パラメーターは、利用可能なデータのサブセットを使って、モデルの運用化に使用できます。 小さなサンプルの一部を使用すると、予測の完成度が低下する代わりに、ランタイムとメモリの要件が下がります。 この件および他のパラメーターに関する詳細については、`python Code\03_Deployment\batch_score_spark -h` を実行してください。

このスクリプトは、お使いのストレージ アカウントにモデルの予測を書き込みます。 予測は、次のセクションの説明に従って検証できます。

## <a name="visualization"></a>グラフ

Workbench プロジェクトの "Code\04_Result_Analysis" サブフォルダーにある "Model prediction analysis"\(モデル予測分析\) Jupyter ノートブックでは、モデルの予測を視覚化します。 次の手順に従って、ノートブックを読み込んで実行します。
1. Workbench でプロジェクトを開き、左側のメニューにあるフォルダー ("ファイル") アイコンをクリックして、ディレクトリ一覧を読み込みます。
2. "Code\04_Result_Analysis" サブフォルダーに移動し、"Model prediction analysis"\(モデル予測分析\) という名前のノートブックをクリックします。 ノートブックのプレビューが表示されます。
3. [Start Notebook Server]\(ノートブック サーバーの起動\) をクリックして、ノートブックを読み込みます。
4. 最初のセルで、結果を分析するモデルの名前を指示された場所に入力します。
5. [セル] > [Run All]\(すべて実行\) をクリックして、ノートブックのすべてのセルを実行します。
6. 提示された分析および視覚化の詳細を、ノートブックと共に読み取って確認します。

## <a name="cleanup"></a>クリーンアップ
例を完了したら、Azure コマンド ライン インターフェイスから次のコマンドを実行して、作成したすべてのリソースを削除することお勧めします。

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>参照

- 「[The Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)」 (驚異的な並行画像分類) リポジトリ
   - 自由に利用できる画像およびラベルからのデータセットの構築について説明します。
- 「[MMLSpark](https://github.com/Azure/mmlspark)」 GitHub リポジトリ
   - MMLSpark でのモデルのトレーニングおよび評価の追加サンプルを含みます。

## <a name="conclusions"></a>まとめ

Azure Machine Learning Workbench を使用すると、データ サイエンティストはリモート コンピューティングのターゲットに簡単にコードをデプロイできます。 この例では、ローカルのコードが、リモート実行のために HDInsight クラスター上にデプロイされました。 Azure Machine Learning Workbench の実行履歴機能では、複数のモデルのパフォーマンスを追跡し、最も精度の高いモデルを特定しました。 Workbench の Jupyter ノートブック機能では、インタラクティブなグラフィック環境でモデルの予測を視覚化しました。

## <a name="next-steps"></a>次のステップ
この例をさらに詳しく確認するには、次の操作を実行してください。
- Azure Machine Learning Workbench 実行履歴機能で、歯車の記号をクリックして、表示するグラフィックおよびメトリックを選択します。
- `run_logger` を呼び出すステートメントのサンプル スクリプトを検証します。 各メトリックが記録される方法を理解していることを確認してください。
- `blob_service` を呼び出すステートメントのサンプル スクリプトを検証します。 トレーニング済みモデルおよび予測が保存されクラウドから取得される方法を理解していることを確認してください。
- BLOB ストレージ アカウントで作成されたコンテナーの内容を調べます。 どのスクリプトまたはコマンドがファイルの各グループの作成を担っているかを理解していることを確認してください。
- 異なる MMLSpark モデルの種類をトレーニングするか、またはモデルのハイパーパラメーターを変更するように、トレーニング スクリプトを修正します。 実行履歴機能を使用して、変更によってモデルの精度が向上または低下したかを判断してください。
