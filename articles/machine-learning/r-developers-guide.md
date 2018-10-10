---
title: Azure での R 開発者向けガイド - R プログラミング | Microsoft Docs
description: この記事では、データ サイエンティストが既に持っている R プログラミング言語のスキルを Azure で活用するためのさまざまな方法の概要を示します。 Azure では、R 開発者がデータ サイエンスのワークロードをクラウドに拡張するために活用できる多くのサービスを提供しています。
services: machine-learning
documentationcenter: ''
author: AnalyticJeremy
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: R
ms.topic: article
ms.date: 09/12/2018
ms.author: jepeach
ms.openlocfilehash: bc00bd3b61398355c663d133c0c9a66c2a52aa8d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046948"
---
# <a name="r-developers-guide-to-azure"></a>Azure での R 開発者向けガイド
<img src="media/r-developers-guide/logo_r.svg" alt="R logo" align="right" width="200" />

常に増え続けるデータを扱う多くのデータ サイエンティストが、クラウド コンピューティングの威力を分析に活用するための方法を求めています。  この記事では、データ サイエンティストが既に持っている [R プログラミング言語](https://www.r-project.org)のスキルを Azure で活用するためのさまざまな方法の概要を示します。

Microsoft は、R プログラミング言語をデータ サイエンティスト向けの最上のツールと考えています。  R 開発者が Azure でコードを実行するためのさまざまなオプションを提供することで、データ サイエンティストが大規模なプロジェクトに取り組む際にデータ サイエンスのワークロードをクラウドに拡張できるようになります。

さまざまなオプションと、それぞれに最も有力なシナリオを調べましょう。

## <a name="azure-services-with-r-language-support"></a>Azure サービスと R 言語のサポート
この記事では、R 言語をサポートする次の Azure サービスについて説明します。

|Service                                                          |説明                                                                       |
|-----------------------------------------------------------------|----------------------------------------------------------------------------------|
|[データ サイエンス仮想マシン](#data-science-virtual-machine)    |データ サイエンスのワークステーションとして、またはカスタム コンピューティングのターゲットとして使用するためにカスタマイズされた VM|
|[HDInsight の ML サービス](#ml-services-on-hdinsight)            |多くのノード全体で大規模なデータセットへの R 分析を実行するためのクラスター ベースのシステム   |
|[Azure Databricks](#azure-databricks)                            |R とその他の言語をサポートするコラボレーションによる Spark の環境               |
|[Azure Machine Learning Studio](#azure-machine-learning-studio)  |Azure の機械学習の実験でカスタム R スクリプトを実行する                      |
|[Azure Batch](#azure-batch)                                      |クラスター内の多くのノード全体で R コードを経済的に実行するためのさまざまなオプションを提供する|
|[Azure Notebooks](#azure-notebooks)                              |無料 (ただし制限あり) でクラウドベースのバージョンの Jupyter Notebook                  |
|[Azure SQL Database](#azure-sql-database)                        |SQL Server データベース エンジンの内部で R スクリプトを実行する                            |

## <a name="data-science-virtual-machine"></a>データ サイエンス仮想マシン
[データ サイエンス仮想マシン](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) (DSVM) とは、データ サイエンス専用に構築された Microsoft の Azure クラウド プラットフォーム上にあるカスタマイズされた VM イメージです。 次のような多くの一般的なデータ サイエンス ツールが含まれています。
* [Microsoft R Open](https://mran.microsoft.com/open/)
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)
* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop)
* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server)

Windows または Linux のどちらかをオペレーティング システムとして、DSVM をプロビジョニングできます。  DSVM は対話型のワークステーション、またはカスタム クラスターのコンピューティング プラットフォームという 2 種類の方法で使用することができます。

### <a name="as-a-workstation"></a>ワークステーションとして
R をクラウドで迅速かつ簡単に使い始めるには、これが最善の方法です。  ローカルのワークステーションで R を使用したことのあるすべてのユーザーに馴染みがある環境です。  ただし、ローカル リソースを使用する代わりに、クラウド内の VM で R 環境が実行されます。  データが既に Azure に格納されている場合には、ご自分の R スクリプトが "よりデータに近い場所で" 実行できるという利点もあります。 データがインターネット経由で転送される代わりに、Azure の内部のネットワークを経由してデータにアクセスできるため、アクセス時間が大幅に短縮されます。

DSVM は、R 開発者の小規模なチームで特に便利です。  各開発者の強力なワークステーションに投資したり、チームのメンバーが使用するさまざまなソフトウェア パッケージの各バージョンを同期したりする必要はなく、開発者が必要なときに DSVM のインスタンスを起動できます。

### <a name="as-a-compute-platform"></a>コンピューティング プラットフォームとして
DSVM はワークステーションとして使用されているだけでなく、R プロジェクトの弾力的でスケーラブルなコンピューティング プラットフォームとしても使用されます。  <code>[AzureDSVM](https://github.com/Azure/AzureDSVM)</code> R パッケージを使用すると、DSVM インスタンスの作成と削除をプログラムで制御できます。  インスタンスをクラスターに形成し、分散分析がクラウドで実行されるようにデプロイできます。  この全体のプロセス全体を、ローカル ワークステーションで実行される R コードで制御できます。

DSVM の詳細については、「[Linux および Windows 用の Azure データ サイエンス仮想マシンの概要](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)」を参照してください。

## <a name="ml-services-on-hdinsight"></a>HDInsight の ML サービス
[Microsoft の ML Services](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview) により、データ サイエンティスト、統計学者、R プログラマは、HDInsight でのスケーラブルで分散型の分析手法にオンデマンドでアクセスできるようになります。  このソリューションは、Azure Blob Storage または Data Lake Storage に読み込まれた事実上すべてのサイズのデータセットで R ベースの分析を行うための最新の機能を備えています。

これは、クラスター全体で R コードをスケーリングできるようにするエンタープライズ級のソリューションです。  Microsoft の
<code>[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)</code> パッケージの関数を活用することで、HDInsight 上の R スクリプトがクラスター内の多くのノード全体でデータ処理関数を並列で実行することができます。  これにより R がワークステーション上で実行されるシングル スレッドの R で可能な量よりもはるかに多いデータを分析できるようになります。

このスケーリングの機能により、HDInsight 上の ML Services が大量のデータ セットを抱える R 開発者に対して最適なオプションとなります。  これにより、クラウドで R スクリプトを実行するための柔軟かつスケーラブルなプラットフォームが提供されます。

ML Services クラスターを作成するためのチュートリアルは、「[Azure HDInsight の ML サービスの概要](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-get-started)」の記事を参照してください。

## <a name="azure-databricks"></a>Azure Databricks
[Azure Databricks](https://azure.microsoft.com/services/databricks/) は、Microsoft Azure クラウド サービス プラットフォームに最適化された Apache Spark ベースの分析プラットフォームです。  Apache Spark の創始者たちと共に設計され、Azure に統合された Databricks では、ワンクリックでのセットアップと効率的なワークフローのほか、データ サイエンティスト、データ エンジニア、ビジネス アナリストが共同作業できるインタラクティブなワークスペースが実現されています。

Databricks でのコラボレーションは、プラットフォームのノートブック システムによって有効になります。  ユーザーは、システムの他のユーザーのノートブックを作成、共有、編集できます。  このノートブックにより、Databricks 環境で管理されている Spark クラスターに対して実行されるコードを記述できるようになります。  このノートブックは、R を完全にサポートし、`SparkR` パッケージと `sparklyr` パッケージの両方からの Spark へのアクセス許可をユーザーに与えます。

Databricks は Spark 上に構築され、コラボレーションを特に重視しているため、そのプラットフォームは大規模なデータ セットの複雑な分析を共同で作業するデータ サイエンティストのチームによってよく使用されます。  Databricks のノートブックは R だけでなく他の言語もサポートしているため、アナリストが主要な業務で異なる言語を使用するチームに特に便利です。

「[Azure Databricks とは](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)」の記事
にプラットフォームの詳細と使い始めに役立つ情報が記載されています。

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio/) は、クラウドの予測分析ソリューションの構築、テスト、デプロイをドラッグ アンド ドロップで行うことができる、コラボレーションに対応したツールです。  新しいデータ サイエンティストが多くのコードを記述することなく機械学習モデルを作成してデプロイできるようになります。

ML Studio は、R と Python の両方をサポートしています。  ML Studio では 2 つの方法で R を使用できます。

### <a name="custom-r-scripts-in-your-experiments"></a>実験でのカスタム R スクリプト
まず、カスタム R スクリプトを記述することで ML Studio のデータ操作と機械学習の機能を拡張することができます。
ML Studio にはデータの準備および分析用のさまざまなモジュールが含まれていますが、R などの成熟した言語の機能と適合しません。そのため、このサービスは指定されたモジュールがニーズを満たさない場合に独自のカスタム R スクリプトを導入できるように設計されています。

この機能を活用するには、"R スクリプトの実行" モジュールを自分の実験にドラッグ アンド ドロップします。  次に、"プロパティ" ウィンドウのコード エディターを使用して新しい R スクリプトを記述するか、既存のスクリプトを貼り付けます。  スクリプト内では、外部の R パッケージを参照することができます。  このスクリプトを使用してデータを操作したり、標準の ML Studio モデル ライブラリに含まれない複雑な ML モデルをトレーニングしたりできます。

ML Studio の実験内での R の使用に関する完全な概要については、「[Azure Machine Learning 向け R プログラミング言語クイック スタート チュートリアル](https://docs.microsoft.com/azure/machine-learning/studio/r-quickstart)」を参照してください。

### <a name="create-manage-and-deploy-experiments-from-your-local-r-environment"></a>ローカルの R 環境から実験を作成、管理、デプロイする
ML Studio で R を使用する別の方法が、
<code>[AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)</code> パッケージを使用して R プログラミング環境で実験プロセスを監視し、制御する方法です。  Microsoft が管理するこのパッケージでは、Azure ML との間でデータセットをアップロード/ダウンロードしたり、実験を照会したり、R 関数を Azure ML の Web サービスとして発行したり、既存の Web サービスから R データを実行して出力を取得したりできます。

このパッケージにより、Azure ML をご自分の R コードのスケーラブルなデプロイ プラットフォームとしてはるかに簡単に使用できるようになります。  UI をクリックしてドラッグする代わりに、よく知っているツールを使用してデプロイ プロセス全体を自動化できます。

## <a name="azure-batch"></a>Azure Batch
大規模な R ジョブの場合は、[Azure Batch](https://azure.microsoft.com/services/batch/) を使用できます。  このサービスは、クラウド規模のジョブ スケジューリングとコンピューティング管理を提供するため、仮想マシンの数の増加に合わせて R ワークロードを拡張することができます。  これは汎用的なコンピューティング プラットフォームであるため、Azure Batch で R ジョブを実行するためのオプションがいくつかあります。

1 つ目のオプションは、Microsoft の <code>[doAzureParallel](https://github.com/Azure/doAzureParallel)</code> パッケージを使用する方法です。  この R パッケージは `foreach` パッケージのための並列バックエンドです。  これにより、`foreach` ループの反復を Azure Batch クラスター内のノード上で並列に実行できるようになります。  パッケージの概要については、ブログの投稿「[doAzureParallel: Take advantage of Azure’s flexible compute directly from your R session](https://azure.microsoft.com/blog/doazureparallel/)」(doAzureParallel: R セッションから Azure の柔軟なコンピューティングを直接活用する) を参照してください。

Azure Batch で R スクリプトを実行する別のオプションが、Azure portal の Batch App として自分のコードを "RScript.exe" でバンドルする方法です。  詳細なチュートリアルについては、「[R Workloads on Azure Batch](https://azure.microsoft.com/blog/r-workloads-on-azure-batch/)」(Azure Batch 上での R のワークロード) を参照してください。

3 つ目のオプションが、[Azure Distributed Data Engineering Toolkit](https://github.com/Azure/aztk) (AZTK) を使う方法です。Azure Batch で Docker コンテナーを使用してオンデマンドの Spark クラスターをプロビジョニングできます。  これにより、Azure で Spark のジョブを経済的に実行できます。  [SparklyR を AZTK と共に](https://github.com/Azure/aztk/wiki/SparklyR-on-Azure-with-AZTK) を使用して、R スクリプトをクラウド内で簡単に経済的にスケール アウトすることができます。

## <a name="azure-notebooks"></a>Azure Notebooks
[Azure Notebooks](https://notebooks.azure.com) はノートブックを使用して Azure にコードを実装することを好む R 開発者向けの低コストで問題の少ない方法です。  これは、マークダウンの文、実行可能コード、グラフィックを 1 つのキャンバスに統合できるオープンソースのプロジェクトである [Jupyter](https://jupyter.org/) を使用して自分のブラウザーでコードを開発し、実行するすべてのユーザーのための無料サービスです。

Azure Notebooks は小規模なプロジェクトで実行可能なオプションですが、大規模なデータ サイエンス プロジェクトでは適さないいくつかの制限があります。  現時点で、このサービスは各ノートブックのプロセスのメモリが 4 GB に制限されており、データ セットは 1 GB しか許容されていません。  しかし、小規模な分析を発行する場合には、これが簡単でコストがかからないオプションです。

## <a name="azure-sql-database"></a>Azure SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) はフル マネージドで完全にインテリジェントな Microsoft のリレーショナル クラウド データベース サービスです。  インフラストラクチャのセットアップの手間なく SQL Server のすべての機能を使用することができます。  これには、最近 SQL Service に追加された [Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017) が含まれます。

この機能は、ストアド プロシージャ、R のステートメントを含む T-SQL スクリプト、または T-SQL を含む R コードのいずれかとして SQL Server データベース内で R コードを実行できる埋め込みの予測分析とデータ サイエンス エンジンを提供します。  データベースからデータを抽出して R 環境に読み込むのではなく、R コードを直接データベースに読み込んで、データと共にすぐに実行させます。

Machine Learning Services は 2016 以降、オンプレミスの SQL Server の一部となっていますが、Azure SQL Database に対しては比較的新しいものです。  現在は[限定プレビュー](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#azure-sql-database-roadmap)中ですが、今後も進化し続けます。


### <a name="next-steps"></a>次の手順
* [mrsdeploy を使った Azure での R コードの実行](http://blog.revolutionanalytics.com/2017/03/running-your-r-code-azure.html)
* [クラウド内の Machine Learning Server](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-in-the-cloud)
* [Machine Learning Server および Microsoft R 用のその他のリソース](https://docs.microsoft.com/machine-learning-server/resources-more)
* [Azure 上での R](https://github.com/yueguoguo/r-on-azure) - Azure で R を使用するためのパッケージ、ツール、ケース スタディの概要

---

<sub>R のロゴは &copy; 2016 The R Foundation で、[Creative Commons Attribution-ShareAlike 4.0 International 国際ライセンス](https://creativecommons.org/licenses/by-sa/4.0/)の条件下で使用されます。</sub>
