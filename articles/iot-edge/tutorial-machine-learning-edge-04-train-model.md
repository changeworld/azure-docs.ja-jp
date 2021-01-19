---
title: チュートリアル:モデルのトレーニングとデプロイ - Azure IoT Edge での Machine Learning
description: このチュートリアルでは、Azure Machine Learning を使用して機械学習モデルをトレーニングし、そのモデルを、Azure IoT Edge モジュールとしてデプロイできるコンテナー イメージにパッケージ化します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2cc96db88d9a2aec02de5e2fc4ed18b445972e7b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121144"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>チュートリアル:Azure Machine Learning モデルをトレーニングしてデプロイする

この記事では、次のタスクを実行します。

* Azure Machine Learning スタジオを使用して機械学習モデルをトレーニングします。
* トレーニング済みのモデルをコンテナー イメージとしてパッケージ化する。
* コンテナー イメージを Azure IoT Edge モジュールとしてデプロイする。

Azure Machine Learning スタジオは、機械学習モデルの実験、トレーニング、デプロイに使用される基本ブロックです。

一般的に、この記事の手順は、データ サイエンティストによって実行される可能性があります。

チュートリアルのこのセクションで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 機械学習モデルをトレーニングするための Jupyter Notebook を Azure Machine Learning ワークスペースで作成する。
> * トレーニングされた機械学習モデルをコンテナー化する。
> * コンテナー化された機械学習モデルから Azure IoT Edge モジュールを作成する。

## <a name="prerequisites"></a>前提条件

この記事は、IoT Edge 上で Azure Machine Learning を使用するためのチュートリアルのシリーズの一部です。 シリーズの各記事は、前の記事の作業に基づいています。 この記事に直接アクセスしている場合は、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md)を参照してください。

## <a name="set-up-azure-machine-learning"></a>Azure Machine Learning を設定する 

Azure Machine Learning スタジオを使用して、2 つの Jupyter ノートブックとサポート ファイルをホストします。 ここでは、Azure Machine Learning プロジェクトを作成して構成します。 Jupyter や Azure Machine Learning スタジオを使用したことがない方のために、いくつかの入門用ドキュメントを次に示します。

