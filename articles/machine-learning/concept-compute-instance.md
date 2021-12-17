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
ms.date: 09/22/2021
ms.openlocfilehash: af7069aae9412f7964cca82c034d0e0966220ada
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081132"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning コンピューティング インスタンスとは

Azure Machine Learning コンピューティング インスタンスは、データ サイエンティスト向けのマネージド クラウドベース ワークステーションです。

コンピューティング インスタンスを使用すると、Azure Machine Learning の開発を簡単に開始できると共に、IT 管理者向けの管理機能とエンタープライズ対応機能が提供されます。

コンピューティング インスタンスは、機械学習のためにクラウド内で完全に構成され管理される自分の開発環境として使用します。 また、開発やテストのためのトレーニングや推論のコンピューティング ターゲットとして使用することもできます。

実稼働レベルのモデルのトレーニングの場合は、マルチノード スケーリング機能を備える [Azure Machine Learning コンピューティング クラスター](how-to-create-attach-compute-cluster.md)を使用してください。 実稼働レベルのモデル デプロイの場合は [Azure Kubernetes Service クラスター](how-to-deploy-azure-kubernetes-service.md)を使用してください。

コンピューティング インスタンスの Jupyter 機能を動作させるには、Web ソケット通信が無効になっていないことを確認してください。 お使いのネットワークで、*. instances.azureml.net と *. instances.azureml.ms への websocket 接続が許可されていることを確認してください。

> [!IMPORTANT]
> この記事で "(プレビュー)" と付記されている項目は、現在、パブリック プレビュー段階です。
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="why-use-a-compute-instance"></a>コンピューティング インスタンスを使用する理由

コンピューティング インスタンスは、自分の機械学習開発環境向けに最適化されるクラウドベースのフルマネージド ワークステーションです。 これには、次のようなメリットがあります。

