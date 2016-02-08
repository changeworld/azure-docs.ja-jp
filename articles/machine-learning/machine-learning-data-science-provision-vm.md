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
	ms.date="12/17/2015" 
	ms.author="bradsev" />


# Microsoft データ サイエンス仮想マシンのプロビジョニング

## はじめに

Microsoft データ サイエンス用仮想マシンは、プレインストールの Azure 仮想マシン (VM) イメージです。データ分析と機械学習用に一般的に使用されているいくつかのツールで構成されています。含まれているツールは、次のとおりです。

- Microsoft R Server Developer エディション
- Anaconda Python ディストリビューション
- Visual Studio Community エディション
- Power BI Desktop
- SQL Server Express エディション
- Azure SDK


データ サイエンスでは、一連のタスクの反復処理を行います。たとえば、データの検索、読み込み、および前処理、モデルの構築とテスト、インテリジェント アプリケーションで使用するためのモデルのデプロイなどです。これらのタスクを実行するために、さまざまなツールを使用するデータ科学者も珍しくありません。適切なバージョンのソフトウェアを見つけ、ダウンロードしてインストールするには、非常に時間がかかる場合があります。Microsoft データ サイエンス仮想マシンはその問題を解決します。

Microsoft データ サイエンス仮想マシンは分析プロジェクトの活性剤となります。これにより、R、Python、SQL、および C# を含むさまざまな言語でタスクを処理できます。Visual Studio には、コードの開発やテストを行うための使いやすい IDE が用意されています。VM に含まれている Azure SDK を使用すると、Microsoft のクラウド プラットフォームにあるさまざまなサービスを利用してアプリケーションを構築できます。

このデータ サイエンス VM イメージにソフトウェア課金はありません。Azure の利用料金は、この VM イメージでプロビジョニングする仮想マシンのサイズにのみ依存します。コンピューティング料金の詳細は[ここ](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)にあります。


## 前提条件

Microsoft データ サイエンス仮想マシンを作成する前に、次を用意する必要があります。

