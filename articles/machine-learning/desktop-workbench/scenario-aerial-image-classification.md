---
title: 航空画像の分類 | Microsoft Docs
description: 航空画像の分類に関する実際のシナリオの手順について説明します。
author: mawah
ms.author: mawah
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.workload: data-services
ms.date: 12/13/2017
ms.openlocfilehash: 74cb72b4ddfbeace5e2409dfac4f4b6bf21ffa25
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="aerial-image-classification"></a>航空画像の分類

この例は、Azure Machine Learning Workbench を使用して、画像分類モデルの分散トレーニングと運用化を調整する方法を示しています。 トレーニングには 2 つのアプローチを使います。(i) [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) GPU クラスターを使って、ディープ ニューラル ネットワークを調整し、(ii) [Microsoft Machine Learning for Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) パッケージを使い、トレーニング済みの CNTK モデルを使って画像を特徴付けし、生成された特徴を使って分類をトレーニングします。 その後、[Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) クラスターを使ってクラウド内の大きな画像セットに並列方式でトレーニングされたモデルを適用します。こうすると、worker ノードを追加または削除することにより、トレーニングと運用化の速度を上げたり下げたりできます。

この例では、学習を転送する 2 つの方法を示します。トレーニング済みのモデルを利用することで、最初からディープ ニューラル ネットワークをトレーニングするコストを回避します。 通常、ディープ ニューラル ネットワークの再トレーニングには GPU の計算が必要ですが、トレーニング セットが十分な大きさの場合は、精度の向上につながる場合があります。 特徴付けした画像に対する単純な分類器のトレーニングは、GPU 計算を必要とせず、本質的に高速で任意にスケーラブルであり、より少ないパラメーターに適合します。 そのため、この方法は、少ないトレーニング サンプルを使用可能な場合 (カスタム ユース ケースでよく見られる例) に最適です。 

この例で使われている Spark クラスターは、40 worker ノードで、運用コストは約 40 ドル/時です。Batch AI クラスター リソースは、8 つの GPU を含み、運用コストは約 10 ドル/時です。 このチュートリアルの完了には約 3 時間かかります。 チュートリアルを完了したら、クリーンアップ手順に従って、作成したリソースを削除し、課金を停止してください。

## <a name="link-to-the-gallery-github-repository"></a>ギャラリーの GitHub リポジトリへのリンク

この実際のシナリオに対応するパブリック GitHub リポジトリには、コード サンプルなど、この例に必要なすべての素材が含まれています。

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>ユース ケースの説明

このシナリオでは、機械学習モデルをトレーニングして、224 m × 224 m プロットの航空画像に表示される土地の種類を分類します。 土地利用の分類モデルでは、定期的に収集した航空画像を使用して、都会化、森林伐採、湿地帯の消失などの主要な環境傾向を追跡することができます。 ここでは、米国National Agriculture Imagery Program の画像と、米国National Land Cover Database が公開している土地利用ラベルに基づいて設定したトレーニングと検証画像を用意しました。 各土地利用クラスの画像の例を次に示します。

![各土地利用ラベルの地域例](media/scenario-aerial-image-classification/example-labels.PNG)

分類モデルのトレーニングと検証の後に、マサチューセッツ州ミドルセックス郡 (Microsoft の New England Research & Development (NERD) Center の拠点) 全体を撮影した航空画像に適用し、これらのモデルを使用して都市開発の傾向を研究することができます。

多くの場合、データ サイエンティストは、転移学習を使用して画像分類を生成するために、方法の範囲を指定して複数のモデルを構築し、最も効率的なモデルを選択します。 データ サイエンティストは Azure Machine Learning Workbench を使用して、さまざまなコンピューティング環境のトレーニングを調整し、複数のモデルのパフォーマンスを追跡して比較し、選択したモデルをクラウドの大規模なデータセットに適用することができます。

## <a name="scenario-structure"></a>シナリオの構造

