---
title: Azure Machine Learning Workbench で Jupyter Notebook を使用する方法 | Microsoft Docs
description: Azure Machine Learning Workbench の Jupyter Notebook 機能を使用するためのガイド
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ROBOTS: NOINDEX
ms.openlocfilehash: c59969d5a56bd2eb4eb8c490d4f1b5af20d7f056
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998697"
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench で Jupyter Notebook を使用する

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Azure Machine Learning Workbench は、Jupyter Notebook との統合を通じて対話型のデータ サイエンス実験をサポートしています。 この記事では、この機能を効果的に使用して対話型のデータ サイエンス実験の速度と質を向上させる方法について説明します。

## <a name="prerequisites"></a>前提条件
- [Azure Machine Learning アカウントを作成し、Azure Machine Learning Workbench をインストールする](quickstart-installation.md)。
- [Jupyter Notebook](http://jupyter.org/) についての詳しい知識を持っている。 この記事では、Jupyter の使用方法については説明していません。

## <a name="jupyter-notebook-architecture"></a>Jupyter Notebook のアーキテクチャ
大まかに言えば、Jupyter Notebook のアーキテクチャには次の 3 つのコンポーネントが含まれています。 各コンポーネントは、さまざまなコンピューティング環境で実行できます。

- **クライアント**: ユーザー入力を受け取り、レンダリングされた出力を表示します。
- **サーバー**: Notebook ファイル (.ipynb ファイル) をホストする Web サーバー。
- **カーネル**: Notebook セルが実行されるランタイム環境。

詳細については、[Jupyter のドキュメント](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html)を参照してください。 次の図は、このクライアント、サーバー、およびカーネルのアーキテクチャが Azure Machine Learning のコンポーネントにどのようにマップされるかを示しています。

![Jupyter Notebook のアーキテクチャ](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Azure Machine Learning Workbench の Notebook のカーネル
Azure Machine Learning Workbench では、プロジェクトの `aml_config` フォルダーに実行構成および計算ターゲットを定義して、さまざまなカーネルにアクセスできます。 `az ml computetarget attach` コマンドを発行して新しい計算ターゲットを追加することは、新しいカーネルを追加することと同じです。

>[!NOTE]
>実行構成と計算ターゲットの詳細については、「[Configuring Azure Machine Learning Experimentation Service](experimentation-service-configuration.md)」(Azure Machine Learning 実験サービスの構成) を参照してください。

### <a name="kernel-naming-convention"></a>カーネルの名前付け規則
Azure Machine Learning Workbench は、カスタムの Jupyter カーネルを生成します。 カーネルには、*\<プロジェクト名> \<実行構成名>* という形式の名前が付けられます。 たとえば、_myIris_ という名前のプロジェクトに _docker-python_ という名前の実行構成がある場合、Azure Machine Learning  によって *myIris docker-python* という名前のカーネルが使用可能になります。 実行中のカーネルは、Jupyter Notebook の **[カーネル]** メニューの **[Change kernel]\(カーネルの変更\)** サブメニューで設定します。 実行中のカーネルの名前は、メニュー バーの右端に表示されます。
 
現在、Azure Machine Learning Workbench では、次の種類のカーネルがサポートされています。

### <a name="local-python-kernel"></a>ローカルの Python カーネル
この Python カーネルは、ローカル コンピューターでの実行をサポートします。 これは Azure Machine Learning の実行履歴と統合されています。 通常、このカーネルの名前は *my_project_name local* です。

>[!NOTE]
>Python 3 カーネルは使用しないでください。 これは、Jupyter で既定で提供されるスタンドアロン カーネルで、Azure Machine Learning の機能とは統合されません。 たとえば、`%azureml` Jupyter マジック関数は、"見つかりません" エラーを返します。 

### <a name="python-kernel-in-docker-local-or-remote"></a>Docker (ローカルまたはリモート) 内の Python カーネル
この Python カーネルは、ローカル コンピューターまたはリモート Linux 仮想マシン (VM) 上の Docker コンテナーで実行されます。 通常、このカーネルの名前は *my_project docker*です。 関連付けられている `docker.runconfig` ファイルの `Framework` フィールドは `Python` に設定されます。

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Docker (ローカルまたはリモート) 内の PySpark カーネル
この PySpark カーネルは、ローカル コンピューターまたはリモート Linux VM 上の Docker コンテナー内で実行されている Spark コンテキストでスクリプトを実行します。 通常、このカーネルの名前は *my_project docker* です。 関連付けられている `docker.runconfig` ファイルの `Framework` フィールドは `PySpark` に設定されます。

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Azure HDInsight クラスター内の PySpark カーネル
このカーネルは、プロジェクトの計算ターゲットとしてアタッチした、リモート Azure HDInsight クラスターで実行されます。 通常、このカーネルの名前は *my_project my_hdi* です。 

>[!IMPORTANT]
>このカーネルを使用するには、HDI 計算ターゲットの `.compute` ファイルで `yarnDeployMode` フィールドを `client` (既定値は `cluster`) に変更する必要があります。 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench から Jupyter サーバーを起動する
Azure Machine Learning Workbench の **[ノートブック]** タブから、ノートブックにアクセスできます。_[Classifying Iris]\(アイリスの分類\)_ サンプル プロジェクトに、`iris.ipynb` サンプルのノートブックが含まれています。

![[ノートブック] タブ](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Azure Machine Learning Workbench でノートブックを開くと、ノートブックはその [ドキュメント] タブに**プレビュー モード**で表示されます。 これは Jupyter サーバーおよびカーネルを実行する必要がない読み取り専用のビューです。

![ノートブックのプレビュー](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

**[Start Notebook Server]\(Notebook サーバーの起動)\** ボタンを選択すると Jupyter サーバーが起動し、ノートブックが**編集モード**に切り替わります。 見慣れた Jupyter Notebook のユーザー インターフェイスが Workbench に埋め込みで表示されます。 これで、**[カーネル]** メニューからカーネルを設定して対話型のノートブック セッションを開始できます。 

>[!NOTE]
>ローカルでないカーネルの場合、最初に使用するときに起動に 1-2 分かかることがあります。 CLI ウィンドウから `az ml experiment prepare` コマンドを実行すると、計算ターゲットの準備後にカーネルがもっと迅速に起動するように計算ターゲットを準備できます。

![編集モード](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

これは、完全な対話型の Jupyter Notebook エクスペリエンスです。 Workbench の **[ノートブック]** タブおよび **[ファイル]** タブから実行できる一部のファイル操作を除く、通常のすべてのノートブック操作とキーボード ショートカットがこのウィンドウからサポートされます。

## <a name="start-a-jupyter-server-from-the-command-line"></a>コマンド ラインから Jupyter サーバーを起動する
コマンド ライン ウィンドウから `az ml notebook start` を発行してノートブック セッションを起動することもできます。
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
Copy and paste this URL into your browser when you connect for the first time, to login with a token: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
既定のブラウザーが、Jupyter サーバーがプロジェクトのホーム ディレクトリをポイントしている状態で自動的に開きます。 URL と CLI ウィンドウに表示されたトークンを使用して、他のブラウザー ウィンドウをローカルで開くこともできます。 

![プロジェクト ダッシュボード](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

これで `.ipynb` ノートブック ファイルを選択して開き、カーネルを設定して (まだ設定されていない場合)、対話型セッションを開始できます。

![プロジェクト ダッシュボード](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>magic コマンドを使用して実験を管理する

ノートブック セル内で [magic コマンド](http://ipython.readthedocs.io/en/stable/interactive/magics.html)を使用して実行履歴を追跡し、モデルやデータセットなどの出力を保存できます。

個々のノートブック セルの実行を追跡するには、 `%azureml history on` magic コマンドを使用します。 履歴を有効にすると、各セルの実行は実行履歴のエントリとして表示されます。

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

セルの実行の追跡をオフにするには、`%azureml history off` magic コマンドを使用します。

`%azureml upload` magic コマンドを使用して、実行のモデルとデータ ファイルを保存できます。 保存されたオブジェクトは、実行履歴ビューに出力として表示されます。

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>出力は、*outputs* というフォルダーに保存する必要があります。

## <a name="next-steps"></a>次の手順
- Jupyter Notebook の使用方法について学習するには、[Jupyter の公式ドキュメント](http://jupyter-notebook.readthedocs.io/en/latest/)を参照します。    
- Azure Machine Learning 実験実行環境をより深く理解するには、「[Configuring Azure Machine Learning Experimentation Service](experimentation-service-configuration.md)」(Azure Machine Learning 実験サービスの構成) を参照します。

