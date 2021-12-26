---
title: Azure Data Science Virtual Machine に含まれるツール
titleSuffix: Azure Data Science Virtual Machine
description: Windows および Ubuntu DSVM イメージに含まれるツールの一覧
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: reference
ms.date: 05/12/2021
ms.custom: contperf-fy20q4
ms.openlocfilehash: ba7006c9dc266819e4c965ae3a72fc0f78506056
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649983"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine に含まれるツール

Data Science Virtual Machine は、クラウドでデータを探索し、機械学習を実行するための簡単な方法です。 Data Science Virtual Machine は、完全なオペレーティング システム、セキュリティ パッチ、ドライバー、一般的なデータ サイエンスおよび開発ソフトウェアを使用してあらかじめ構成されています。 低コストの CPU 中心のマシンから、複数の GPU、NVMe ストレージ、および大容量メモリを搭載した非常に強力なマシンに至るまで、ハードウェア環境を選択することができます。 GPU を搭載したマシンでは、すべてのドライバーがインストールされ、すべての機械学習フレームワークのバージョンが GPU の互換性と一致しています。また、GPU をサポートするすべてのアプリケーション ソフトウェアでアクセラレーションが有効化されています。

Data Science Virtual Machine では、最も有用なデータ サイエンス ツールがプレインストールされます。 

## <a name="build-deep-learning-and-machine-learning-solutions"></a>ディープ ラーニングと機械学習のソリューションを構築する