* **Jupyter Notebook:** [Visual Studio Code での Jupyter Notebook の使用](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning:** [Jupyter Notebook で Azure Machine Learning の作業を開始する](../machine-learning/tutorial-1st-experiment-sdk-setup.md)


> [!NOTE]
> 設定後は、任意のマシンから Azure Machine Learning service にアクセスできるようになります。 設定中は、必要なファイルが揃った開発用 VM を使用する必要があります。

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Visual Studio Code の Azure Machine Learning 拡張機能をインストールする
開発用 VM 上の VS Code には、この拡張機能がインストールされている必要があります。 別のインスタンスで実行している場合は、[こちら](../machine-learning/tutorial-setup-vscode-extension.md)の説明に従って拡張機能を再インストールしてください。

### <a name="create-an-azure-machine-learning-account"></a>Azure Machine Learning アカウントを作成する  
Azure でリソースをプロビジョニングし、ワークロードを実行するには、Azure アカウントの資格情報でサインインする必要があります。

1. Visual Studio Code のメニュー バーから、 **[表示]**  >  **[コマンド パレット]** を選択してコマンド パレットを開きます。 

1. コマンド パレットに「`Azure: Sign In`」コマンドを入力して、サインイン プロセスを開始します。 指示に従って、サインインを完了します。 

1. ワークロードを実行するための Azure ML コンピューティング インスタンスを作成します。 コマンド パレットを使用して、「`Azure ML: Create Compute`」コマンドを入力します。 
1. Azure サブスクリプションを選択する
1. **[+ Create new Azure ML workspace]\(+ 新しい Azure ML ワークスペースを作成する\)** を選択し、名前に「`turbofandemo`」を入力します。
1. このデモに使用しているリソース グループを選択します。
1. VS Code ウィンドウの右下隅に、ワークスペース作成の進行状況 (**ワークスペースを作成しています: turobofandemo**) が表示されます (この処理には 1 分から 2 分かかる場合があります)。 
1. ワークスペースが正常に作成されるまでお待ちください。 "**Azure ML workspace turbofandemo created (Azure ML ワークスペース turbofandemo が作成されました)** " と表示されます。


### <a name="upload-jupyter-notebook-files"></a>Jupyter Notebook ファイルをアップロードする

新しい Azure ML ワークスペースにサンプル ノートブック ファイルをアップロードします。

1. ml.azure.com に移動してサインインします。
1. ご自分の Microsoft ディレクトリと Azure サブスクリプション、および新しく作成した Azure ML ワークスペースを選択します。

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Azure ML ワークスペースを選択する。" :::

1. Azure ML ワークスペースにログインした後、左側のメニューを使用して **[Notebooks]** セクションに移動します。
1. **[My files]\(マイ ファイル\)** タブを選択します。

1. **[アップロード]** (上矢印アイコン) を選択します。 


1. **C:\source\IoTEdgeAndMlSample\AzureNotebooks** に移動します。 一覧からすべてのファイルを選択し、 **[開く]** をクリックします。

1. **[I trust the content of these files]\(これらのファイルのコンテンツを信頼します\)** チェック ボックスをオンにします。

1. **[アップロード]** を選択してアップロードを開始します。プロセスが完了したら、 **[完了]** を選択します。

### <a name="jupyter-notebook-files"></a>Jupyter Notebook ファイル

Azure ML ワークスペースにアップロードしたファイルを見てみましょう。 このチュートリアル部分のアクティビティは 2 つのノートブック ファイルにまたがっており、そこでいくつかの関連ファイルが使用されています。

* **01-turbofan\_regression.ipynb:** このノートブックは、Machine Learning service ワークスペースを使用して、機械学習実験を作成および実行します。 大まかに言うと、このノートブックは次のステップを実行するものです。

  1. デバイス ハーネスによって生成されたデータを Azure Storage アカウントからダウンロードします。
  1. データを調査、準備した後、そのデータを使用して分類器モデルをトレーニングします。
  1. テスト データセット (Test\_FD003.txt) を使用して実験からのモデルを評価します。
  1. 最良の分類器モデルを Machine Learning service ワークスペースに発行します。

* **02-turbofan\_deploy\_model.ipynb:** このノートブックは、前のノートブックで作成したモデルを受け取り、それを使用して、Azure IoT Edge デバイスにデプロイできるコンテナー イメージを作成します。 このノートブックは、次のステップを実行します。

  1. モデルのスコアリング スクリプトを作成します。
  1. Machine Learning service ワークスペースに保存した分類器モデルを使用してコンテナー イメージを生成します。
  1. Azure コンテナー インスタンス上でイメージを Web サービスとしてデプロイします。
  1. Web サービスを使用して、モデルとイメージが期待どおりに機能することを検証します。 検証済みのイメージは、このチュートリアルの「[カスタム IoT Edge モジュールの作成とデプロイ](tutorial-machine-learning-edge-06-custom-modules.md)」の部分で IoT Edge デバイスにデプロイします。

* **Test\_FD003.txt:** このファイルには、トレーニング済み分類子を検証するときにテスト セットとして使用するデータが含まれています。 簡潔にするために、元のコンテストで提供されたテスト データをテスト セットとして使用することにしました。

* **RUL\_FD003.txt:** このファイルには、Test\_FD003.txt ファイル内の各デバイスの最後のサイクルの残存耐用年数 (RUL) が含まれています。 データの詳細な説明については、C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan 内の readme.txt および Damage Propagation Modeling.pdf ファイルを参照してください。

* **Utils.py:** データを操作するための Python ユーティリティ関数のセットが含まれています。 最初のノートブックに関数の詳細な説明が含まれています。

* **README.md:** ノートブックの用途を説明する Readme。  

## <a name="run-jupyter-notebooks"></a>Jupyter ノートブックの実行

ワークスペースが作成されたら、ノートブックを実行できます。 

1. **[My files]\(マイ ファイル\)** ページから **[01-turbofan\_regression.ipynb]** を選択します。

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="実行する最初のノートブックを選択する。":::

1. ノートブックが "**信頼なし**" と表示される場合は、ノートブックの右上にある **[信頼なし]** ウィジェットをクリックします。 ダイアログが表示されたら、 **[信頼する]** を選択します。

1. 最良の結果を得るためには、各セルのドキュメントを読み取って個別に実行します。 ツール バーの **[Run]\(実行\)** を選択します。 しばらくすると、複数のセルを実行するのが得策であることがわかります。 アップグレードと非推奨化の警告は無視してかまいません。

    セルが実行されているときは、角かっこで囲まれたアスタリスク ([\*]) が表示されます。 セルの処理が完了すると、アスタリスクが番号に置き換えられます。関連する出力が表示されることもあります。 ノートブックのセルは逐次的にビルドされ、実行できるセルは一度に 1 つだけです。

    **[Cell]\(セル\)** メニューから実行オプションを使用したり、`Ctrl` + `Enter` でセルを実行したり、`Shift` + `Enter` でセルを実行して次のセルに進んだりすることもできます。

    > [!TIP]
    > ブラウザーの複数のタブから同じノートブックを実行することは、セルの処理の一貫性を確保するため避けてください。

1. **[Set global properties]\(グローバル プロパティを設定する\)** 手順の後のセルに移動し、ご利用の Azure サブスクリプション、設定、リソースの値を入力します。 そのうえで、セルを実行します。

    ![ノートブックのグローバル プロパティを設定する](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. **[ワークスペースの詳細]** の前のセルで、その実行後、認証のためのサインインを求めるリンクを探します。

    ![デバイス認証用のサインイン プロンプト](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    リンクを開き、指定したコードを入力します。 このサインイン手順では、Microsoft Azure クロスプラットフォーム コマンド ライン インターフェイスを使用して、Jupyter Notebook の認証を行って Azure リソースにアクセスします。  

    ![デバイスでのアプリケーション認証の確認](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. **[Explore the results]\(結果を調査する\)** の前のセルから実行 ID の値をコピーし、 **[Reconstitute a run]\(実行を再構成する\)** の後のセルの実行 ID に貼り付けます。

   ![セル間で実行 ID をコピーする](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. ノートブックで残りのセルを実行します。

1. ノートブックを保存して、プロジェクト ページに戻ります。

1. **02-turbofan\_deploy\_model.ipynb** を開いて、各セルを実行します。 **[Configure workspace]\(ワークスペースを構成する\)** の後のセルで、サインインして認証を行う必要があります。

1. ノートブックを保存して、プロジェクト ページに戻ります。

### <a name="verify-success"></a>成功を確認する

ノートブックが正常に完了したことを確かめるために、いくつかの項目が作成されていることを確認します。

1. Azure ML ノートブックの **[My files]\(マイ ファイル\)** タブで、 **[refresh]\(最新の情報に更新\)** を選択します。

1. 次のファイルが作成されていることを確認します。

    | ファイル | 説明 |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Azure Machine Learning ワークスペースの作成に使用される構成ファイル。 |
    | ./aml_config/model_config.json | Azure の **turbofanDemo** Machine Learning ワークスペースでモデルをデプロイするために必要となる構成ファイル。 |
    | myenv.yml| デプロイされた機械学習モデルの依存関係に関する情報を提供します。|

1. 次の Azure リソースが作成されていることを確認します。 一部のリソース名には、ランダムな文字が追加されます。

    | Azure リソース | 名前 |
    | --- | --- |
    | Machine Learning ワークスペース | turborfanDemo |
    | Container Registry | turbofandemoxxxxxxxx |
    | Applications Insights | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | ストレージ | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>デバッグ

ノートブックには、値を表示するための `print()` コマンドなど、デバッグ用の Python ステートメントを挿入することができます。 定義されていない変数やオブジェクトが見つかった場合は、それらが最初に宣言またはインスタンス化されているセルを実行してください。

ノートブックを再実行する必要がある場合は、先ほど作成したファイルと Azure リソースは削除する必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルはセットの一部であり、各記事は前の記事の作業が行われたことが前提になっています。 最後のチュートリアルを完了するまで、リソースのクリーンアップはしないでください。

## <a name="next-steps"></a>次のステップ

この記事では、Azure ML スタジオで実行される 2 つの Jupyter ノートブックを使用して、ターボファン デバイスからのデータを使用した残存耐用時間数 (RUL) 分類子のトレーニング、モデルとしての分類子の保存、コンテナー イメージの作成、Web サービスとしてのイメージのデプロイおよびテストを行いました。

次の記事に進んで、IoT Edge デバイスを作成してください。

> [!div class="nextstepaction"]
> [IoT Edge デバイスを構成する](tutorial-machine-learning-edge-05-configure-edge-device.md)