この例では、画像データとトレーニング済みのモデルは Azure ストレージ アカウントに保存されます。 Azure HDInsight Spark クラスターはこれらのファイルを読み取り、MMLSpark を使用して画像分類モデルを構築します。 トレーニング済みモデルとその予測はストレージ アカウントに書き込まれます。書き込まれたデータは、ローカルで実行されている Jupyter ノートブックで分析し、視覚化することができます。 Azure Machine Learning Workbench は、Spark クラスター上のスクリプトのリモート実行を調整します。 また、異なる方法を使用してトレーニングされた複数のモデルについて精度メトリックも追跡し、最も効率的なモデルをユーザーが選択できるようにします。

![航空画像分類の実際のシナリオに関する概略図](media/scenario-aerial-image-classification/scenario-schematic.PNG)

これらの手順は、データ転送と依存関係のインストールなど、Azure ストレージ アカウントと Spark クラスターの作成と準備の説明から始まります。 次に、トレーニング ジョブの開始方法と、結果のモデルのパフォーマンスを比較する方法について説明します。 最後に、Spark クラスターに設定されている大規模な画像に選択したモデルを適用し、ローカルで予測結果を分析する方法について説明します。


## <a name="set-up-the-execution-environment"></a>実行環境を設定する

次の手順では、この例の実行環境を設定するプロセスを説明します。

