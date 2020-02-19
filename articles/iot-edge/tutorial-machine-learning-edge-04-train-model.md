---
title: チュートリアル:モデルのトレーニングとデプロイ - Azure IoT Edge での Machine Learning
description: このチュートリアルでは、Azure Machine Learning を使用して機械学習モデルをトレーニングし、そのモデルを、Azure IoT Edge モジュールとしてデプロイできるコンテナー イメージにパッケージ化します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5f576d28d30907f3834600d0a6a5c152025cf912
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133485"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>チュートリアル:Azure Machine Learning モデルをトレーニングしてデプロイする

> [!NOTE]
> この記事は、IoT Edge 上で Azure Machine Learning を使用するためのチュートリアルのシリーズの一部です。 この記事に直接アクセスしている場合は、最適な結果を得るために、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md) から始めることをお勧めします。

この記事では、次のタスクを実行します。

* Azure Notebooks を使用して機械学習モデルをトレーニングする。
* トレーニング済みのモデルをコンテナー イメージとしてパッケージ化する。
* コンテナー イメージを Azure IoT Edge モジュールとしてデプロイする。

Azure Notebooks では、機械学習モデルの実験、トレーニング、デプロイに使用される基本ブロックである Azure Machine Learning ワークスペースを利用します。

一般的に、この記事の手順は、データ サイエンティストによって実行される可能性があります。

## <a name="set-up-azure-notebooks"></a>Azure Notebooks を設定する

Azure Notebooks を使用して、2 つの Jupyter ノートブックとサポート ファイルをホストします。 ここでは、Azure Notebooks プロジェクトを作成して構成します。 Jupyter Notebook や Azure Notebooks を使用したことがない方のために、いくつかの入門用ドキュメントを次に示します。

