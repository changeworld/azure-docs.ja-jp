---
title: DSVM に含まれているツール
titleSuffix: Azure Data Science Virtual Machine tools
description: Windows および Ubuntu DSVM イメージに含まれるツールの一覧
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 09/27/2019
ms.openlocfilehash: 79de3406e47b84a6120496acce5bba948fbb1a6d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803548"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine に含まれるツール

ここには、Data Science Virtual Machine に含まれるツールの最新の一覧と、各ツールの構成方法を理解するためのリンクが記載されています。


| **ツール**                                                           | **Windows DSVM** | **Linux DSVM** | **使用上の注意** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| 人気のパッケージがプレインストールされた [Microsoft R Open](https://mran.microsoft.com/open/)   |<span class='green-check'>&#9989;</span>                     |<span class='green-check'>&#9989;</span>  | [DSVM での R の使用](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R、Python)](https://docs.microsoft.com/machine-learning-server/) Developer エディションには、次のものが含まれます。 <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR および revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) の並列および分散型の高機能フレームワーク (R および Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) - Microsoft の新しい最先端機械学習アルゴリズム <br />  &nbsp;&nbsp;&nbsp;&nbsp; [R および Python の運用化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus (共有アクティベーション付き): Excel、Word、PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| 人気のパッケージがプレインストールされた [Anaconda Python](https://www.continuum.io/) 2.7 および 3.5    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | |
| Julia 言語用の人気のパッケージがプレインストールされた [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | [DSVM での Julia の使用](./dsvm-languages.md#julia) |
| リレーショナル データベース                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer エディション| [PostgreSQL](https://www.postgresql.org/) (CentOS)、<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer エディション (Ubuntu) | (./dsvm-data-platforms#sql-server-2016-developer-edition.md) |
| データベース ツール                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC および JDBC ドライバー|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (クエリ ツール)、 <br />  bcp、sqlcmd <br />  ODBC および JDBC ドライバー| |
| SQL Server 機械学習サービス (R、Python) によるスケーラブルなデータベース内分析 |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| 次のカーネルを備えた [Jupyter Notebook Server](https://jupyter.org/)                                  |<span class='green-check'>&#9989;</span>     |<span class='green-check'>&#9989;</span> | [Jupyter Notebook のサンプル](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [R Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Python Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Julia Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyter のサンプル](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu のみ) | |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (マルチユーザー Notebook サーバー)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (マルチユーザー Notebook サーバー) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu のみ) | |
| 開発ツール、IDE、コード エディター:| | |
| Git プラグイン、Azure HDInsight (Hadoop)、Azure Data Lake、SQL Server Data Tools、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](https://aka.ms/ptvs)、および [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) を備えた &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上の Visual Studio 2019](./dsvm-tools-development.md#visual-studio-2019) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上の Visual Studio Code](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上の RStudio Desktop](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [DSVM 上の RStudio Server](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [DSVM 上の PyCharm](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上の Juno](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; Vim および Emacs |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; Git および Git Bash |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| Power BI Desktop |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   |
| Azure および Cortana Intelligence Suite のサービスにアクセスするための SDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上の Power BI デスクトップ](./dsvm-tools-development.md#power-bi-desktop) |
| データ移動および管理ツール: | | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure Storage Explorer |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure CLI](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上の Azcopy](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [BLOB FUSE ドライバー](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [DSVM 上の blobfuse](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上の Adlcopy](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Cosmos DB データ移行ツール](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上の Cosmos DB](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): オンプレミスとクラウド間でデータを移動する |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM の使用](./dsvm-tools-ingestion.md#microsoft-data-management-gateway) |
| &nbsp;&nbsp;&nbsp;&nbsp; Unix および Linux コマンドライン ツール |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| データ探索用 [Apache Drill](https://drill.apache.org) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上の Apache Drill](./dsvm-tools-explore-and-visualize.md#apache-drill)
| 機械学習ツール: ||||
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) との統合 (R、Python) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上の XGBoost](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上の Vowpal Wabbit](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上の Weka](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu のみ) | [DSVM 上の LightGBM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu のみ) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/)、[Sparkling Water](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu のみ) | [DSVM 上の H20](./dsvm-ml-data-science-tools.md#h2o) |
| GPU または CPU 上で動作するディープ ラーニング ツール: |  |  | 使用方法の詳細については、以下のツールの名前をクリックしてください |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) (Windows 2016) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |<span class='green-check'>&#9989;</span> (Windows 2016) |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](./dsvm-deep-learning-ai-frameworks.md#horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe および Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](./dsvm-deep-learning-ai-frameworks.md#chainer) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](./dsvm-deep-learning-ai-frameworks.md#keras)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](./dsvm-deep-learning-ai-frameworks.md#pytorch)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA、cuDNN、NVIDIA ドライバー](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
