<properties 
	pageTitle="Microsoft データ サイエンス仮想マシンのプロビジョニング | Microsoft Azure" 
	description="分析と機械学習を行うために、Azure でデータ サイエンス用仮想マシンを構成および作成します。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2016" 
	ms.author="bradsev" />


# Microsoft データ サイエンス仮想マシンのプロビジョニング

Microsoft データ サイエンス用仮想マシンは、プレインストールの Azure 仮想マシン (VM) イメージです。データ分析と機械学習用に一般的に使用されているいくつかのツールで構成されています。含まれているツールは、次のとおりです。

- Microsoft R Server Developer エディション
- Anaconda Python ディストリビューション
- Jupyter Notebook (と R および Python カーネル)
- Visual Studio Community エディション
- Power BI Desktop
- SQL Server 2016 Developer エディション
- 機械学習ツール
    - [Computational Network Toolkit (CNTK)](https://github.com/Microsoft/CNTK): Microsoft Research のディープ ラーニング ソフトウェア ツールキット。
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): オンライン、ハッシュ、allreduce、リダクション、learning2search、アクティブ、対話型学習などの手法をサポートする高速機械学習システム。
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): 迅速かつ正確なブースト ツリー実装を提供するツール。
    - [Rattle](http://rattle.togaware.com/) (R Analytical Tool To Learn Easily): GUI ベースのデータ探索と R コードの自動生成によるモデリングにより、R でデータ分析と機械学習を簡単に開始できるツール。
    - [mxnet](https://github.com/dmlc/mxnet) : 効率性と柔軟性の両方のために設計されたディープ ラーニング フレームワーク
- Azure Machine Learning などの Azure サービスで使用する R と Python のライブラリ
- GitHub、Visual Studio Team Services を含むソース コード リポジトリを操作する Git Bash を含む Git
- コマンド プロンプトからアクセスできる、いくつかの一般的な Linux コマンド ライン ユーティリティ (awk, sed, perl, grep, find, wget, curl などを含む) の Windows のポート。


データ サイエンスでは、一連のタスクの反復処理を行います。たとえば、データの検索、読み込み、および前処理、モデルの構築とテスト、インテリジェント アプリケーションで使用するためのモデルのデプロイなどです。これらのタスクを実行するために、さまざまなツールを使用するデータ科学者も珍しくありません。適切なバージョンのソフトウェアを見つけ、ダウンロードしてインストールするには、非常に時間がかかる場合があります。Microsoft データ サイエンス仮想マシンは、プレインストールされ、構成されているいくつかの一般的なツールを使って Azure 上でプロビジョニング可能なすぐに使用できるイメージにより、この負荷を容易にすることができます。

Microsoft データ サイエンス仮想マシンは分析プロジェクトの活性剤となります。これにより、R、Python、SQL、および C# を含むさまざまな言語でタスクを処理できます。Visual Studio には、コードの開発やテストを行うための使いやすい IDE が用意されています。VM に含まれている Azure SDK を使用すると、Microsoft のクラウド プラットフォームにあるさまざまなサービスを利用してアプリケーションを構築できます。

このデータ サイエンス VM イメージにソフトウェア課金はありません。Azure の利用料金は、この VM イメージでプロビジョニングする仮想マシンのサイズにのみ依存します。コンピューティング料金の詳細は[ここ](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)にあります。


## 前提条件

Microsoft データ サイエンス仮想マシンを作成する前に、次を用意する必要があります。

- **Azure サブスクリプション**: これを取得する場合は、[Azure 無試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

*   **Azure ストレージ アカウント**: これを作成する場合は、[Azure ストレージ アカウントの作成](storage-create-storage-account.md#create-a-storage-account)に関するページを参照してください。既存のアカウントを使用しない場合は、VM を作成する手順の一環としてストレージ アカウントを作成することもできます。


## Microsoft データ サイエンス仮想マシンの作成

Microsoft データ サイエンス仮想マシンのインスタンスを作成する手順を以下に示します。

1.	[Azure Portal](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm) に一覧表示されている仮想マシンに移動します。
2.	 下部にある **[作成]** ボタンをクリックして、ウィザードを起動します。![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 Microsoft データ サイエンス仮想マシンを作成するために使用されるウィザードでは、この図の右側に列挙されている **5 つのステップ**それぞれで**入力**が必要です。以下は、これらの各ステップを構成するために必要な入力項目です。

     **a.基本**:

   - **名前**: 作成するデータ サイエンス サーバーの名前です。
   - **ユーザー名**: 管理者アカウントのログイン ID です。
   - **パスワード**: 管理者アカウントのパスワードです。
   - **サブスクリプション**: 複数のサブスクリプションがある場合は、マシンが作成されて課金されるサブスクリプションを選択します。
   - **リソース グループ**: 新しいリソース グループを作成するか、既存のグループを使用します。
   - **場所**: 最適なデータ センターを選択します。通常は、ネットワーク アクセスを最速にするために、データの大部分があるか、物理的に最も近いデータ センターを選びます。


     **b.サイズ**:

   - 機能の要件とコストの制約を満たしている、いずれかのサーバーの種類を選択します。[すべて表示] を選択すると、他の VM のサイズも表示されます。

     **c.設定**:


   - **ディスクの種類**: ソリッド ステート ドライブ (SSD) を使用する場合は、[Premium] を選択します。それ以外の場合は、[Standard] を選択します。
  
   - **ストレージ アカウント**: サブスクリプションに新しい Azure ストレージ アカウントを作成することも、ウィザードの [基本] ステップで選択したのと同じ*場所*の既存のストレージ アカウントを使用することもできます。
  
   - **他のパラメーター**: 通常、既定値を使用します。既定以外の値の使用を検討する場合は、情報リンクにポインターを合わせ、該当するフィールドのヘルプを表示できます。


     **d.概要**:

   - 入力したすべての情報が正しいことを確認します。


     **e.購入**:

   - プロビジョニングを開始するには、**[購入]** をクリックします。取引条件へのリンクが用意されています。**[サイズ]** ステップで選択したサーバー サイズのコンピューティングを超える追加の課金が VM によって発生することはありません。


プロビジョニングには、10 ～ 20 分くらいかかります。プロビジョニングの状態は、Azure ポータルに表示されます。

## Microsoft データ サイエンス仮想マシンにアクセスする方法

VM を作成した後は、リモート デスクトップを使用して、手順 4. の [基本] セクションで作成した管理者アカウントの資格情報でログインできます。

VM が作成され、プロビジョニングされた後は、VM にインストールされて構成されたツールを使い始めることができます。多くのツールには、スタート メニュー タイルとデスクトップ アイコンがあります。

## Jupyter ノートブック サーバーに強力なパスワードを作成する方法 

コンピューターにインストールされた Jupyter ノートブック サーバーに強力なパスワードを作成するには、データ サイエンス仮想マシンのコマンド プロンプトで次のコマンドを実行します。

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

メッセージが表示されたら、強力なパスワードを選択します。

"sha1:xxxxxx" 形式のパスワード ハッシュが出力されます。このパスワード ハッシュをコピーし、ノートブックの構成ファイル (**C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py**) 内の既存のハッシュをパラメーター名 ***c.NotebookApp.password*** で置き換えます。

引用符内にある既存のハッシュ値のみを置き換える必要があります。引用符とパラメーター値の ***sha1:*** プレフィックスを保持する必要があります。

最後に、VM 上で実行中の Jupyter サーバーを停止し、"Start\_IPython\_Notebook" と呼ばれる Windows スケジュール タスクとして再起動する必要があります。このタスクを再起動した後、新しいパスワードが拒否される場合は、タスク マネージャーから実行中のすべての python プロセスを終了し、上記のスケジュールされたタスクを再起動するか、仮想マシンを再起動してみます。

## Microsoft データ サイエンス仮想マシンにインストールされているツール

### Microsoft R Server Developer エディション
分析に R を使用できるように、VM に Microsoft R Server Developer エディションがインストールされています。Microsoft R Server は、サポート対象のスケーラブルで安全な R に基づく、広範にデプロイできるエンタープライズ クラスの分析プラットフォームです。さまざまなビッグ データ統計、予測モデリング、および機械学習の機能をサポートする R Server は、幅広い分析機能 (探索、分析、視覚化、モデリングなど) をサポートしています。オープン ソース R を使用、拡張することで、Microsoft R Server は R スクリプト、関数、CRAN パッケージと完全に互換性のある形式で、エンタープライズ規模でデータを分析します。また、Microsoft R Server でデータの並列処理やチャンク処理を追加することにより、オープン ソース R のインメモリの制限に対処します。これにより、ユーザーはメイン メモリーに収まる容量よりはるかに大きいデータに対し分析を実行できます。VM にインストールされている Visual Studio Community エディションには Visual Studio 拡張機能のための R のツールがあり、R を操作するための完全な IDE を提供します。[RStudio](http://www.rstudio.com) などの他の IDE をダウンロードして使用することもできます。

### Python
Python を使用して開発するために、Anaconda Python ディストリビューション 2.7 および 3.5 がインストールされています。このディストリビューションには、基本 Python と、約 300 の最も一般的な数学、エンジニアリング、およびデータ分析パッケージが含まれています。Visual Studio 2015 Community 版でインストールされている、または IDLE や Spyder など、Anaconda でバンドルされている IDE の 1 つである Python Tools for Visual Studio (PTVS) を利用できます。検索バーで検索して (**Win** + **S** キー)、いずれかの IDE を起動できまます。

>[AZURE.NOTE] Anaconda Python 2.7 と 3.5 における Visual Studio の Python ツールをポイントするためには、各バージョン用のカスタム環境を作成する必要があります。**[ツール]**、**[Python Tools]**、**[Python Environments]** の順に移動し、**[+ Custom]** をクリックして、Visual Studio 2015 Community Edition にこれらの環境パスを設定します。

Anaconda Python 2.7 のインストール先は C:\\Anaconda、Anaconda Python 3.5 は c:\\Anaconda\\envs\\py35 です。詳細な手順については、[PTVS のドキュメント](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it)をご覧ください。

### Jupyter Notebook
Anaconda ディストリビューションは、コードと分析を共有するための環境である Jupyter ノートブックにも付属しています。Jupyter ノートブック サーバーは、Python 2、Python 3、R カーネルがあらかじめ構成されています。Notebook サーバーにアクセスするためのブラウザーを起動する Jupyter Notebook という名前のデスクトップ アイコンがあります。リモート デスクトップを通じて VM を利用している場合は、VM にログインしているときにJupyter ノートブック サーバーにアクセスするために、[https://localhost:9999/](https://localhost:9999/) を使用することもできます。
 
>[AZURE.NOTE] 証明書の警告が表示されても続行してください。

サンプルのノートブックがパッケージングされています (Python と R にそれぞれ 1 つずつ)。前の手順で作成したパスワードを使用して Jupyter ノートブックを認証すると、ノートブックのホーム ページにサンプルへのリンクを表示できます。Jupyter にログインすると、Microsoft R Server Services、SQL Server 2016 R Services (In-Database 分析) 、Python、その他の Azure のテクノロジの使用方法を示す Jupyter Notebook のサンプルを参照できます。

### Visual Studio 2015 Community エディション
Visual Studio Community エディションが VM にインストールされています。これは、Microsoft の人気のある IDE の無料版で、評価用または非常に小規模なチーム用に使用できます。ライセンス条項は、[ここ](https://www.visualstudio.com/support/legal/mt171547)で確認することができます。Visual Studio を開くには、デスクトップ アイコンをダブルクリックするか、**[スタート]** メニューを使用します。**Win** + **S** キーを押し、「Visual Studio」と入力することで、プログラムを検索することもできます。C#、Python、R、Node.JSなどの言語でプロジェクトを作成できます。また、インストールされたプラグインにより、Azure Data Catalog、Azure HDInsight (Hadoop、Spark)、Azure Data Lake などの Azure サービスを簡単に操作できるようになります。

>[AZURE.NOTE] 評価期間が終了したことを示すメッセージが表示される場合があります。Visual Studio Community エディションにアクセスするには、Microsoft アカウント資格情報を入力するか、無料アカウントを作成して、それを入力する必要があります。

### SQL Server 2016 Developer エディション
In-Database 分析を実行する SQL Server 2016 R Services の開発者向けのバージョンは、VM で提供されています。R Services はインテリジェント アプリケーションを開発、デプロイし、新しい洞察を明らかにするためのプラットフォームです。豊富で強力な R 言語とコミュニティの多くのパッケージを使用して、SQL Server データを使ったモデルの作成や予測の生成ができます。R Services (In-Database 分析)は R 言語と SQL Server を統合するため、データを常に分析し、データの移動に関連するコストとセキュリティ リスクを排除できます。SQL Server 2016 Developer エディションは開発およびテスト目的でのみ使用できます(運用環境で実行するには、ライセンスが必要です)。

SQL サーバーにアクセスするには、**SQL Server Management Studio** を起動します。VM の名前は、サーバー名として設定されます。Windows の管理者としてログインする場合は、Windows 認証を使用します。SQL Server Management Studio にアクセスできたら、他のユーザーの作成、データベースの作成、データのインポート、SQL クエリの実行などを行うことができます。

Microsoft R を使った In-Database 分析を有効にするには SQL Server Management Studio でサーバー管理者としてログオンした後、次のコマンドを、1 回限りのアクションとして実行します。

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### Azure 
VM には複数の Azure ツールがインストールされます。
- Azure SDK のドキュメントにアクセスするためのデスクトップ ショートカットがあります。
- **AzCopy** は、Microsoft Azure ストレージ アカウントとの間でのデータ移動に使用します。コマンド プロンプトで「**Azcopy** 」と入力するだけで、利用状況を表示できます。
- **Microsoft Azure ストレージ エクスプローラー**は、Azure ストレージ アカウントに保存したオブジェクトを参照し、Azure Storage との間でデータを転送するために使用します。検索で「**Storage Explorer**」と入力するか、Windows の [スタート] メニューから このツールにアクセスします。
- **Adlcopy** は Azure Data Lake にデータを移動するために使用します。コマンド プロンプトで「**adlcopy** 」と入力するだけで、利用状況を表示できます。
- **dtui** はクラウドにおける NoSQL データベースである Azure DocumentDB との間でデータを移動するために使用します。コマンド プロンプトに「**dtui**」と入力するだけです。
- **Microsoft Data Management Gateway** は、オンプレミスのデータ ソースとクラウド間のデータ移動を可能にします。Azure Data Factory のようなツール内で使用されます。
- **Microsoft Azure PowerShell** は、PowerShell スクリプト言語で Azure リソースを管理するためのツールです。やはり VM にインストールされます。

###Power BI

ダッシュボードの構築と高度な視覚化を支援するために、**Power BI Desktop** がインストールされています。このツールを使用すると、他のソースからデータを抽出し、ダッシュボードとレポートを作成し、それらをクラウドに公開することができます。詳細については、[Power BI](http://powerbi.microsoft.com) サイトを参照してください。Power BI Desktop は [スタート] メニューにあります。

>[AZURE.NOTE] Power BI にアクセスするには、Office 365 アカウントが必要です。

## その他の Microsoft 開発ツール
[**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) は、他の Microsoft 開発ツールを検出し、ダウンロードするために使用できます。Microsoft データ サイエンス仮想マシンのデスクトップにはツールのショートカットもあります。

## VM 上の重要なディレクトリ

| 項目 | Directory |
| ------------------------------| ---------------- |
|Jupyter Notebook サーバーの構成 | C:\\ProgramData\\jupyter |
|Jupyter Notebook のサンプルのホーム ディレクトリ| c:\\dsvm\\notebooks|
|その他のサンプル | c:\\dsvm\\samples|
| Anaconda (既定: Python 2.7) | c:\\Anaconda |
| Anaconda Python 3.5 環境 | c:\\Anaconda\\envs\\py35|
|スタンドアロンの R Server のインスタンスのディレクトリ (既定の R のインスタンス) | C:\\Program Files\\Microsoft SQL Server\\130\\R\_SERVER |
| R Server の In-Database インスタンスのディレクトリ | C:\\Program Files\\Microsoft SQL Server\\MSSQL13.MSSQLSERVER\\R\_SERVICES |
| その他のツール | c:\\dsvm\\tools|

>[AZURE.NOTE] 1.5.0 より前 (2016 年 9 月 3 日より前) に作成された Microsoft データ サイエンス仮想マシンのインスタンスは、上記と若干異なるディレクトリ構造を使用しています。

## 次のステップ
引き続き学習や調査に役立つ手順をいくつか紹介します。

* [スタート] メニューをクリックし、メニューに一覧表示されたツールを確認して、データ サイエンス VM 上のさまざまなデータ サイエンス ツールを検討できます。
* **C:\\Program Files\\Microsoft SQL Server\\130\\R\_SERVER\\library\\RevoScaleR\\demoScripts** に移動し、エンタープライズ規模でのデータ分析をサポートする R で RevoScaleR ライブラリを使用するサンプルを入手します。
* 「[データ サイエンス仮想マシンでできる 10 のこと](http://aka.ms/dsvmtenthings)」をご覧ください。
* [Team Data Science Process](https://azure.microsoft.com/documentation/learning-paths/data-science-process/) を使用して、エンド ツー エンドの分析ソリューションを体系的に構築する方法を確認します。
* [Cortana Intelligence ギャラリー](http://gallery.cortanaintelligence.com)では、Cortana Intelligence Suite を使用して機械学習やデータ分析のサンプルを入手できます。アクセスしやすいように、[スタート] メニューや仮想マシンのデスクトップにもアイコンが用意されています。

<!---HONumber=AcomDC_0907_2016-->