### <a name="prerequisites"></a>前提条件
- [Azure アカウント](https://azure.microsoft.com/free/) (無料試用版もご利用いただけます)
    - worker ノード数が 40 個の HDInsight Spark クラスターを作成します (合計 168 コア)。 Azure Portal のサブスクリプションの [使用量 + クォータ] タブで、アカウントで使用できるコアが十分にあることを確認します。
       - 使用可能なコアがあまりない場合は、HDInsight クラスター テンプレートを変更して、プロビジョニングする worker 数を減らすことができます。 この手順については、「HDInsight Spark クラスターの作成」セクションを参照してください。
    - このサンプルでは、2 つの NC6 (1 GPU、6 vCPU) VM から成る Batch AI トレーニング クラスターを作成します。 Azure Portal のサブスクリプションの [使用量 + クォータ] タブで、米国東部リージョンにアカウントで使用できるコアが十分にあることを確認します。
- [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)
    - [インストールと作成のクイックスタート](../service/quickstart-installation.md)に関するページを参照して Azure Machine Learning Workbench をインストールし、実験用アカウントとモデル管理アカウントを作成します。
- [Batch AI](https://github.com/Azure/BatchAI) Python SDK と Azure CLI 2.0
    - [Batch AI Recipes の README](https://github.com/Azure/BatchAI/tree/master/recipes) の次のセクションを完了します。
        - "Prerequisites (前提条件)"
        - "Create and get your Azure Active Directory (AAD) application (Azure Active Directory (AAD) アプリケーションの作成と取得)"
        - "Register BatchAI Resource Providers (BatchAI リソースプロバイダーの登録)" ("Run Recipes Using Azure CLI 2.0 (Azure CLI 2.0 を使用したレシピの実行)" の下)
        - "Install Azure Batch AI Management Client (Azure Batch AI 管理クライアントのインストール)"
        - "Install Azure Python SDK (Azure Python SDK のインストール)"
    - 作成するよう指示された Azure Active Directory アプリケーションのクライアント ID、シークレット、テナント ID をメモしておきます。 これらの資格情報は、このチュートリアルで後ほど使用します。
    - このドキュメントの作成時点では、Azure Machine Learning Workbench と Azure Batch AI は Azure CLI 2.0 の別々の分岐を使用しています。 はっきりさせるため、Workbench の CLI バージョンを "Azure Machine Learning Workbench から起動された CLI" と呼び、一般公開バージョン (Batch AI を含むもの) を "Azure CLI 2.0" と呼びます。
- [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)。Azure ストレージ アカウント間のファイル転送を調整する無料のユーティリティです。
    - AzCopy の実行可能ファイルを含むフォルダーが、システム PATH 環境変数にあることを確認します (環境変数の変更手順については、[こちら](https://support.microsoft.com/help/310519/how-to-manage-environment-variables-in-windows-xp)をご覧ください)。
- SSH クライアント。[PuTTY](http://www.putty.org/) をお勧めします。

この例は、Windows 10 PC でテストされましたが、Azure Data Science Virtual Machines など、任意の Windows マシンから実行することができます。 Azure CLI 2.0 は、[こちらの説明](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials)に従って MSI からインストールされました。 この例を macOS 上で実行するには、若干の変更が必要になる可能性があります (ファイルパスの変更など)。

### <a name="set-up-azure-resources"></a>Azure リソースの設定

この例では、関連するファイルをホストするために HDInsight Spark クラスターと Azure ストレージ アカウントが必要です。 これらの手順に従って、新しい Azure リソース グループに以下のリソースを作成します。

#### <a name="create-a-new-workbench-project"></a>新しい Workbench プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Azure Machine Learning Workbench を開きます
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに「Aerial Image Classification」と入力し、テンプレートを選択します
5.  **[作成]**
 
#### <a name="create-the-resource-group"></a>リソース グループの作成

1. Azure Machine Learning Workbench でプロジェクトを読み込んだ後、[ファイル] > [コマンド プロンプトを開く] の順にクリックしてコマンドライン インターフェイス (CLI) を開きます。
    チュートリアルの大部分ではこのバージョンの CLI を使います  (明記されている場合は、別のバージョンの CLI を開いて Batch AI リソースを準備する必要があります)。

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

1. 次のコマンドを実行して、ストレージ アカウント キーを一覧表示します。

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    `key1` の値を次のコマンドのストレージ キーとして記録し、コマンドを実行して値を格納します。
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. 次のコマンドで、`baitshare` という名前のファイル共有をストレージ アカウントに作成します。

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. 使い慣れたテキスト エディターで、Azure Machine Learning Workbench プロジェクトの "Code" サブディレクトリから `settings.cfg` ファイルを読み込み、指示されたストレージ アカウント名とキーを挿入します。 `settings.cfg` ファイルを保存して閉じます。
1. まだ完了していない場合は、[AzCopy](http://aka.ms/downloadazcopy) ユーティリティをダウンロードしてインストールします。 「AzCopy」と入力し、Enter キーを押してドキュメントを表示して、AzCopy の実行可能ファイルがシステム パス上にあることを確認します。
1. 次のコマンドを発行して、すべてのサンプル データ、事前トレーニング済みモデル、およびモデル トレーニング スクリプトをストレージ アカウントの適切な場所にコピーします。

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    ファイル転送には約 1 時間かかります。 待っている間に、以下のセクションに進んでもかまいません。Workbench から別のコマンド ライン インターフェイスを開き、そこで一時変数を再定義する必要が生じる場合があります。

#### <a name="create-the-hdinsight-spark-cluster"></a>HDInsight Spark クラスターの作成

お勧めする HDInsight クラスターの作成方法では、このプロジェクトの "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" サブフォルダーに含まれる HDInsight Spark クラスター Resource Manager テンプレートを使います。

1. HDInsight Spark クラスター テンプレートは、このプロジェクトの "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" サブフォルダーにある "template.json" ファイルです。 テンプレートでは既定で、40 ワーカー ノードの Spark クラスターを作成します。 この数値を調整する必要がある場合は、使い慣れたテキスト エディターでテンプレートを開き、すべてのインスタンスの "40" を指定したいワーカー ノード数に置き換えます。
    - 指定したワーカー ノード数が少ないと、後でメモリ不足エラーが発生する可能性があります。 メモリ エラーに対処するために、このドキュメントで後から説明する手順に従って、利用可能なデータのサブセット上でトレーニング スクリプトおよび運用化スクリプトを実行できます。
2. HDInsight クラスターの一意名とパスワードを選び、次のコマンドの指示された場所に記述します。その後、コマンドを実行してクラスターを作成します。

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

クラスターのデプロイには、最大 30 分かかる場合があります (プロビジョニングとスクリプト操作の実行を含む)。

### <a name="set-up-batch-ai-resources"></a>Batch AI リソースをセットアップする

ストレージ アカウント ファイルの転送と Spark クラスターのデプロイが完了するのを待つ間に、Batch AI ネットワーク ファイル サーバー (NFS) と GPU クラスターを準備できます。 Azure CLI 2.0 のコマンド プロンプトを開き、次のコマンドを実行します。

```
az --version 
```

インストール済みモジュールの一覧に `batchai` が含まれることを確認します。 ない場合は、異なるコマンド ライン インターフェイス (Workbench から開いたものなど) を使っている可能性があります。

次に、プロバイダーの登録が正常に完了していることを確認します  (プロバイダーの登録には最大で 15 分かかるので、[Batch AI セットアップ手順](https://github.com/Azure/BatchAI/tree/master/recipes)を最近完了した場合はまだ処理中である可能性があります)。次のコマンドの出力で、"Microsoft.Batch" と "Microsoft.BatchAI" の状態がどちらも "Registered" と表示されることを確認します。

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

そうでない場合は、次のプロバイダー登録コマンドを実行し、登録が完了するまで約 15 分待ちます。
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

次のコマンドを変更し、かっこで囲まれた式を、前のリソース グループとストレージ アカウントの作成時に使った値に置き換えます。 その後、次のコマンドを実行し、値を変数として格納します。
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Azure Machine Learning プロジェクトが含まれるフォルダーを確認します (例: `C:\Users\<your username>\AzureML\aerialimageclassification`)。 かっこで囲まれた値をフォルダーのファイルパス (末尾の円記号は除去) に置き換えて、コマンドを実行します。
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
これで、このチュートリアルに必要な Batch AI リソースを作成する準備が整いました。

#### <a name="prepare-the-batch-ai-network-file-server"></a>Batch AI ネットワーク ファイル サーバーを準備する

Batch AI クラスターは、ネットワーク ファイル サーバー上のトレーニング データにアクセスします。 Azure ファイル共有または Azure Blob Storage と比べて、NFS のファイルにアクセスするとデータ アクセスが数倍速くなる場合があります。

1. ネットワーク ファイル サーバーを作成するには、次のコマンドを実行します。

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p "Dem0Pa$$w0rd" --vm-size Standard_DS2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. 次のコマンドを使って、ネットワーク ファイル サーバーのプロビジョニング状態を確認します。

    ```
    az batchai file-server list
    ```

    "landuseclassifier" という名前のネットワーク ファイル サーバーの "provisioningState" が "succeeded" である場合は、使用可能な状態です。 プロビジョニングには 5 分程度かかります。
1. 前のコマンドの出力で NFS の IP アドレスを確認します("mountSettings" の "fileServerPublicIp" プロパティ)。 次のコマンドで示されている場所に IP アドレスを書き込み、コマンドを実行して値を格納します。

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. 使い慣れた SSH ツールを使い (次のサンプル コマンドでは [PuTTY](http://www.putty.org/) を使っています)、NFS に対してこのプロジェクトの `prep_nfs.sh` スクリプトを実行して、トレーニング用と検証用の画像セットを NFS に転送します。

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    データのダウンロードと展開の進行によるシェル ウィンドウのスクロール更新が速すぎて判読できなくても心配しないでください。

必要な場合は、好みの SSH ツールでファイル サーバーにログインして、`/mnt/data` ディレクトリの内容を調べることで、データ転送が計画どおりに行われたことを確認できます。 training_images と validation_images の 2 つのフォルダーが作成され、それぞれに土地利用カテゴリに対応した名前のサブフォルダーが含まれているはずです。  トレーニング用セットと検証用セットには、それぞれ、約 44 K と約 11 K の画像が含まれます。

#### <a name="create-a-batch-ai-cluster"></a>Batch AI クラスターを作成する

1. 次のコマンドを実行してクラスターを作成します。

    ```
    az batchai cluster create -n landuseclassifier2 -u demoUser -p "Dem0Pa$$w0rd" --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 --storage-account-name %STORAGE_ACCOUNT_NAME% 
    ```

1. クラスターのプロビジョニング状態を確認するには、次のコマンドを使います。

    ```
    az batchai cluster list
    ```

    "landuseclassifier" という名前のクラスターの割り当て状態が resizing から steady に変わったら、ジョブを送信できます。 ただし、クラスター内のすべての VM が "preparing" 状態ではなくなるまで、ジョブの実行は開始しません。 クラスターの "errors" プロパティが null ではない場合は、クラスターの作成中にエラーが発生しており、使うことはできません。

#### <a name="record-batch-ai-training-credentials"></a>Batch AI トレーニング資格情報を記録する

クラスターの割り当てが完了するのを待っている間に、このプロジェクトの "Code" サブディレクトリにある `settings.cfg` ファイルを任意のテキスト エディターで開きます。 次の変数を自分の資格情報で更新します。
- `bait_subscription_id` (36 文字の Azure サブスクリプション ID)
- `bait_aad_client_id` (「前提条件」セクションで説明されている Azure Active Directory アプリケーション/クライアント ID)
- `bait_aad_secret` (「前提条件」セクションで説明されている Azure Active Directory アプリケーション シークレット)
- `bait_aad_tenant` (「前提条件」セクションで説明されている Azure Active Directory テナント ID)
- `bait_region` (このドキュメントの作成時点では、eastus が唯一のオプション)
- `bait_resource_group_name` (前に選んだリソース グループ)

これらの値を割り当てた後、settings.cfg ファイルの変更後の行は次のテキストのようになります。

```
[Settings]
    # Credentials for the Azure Storage account
    storage_account_name = yoursaname
    storage_account_key = kpIXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ==
    
    # Batch AI training credentials
    bait_subscription_id = 0caXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX9c3
    bait_aad_client_id = d0aXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX7f8
    bait_aad_secret = ygSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX6I=
    bait_aad_tenant = 72fXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXb47
    bait_region = eastus
    bait_resource_group_name = yourrgname
```

`settings.cfg` を保存して閉じます。

Batch AI リソース作成コマンドを実行した CLI ウィンドウは閉じてもかまいません。 このチュートリアルの以降の手順はすべて、Azure Machine Learning Workbench から起動された CLI を使って行います。

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Azure Machine Learning Workbench 実行環境の準備

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>HDInsight クラスターを Azure Machine Learning Workbench コンピューティング ターゲットとして登録する

HDInsight クラスターの作成が完了したら、次の手順に従って、クラスターをお使いのプロジェクトのコンピューティング ターゲットとして登録します。

1.  Azure Machine Learning コマンド ライン インターフェイスから次のコマンドを発行します。

    ```
    az ml computetarget attach cluster --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD%
    ```

    このコマンドでは、`myhdi.runconfig` および `myhdi.compute` という 2 つのファイルをお使いのプロジェクトの `aml_config` フォルダーに追加します。

1. 使い慣れたテキスト エディターで `myhdi.compute` ファイルを開きます。 `yarnDeployMode: client` を読み取るように `yarnDeployMode: cluster` 行を変更してから、ファイルを保存して閉じます。
1. 次のコマンドを実行して、HDInsight 環境を使えるように準備します。
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>ローカルの依存関係のインストール

Azure Machine Learning Workbench から CLI を開き、次のコマンドを発行して、ローカル実行に必要な依存関係をインストールします。

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
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

### <a name="training-models-with-azure-batch-ai"></a>Azure Batch AI でのモデルのトレーニング

Batch AI トレーニング ジョブを実行するには、Workbench プロジェクトの "Code\02_Modeling" サブフォルダーにある `run_batch_ai.py` スクリプトを使います。 このジョブでは、ユーザーが選んだ画像分類子 DNN が再トレーニングされます (ImageNet でトレーニング済みの AlexNet または ResNet 18)。 再トレーニングの深さを指定することもできます。ネットワークの最後のレイヤーだけを再トレーニングすると、使用できるトレーニング サンプルが少ないときのオーバーフィットを減らすことができます。一方、ネットワーク全体 (または、AlexNet の場合は、完全に接続されたレイヤー) を細かくチューニングすると、トレーニング セットが十分に大きいときのモデルのパフォーマンスを大きく向上させることができます。

トレーニング ジョブが完了すると、このスクリプトはモデル (および、モデルの整数出力と文字ラベルの間のマッピングを記述したファイル) と予測を BLOB ストレージに保存します。 BAIT ジョブのログ ファイルが解析されて、トレーニング エポックでのエラー率改善の経時変化が抽出されます。 エラー率改善の経時変化は、後で確認できるように、AML Workbench の実行履歴に記録されます。

トレーニング済みモデルの名前、事前トレーニング済みモデルの種類、および再トレーニングの深さを選びます。 次のコマンドの指示された場所に選択内容を記述して、Azure ML コマンド ライン インターフェイスからコマンドを実行し、再トレーニングを開始します。

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Azure Machine Learning の実行が完了するまで、約 30 分かかります。 異なる方法でトレーニングされたモデルのパフォーマンスを比較できるよう、いくつかの似たコマンド (出力モデル名、事前トレーニング済みモデルの種類、再トレーニングの深さを変えたもの) を実行することをお勧めします。

### <a name="training-models-with-mmlspark"></a>MMLSpark によるモデルのトレーニング

Workbench プロジェクトの "Code\02_Modeling" サブフォルダーにある `run_mmlspark.py` スクリプトは、画像分類用の [MMLSpark](https://github.com/Azure/mmlspark) モデルのトレーニングに使用されます。 スクリプトでは最初に、ImageNet データセット (AlexNet または 18 レイヤーの ResNet) 上で事前にトレーニングされた画像分類子 DNN を使用して、トレーニング セットの画像の特徴付けを行います。 次に、スクリプトは、特徴付けした画像を使って MMLSpark モデル (ランダム フォレストまたはロジスティック回帰モデルのいずれか) をトレーニングし、画像を分類します。 その後、テスト画像セットは、トレーニング済みのモデルを使って特徴付けおよびスコア付けされます。 テスト セット上のモデルの予測精度が計算され、Azure Machine Learning Workbench の実行履歴機能に記録されます。 最後に、テスト セット上のトレーニング済みの MMLSpark モデルと予測が、BLOB ストレージに保存されます。

トレーニング済みモデルの一意の出力モデル名、事前トレーニング済みモデルの種類、および MMLSpark モデルの種類を選択します。 次のコマンド テンプレートの指示された場所に選択内容を記述して、Azure ML コマンド ライン インターフェイスからコマンドを実行して、再トレーニングを開始します。

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

追加の `--sample_frac` パラメーターは、利用可能なデータのサブセットを使って、モデルのトレーニングおよびテストに使用できます。 小さなサンプルの一部を使用すると、トレーニング済みモデルの精度が低下する代わりに、ランタイムとメモリの要件が下がります。 (たとえば、`--sample_frac 0.1` で実行すると、約 20 分間かかります。)この件および他のパラメーターに関する詳細については、`python Code\02_Modeling\run_mmlspark.py -h` を実行してください。

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

Azure Machine Learning Workbench を使用すると、データ サイエンティストはリモート コンピューティングのターゲットに簡単にコードをデプロイできます。 この例では、HDInsight クラスターでのリモート実行用にローカル MMLSpark トレーニング コードがデプロイされており、ローカル スクリプトが Azure Batch AI GPU クラスターでトレーニング ジョブを開始しました。 Azure Machine Learning Workbench の実行履歴機能では、複数のモデルのパフォーマンスを追跡し、最も精度の高いモデルを特定しました。 Workbench の Jupyter ノートブック機能では、インタラクティブなグラフィック環境でモデルの予測を視覚化しました。

## <a name="next-steps"></a>次の手順
この例をさらに詳しく確認するには、次の操作を実行してください。
- Azure Machine Learning Workbench 実行履歴機能で、歯車の記号をクリックして、表示するグラフィックおよびメトリックを選択します。
- `run_logger` を呼び出すステートメントのサンプル スクリプトを検証します。 各メトリックが記録される方法を理解していることを確認してください。
- `blob_service` を呼び出すステートメントのサンプル スクリプトを検証します。 トレーニング済みモデルおよび予測が保存されクラウドから取得される方法を理解していることを確認してください。
- BLOB ストレージ アカウントで作成されたコンテナーの内容を調べます。 どのスクリプトまたはコマンドがファイルの各グループの作成を担っているかを理解していることを確認してください。
- 異なる MMLSpark モデルの種類をトレーニングするか、またはモデルのハイパーパラメーターを変更するように、トレーニング スクリプトを修正します。 実行履歴機能を使用して、変更によってモデルの精度が向上または低下したかを判断してください。