* **クイックスタート:** [ノートブックの作成と共有](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **チュートリアル:** [Python を使用して Jupyter ノートブックを作成し、実行する](../notebooks/tutorial-create-run-jupyter-notebook.md)

Azure Notebooks を使用することで、演習のための一貫した環境を確保できます。

> [!NOTE]
> 設定すると、任意のマシンから Azure Notebooks サービスにアクセスできるようになります。 設定中は、必要なファイルが揃った開発用 VM を使用する必要があります。

### <a name="create-an-azure-notebooks-account"></a>Azure Notebooks アカウントを作成する

Azure Notebooks を使用するには、アカウントを作成する必要があります。 Azure Notebook アカウントは、Azure サブスクリプションから独立しています。

1. [Azure Notebooks](https://notebooks.azure.com) に移動します。

1. ページの右上隅にある **[サインイン]** をクリックします。

1. 自分の職場または学校アカウント (Azure Active Directory) または個人用アカウント (Microsoft アカウント) でサインインします。

1. これまでに Azure Notebooks を使用したことがない場合は、Azure Notebooks アプリについてアクセスを許可するように求められます。

1. Azure Notebooks のユーザー ID を作成します。

### <a name="upload-jupyter-notebook-files"></a>Jupyter ノートブック ファイルをアップロードする

新しい Azure Notebooks プロジェクトにサンプル ノートブック ファイルをアップロードします。

1. 新しいアカウントのユーザー ページで、最上部のメニュー バーから **[マイ プロジェクト]** を選択します。

1. **[新しいプロジェクトの作成]** ダイアログ ボックスで、**プロジェクト名**を指定します。それによって、**プロジェクト ID** も自動的に形成されます。

1. プロジェクトを公開する必要も、Readme を用意する必要もないため、 **[Public]\(パブリック\)** と **[README]** はオフのままにしてください。

1. **作成** を選択します。

1. **[アップロード]** (上矢印アイコン) を選択し、 **[From Computer]\(コンピューターから\)** を選択します。

1. **[ファイルの選択]** を選択します。

1. **C:\source\IoTEdgeAndMlSample\AzureNotebooks** に移動します。 一覧からすべてのファイルを選択し、 **[開く]** をクリックします。

1. **[アップロード]** を選択してアップロードを開始します。プロセスが完了したら、 **[完了]** を選択します。

### <a name="azure-notebook-files"></a>Azure Notebooks ファイル

Azure Notebooks プロジェクトにアップロードしたファイルを見てみましょう。 このチュートリアル部分のアクティビティは 2 つのノートブック ファイルにまたがっており、そこでいくつかの関連ファイルが使用されています。

* **01-turbofan\_regression.ipynb:** このノートブックは、Machine Learning service ワークスペースを使用して、機械学習実験を作成および実行します。 大まかに言うと、このノートブックは次のステップを実行するものです。

  1. デバイス ハーネスによって生成されたデータを Azure Storage アカウントからダウンロードします。
  1. トレーニング用のデータと分類器 AD モデルを調査して準備します。
  1. テスト データセット (Test\_FD003.txt) を使用して実験からのモデルを評価します。
  1. 最良の分類器モデルを Machine Learning service ワークスペースに発行します。

* **02-turbofan\_deploy\_model.ipynb:** このノートブックは、前のノートブックで作成したモデルを受け取り、それを使用して、Azure IoT Edge デバイスにデプロイできるコンテナー イメージを作成します。 このノートブックは、次のステップを実行します。

  1. モデルのスコアリング スクリプトを作成します。
  1. Machine Learning service ワークスペースに保存した分類器モデルを使用してコンテナー イメージを生成します。
  1. Azure コンテナー インスタンス上でイメージを Web サービスとしてデプロイします。
  1. Web サービスを使用して、モデルとイメージが期待どおりに機能することを検証します。 検証済みのイメージは、このチュートリアルの「[カスタム IoT Edge モジュールの作成とデプロイ](tutorial-machine-learning-edge-06-custom-modules.md)」の部分で IoT Edge デバイスにデプロイします。

* **Test\_FD003.txt:** このファイルには、トレーニング済み分類子を検証するときにテスト セットとして使用するデータが含まれています。 簡潔にするために、元のコンテストで提供されたテスト データをテスト セットとして使用することにしました。

* **RUL\_FD003.txt:** このファイルには、Test\_FD003.txt ファイル内の各デバイスの最後のサイクルの RUL が含まれています。 データの詳細な説明については、C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan 内の readme.txt および Damage Propagation Modeling.pdf ファイルを参照してください。

* **Utils.py:** データを操作するための Python ユーティリティ関数のセットが含まれています。 最初のノートブックに関数の詳細な説明が含まれています。

* **README.md:** ノートブックの用途を説明する Readme。  

## <a name="run-azure-notebooks"></a>Azure Notebooks を実行する

プロジェクトが作成されたら、ノートブックを実行できます。 

1. プロジェクト ページから、**01-turbofan\_regression.ipynb** を選択します。

    ![実行する最初のノートブックを選択する](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. メッセージが表示されたら、ダイアログから Python 3.6 カーネルを選択し、 **[Set Kernel]\(カーネルの設定\)** を選択します。

1. ノートブックが "**信頼なし**" と表示される場合は、ノートブックの右上にある **[信頼なし]** ウィジェットをクリックします。 ダイアログが表示されたら、 **[信頼する]** を選択します。

1. ノートブックを下へスクロールして、 **[Set global properties]\(グローバル プロパティを設定する\)** 手順の `AZURE_SUBSCRIPTION_ID =` というコードで始まるセルに移動し、ご利用の Azure サブスクリプション、設定、リソースの値を入力します。

    ![ノートブックのグローバル プロパティを設定する](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. ツール バーの **[実行]** を選択してセルを実行します。

    セルが実行されているときは、角かっこで囲まれたアスタリスク ([\*]) が表示されます。 セルの処理が完了すると、アスタリスクが番号に置き換えられます。関連する出力が表示されることもあります。 ノートブックのセルは逐次的にビルドされ、実行できるセルは一度に 1 つだけです。

    ノートブックの指示に従います。 **[Cell]\(セル\)** メニューから実行オプションを使用したり、`Ctrl` + `Enter` でセルを実行したり、`Shift` + `Enter` でセルを実行して次のセルに進んだりすることもできます。

    > [!TIP]
    > ブラウザーの複数のタブから同じノートブックを実行することは、セルの処理の一貫性を確保するため避けてください。

1. 下へスクロールして、 **[Create a workspace]\(ワークスペースを作成する\)** の概要テキストの直後のセルに移動して、そのセルを実行します。 セルの出力で、認証のためのサインインを求めるリンクを探します。 

    ![デバイス認証用のサインイン プロンプト](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    リンクを開き、指定したコードを入力します。 このサインイン手順では、Microsoft Azure クロスプラットフォーム コマンド ライン インターフェイスを使用して、Jupyter Notebook の認証を行って Azure リソースにアクセスします。  

    ![デバイスでのアプリケーション認証の確認](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. この時点で、残りのセルを実行することができます。 セル内のコードが連続的に実行されるよう、すべてのセルを実行することをお勧めします。 **[Cell]\(セル\)** メニューの **[Run All]\(すべて実行\)** を選択します。 ノートブックを上へスクロールして、セルの処理がどのように実行されたかを見てみましょう。

    **[Explore the data]\(データの調査\)** セクションに、センサーの測定値の散布図をレンダリングする **[Sensor readings and RUL]\(センサーの測定値と RUL\)** というサブセクションがあり、そこでセルを確認できます。

    ![センサーの測定値の散布図](media/tutorial-machine-learning-edge-04-train-model/sensor-readings.png)

1. ノートブックを保存し、ノートブックの右上隅のプロジェクト名をクリックするか、お使いのブラウザーで戻ることで、プロジェクト ページに戻ります。

1. **02-turbofan\_deploy\_model.ipynb** を開き、この作業の手順を繰り返して 2 番目のノートブックを実行します。

1. ノートブックを保存し、ノートブックの右上隅のプロジェクト名をクリックするか、お使いのブラウザーで戻ることで、プロジェクト ページに戻ります。

### <a name="verify-success"></a>成功を確認する

ノートブックが正常に完了したことを確かめるために、いくつかの項目が作成されていることを確認します。

1. Azure Notebooks のプロジェクト ページで、ピリオドで始まる項目名が表示されるよう **[隠しファイルの表示]** を選択します。

1. 次のファイルが作成されていることを確認します。

    | ファイル | 説明 |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Azure Machine Learning ワークスペースの作成に使用される構成ファイル。 |
    | ./aml_config/model_config.json | Azure の **turbofanDemo** Machine Learning ワークスペースでモデルをデプロイするために必要となる構成ファイル。 |
    | myenv.yml| デプロイされた機械学習モデルの依存関係に関する情報を提供します。|

1. Azure portal で、ご利用のリソース グループに **turboFanDemo** Machine Learning ワークスペースが存在することを確認します。

### <a name="debugging"></a>デバッグ

ノートブックには、`print()` コマンドをはじめとする、デバッグ用の Python ステートメントを挿入することができます。 定義されていない変数やオブジェクトが見つかった場合は、それらが最初に宣言またはインスタンス化されているセルを実行してください。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Notebooks で実行される 2 つの Jupyter ノートブックを使用して、ターボファン デバイスからのデータを使用した残存耐用時間数 (RUL) 分類子のトレーニング、モデルとしての分類子の保存、コンテナー イメージの作成、Web サービスとしてのイメージのデプロイおよびテストを行いました。

次の記事に進んで、IoT Edge デバイスを作成してください。

> [!div class="nextstepaction"]
> [IoT Edge デバイスを構成する](tutorial-machine-learning-edge-05-configure-edge-device.md)
