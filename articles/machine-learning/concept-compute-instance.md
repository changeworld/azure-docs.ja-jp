---
title: Azure Machine Learning コンピューティング インスタンスとは
titleSuffix: Azure Machine Learning
description: フル マネージドのクラウドベース ワークステーションである Azure Machine Learning コンピューティング インスタンスについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 02655a3697139ae3a9c4c36b423b874b6e5d34f9
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/28/2019
ms.locfileid: "75535735"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning コンピューティング インスタンスとは

Azure Machine Learning コンピューティング インスタンス (プレビュー) は、データ サイエンティスト向けのフル マネージド クラウドベース ワークステーションです。 

コンピューティング インスタンスを使用すると、Azure Machine Learning の開発を簡単に開始できると共に、IT 管理者向けの管理機能とエンタープライズ対応機能が提供されます。  

コンピューティング インスタンスは、クラウド内で完全に構成され管理される自分の開発環境として使用します。

通常、コンピューティング インスタンスは開発環境として使用されます。  また、開発時やテスト時におけるトレーニングや推論のコンピューティング ターゲットとして使用することもできます。  大規模なタスクの場合、マルチノード スケーリング機能を備える [Azure Machine Learning コンピューティング クラスター](how-to-set-up-training-targets.md#amlcompute)は、コンピューティング ターゲットの選択肢として適しています。

> [!NOTE]
> 現在、コンピューティング インスタンスは、**米国中北部**または**英国南部**リージョンのワークスペースでのみ使用できます。他のリージョンは近日中にサポートされる予定です。
>ワークスペースが他のリージョンにある場合は、代わりに [Notebook VM](concept-compute-instance.md#notebookvm) を引き続き作成して使用することができます。 

## <a name="why-use-a-compute-instance"></a>コンピューティング インスタンスを使用する理由

コンピューティング インスタンスは、自分の機械学習開発環境向けに最適化されるクラウドベースのフルマネージド ワークステーションです。 これには、次のようなメリットがあります。

|主な利点||
|----|----|
|生産性|データ サイエンティストは、統合ノートブックや次のツールを自分の Web ブラウザーで使用して、モデルを構築およびデプロイすることができます。<br/>-  Jupyter<br/>-  JupyterLab<br/>-  RStudio|
|マネージドおよび安全|セキュリティ占有領域を削減し、エンタープライズ セキュリティ要件へのコンプライアンスを追加できます。 コンピューティング インスタンスでは、厳格な管理ポリシーと安全なネットワーク構成が提供されます。<br/><br/>- Resource Manager テンプレートまたは Azure Machine Learning SDK からの自動プロビジョニング。<br/>- [ロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/overview)<br/>- [仮想ネットワークのサポート](how-to-enable-virtual-network.md#compute-instance)<br/>- SSH アクセスを有効または無効にする SSH ポリシー|
|事前構成済み&nbsp;または&nbsp; ML|事前に構成された最新の ML パッケージ、ディープ ラーニング フレームワーク、GPU ドライバーを使用して、セットアップ タスクの時間を節約できます。|
|フル カスタマイズが可能|Azure VM の種類 (GPU を含む) や永続的な低レベル カスタマイズ (パッケージやドライバーのインストールなど) が広範にサポートされているので、高度なシナリオに簡単に対応できます。 |

## <a name="contents"></a>ツールと環境

Azure Machine Learning コンピューティング インスタンスを使用すると、ワークスペース内の完全統合型ノートブックでモデルを作成し、トレーニングし、デプロイすることができます。


コンピューティング インスタンスには、次のツールと環境がインストールされています。 

|一般的なツールと環境|詳細|
|----|:----:|
|ドライバー|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI ライブラリ||
|Azure CLI ||
|Azure Machine Learning サンプル ||
|Azure Machine Learning EDAT エンジン ||            
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**R** ツールと環境|詳細|
|----|:----:|
|RStudio Server Open Source Edition||
|R カーネル||
|Azure Machine Learning SDK for R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK のサンプル|

|**PYTHON** ツールおよび環境|詳細|
|----|----|
|Anaconda Python||
|Jupyter と拡張機能||
|Jupyterlab と拡張機能||
|Visual Studio Code ||
[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>PyPI から|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|その他の PyPI パッケージ|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda パッケージ|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|ディープ ラーニング パッケージ|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX パッケージ|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|                           
|Azure Machine Learning の Python と R SDK のサンプル||

通常、コンピューティング インスタンスは開発環境として使用されます。  また、開発時やテスト時におけるトレーニングや推論のコンピューティング ターゲットとして使用することもできます。  大規模なタスクの場合、マルチノード スケーリング機能を備える [Azure Machine Learning コンピューティング クラスター](how-to-set-up-training-targets.md#amlcompute)は、コンピューティング ターゲットの選択肢として適しています。


## <a name="accessing-files"></a>ファイルにアクセスする

ノートブックと R スクリプトは、Azure ファイル共有内のワークスペースの既定のストレージアカウントに格納されます。  これらのファイルは、"User files" ディレクトリの下に置かれます。 このストレージによって、コンピューティング インスタンス間でノートブックを簡単に共有できるようになります。 また、ストレージ アカウントでは、コンピューティング インスタンスを停止したり削除したりした場合でも、ノートブックが安全に維持されます。

ワークスペースの Azure ファイル共有アカウントは、コンピューティング インスタンスにドライブとしてマウントされます。 このドライブは、Jupyter、Jupyter Labs、および RStudio の既定の作業ディレクトリです。

ファイル共有内のファイルには、同じワークスペース内のすべてのコンピューティング インスタンスからアクセスできます。 コンピューティング インスタンスでこれらのファイルに加えた変更は、ファイル共有に確実に反映されます。

また、最新の Azure Machine Learning サンプルを、ワークスペース ファイル共有の user files ディレクトリの下にある自分のフォルダーに複製することもできます。

小さいファイルの書き込みをネットワーク ドライブ上で行うと、VM 自体に書き込む場合よりも遅くなる可能性があります。  小さなファイルを多数作成する場合は、コンピューティング インスタンス上に直接配置されているディレクトリ (`/tmp` ディレクトリなど) を使用するようにしてください。 なお、これらのファイルには、ワークスペース内の他のコンピューティング インスタンスからはアクセスできなくなることに注意してください。

## <a name="managing-a-compute-instance"></a>コンピューティング インスタンスの管理

Azure Machine Learning Studio 内のご利用のワークスペースで、 **[コンピューティング]** を選択してから、上部にある **[コンピューティング インスタンス]** を選択します。

![コンピューティング インスタンスを管理する](./media/concept-compute-instance/manage-compute-instance.png)

次の操作を行うことができます。

* コンピューティング インスタンスを作成する。 名前を指定したり、Azure VM の種類 (GPU を含む) を指定したり、SSH アクセスを有効または無効にしたり、必要に応じて仮想ネットワークの設定を構成することができます。 また、統合ノートブック、Azure portal、Resource Manager テンプレート、または Azure Machine Learning SDK からインスタンスを直接作成することもできます。 コンピューティング インスタンスの作成に適用されるリージョンあたりの専用コア数は、Azure Machine Learning コンピューティング クラスターのクォータと統合され、共有されます。
* コンピューティング インスタンス タブを更新する
* コンピューティング インスタンスを開始、停止、再起動する
* コンピューティング インスタンスを削除する

ご利用のワークスペース内のコンピューティング インスタンスごとに、次のことができます。

* コンピューティング インスタンス上の Jupyter、JupyterLab、RStudio、VS Code URI にアクセスする
* コンピューティング インスタンスに SSH 接続する。 SSH アクセスは既定で無効になっていますが、コンピューティング インスタンスの作成時に有効にすることができます。 SSH アクセスは、公開/秘密キーのメカニズムを通じて実行されます。 このタブには、SSH 接続の詳細が表示されます (IP アドレス、ユーザー名、ポート番号など)。
* 特定のコンピューティング インスタンスに関する詳細を取得する (IP アドレス、リージョンなど)。

[RBAC](/azure/role-based-access-control/overview) を使用すると、ワークスペース内のどのユーザーにコンピューティング インスタンスの作成、削除、開始、停止、再起動を許可するかを制御できます。 ワークスペースの共同作成者および所有者ロール内のユーザーはすべて、ワークスペース全体でコンピューティング インスタンスを作成、削除、開始、停止、および再起動することができます。 ただし、特定のコンピューティング インスタンス上の Jupyter、JupyterLab、および RStudio へのアクセスが許可されるのは、そのコンピューティング インスタンスの作成者だけです。 コンピューティング インスタンスの作成者は、専用のコンピューティング インスタンスを持ち、ルート アクセス権を有し、Jupyter を介してターミナルを使用できます。 コンピューティング インスタンスには、作成者ユーザーのシングルユーザー ログインが用意され、すべてのアクションで、実験実行の属性および RBAC にそのユーザー ID が使用されます。 SSH アクセスは、公開/秘密キーのメカニズムを通じて制御されます。

インスタンスを作成することもできます
* 統合ノートブックのエクスペリエンスから直接
* Azure Portal
* Azure Resource Manager テンプレートから
* Azure Machine Learning SDK を使用して

コンピューティング インスタンスの作成に適用されるリージョンあたりの専用コア数は、Azure Machine Learning トレーニング クラスターのクォータと統合され、共有されます。 

## <a name="compute-target"></a>コンピューティング ターゲット

コンピューティング インスタンスは、Azure Machine Learning コンピューティング トレーニング クラスターのように、[トレーニング コンピューティング ターゲット](concept-compute-target.md#train)として使用できます。 TensorFlow/PyTorch エスティメーターを使用して分散トレーニング ジョブを実行するためのマルチ GPU VM をプロビジョニングします。 また、実行構成を作成し、それをコンピューティング インスタンス上での実験に使用することもできます。 コンピューティング インスタンスは、テストまたはデバッグのシナリオのためのローカル推論配置ターゲットとして使用できます。

## <a name="notebookvm"></a>Notebook VM はどうなったのか

Notebook VM は、コンピューティング インスタンスに置き換えられつつあります。  コンピューティング インスタンスがまだ利用できないリージョンでは、Notebook VM を引き続きフル機能で使用したり、新しい Notebook VM を作成することができます。

ワークスペースのファイル共有に格納されているノートブック ファイルと、ワークスペースのデータストア内にあるデータには、コンピューティング インスタンスからアクセスできます。 ただし、以前に Notebook VM にインストールしたカスタム パッケージは、コンピューティング インスタンスに再インストールする必要があります。 コンピューティング クラスターの作成に適用されるクォータ制限は、コンピューティング インスタンスの作成にも適用されます。 

コンピューティング インスタンスが使用可能なリージョンでは、新しい Notebook VM を作成することはできません。 ただし、既に作成した Notebook VM には引き続きアクセスでき、フル機能で使用することができます。 コンピューティング インスタンスは、既存の Notebook VM と同じワークスペース内に作成できます。 


## <a name="next-steps"></a>次のステップ

 * [チュートリアル:最初の ML モデルをトレーニングする)](tutorial-1st-experiment-sdk-train.md)」では、統合ノートブックでコンピューティング インスタンスを使用する方法を示しています。