|主な利点|説明|
|----|----|
|生産性|統合ノートブックや次のツールを Azure Machine Learning Studio で使用して、モデルを構築およびデプロイすることができます。<br/>-  Jupyter<br/>-  JupyterLab<br/>- VS Code (プレビュー)<br/>-  RStudio (プレビュー)<br/>コンピューティング インスタンスは Azure Machine Learning ワークスペースおよびスタジオと完全に統合されています。 ワークスペース内の他のデータ サイエンティストとノートブックやデータを共有できます。<br/> コンピューティング インスタンスで VS Code を使用することもできます。
|マネージドおよび安全|セキュリティ占有領域を削減し、エンタープライズ セキュリティ要件へのコンプライアンスを追加できます。 コンピューティング インスタンスでは、厳格な管理ポリシーと安全なネットワーク構成が提供されます。<br/><br/>- Resource Manager テンプレートまたは Azure Machine Learning SDK からの自動プロビジョニング<br/>- [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md)<br/>- [仮想ネットワークのサポート](./how-to-secure-training-vnet.md#compute-cluster)<br/> - SSH アクセスを有効または無効にする SSH ポリシー<br/>- [[パブリック IP なし]](./how-to-secure-training-vnet.md#no-public-ip) では、パブリック IP アドレスに依存せずに、セキュリティで保護された接続ソリューションを有効にすることができます</br>- TLS 1.2 が有効 |
|ML &nbsp;用&nbsp;に事前構成済み|事前に構成された最新の ML パッケージ、ディープ ラーニング フレームワーク、GPU ドライバーを使用して、セットアップ タスクの時間を節約できます。|
|フル カスタマイズが可能|Azure VM の種類 (GPU を含む) や永続的な低レベル カスタマイズ (パッケージやドライバーのインストールなど) が広範にサポートされているので、高度なシナリオに簡単に対応できます。 |

* **[[パブリック IP なし (プレビュー)]](./how-to-secure-training-vnet.md#no-public-ip)** を使用してコンピューティング インスタンスをセキュリティで保護します
* コンピューティング インスタンスは、コンピューティング クラスターに似た安全なトレーニング コンピューティング先でもありますが、これは単一ノードです。
* 自分で [コンピューティング インスタンスを作成する](how-to-create-manage-compute-instance.md?tabs=python#create)ことも、管理者が **[代わりにコンピューティング インスタンスを作成する](how-to-create-manage-compute-instance.md?tabs=python#on-behalf)** こともできます。
* また、ニーズに応じてコンピューティング インスタンスを自動的にカスタマイズして構成する方法として、 **[セットアップ スクリプト (プレビュー)](how-to-create-manage-compute-instance.md#setup-script)** を使用することもできます。
* コストを節約するには、コンピューティング インスタンスを自動的に開始および停止 (プレビュー) するために **[スケジュールを作成します](how-to-create-manage-compute-instance.md#schedule)** 。


## <a name="tools-and-environments"></a><a name="contents"></a>ツールと環境

Azure Machine Learning コンピューティング インスタンスを使用すると、ワークスペース内の完全統合型ノートブックでモデルを作成し、トレーニングし、デプロイすることができます。

SSH を必要としないリモート サーバーとしてコンピューティング インスタンスを使用して、[VS Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) で Jupyter Notebook を実行できます。 [リモート SSH 拡張機能](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/)を使用して、VS Code 統合を有効にすることもできます。

[パッケージをインストール](how-to-access-terminal.md#install-packages)して、コンピューティング インスタンスに[カーネルを追加](how-to-access-terminal.md#add-new-kernels)することができます。

コンピューティング インスタンスには、次のツールと環境が既にインストールされています。

|一般的なツールと環境|詳細|
|----|:----:|
|ドライバー|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI ライブラリ||
|Azure CLI ||
|Azure Machine Learning サンプル ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf||

|**R** ツールと環境|詳細|
|----|:----:|
|RStudio Server Open Source Edition (プレビュー)||
|R カーネル||

|**PYTHON** ツールおよび環境|詳細|
|----|----|
|Anaconda Python||
|Jupyter と拡張機能||
|Jupyterlab と拡張機能||
[Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro)</br>PyPI から|ほとんどの azureml 追加パッケージが含まれています。  完全な一覧を表示するには [コンピューティング インスタンスでターミナル ウィンドウを開き](how-to-access-terminal.md)、以下を実行します <br/> `conda list -n azureml_py36 azureml*` |
|その他の PyPI パッケージ|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda パッケージ|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|ディープ ラーニング パッケージ|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX パッケージ|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python のサンプル||

Python パッケージはすべて、**Python 3.8 - AzureML** 環境にインストールされます。 コンピューティング インスタンスには、ベース OS として Ubuntu 18.04 があります。

## <a name="accessing-files"></a>ファイルにアクセスする

ノートブックと R スクリプトは、Azure ファイル共有内のワークスペースの既定のストレージアカウントに格納されます。  これらのファイルは、"User files" ディレクトリの下に置かれます。 このストレージによって、コンピューティング インスタンス間でノートブックを簡単に共有できるようになります。 また、ストレージ アカウントでは、コンピューティング インスタンスを停止したり削除したりした場合でも、ノートブックが安全に維持されます。

ワークスペースの Azure ファイル共有アカウントは、コンピューティング インスタンスにドライブとしてマウントされます。 このドライブは、Jupyter、Jupyter Labs、および RStudio の既定の作業ディレクトリです。 つまり、Jupyter、JupyterLab、または RStudio で作成したノートブックとその他のファイルは、ファイル共有に自動的に格納され、他のコンピューティング インスタンスでも使用できるようになります。

ファイル共有内のファイルには、同じワークスペース内のすべてのコンピューティング インスタンスからアクセスできます。 コンピューティング インスタンスでこれらのファイルに加えた変更は、ファイル共有に確実に反映されます。

また、最新の Azure Machine Learning サンプルを、ワークスペース ファイル共有の user files ディレクトリの下にある自分のフォルダーに複製することもできます。

小さいファイルの書き込みをネットワーク ドライブに対して行うと、コンピューティング インスタンス自体のローカル ディスクに書き込む場合よりも遅くなる可能性があります。  小さなファイルを多数作成する場合は、コンピューティング インスタンス上に直接配置されているディレクトリ (`/tmp` ディレクトリなど) を使用するようにしてください。 なお、これらのファイルには、他のコンピューティング インスタンスからはアクセスできなくなることにご注意ください。

ノートブック ファイル共有にトレーニング データを格納しないでください。 一時データにはコンピューティング インスタンスの `/tmp` ディレクトリを使用できます。  ただし、コンピューティング インスタンスの OS ディスクに非常に大きなデータ ファイルを書き込むことは避けてください。 コンピューティング インスタンス上の OS ディスクには 128 GB の容量があります。 また、/mnt にマウントされた一時ディスクに一時的なトレーニング データを格納することもできます。 一時ディスクのサイズは、選択した VM サイズに基づいて構成可能であり、より大きなサイズの VM が選択されている場合は、より大量のデータを格納できます。 また、[データストアとデータセット](concept-azure-machine-learning-architecture.md#datasets-and-datastores)をマウントすることもできます。 インストールしたソフトウェア パッケージは、コンピューティング インスタンスの OS ディスクに保存されます。 カスタマー マネージド キーの暗号化は現在、OS ディスクではサポートされていないことに注意してください。 コンピューティング インスタンスの OS ディスクは、Microsoft マネージド キーを使用して暗号化されます。 

### <a name="create-a-compute-instance"></a><a name="create"></a>コンピューティング インスタンスを作成する

管理者は、 **[ワークスペース内で他のユーザーに代わってコンピューティング インスタンスを作成できます (プレビュー)](how-to-create-manage-compute-instance.md#on-behalf)** 。

また、コンピューティング インスタンスを自動的にカスタマイズして構成する方法として、 **[セットアップ スクリプト (プレビュー)](how-to-create-manage-compute-instance.md#setup-script)** を使用することもできます。

自分でコンピューティング インスタンスを作成するには、Azure Machine Learning スタジオのワークスペースを使用して、いずれかのノートブックを実行する準備ができたら、 **[計算]** セクションまたは **[ノートブック]** セクションから [新しいコンピューティング インスタンスを作成](how-to-create-manage-compute-instance.md?tabs=azure-studio#create)します。

インスタンスを作成することもできます
* [統合ノートブックのエクスペリエンス](tutorial-train-models-with-aml.md#azure)から直接
* Azure Portal
* Azure Resource Manager テンプレートから。 テンプレートの例については、[Azure Machine Learning コンピューティング インスタンス テンプレートの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)に関する記事を参照してください。
* [Azure Machine Learning SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md) を使用して
* [Azure Machine Learning 用の CLI 拡張機能](reference-azure-machine-learning-cli.md#computeinstance)から

コンピューティング インスタンスの作成に適用されるリージョンあたりの専用コア数は、VM ファミリ クォータ別およびリージョン合計クォータ別に、Azure Machine Learning コンピューティング クラスターのクォータと統合され、共有されます。 コンピューティング インスタンスを停止しても、コンピューティング インスタンスを再起動できるように、クォータは解放されません。 Sudo シャットダウンを実行し、OS ターミナルを介してコンピューティング インスタンスを停止しないでください。

コンピューティング インスタンスには P10 OS ディスクが付属しています。 一時ディスクの種類は、選択された VM サイズによって異なります。 現在、OS ディスクの種類を変更することはできません。


## <a name="compute-target"></a>コンピューティング ターゲット

コンピューティング インスタンスは、Azure Machine Learning コンピューティング トレーニング クラスターのように、[トレーニング コンピューティング ターゲット](concept-compute-target.md#train)として使用できます。

コンピューティング インスタンスは、
* ジョブ キューがあります。
* 企業で SSH ポートを開かなくても、仮想ネットワーク環境でジョブを安全に実行できます。 ジョブはコンテナー化された環境で実行され、モデルの依存関係が Docker コンテナーにパッケージ化されます。
* 複数の小さいジョブを並列で実行できます (プレビュー)。  コアごとに 2 つのジョブを並列で実行できますが、残りのジョブはキューに登録されます。
* シングルノード マルチ GPU [分散トレーニング](how-to-train-distributed-gpu.md) ジョブをサポートする

コンピューティング インスタンスは、テストまたはデバッグのシナリオのためのローカル推論配置ターゲットとして使用できます。

> [!TIP]
> コンピューティング インスタンスには 120 GB の OS ディスクがあります。 ディスク領域が不足し、使用できない状態になった場合は、コンピューティング インスタンスのターミナルからファイルまたはフォルダーを削除して OS ディスク (/ にマウントされている) 上のディスク領域を 5 GB 以上クリアしてから、`sudo reboot` を実行してください。 ターミナルにアクセスするには、コンピューティング リスト ページまたはコンピューティング インスタンスの詳細ページに移動し、 **[ターミナル]** リンクをクリックします。 ターミナルで `df -h` を実行することによって、使用可能なディスク領域を確認できます。 `sudo reboot` を実行する前に、5 GB 以上の領域をクリアしてください。 5 GB のディスク領域がクリアされるまで、Studio を使用してコンピューティング インスタンスの停止も再起動も実行しないでください。

## <a name="next-steps"></a>次のステップ

* [コンピューティング インスタンスを作成および管理する](how-to-create-manage-compute-instance.md)
* [チュートリアル:最初の ML モデルをトレーニングする)](tutorial-1st-experiment-sdk-train.md)」では、統合ノートブックでコンピューティング インスタンスを使用する方法を示しています。
