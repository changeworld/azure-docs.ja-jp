---
title: Azure での Windows データ サイエンス仮想マシンのプロビジョニング | Microsoft Docs
description: 分析と機械学習を行うために、Azure でデータ サイエンス用仮想マシンを構成および作成します。
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma
ms.openlocfilehash: b749d8a904bc40eba3346cc03d9274236380c80d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450292"
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Azure での Windows データ サイエンス仮想マシンのプロビジョニング
Microsoft データ サイエンス用仮想マシンは、プレインストールの Windows Azure 仮想マシン (VM) イメージです。データ分析と機械学習用に一般的に使用されているいくつかのツールで構成されています。 含まれているツールは、次のとおりです。

* [Azure Machine Learning](../service/index.yml) Workbench
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer エディション
* Anaconda Python ディストリビューション
* Jupyter Notebook (R、Python、PySpark カーネル)
* Visual Studio Community エディション
* Power BI Desktop
* SQL Server 2017 Developer エディション
* ローカル開発およびテスト用のスタンドアロン Spark インスタンス
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* 機械学習ツールとデータ分析ツール
  * ディープ ラーニング フレームワーク: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)、[TensorFlow](https://www.tensorflow.org/)、[Chainer](https://chainer.org/)、mxNet などの AI フレームワークの豊富なセットが VM に含まれています。
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): オンライン、ハッシュ、allreduce、リダクション、learning2search、アクティブ、対話型学習などの手法をサポートする高速機械学習システム。
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): 迅速かつ正確なブースト ツリー実装を提供するツール。
  * [Rattle](http://rattle.togaware.com/) (R Analytical Tool To Learn Easily): R でデータ分析と機械学習を簡単に開始できるツール。 これには、R コードの自動生成を使用した GUI ベースのデータ探索とモデリングが含まれます。
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/): Java のビジュアル データ マイニングと機械学習ソフトウェア。
  * [Apache Drill](https://drill.apache.org/): Hadoop、NoSQL、およびクラウド ストレージ向けのスキーマフリーの SQL クエリ エンジン。  ODBC と JDBC のインターフェイスをサポートし、PowerBI、Excel、Tableau などの標準の BI ツールから NoSQL やファイルに対してクエリを実行できます。
* Azure Machine Learning などの Azure サービスで使用する R と Python のライブラリ
* GitHub、Visual Studio Team Services を含むソース コード リポジトリを操作する Git Bash を含む Git。git-bash およびコマンド プロンプト上の両方でアクセス可能な、よく使用される複数の Linux コマンドライン ユーティリティ (awk、sed、perl、grep、find、wget、curl などを含む) が用意されています。 

データ サイエンスでは、次の一連のタスクを反復処理します。

1. データの検索、読み込み、前処理
1. モデルの作成とテスト
1. インテリジェント アプリケーションで使用するためのモデルのデプロイ

データ サイエンティストは、こうしたタスクを行うためにさまざまなツールを使用します。 適切なバージョンのソフトウェアを見つけ、ダウンロードしてインストールするには、非常に時間がかかる場合があります。 Microsoft データ サイエンス仮想マシンは、プレインストールされ、構成されているいくつかの一般的なツールを使って Azure 上でプロビジョニング可能なすぐに使用できるイメージにより、この負荷を容易にすることができます。 

Microsoft データ サイエンス仮想マシンは分析プロジェクトの活性剤となります。 R、Python、SQL、C# など、さまざまな言語でタスクを処理できます。 Visual Studio には、コードの開発やテストを行うための使いやすい IDE が用意されています。 VM に含まれている Azure SDK を使用すると、Microsoft のクラウド プラットフォームにあるさまざまなサービスを利用してアプリケーションを構築できます。 

このデータ サイエンス VM イメージにソフトウェア課金はありません。 Azure の利用料金は、プロビジョニングする仮想マシンのサイズにのみ依存します。 コンピューティング料金の詳細については、 [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) ページに記載されている [価格の詳細] セクションをご覧ください。 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>データ サイエンス仮想マシンのその他のバージョン
多数の類似ツールに加えて、いくつかの追加のディープ ラーニング フレームワークを備えた [Ubuntu](dsvm-ubuntu-intro.md) イメージを利用することもできます。 [CentOS](linux-dsvm-intro.md) イメージも利用可能です。 また、Windows Server 2016 エディションのみで利用可能ないくつかのツールを介して、データ サイエンス仮想マシンの [Windows Server 2012 エディション](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm)も提供しています。  その他、この記事は Windows Server 2012 エディションにも該当します。

## <a name="prerequisites"></a>前提条件
Microsoft データ サイエンス仮想マシンを作成する前に、次を用意する必要があります。

* **Azure サブスクリプション**: これを取得する場合は、 [Azure 無試用版の取得](http://azure.com/free)に関するページを参照してください。


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Microsoft データ サイエンス仮想マシンの作成
Microsoft データ サイエンス仮想マシンのインスタンスを作成するには、次の手順に従ってください。

1. [Azure ポータル](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016)に一覧表示されている仮想マシンに移動します。
1. 下部にある **[作成]** ボタンを選択して、ウィザードを起動します。![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)
1. Microsoft データ サイエンス仮想マシンを作成するために使用されるウィザードでは、この図の右側に列挙されているそれぞれの**ステップ**で**入力**が必要です。 以下は、これらの各ステップを構成するために必要な入力項目です。
   
   1. **基本**
      
      1. **名前**: 作成するデータ サイエンス サーバーの名前です。
      1. **VM ディスクの種類**: SSD または HDD を選択します。 NC_v1 GPU インスタンス (NVidia Tesla K80 ベース) の場合、ディスクの種類として **HDD** を選択します。 
      1. **ユーザー名**: 管理者アカウントのログイン ID です。
      1. **パスワード**: 管理者アカウントのパスワードです。
      1. **サブスクリプション**: 複数のサブスクリプションがある場合は、マシンが作成されて課金されるサブスクリプションを選択します。
      1. **リソース グループ**: 新しいリソース グループを作成するか、既存のグループを使用します。
      1. **場所**: 最適なデータ センターを選択します。 通常は、ネットワーク アクセスを最速にするために、データの大部分があるか、物理的に最も近いデータ センターを選びます。
   1. **サイズ**: 機能の要件とコストの制約を満たしている、いずれかのサーバーの種類を選択します。 [すべて表示] を選択すると、他の VM のサイズも表示されます。
   1. **設定**:
      
      1. 
  **管理ディスクを使用**: Azure で VM のディスクを管理する場合、[管理対象] を選択します。  それ以外の場合は、新規または既存のストレージ アカウントを指定する必要があります。 
      1. **他のパラメーター**: 通常は既定値を使用します。 既定値以外の値の使用を検討する場合は、情報リンクにポインターを合わせて、該当するフィールドのヘルプを表示します。
    a. **総括**: 入力したすべての情報が正しいことを確認して、**[作成]** をクリックします。 **注**: 「**サイズ**」ステップで選択したサーバー サイズのコンピューティングを超える追加の課金が VM で発生することはありません。 

> [!NOTE]
> プロビジョニングには、10 ～ 20 分くらいかかります。 プロビジョニングの状態は、Azure ポータルに表示されます。
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Microsoft データ サイエンス仮想マシンにアクセスする方法
VM を作成した後は、前述の **[基本]** セクションで作成した管理者アカウントの資格情報を使って、リモート デスクトップを使用できます。 

VM が作成され、プロビジョニングされた後は、VM にインストールされて構成されたツールを使い始めることができます。 多くのツールには、スタート メニュー タイルとデスクトップ アイコンがあります。 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Microsoft データ サイエンス仮想マシンにインストールされているツール



### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer エディション
分析のためのスケーラブルな R または Python 用の Microsoft エンタープライズ ライブラリを使用したい場合、VM に Microsoft ML Server Developer エディション (以前は Microsoft R Server と呼ばれていました) をインストールします。 Microsoft ML Server は R および Python の両方で使用でき、幅広くデプロイできる企業クラスの分析プラットフォームであり、スケーラブルかつ商業用にサポートされていて、安全です。 さまざまなビッグ データ統計、予測モデリング、および機械学習の機能をサポートする ML Server は、幅広い分析機能 (探索、分析、視覚化、モデリングなど) をサポートしています。 オープン ソース R および Python を使用、拡張することで、Microsoft ML Server は R および Python スクリプト、関数、CRAN / pip / Conda パッケージと完全に互換性のある形式で、エンタープライズ規模でデータを分析します。 データの並列処理とチャンク処理を追加することで、オープン ソース R のメモリ内制限も対処します。 これにより、メイン メモリに収まりきるよりもはるかに大きなデータに対して、分析を実行することができます。  VM に搭載されている Visual Studio Community Edition には R Tools for Visual Studio と、R または Python を操作するための完全な IDE を提供する Python tools for Visual Studio が含まれています。 また、[RStudio](http://www.rstudio.com) や [PyCharm Community エディション](https://www.jetbrains.com/pycharm/)などの他の IDE も VM 上に提供しています。 

### <a name="python"></a>Python
Python を使用して開発するために、Anaconda Python ディストリビューション 2.7 および 3.6 がインストールされています。 このディストリビューションには、基本 Python と、約 300 の最も一般的な数学、エンジニアリング、およびデータ分析パッケージが含まれています。 Visual Studio 2017 Community 版でインストールされている、または IDLE や Spyder など、Anaconda でバンドルされている IDE の 1 つである Python Tools for Visual Studio (PTVS) を利用できます。 検索バーで検索して (**Win** + **S** キー)、いずれかの IDE を起動できまます。

> [!NOTE]
> Anaconda Python 2.7 で Python Tools for Visual Studio をポイントするには、各バージョン用のカスタム環境を作成する必要があります。 **[ツール]** -> **[Python Tools]** -> **[Python Environments]** の順に移動し、**[+ Custom]** をクリックして、Visual Studio 2017 Community Edition にこれらの環境パスを設定します。 
> 
> 

Anaconda Python 3.6 のインストール先は C:\Anaconda、Anaconda Python 2.7 は c:\Anaconda\envs\python2 です。 詳細な手順については、 [PTVS のドキュメント](/visualstudio/python/installing-python-interpreters.md) をご覧ください。 

### <a name="jupyter-notebook"></a>Jupyter Notebook
Anaconda ディストリビューションは、コードと分析を共有するための環境である Jupyter Notebook にも付属しています。 Jupyter Notebook サーバーには、Python 2.7、Python 3.x、PySpark、Julia、および R カーネルがあらかじめ構成されています。 Jupyter サーバーを開始して Notebook サーバーにアクセスするためにブラウザーを起動する、"Jupyter Notebook" という名前のデスクトップ アイコンがあります。 

Python および R には、複数のサンプル ノートブックがパッケージングされています。Jupyter にアクセスすると、Microsoft ML Server、SQL Server ML Services (In-Database 分析)、Python、Microsoft Cognitive ToolKit、Tensorflow、およびその他の Azure テクノロジの使用方法が Jupyter Notebook に表示されます。 前の手順で作成したパスワードを使用して Jupyter ノートブックを認証すると、ノートブックのホーム ページにサンプルへのリンクを表示できます。 

### <a name="visual-studio-2017-community-edition"></a>Visual Studio 2017 Community エディション
Visual Studio Community エディションが VM にインストールされています。 これは、Microsoft の人気のある IDE の無料版で、評価用または小規模なチーム用に使用できます。 ライセンス条項は、 [ここ](https://www.visualstudio.com/support/legal/mt171547)で確認することができます。  Visual Studio を開くには、デスクトップ アイコンをダブルクリックするか、 **[スタート]** メニューを使用します。 **Win** + **S** キーを押し、「Visual Studio」と入力することで、プログラムを検索することもできます。 C#、Python、R、Node.JSなどの言語でプロジェクトを作成できます。 また、インストールされたプラグインにより、Azure Data Catalog、Azure HDInsight (Hadoop、Spark)、Azure Data Lake などの Azure サービスを簡単に操作できるようになります。 Azure Machine Learning とシームレスに統合し、AI アプリケーションの迅速な構築に役立つ、```Visual Studio Tools for AI``` というプラグインもあります。 

> [!NOTE]
> 評価期間が終了したことを示すメッセージが表示される場合があります。 Visual Studio Community エディションにアクセスするには、Microsoft アカウント資格情報を入力するか、新しい無料アカウントを作成してください。 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer エディション
In-Database 分析を実行する SQL Server 2017 ML Services の開発者向けのバージョンは、R または Python のいずれかの VM 上に提供されています。 ML Services はインテリジェント アプリケーションを開発、デプロイするためのプラットフォームです。 豊富で強力なこれらの言語とコミュニティの多くのパッケージを使用して、SQL Server データを使ったモデルの作成や予測の生成ができます。 ML サービス (In-Database) は SQL Server 内で R と Python の両方の言語を統合するので、分析とデータを緊密に保つことができます。 これにより、データ移動に伴うコストとセキュリティ リスクの低減ができます。

> [!NOTE]
> SQL Server Developer エディションは、開発およびテスト目的でのみ使用できます。 運用環境で実行するためにはライセンスが必要です。 
> 
> 

SQL サーバーにアクセスするには、 **SQL Server Management Studio**を起動します。 VM の名前は、サーバー名として設定されます。 Windows の管理者としてログインする場合は、Windows 認証を使用します。 SQL Server Management Studio にアクセスできたら、他のユーザーの作成、データベースの作成、データのインポート、SQL クエリの実行などを行うことができます。 

SQL ML Services を使った In-Database 分析を有効にするには、SQL Server Management Studio でサーバー管理者としてログオンした後、次のコマンドを、1 回限りのアクションとして実行します。 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
VM には複数の Azure ツールがインストールされます。

* Azure SDK のドキュメントにアクセスするためのデスクトップ ショートカットがあります。 
* **AzCopy**: Microsoft Azure ストレージ アカウントとの間でのデータ移動に使用します。 利用状況を表示するには、コマンド プロンプトで「 **Azcopy** 」と入力します。 
* **Microsoft Azure ストレージ エクスプローラー**: Azure ストレージ アカウントに保存したオブジェクトを参照し、Azure ストレージとの間でデータを転送するために使用します。 検索で「 **Storage Explorer** 」と入力するか、Windows の [スタート] メニューから このツールにアクセスします。 
* **Adlcopy**: Azure Data Lake にデータを移動するために使用します。 利用状況を表示するには、コマンド プロンプトで「 **adlcopy** 」と入力します。 
* **dtui** はクラウドにおける NoSQL データベースである Azure Cosmos DB との間でデータを移動するために使用します。 コマンド プロンプトに「 **dtui** 」と入力します。 
* **Azure Data Factory Integration Runtime**: オンプレミスのデータ ソースとクラウド間のデータ移動を可能にします。 Azure Data Factory のようなツール内で使用されます。 
* **Microsoft Azure PowerShell**: PowerShell スクリプト言語で Azure リソースを管理するためのツールです。やはり VM にインストールされます。 

### <a name="power-bi"></a>Power BI
ダッシュボードの構築と高度な視覚化を支援するために、 **Power BI Desktop** がインストールされています。 このツールを使用すると、他のソースからデータを抽出し、ダッシュボードとレポートを作成し、それらをクラウドに公開することができます。 詳細については、 [Power BI](http://powerbi.microsoft.com) サイトを参照してください。 Power BI Desktop は [スタート] メニューにあります。 

> [!NOTE]
> Power BI にアクセスするには、Office 365 アカウントが必要です。 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Azure Machine Learning Workbench はデスクトップ アプリおよびコマンド ライン インターフェイスです。 Workbench には、ユーザーが実行したデータ準備手順を学習する組み込みのデータ準備機能が用意されています。 また、生産性を向上させるプロジェクト管理、実行履歴、ノートブック統合も提供しています。 モデルを開発するには、TensorFlow、Cognitive Toolkit、Spark ML、scikit-learn など、最良のオープンソース フレームワークを使用できます。 DSVM では、個々のユーザーの %LOCALAPPDATA% ディレクトリに Azure Machine Learning ワークベンチをインストールするためのデスクトップ アイコンを提供しています。 Workbench を使用する必要がある各ユーザーは、```AzureML Workbench Setup``` デスクトップ アイコンをダブルクリックする 1 回限りの操作を実行して、Workbench のインスタンスをインストールする必要があります。 また、Azure Machine Learning では、%LOCALAPPDATA%\amlworkbench\python で抽出されるユーザーごとの Python 環境を作成して使用します。

## <a name="additional-microsoft-development-tools"></a>その他の Microsoft 開発ツール
[**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) は、他の Microsoft 開発ツールを検出し、ダウンロードするために使用できます。 Microsoft データ サイエンス仮想マシンのデスクトップにはツールのショートカットもあります。  

## <a name="important-directories-on-the-vm"></a>VM 上の重要なディレクトリ
| 項目 | Directory |
| --- | --- |
| Jupyter Notebook サーバーの構成 |C:\ProgramData\jupyter |
| Jupyter Notebook のサンプルのホーム ディレクトリ |c:\dsvm\notebooks および c:\users\<ユーザー名>\notebooks|
| その他のサンプル |c:\dsvm\samples |
| Anaconda (既定: Python 3.6) |c:\Anaconda |
| Anaconda Python 2.7 環境 |c:\Anaconda\envs\python2 |
| Microsoft ML Server スタンドアロンの Python  | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| 既定の R インスタンス (ML Server スタンドアロン) |C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL ML Services の In-Database インスタンスのディレクトリ |C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Azure Machine Learning Workbench (ユーザーごと) | %localappdata%\amlworkbench | 
| その他のツール |c:\dsvm\tools |

> [!NOTE]
> DSVM の Windows Server 2012 エディションおよび 2018 年 3 月以前の Windows Server 2016 ディションでは、既定の Anaconda 環境は Python 2.7 です。 セカンダリ環境は、c:\Anaconda\envs\py35 に配置されている Python 3.5 です。 
> 
> 

## <a name="next-steps"></a>次の手順
引き続き学習や調査に役立つ手順をいくつか紹介します。 

* [スタート] メニューをクリックし、メニューに一覧表示されたツールを確認して、データ サイエンス VM 上のさまざまなデータ サイエンス ツールを検討できます。
* Azure Machine Learning Services および Workbench については、製品の[クイック スタートおよびチュートリアルのページ](../service/index.yml)をご覧ください。 
* **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** に移動し、エンタープライズ規模でのデータ分析をサポートする R で RevoScaleR ライブラリを使用するサンプルを入手します。  
* 「 [データ サイエンス仮想マシンでできる 10 のこと](http://aka.ms/dsvmtenthings)
* [Team Data Science Process](../team-data-science-process/index.yml)を使用して、エンド ツー エンドの分析ソリューションを体系的に構築する方法を確認します。
* Azure Machine Learning および関連する Azure 上のデータ サービスを使用した機械学習およびデータ分析については、[Azure AI Gallery](http://gallery.cortanaintelligence.com) をご覧ください。 このギャラリーへは、 **[スタート]** メニューや仮想マシンのデスクトップにもアイコンが用意されています。

