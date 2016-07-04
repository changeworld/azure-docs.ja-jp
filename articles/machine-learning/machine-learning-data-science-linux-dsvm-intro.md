<properties
	pageTitle="Linux データ サイエンス仮想マシンのプロビジョニング | Microsoft Azure"
	description="分析と機械学習を行うために、Azure で Linux データ サイエンス仮想マシンを構成および作成します。"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="gokuma;bradsev" />

# Linux データ サイエンス仮想マシンのプロビジョニング 


Linux データ サイエンス仮想マシンは、プレインストールの Azure 仮想マシン (VM) イメージです。データ分析と機械学習用に一般的に使用されているいくつかのツールで構成されています。含まれている主なソフトウェア コンポーネントは次のとおりです。

- Microsoft R Open
- 一般的に使用されているデータ分析ライブラリを含む Anaconda Python ディストリビューション (v 2.7 および v3.5)
- Jupyter Notebook (R、Python)
- Azure Storage エクスプローラー
- Azure リソースを管理するための Azure コマンド ライン
- PostgresSQL データベース
- 機械学習ツール
    - [Computational Network Toolkit (CNTK)](https://github.com/Microsoft/CNTK): Microsoft Research によるディープ ラーニング ソフトウェア
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): オンライン、ハッシュ、allreduce、リダクション、learning2search、アクティブ、対話型学習などの手法をサポートする高速な機械学習システム
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): 迅速かつ正確なブースト ツリー実装を提供するツール
    - [Rattle](http://rattle.togaware.com/) (R Analytical Tool To Learn Easily): GUI ベースのデータ探索とモデリングおよび R コードの自動生成により、R で非常に簡単にデータ分析と機械学習を開始できるツール 
- Java、Python、node.js、Ruby、PHP の Azure SDK
- Azure Machine Learning などの Azure サービスで使用する R と Python のライブラリ
- 開発ツールおよびエディター (Eclipse、Emacs、gedit、vi)

データ サイエンスでは、次の一連のタスクを反復処理します。

- データの検索、読み込み、前処理 
- モデルの作成とテスト 
- インテリジェント アプリケーションで使用するためのモデルのデプロイ 

これらのタスクを実行するために、さまざまなツールを使用するデータ科学者も珍しくありません。適切なバージョンのソフトウェアを見つけ、ダウンロードし、コンパイルして、インストールするには、非常に時間がかかる場合があります。

Linux データ サイエンス仮想マシンはそうした問題の大部分を解決します。この仮想マシンを使うと、分析プロジェクトをすぐに開始し、R、Python、SQL、Java、C++ など、さまざまな言語でタスクを処理できます。Eclipse には、コードの開発やテストを行うための使いやすい IDE が用意されています。VM に含まれている Azure SDK を使用すると、Linux で、Microsoft のクラウド プラットフォームのさまざまなサービスを利用してアプリケーションを構築できます。また、プレインストールされた Ruby、Perl、PHP、node.js などの他の言語も利用できます。

このデータ サイエンス VM イメージにソフトウェア課金はありません。支払うのは、VM イメージでプロビジョニングした仮想マシンのサイズを基に評価された Azure のハードウェアの利用料金のみです。コンピューティング料金の詳細は[ここ](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linuxdsvm/)にあります。


## 前提条件

Linux データ サイエンス仮想マシンを作成する前に、次を用意する必要があります。

- **Azure サブスクリプション**: これを取得する場合は、[Azure 無試用版の取得](https://azure.microsoft.com/free/)に関するページを参照してください。
- **Azure ストレージ アカウント**: これを作成する場合は、[Azure ストレージ アカウントの作成](storage-create-storage-account.md#create-a-storage-account)に関するページを参照してください。既存のアカウントを使用しない場合は、VM を作成する手順の一環としてストレージ アカウントを作成することもできます。


## Linux データ サイエンス仮想マシンの作成

Linux データ サイエンス仮想マシンのインスタンスを作成する手順を以下に示します。

1.	[Azure ポータル](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm)に一覧表示されている仮想マシンに移動します。
2.	 下部にある **[作成]** ボタンをクリックして、ウィザードを起動します。![configure-data-science-vm](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.	 以下のセクションでは、Microsoft データ サイエンス仮想マシンを作成するために使用されるウィザードの **5 つのステップ** (上の図の右に列挙してあります) の各ステップでの**入力**について説明します。以下は、これらの各ステップを構成するために必要な入力項目です。

  **a.基本**:

   - **名前**: 作成するデータ サイエンス サーバーの名前です。
   - **ユーザー名**: 最初のアカウントのログイン ID
   - **パスワード**: 最初のアカウントのパスワード (パスワードの代わりに SSH 公開キーを使用できます)
   - **サブスクリプション**: 複数のサブスクリプションがある場合は、マシンが作成されて課金されるサブスクリプションを選択します。注: そのサブスクリプションに対するリソース作成権限が必要です。 
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

## Linux データ サイエンス仮想マシンにアクセスする方法

VM を作成した後は、テキスト シェル インターフェイスで SSH を使用して、手順 3. の [基本] セクションで作成したアカウントの資格情報でログインできます。Windows では、[Putty](http://www.putty.org) などの SSH クライアント ツールをダウンロードできます。グラフィカル デスクトップ (X Windows System) を使用する場合は、Putty で X11 転送を使用するか、X2Go クライアントをインストールすることができます。

>[AZURE.NOTE] テストでは、パフォーマンスは、X11 転送よりも、X2Go クライアントの方が非常に優れていました。そのため、グラフィカル デスクトップ インターフェイスでは、X2Go クライアントを使うことをお勧めします。


## X2Go クライアントのインストールと構成

Linux VM は、既に X2Go サーバーでプロビジョニングされており、クライアント接続を受け入れる準備ができています。Linux VM のグラフィカル デスクトップに接続するには、クライアントで次の操作を実行する必要があります。

1. お使いのクライアント プラットフォームに適した X2Go クライアントを[ここ](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)からダウンロードして、インストールします。    
2. X2Go クライアントを実行し、*[New Session]* (新しいセッション) を選択します。複数のタブがある構成ウィンドウが開きます。次の構成パラメーターを入力します。 
    * **[Session] (セッション) タブ**:
        - **[Host] (ホスト)**: Linux データ サイエンス VM のホスト名または IP アドレス。
        - **[Login] (ログイン)**: Linux VM のログイン ユーザー名。
        - **[SSH Port] (SSH ポート)**: 既定値の 22 のままにします。
        - **[Session Type] (セッションの種類)**: 値を XFCE に変更します。注: 現在、Linux VM では XFCE デスクトップのみをサポートしています。
    * **[Media] (メディア) タブ**: 音声のサポートとクライアントの印刷を使用しない場合は、それらをオフにできます。 
    * **[Shared folders] (共有フォルダー)**: クライアント コンピューターのディレクトリを Linux VM にマウントする場合は、このタブで VM と共有するクライアント コンピューターのディレクトリを追加します。 

SSH クライアントを使用して、または X2Go クライアントから XFCE グラフィカル デスクトップを使用して、VM にログインすると、VM にインストールされて構成されているツールの使用をすぐに開始できます。XFCE では、アプリケーション メニューのショートカットとさまざまなツールのデスクトップ アイコンが表示されます。

## Jupyter ノートブック サーバーに強力なパスワードを作成する方法 

コンピューターにインストールされた Jupyter ノートブック サーバーに強力なパスワードを作成するには、データ サイエンス仮想マシンのコマンド プロンプトで次のコマンドを実行します。

	python -c "import IPython;print(IPython.lib.passwd())"

メッセージが表示されたら、強力なパスワードを入力します。

"sha1:xxxxxx" 形式のパスワード ハッシュが出力されます。このパスワード ハッシュをコピーし、Notebook の構成ファイル (**/usr/local/etc/jupyter/jupyter\_notebook\_config.py**) 内の既存のハッシュを ***c.NotebookApp.password*** パラメーターで置き換えます。このファイルは、**root ユーザー**として編集する必要があります。

引用符内にある既存のハッシュ値のみを置き換える必要があります。引用符とパラメーター値の ***sha1:*** プレフィックスを保持する必要があります。

最後に、/etc/init.d/jupyter フォルダーにインストールされている Jupyter サービスを停止して再起動する必要があります。

>[AZURE.NOTE] Jupyter を再起動した後、新しいパスワードが拒否される場合、または Jupyter が停止する問題が発生する場合は、仮想マシンを再起動してください。

## Linux データ サイエンス仮想マシンにインストールされているツール

### Microsoft R Open 
R は、データ分析と機械学習に最もよく使われる言語の 1 つです。分析に R を使用できるように、VM に Microsoft R Open (MRO) と Math Kernel Library (MKL) がインストールされています。MKL は、分析アルゴリズムの一般的な算術演算を最適化します。MRO は、CRAN-R と 100% 互換性があり、CRAN で公開されているすべての R ライブラリを MRO にインストールできます。R プログラムは、vi、Emacs、gedit など、既定のいずれかのエディターで編集することができます。また、[RStudio](http://www.rstudio.com) などの他の IDE をダウンロードして使用することもできます。利便性を考えて、**/dsvm/tools** ディレクトリには、RStudio をインストールする簡単なスクリプト (installRStudio.sh) が用意されています。Emacs エディターを使用するユーザーのために、Emacs エディター内で R ファイルを簡単に操作できるようにする Emacs パッケージの ESS (Emacs Speaks Statistics) がプレインストールされています。

R を起動するには、シェルで「***R***」と入力するだけです。入力すると、対話型環境に移動します。R プログラムを開発するには、通常、Emacs、vi、gedit などのエディターを使用し、R 内でスクリプトを実行します。RStudio をインストールすると、完全なグラフィカル IDE 環境で R プログラムを開発できます。

必要な場合に備えて、[上位 20 の R パッケージ](http://www.kdnuggets.com/2015/06/top-20-r-packages.html)をインストールするための R スクリプトも用意されています。このスクリプトを実行するには、前述のとおり、シェルで「R」と入力して R の対話型インターフェイスに入る必要があります。

### Python
Python を使用して開発するために、Anaconda Python ディストリビューション 2.7 および 3.5 がインストールされています。このディストリビューションには、基本 Python と、約 300 の最も一般的な数学、エンジニアリング、およびデータ分析パッケージが含まれています。既定のテキスト エディターを使用できます。また、Anaconda Python ディストリビューションにバンドルされている Spyder (Python IDE) を使用することもできます。Spyder を使用するには、グラフィカル デスクトップまたは X11 転送が必要です。Spyder へのショートカットは、グラフィカル デスクトップに用意されています。

Python 2.7 と 3.5 の両方が用意されているため、現在のセッションで利用する目的の Python バージョンを明示的にアクティブ化する必要があります。アクティブ化プロセスにより、PATH 変数は Python の目的のバージョンに設定されます。

Python 2.7 をアクティブ化するには、シェルから次のコマンドを実行します。

	source /anaconda/bin/activate root

Python 2.7 は、*/anaconda/bin* にインストールされています。

Python 3.5 をアクティブ化するには、シェルから次のコマンドを実行します。

	source /anaconda/bin/activate py35


Python 3.5 は、*/anaconda/envs/py35/bin* にインストールされています。

これで、シェルで「***python***」と入力するだけで、python の対話型セッションを呼び出すことができます。グラフィカル インターフェイスを利用している場合または X11 転送を設定している場合は、***spyder*** コマンドを入力して Python IDE を起動できます。

### Jupyter Notebook
Anaconda ディストリビューションは、コードと分析を共有するための環境である Jupyter Notebook にも付属しています。Jupyter ノートブック サーバーは、Python 2、Python 3、R カーネルがあらかじめ構成されています。Notebook サーバーにアクセスするためのブラウザーを起動する Jupyter Notebook という名前のデスクトップ アイコンがあります。SSH または X2Go クライアント経由で VM を使用している場合は、[https://localhost:9999/](https://localhost:9999/) で Jupyter Notebook サーバーにアクセスすることもできます。

>[AZURE.NOTE] 証明書の警告が表示されても続行してください。

Jupyter Notebook サーバーは、どのホストからでもアクセスできます。「https://<VM DNS 名または IP アドレス>:9999/」と入力するだけです。サンプルの Notebook がパッケージングされています (Python と R にそれぞれ 1 つずつ)。前に作成したパスワードを使用して Jupyter Notebook を認証すると、Notebook のホーム ページにサンプルへのリンクを表示できます。[New] (新規) を選択し、言語カーネルを選択して、新しい Notebook を作成できます。[New] (新規) ボタンが表示されない場合は、左上にある Jupyter アイコンをクリックして、Notebook サーバーのホーム ページに移動します。


### IDE とエディター 

コード エディターには、いくつかの選択肢があります。選択肢として、vi/VIM、Emacs、gEdit、Eclipse があります。gEdit と Eclipse はグラフィカル エディターで、使用するにはグラフィカル デスクトップにログインする必要があります。これらのエディターには、起動するためのデスクトップとアプリケーションのメニューのショートカットが用意されています。

**VIM** と **Emacs** はテキスト ベースのエディターです。Emacs に関しては、Emacs エディター内で R を簡単に利用できるようにする Emacs Speaks Statistics (ESS) というアドオン パッケージがインストールされています。詳細については、[ESS](http://ess.r-project.org/) に関するページで確認できます。

**Eclipse** は、複数の言語をサポートする拡張可能なオープン ソースの IDE です。VM にインストールされているインスタンスは、Java 開発者用のエディションです。一般的な言語向けのプラグインがいくつか用意されています。それらのプラグインをインストールすると、Eclipse 環境を拡張できます。また、Eclipse には、**Azure Toolkit for Eclipse** というプラグインもインストールされています。このプラグインを使用すると、Java などの言語をサポートする Eclipse 開発環境を使用して、Azure アプリケーションを簡単に作成、開発、テスト、デプロイできます。また、**Azure SDK for Java** を使用すると、Java 環境内からさまざまな Azure サービスにアクセスすることができます。Azure Toolkit for Eclipse の詳細については、「[Azure Toolkit for Eclipse](../azure-toolkit-for-eclipse.md)」を参照してください。

**LaTex** は、texlive パッケージによって、Emacs アドオンである [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) パッケージと共にインストールされています。auctex を使用すると、Emacs 内で LaTex ドキュメントを簡単に作成できます。

### データベース

#### Postgres
オープン ソース データベースの **Postgres** は、サービスが実行され、initdb が既に完了している VM で使用できます。ただし、引き続きデータベースとユーザーを作成する必要があります。Postgres のドキュメントを参照してください。

####  グラフィカル SQL クライアント
**SQuirrel SQL** (グラフィカル SQL クライアント) は、さまざまなデータベース (Microsoft SQL Server、Postgres、MySQL など) に接続し、SQL クエリを実行するために用意されています。SQuirrel SQL は、グラフィカル デスクトップ セッションから (たとえば X2Go クライアントを使用して) 実行できます。SQuirrel SQL を起動するには、デスクトップ上のアイコンから起動するか、シェルで次のコマンドを実行します。

	/usr/local/squirrel-sql-3.7/squirrel-sql.sh 

初めて使用する場合は、先にドライバーとデータベースのエイリアスを設定する必要があります。JDBC ドライバーは次の場所にあります。

*/usr/share/java/jdbcdrivers*

SQuirrel SQL の詳細については、[SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots) の Web サイトを参照してください。

#### Microsoft SQL Server にアクセスするためのコマンド ライン ツール

Microsoft SQL Server の ODBC ドライバー パッケージには、次の 2 つのコマンド ライン ツールが付属しています。

**bcp** - bcp ユーティリティでは、Microsoft SQL Server のインスタンスとユーザーが指定した形式のデータ ファイルとの間でデータを一括コピーします。SQL Server のテーブルに多数の新しい行をインポートする、またはテーブルからデータ ファイルにデータをエクスポートする場合には、BCP ユーティリティを使用できます。テーブルにデータをインポートするには、そのテーブルに対して作成されたフォーマット ファイルを使用するか、テーブルの構造と列の有効なデータの種類を把握する必要があります。

詳細については、「[bcp による接続](https://msdn.microsoft.com/library/hh568446.aspx)」を参照してください。

**sqlcmd** - sqlcmd ユーティリティを使用すると、コマンド プロンプトで Transact-SQL ステートメント、システム プロシージャ、およびスクリプト ファイルを入力できます。このユーティリティでは、ODBC を使用して、Transact-SQL バッチを実行します。

詳細については、「[sqlcmd による接続](https://msdn.microsoft.com/library/hh568447.aspx)」を参照してください。

>[AZURE.NOTE] このユーティリティには、Linux と Windows プラットフォームでいくつか違いがあります。詳細については、上記のドキュメント ページを参照してください。


#### データベース アクセス ライブラリ

Python と R には、データベースにアクセスするためのライブラリが用意されています。

- R では、**RODBC** パッケージまたは **dplyr** パッケージを使用して、データベース サーバーに対して照会することや、SQL ステートメントを実行することができます。 
- Python では、**pyodbc** ライブラリを使用して、基盤となるレイヤーとして ODBC でデータベースにアクセスできます。  

**Postgres** にアクセスする方法は次のとおりです。

- Python の場合: **psycopg2** ライブラリを使用します。 
- R の場合: **RPostgreSQL** パッケージを使用します。


### Azure ツール 
VM には、次の Azure ツールがインストールされています。

- **Azure コマンド ライン インターフェイス**: Azure コマンド ライン インターフェイス (CLI) では、シェル コマンドを使用して Azure リソースを作成および管理できます。Azure ツールを起動するには、「***azure help***」と入力します。詳細については、[Azure CLI ドキュメント ページ](../virtual-machines-command-line-tools.md)を参照してください。
- **Microsoft Azure Storage Explorer**: Microsoft Azure Storage Explorer は、Azure ストレージ アカウントに保存したオブジェクトを参照したり、Azure BLOB のデータをアップロード/ダウンロードしたりするために使用するグラフィカル ツールです。Storage Explorer には、デスクトップのショートカット アイコンからアクセスできます。シェル プロンプトから「***StorageExplorer***」と入力して起動することもできます。X2Go クライアントからログインしているか、X11 転送を設定済みである必要があります。 
- **Azure ライブラリ**: インストールされており、利用可能なライブラリのいくつかを次に示します。

- **Python**: Python のインストールされている Azure 関連ライブラリは、***azure***、***azureml***、***pydocumentdb***、***pyodbc*** です。最初の 3 つのライブラリを使用すると、Azure ストレージ サービス、Azure Machine Learning、Azure DocumentDB (Azure の NoSQL データベース) にアクセスできます。4 番目のライブラリ pyodbc を (Microsoft ODBC Driver for SQL Server と共に) 使用すると、Python から ODBC インターフェイスを使用して Microsoft SQL Server、Azure SQL Database、Azure SQL Data Warehouse にアクセスできます。すべてのライブラリを一覧表示するには、「***pip list***」と入力してください。このコマンドは、必ず Python 2.7 と 3.5 の両方の環境で実行してください。

- **R**: R のインストールされている Azure 関連ライブラリは、***AzureML*** と ***RODBC*** です。

- **Java**: Azure Java ライブラリの一覧については、VM の ***/dsvm/sdk/AzureSDKJava*** ディレクトリを確認してください。主要なライブラリは、Azure Storage と管理 API、DocumentDB、SQL Server の JDBC ドライバーです。

[Azure ポータル](https://portal.azure.com)には、プレインストールされている Firefox ブラウザーからアクセスできます。Azure ポータルでは、Azure リソースを作成、管理、監視できます。

### Azure Machine Learning

Azure Machine Learning (Azure ML) は、完全に管理されたクラウド サービスで、ユーザーは予測分析ソリューションを構築、デプロイ、共有できます。実験やモデルは、Azure Machine Learning Studio で作成します。Azure Machine Learning Studio にアクセスするには、データ サイエンス仮想マシン上の Web ブラウザーから [Microsoft Azure Machine Learning](https://studio.azureml.net) にアクセスします。

Azure Machine Learning Studio にログインすると、実験キャンバスにアクセスできるようになります。実験キャンバスでは、機械学習アルゴリズムの論理フローを作成できます。また、Azure ML でホストされている Jupyter Notebook にアクセスし、Studio 内の実験をシームレスに操作することもできます。Azure ML では、作成した ML モデルを Web サービス インターフェイスでラップすることで、そのモデルを運用可能な状態にできます。これにより、任意の言語で記述されたクライアントで、ML モデルから予測を呼び出すことができます。Azure ML の詳細については、[Machine Learning のドキュメント](https://azure.microsoft.com/documentation/services/machine-learning/)を参照してください。

また、VM で R または Python を使ってモデルを作成し、Azure ML の運用環境にデプロイすることもできます。この機能を有効にするために、R と Python にライブラリをインストールしました。

- R のライブラリは、***AzureML*** という名前です。 
- Python の場合は、***azureml*** という名前です。 

R と Python で Azure ML にモデルをデプロイする方法の詳細については、「[データ サイエンス仮想マシンでできる 10 のこと](machine-learning-data-science-vm-do-ten-things.md)」の「*R または Python を使ってモデルを構築し、Azure Machine Learning を使って運用可能な状態にする*」を参照してください。
 
>[AZURE.NOTE] これらの手順は、Windows バージョンのデータ サイエンス VM 向けに記述されていますが、Azure ML へのモデルのデプロイに関する情報は Linux VM にも適用できます。

### 機械学習ツール

VM には、いくつかの ML ツールやアルゴリズムがプリコンパイルされ、ローカルにプレインストールされています。次に例を示します。

* **CNTK** (Microsoft Research の Computational Network Toolkit) - ディープ ラーニング ツールキット
* **Vowpal Wabbit** - 迅速なオンライン学習アルゴリズム
* **xgboost** - 最適化されたブースト ツリー アルゴリズムを提供するツール
* **Python** - Anaconda Python には、Scikit-learn などのライブラリと ML アルゴリズムがバンドルされています。その他のライブラリは、pip install を実行することでインストールできます。
* **R** - R 向けに、ML 関数の豊富なライブラリが用意されています。プレインストールされているライブラリには、lm、glm、randomForest、rpart などがあります。その他のライブラリは、次のコマンドを実行してインストールできます。 

		install.packages(<lib name>)

紹介した最初の 3 つの ML ツールについての追加情報を次に示します。

#### CNTK
これは、オープン ソースのディープ ラーニング ツールキットです。コマンド ライン ツール (cntk) で、既に PATH 内にあります。

基本的なサンプルを実行するには、シェルで次のコマンドを実行します。

	# Copy samples to your home directory and execute cntk
	cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo 
	cd cntkdemo/Data
	cntk configFile=../Config/Simple.cntk

モデルは *~/cntkdemo/Output/Models* に出力されます。

CNTK の詳細については、[github.com/Microsoft/CNTK](https://github.com/Microsoft/CNTK) および [CNTK Wiki](https://github.com/Microsoft/CNTK/wiki) を参照してください。


#### Vowpal Wabbit (VW):

Vowpal Wabbit は、オンライン、ハッシュ、allreduce、リダクション、learning2search、アクティブ、対話型学習などの手法を使用して機械学習の新境地を開拓する機械学習システムです。

非常に基本的な例でこのツールを実行するには、次のコマンドを実行します。

	cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
	cd vwdemo
	vw house_dataset

そのディレクトリには、ほかにも多数のデモが用意されています。VW の詳細については、[github.com/JohnLangford/vowpal\_wabbit](https://github.com/JohnLangford/vowpal_wabbit) および [Vowpal Wabbit Wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki) を参照してください。

#### xgboost
これは、ブースト (ツリー) アルゴリズム向けに設計および最適化されたライブラリです。このライブラリの目的は、コンピューターの計算能力の限界を超えて、スケーラブルで移植できる正確で大規模なツリー ブーストを提供することです。

コマンド ラインだけでなく、R ライブラリも用意されています。

R でこのライブラリを使用するには、(シェルで「*R*」と入力して) 対話型の R セッションを開始し、ライブラリを読み込みます。

R プロンプトで実行できる簡単な例を次に示します。

	library(xgboost)

	data(agaricus.train, package='xgboost')
	data(agaricus.test, package='xgboost')
	train <- agaricus.train
	test <- agaricus.test
	bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
	pred <- predict(bst, test$data)

xgboost コマンド ラインを実行するには、シェルで次のコマンドを実行します。

	cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
	cd xgboostdemo
	xgboost mushroom.conf


.model ファイルは、指定したディレクトリに書き込まれます。このデモの例については、[こちら](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)を参照してください。

xgboost の詳細については、[xgboost ドキュメント ページ](https://xgboost.readthedocs.org/en/latest/)および xgboost の [GitHub リポジトリ](https://github.com/dmlc/xgboost)を参照してください。

#### Rattle
Rattle (R Analytical Tool To Learn Easily) を使用すると、GUI ベースのデータ探索とモデリングにより、R で非常に簡単にデータ マイニングを開始できます。Rattle では、データの統計の概要を視覚的に表示し、簡単にモデル化できるようにデータを変換して、データから教師なしと教師ありの両方のモデルを作成し、モデルのパフォーマンスを視覚的に表示して、新しいデータセットをスコア付けできます。また、UI での操作をレプリケートする R コードを生成することもできます。生成したコードは、R で直接実行することや、詳細な分析の開始点として使用することができます。

Rattle を実行するには、グラフィカル デスクトップのログイン セッションに入っている必要があります。ターミナルで、「```R```」と入力して、R 環境に入ります。R プロンプトで、次のコマンドを入力します。

	library(rattle)
	rattle()
	
これで、グラフィカル インターフェイスが開き、一連のタブが表示されます。Rattle でサンプルの天候データセットを使用して、モデルを作成するクイック スタート手順を次に示します。以下の手順の一部では、必要な R パッケージのうち、システム上にないものを自動的にインストールして読み込むことを確認するメッセージが表示されます。**注**: システム ディレクトリ (既定) にパッケージをインストールするためのアクセス許可がない場合は、R コンソール ウィンドウに個人用ライブラリにパッケージをインストールするかどうかをたずねるメッセージが表示されます。そのメッセージが表示された場合は、"y" で回答します。

1. [Execute] (実行) をクリックします。
2. サンプルの天候データセットを使用するかどうかをたずねるダイアログが表示されます。[Yes] (はい) をクリックして、そのサンプルを読み込みます。
3. [Model] (モデル) タブをクリックします。
4. [Execute] (実行) をクリックして、デシジョン ツリーを作成します。
5. [Draw] (表示) をクリックして、デシジョン ツリーを表示します。 
6. [Forest] (フォレスト) ラジオ ボタンをクリックし、[Execute] (実行) をクリックして、ランダム フォレストを作成します。 
7. [Evaluate] (評価) タブをクリックします。
8. [Risk] (リスク) ラジオ ボタンをクリックし、[Execute] (実行) をクリックして、2 つのリスク (累積) パフォーマンス プロットを表示します。
9. [Log] (ログ) タブをクリックして、上記の操作用に生成された R コードを表示します (注: Rattle の現在のリリースにはバグがあります。ログのテキストで "Export this log ..." の前に "# " を挿入してください)。
10. [Export] (エクスポート) ボタンをクリックして、R スクリプトをホーム フォルダーに weather\_script.R というファイル名で保存します。

Rattle と R を終了できます。これで、生成された R スクリプトを変更することも、そのまま使用することもできます。スクリプトは、いつでも実行して、Rattle UI 内で実行したすべての操作を繰り返すことができます。これは、特に R を初めて使うユーザーにとって簡単な方法です。この方法では、シンプルなグラフィカル インターフェイスですばやく分析と機械学習を実行できる一方で、変更または学習する R のコードを自動的に生成できます。


## 次のステップ
引き続き学習や調査に役立つ手順をいくつか紹介します。

* この記事で説明しているツールを試して、データ サイエンス VM 上のさまざまなデータ サイエンス ツールを確認します。VM にインストールされているツールの基本的な概要と詳細情報を入手できる場所は、仮想マシン内のシェルで *dsvm-more-info* を実行して確認することもできます。  
* [Team Data Science Process](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) を使用して、エンド ツー エンドの分析ソリューションを体系的に構築する方法を確認します
* [Cortana Analytics ギャラリー](http://gallery.cortanaanalytics.com)では、Cortana Analytics Suite を使用して機械学習やデータ分析のサンプルを入手できます。 

<!---HONumber=AcomDC_0622_2016-->