| ツール | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 使用上の注意 |
|--|:-:|:-:|:-:|
| [CUDA、cuDNN、NVIDIA ドライバー](https://developer.nvidia.com/cuda-toolkit) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM 上の CUDA、cuDNN、NVIDIA ドライバー](./dsvm-tools-deep-learning-frameworks.md#cuda-cudnn-nvidia-driver) |
| [Horovod](https://github.com/horovod/horovod) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [DSVM 上の Horovod](./dsvm-tools-deep-learning-frameworks.md#horovod) |
| [NVidia システム管理インターフェイス (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [DSVM 上の nvidia-smi](./dsvm-tools-deep-learning-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| [PyTorch](https://pytorch.org) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [DSVM 上の PyTorch](./dsvm-tools-deep-learning-frameworks.md#pytorch) |
| [TensorFlow](https://www.tensorflow.org) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM 上の TensorFlow](./dsvm-tools-deep-learning-frameworks.md#tensorflow) |
| [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) との統合 (Python) | <span class='green-check'>&#9989;</span></br> (Python SDK、サンプル) | <span class='green-check'>&#9989;</span></br> (Python SDK、CLI、サンプル) | [Azure ML SDK](./dsvm-tools-data-science.md#azure-machine-learning-sdk-for-python) |
| [XGBoost](https://github.com/dmlc/xgboost) | <span class='green-check'>&#9989;</span></br> (CUDA のサポート) | <span class='green-check'>&#9989;</span></br> (CUDA のサポート) | [DSVM 上の XGBoost](./dsvm-tools-data-science.md#xgboost) |
| [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> | [DSVM 上の Vowpal Wabbit](./dsvm-tools-data-science.md#vowpal-wabbit) |
| [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | <span class='red-x'>&#10060;</span> | <span class='red-x'>&#10060;</span> |  |
| LightGBM | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (GPU、MPI のサポート) |  |
| H2O | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| CatBoost | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Intel MKL | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| OpenCV | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Dlib | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Docker | <span class='green-check'>&#9989;</span> <br/> (Windows コンテナーのみ) | <span class='green-check'>&#9989;</span> |  |
| Nccl | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Rattle | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| ONNX Runtime | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |



## <a name="store-retrieve-and-manipulate-data"></a>データを格納、取得、操作する

| ツール | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 使用上の注意 |
|--|-:|:-:|:-:|
| リレーショナル データベース | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer エディション | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer エディション | [DSVM 上の SQL Server](./dsvm-tools-data-platforms.md#sql-server-developer-edition) |
| データベース ツール | SQL Server Management Studio<br/> SQL Server Integration Services<br/> [bcp、sqlcmd](/sql/tools/command-prompt-utility-reference-database-engine) | [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (クエリ ツール)、 <br /> bcp、sqlcmd <br /> ODBC および JDBC ドライバー |  |
| [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [Azure CLI](/cli/azure) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [AzCopy](../../storage/common/storage-use-azcopy-v10.md) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> | [DSVM 上の AzCopy](./dsvm-tools-ingestion.md#azcopy) |
| [BLOB FUSE ドライバー](https://github.com/Azure/azure-storage-fuse) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [DSVM 上の blobfuse](./dsvm-tools-ingestion.md#blobfuse) |
| [Azure Cosmos DB データ移行ツール](../../cosmos-db/import-data.md) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [DSVM 上の Cosmos DB](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| UNIX および Linux コマンドライン ツール | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Apache Spark 3.1 (スタンドアロン) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> |  |

## <a name="program-in-python-r-julia-and-nodejs"></a>Python、R、Julia、Node.js でのプログラミング

| ツール | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 使用上の注意 |
|--|:-:|:-:|:-:|
| 人気のパッケージがプレインストールされた [CRAN-R](https://cran.r-project.org/) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| 人気のパッケージがプレインストールされた [Anaconda Python](https://www.continuum.io/) | <span class='green-check'>&#9989;</span><br/> (Miniconda) | <span class='green-check'>&#9989;</span></br> (Miniconda) |  |
| [Julia (Julialang)](https://julialang.org/) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterHub (マルチユーザー Notebook サーバー) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterLab (マルチユーザー Notebook サーバー) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| Node.js | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| 次のカーネルを備えた [Jupyter Notebook Server](https://jupyter.org/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span> | [Jupyter Notebook のサンプル](./dsvm-samples-and-walkthroughs.md) |
| &nbsp;&nbsp;&nbsp;&nbsp; R |  |  | [R Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#r-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Python |  |  | [Python Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#python-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Julia |  |  | [Julia Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#julia-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; PySpark |  |  | [pySpark Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#sparkml) |

**Ubuntu 18.04 DSVM と Windows Server 2019 DSVM** には、次の Jupyter カーネルがあります。</br> 
* Python 3.8 - 既定</br>  
* Python 3.8 - PyTorch</br>  
* Python 3.8 - TensorFlow</br>  
* Python 3.6 - AzureML - TensorFlow</br>  
* Python 3.6 - AzureML - PyTorch</br>  
* Python 3.6 - AzureML - AutoML</br>  
* R</br>  
* Python 3.7 - Spark (ローカル)</br>  
* Julia 1.2.0</br>  
* R Spark - HDInsight</br>  
* Scala Spark - HDInsight</br>  
* Python 3 Spark - HDInsight</br>  

**Ubuntu 18.04 DSVM と Windows Server 2019 DSVM** には、次の Conda 環境があります。</br> 
* py38_default  </br>
* py38_tensorflow </br> 
* py38_pytorch  </br>
* azureml_py36_tensorflow  </br>
* azureml_py36_pytorch  </br>
* azureml_py36_automl  </br>


## <a name="use-your-preferred-editor-or-ide"></a>好みのエディターまたは IDE を使用する

| ツール | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 使用上の注意 |
|--|:-:|:-:|:-:|
| [Notepad++](https://notepad-plus-plus.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Nano](https://www.nano-editor.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Visual Studio 2019 Community エディション](https://www.visualstudio.com/community/) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [DSVM 上の Visual Studio](dsvm-tools-development.md#visual-studio-community-edition) |
| [Visual Studio Code](https://code.visualstudio.com/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM 上の Visual Studio Code](./dsvm-tools-development.md#visual-studio-code) |
| [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM 上の RStudio Desktop](./dsvm-tools-development.md#rstudio-desktop) |
| [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) <br/> (既定で無効) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM 上の PyCharm](./dsvm-tools-development.md#pycharm) |
| [IntelliJ IDEA](https://www.jetbrains.com/idea/) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [Vim](https://www.vim.org) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Git](https://git-scm.com/) および Git Bash | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [OpenJDK](https://openjdk.java.net) 11 | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| .NET Framework | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| Azure SDK | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |

## <a name="organize--present-results"></a>結果を整理および表示する

| ツール | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 使用上の注意 |
|--|:-:|:-:|:-:|
| [Microsoft 365](https://www.microsoft.com/microsoft-365) (Word、Excel、PowerPoint) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Microsoft Teams](https://www.microsoft.com/microsoft-teams) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Power BI Desktop](https://powerbi.microsoft.com/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| Microsoft Edge ブラウザー | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