- **Azure サブスクリプション**: これを取得する場合は、[Azure 無試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

*   **Azure ストレージ アカウント**: これを作成する場合は、[Azure ストレージ アカウントの作成](storage-create-storage-account.md#create-a-storage-account)に関するページを参照してください。既存のアカウントを使用しない場合は、VM を作成する手順の一環としてストレージ アカウントを作成することもできます。


## Microsoft データ サイエンス仮想マシンの作成

Microsoft データ サイエンス仮想マシンのインスタンスを作成する手順を以下に示します。

1.	[Azure ポータル](https://ms.portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm)に一覧表示されている仮想マシンに移動します。
2.	 下部にある **[作成]** ボタンをクリックして、ウィザードを起動します。![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 以下のセクションでは、Microsoft データ サイエンス仮想マシンを作成するために使用されるウィザードの **5 つのステップ** (上の図の右に列挙してあります) の各ステップでの**入力**について説明します。以下は、これらの各ステップを構成するために必要な入力項目です。

  **a.基本**:

   - **名前**: 作成するデータ サイエンス サーバーの名前です。
   - **ユーザー名**: 管理者アカウントのログイン ID です。
   - **パスワード**: 管理者アカウントのパスワードです。
   - **サブスクリプション**: 複数のサブスクリプションがある場合は、マシンが作成されて課金されるサブスクリプションを選択します。
   - **リソース グループ**: 新しいリソース グループを作成するか、既存のグループを使用します。
   - **場所**: 最適なデータ センターを選択します。通常は、ネットワーク アクセスを最速にするために、データの大部分があるか、物理的に最も近いデータ センターを選びます。

  **b.サイズ**:

   - 機能の要件とコストの制約を満たしている、いずれかのサーバーの種類を選択します。[すべて表示] を選択すると、他の VM のサイズも表示されます。

  **c.設定**

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

"sha1:xxxxxx" 形式のパスワード ハッシュが出力されます。このパスワード ハッシュをコピーし、ノートブックの構成ファイル (**C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py**) 内の既存のハッシュを ***c.NotebookApp.password*** パラメーターで置き換えます。

引用符内にある既存のハッシュ値のみを置き換える必要があります。引用符とパラメーター値の ***sha1:*** プレフィックスを保持する必要があります。

最後に、VM 上で実行中の Ipython サーバーを停止し、"Start\_IPython\_Notebook" と呼ばれる Windows スケジュール タスクとして再起動する必要があります。このタスクを再起動した後、新しいパスワードが拒否される場合は、仮想マシンを再起動してください。

## Microsoft データ サイエンス仮想マシンにインストールされているツール

### Microsoft R Server Developer エディション
分析に R を使用できるように、VM に Microsoft R Server Developer エディションがインストールされています。Microsoft R Server は、サポート対象のスケーラブルで安全な R に基づく、広範にデプロイできるエンタープライズ クラスの分析プラットフォームです。さまざまなビッグ データ統計、予測モデリング、および機械学習の機能をサポートする R Server は、幅広い分析機能 (探索、分析、視覚化、モデリングなど) をサポートしています。オープン ソース R を使用、拡張することで、Microsoft R Server は R スクリプト、関数、CRAN パッケージと完全に互換性のある形式で、エンタープライズ規模でデータを分析します。また、Microsoft R Server でデータの並列処理やチャンク処理を追加することにより、オープン ソース R のメモリ内の制限に対処します。これにより、ユーザーはメイン メモリーに収まる容量よりはるかに大きいデータに対し分析を実行できます。さらに、IDE for R が VM にパッケージングされており、[スタート] メニューまたはデスクトップ上のアイコン "Revolution R Enterprise 8.0" をクリックしてアクセスできます。[RStudio](http://www.rstudio.com) のような他の IDE をダウンロードして使用することもできます。

### Python
Python を使用して開発するために、Anaconda Python ディストリビューション 2.7 および 3.5 がインストールされています。このディストリビューションには、基本 Python と、約 300 の最も一般的な数学、エンジニアリング、およびデータ分析パッケージが含まれています。Visual Studio 2015 Community 版でインストールされている、または IDLE や Spyder など、Anaconda でバンドルされている IDE の 1 つである Python Tools for Visual Studio (PTVS) を利用できます。検索バーで検索して (**Win** + **S** キー)、いずれかの IDE を起動できまます。**注**: Anaconda Python 2.7 および 3.5 で Python Tools for Visual Studio をポイントするには、各バージョン用のカスタム環境を作成する必要があります。それには、Visual Studio 2015 Community 版で [ツール] -> [Python Tools] -> [Python Environments] に移動し、[+ Custom] をクリックして環境パスを設定します。Anaconda Python 2.7 のインストール先は C:\\Anaconda、Anaconda Python 3.5 は c:\\Anaconda\\envs\\py35 です。詳細な手順については、[PTVS のドキュメント](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it)を参照してください。

### Jupyter Notebook
Anaconda ディストリビューションは、コードと分析を共有するための環境である Jupyter ノートブックにも付属しています。Jupyter ノートブック サーバーは、Python 2、Python 3、R カーネルがあらかじめ構成されています。Notebook サーバーにアクセスするためのブラウザーを起動する Jupyter Notebook という名前のデスクトップ アイコンがあります。リモート デスクトップを通じて VM を利用している場合は、Jupyter ノートブック サーバーにアクセスするために、[https://localhost:9999/](https://localhost:9999/) を使用することもできます (注: 証明書の警告が表示されても続行します)。サンプルのノートブックがパッケージングされています (Python と R にそれぞれ 1 つずつ)。前の手順で作成したパスワードを使用して Jupyter ノートブックを認証すると、ノートブックのホーム ページにサンプルへのリンクを表示できます。

### Visual Studio 2015 Community エディション
Visual Studio Community エディションが VM にインストールされています。これは、Microsoft の人気のある IDE の無料版で、評価用または非常に小規模なチーム用に使用できます。ライセンス条項は、[ここ](https://www.visualstudio.com/support/legal/mt171547)で確認することができます。Visual Studio を開くには、デスクトップ アイコンをダブルクリックするか、**[スタート]** メニューを使用します。**Win** + **S** キーを押し、「Visual Studio」と入力することで、プログラムを検索することもできます。C#、Python などの言語でプロジェクトを作成できます。また、インストールされたプラグインにより、Azure Data Catalog、Azure HDInsight (Hadoop、Spark)、Azure Data Lake などの Azure サービスを簡単に操作できるようになります。

注: 評価期間が終了したことを示すメッセージが表示される場合があります。Visual Studio Community エディションにアクセスするには、Microsoft アカウント資格情報を入力するか、Microsoft アカウント資格情報を作成して、それを入力します。

### SQL Server Express
SQL Server の制限付きバージョンも、Visual Studio Community エディションにパッケージ化されています。SQL サーバーにアクセスするには、**SQL Server Management Studio** を起動します。VM の名前は、サーバー名として設定されます。Windows の管理者としてログインする場合は、Windows 認証を使用します。SQL Server Management Studio にアクセスできたら、他のユーザーの作成、データベースの作成、データのインポート、SQL クエリの実行などを行うことができます。

### Azure 
いくつかの Azure ツールが、VM にインストールされています。Azure SDK ドキュメントにアクセスするためのデスクトップ ショートカットがあります。**AzCopy** は、Microsoft Azure Storage アカウントとの間でデータを移動するために使用されます。**Azure Storage エクスプローラー**は、Azure Storage アカウントに格納されているオブジェクトを参照するために使用されます。Powershell スクリプト言語で Azure リソースを管理するための **Microsoft Azure Powershell** も VM にインストールされています。

###Power BI

ダッシュボードの構築と高度な視覚化を支援するために、**Power BI Desktop** がインストールされています。このツールを使用すると、他のソースからデータを抽出し、ダッシュボードとレポートを作成し、それらをクラウドに公開することができます。詳細については、[Power BI](http://powerbi.microsoft.com) サイトを参照してください。

注: Power BI にアクセスするには、Office 365 アカウントが必要です。

## その他の Microsoft 開発ツール
[**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) は、他の Microsoft 開発ツールを検出し、ダウンロードするために使用できます。Microsoft データ サイエンス仮想マシンのデスクトップにはツールのショートカットもあります。

## 次のステップ
引き続き学習や調査に役立つ手順をいくつか紹介します。

* [スタート] メニューをクリックし、メニューに一覧表示されたツールを確認して、データ サイエンス VM 上のさまざまなデータ サイエンス ツールを検討できます。
* **C:\\Program Files\\Microsoft\\MRO-for-RRE\\8.0\\R-3.2.2\\library\\RevoScaleR\\demoScripts** に移動し、エンタープライス規模でのデータ分析をサポートする R で RevoScaleR ライブラリを使用して、サンプルを入手します。  
* [データ サイエンス プロセス](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)を体系的に使用して、エンド ツー エンドの分析ソリューションを構築する方法を確認します。
* [Cortana Analytics Gallery](http://gallery.cortanaanalytics.com) では、Cortana Analytics Suite を使用して機械学習やデータ分析のサンプルを入手できます。アクセスしやすいように、[スタート] メニューや仮想マシンのデスクトップにもアイコンが用意されています。 

<!---HONumber=AcomDC_0128_2016-->