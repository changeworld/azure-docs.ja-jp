---
title: 'リファレンス: Windows DSVM'
description: Windows Data Science VM に含まれるツールの詳細
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: be4397de477891f2a698c9f7dcb131da79479529
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200015"
---
# <a name="reference-windows-data-science-virtual-machine"></a>リファレンス: Windows Data Science Virtual Machine

Windows Data Science Virtual Machine 上で使用可能なツールの一覧については、以下を参照してください。 

## <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer エディション

VM に Machine Learning Server Developer エディションがインストールされているため、分析用の Microsoft Enterprise Library を使用できます。 以前は Microsoft R Server と呼ばれていた Machine Learning Server は、広範にデプロイできる分析プラットフォームです。 スケーラブルで、商用サポートがあります。

Machine Learning Server では、さまざまなビッグ データ統計、予測モデリング、機械学習のタスクがサポートされます。 幅広い分析機能 (探索、分析、視覚化、モデリングなど) をサポートしています。 Machine Learning Server は、オープン ソースの R と Python を使用して拡張することで、R と Python のスクリプトおよび関数と互換性があります。 また、エンタープライズ規模でのデータ分析のために、CRAN、pip、および Conda パッケージとの互換性もあります。

Machine Learning Server は、データの並列処理とチャンク処理を追加することで、オープン ソース R のメモリ内制限に対処します。 これにより、メイン メモリに収まりきるよりもはるかに大きなデータに対して、分析を実行することができます。 

VM には Visual Studio Community が含まれています。 R Tools for Visual Studio と、R または Python を操作するための完全な IDE を提供する Python Tools for Visual Studio (PTVS) 拡張機能が含まれています。 また、[RStudio](https://www.rstudio.com) や [PyCharm Community エディション](https://www.jetbrains.com/pycharm/)などの他の IDE も VM 上に提供しています。

## <a name="python"></a>Python

Python を使用して開発するために、Anaconda Python ディストリビューション 2.7 および 3.6 がインストールされています。 このディストリビューションには、基本 Python と、約 300 の最も一般的な数学、エンジニアリング、および Data Analytics パッケージが含まれています。 Visual Studio Community 2017 にインストールされている PTVS を使用できます。 または、IDLE や Spyder など、Anaconda でバンドルされている IDE の 1 つを使用できます。 これらのパッケージのいずれかを検索して開きます (Windows ロゴ キーを押しながら S キーを押します)。

> [!NOTE]
> Anaconda Python 2.7 で Python Tools for Visual Studio をポイントするには、各バージョン用のカスタム環境を作成する必要があります。 Visual Studio 2017 Community でこれらの環境パスを設定するには、 **[ツール]**  >  **[Python ツール]**  >  **[Python 環境]** に移動します。 そして、 **[+ カスタム]** を選択します。

Anaconda Python 3.6 は、C:\Anaconda にインストールされています。 Anaconda Python 2.7 は、C:\Anaconda\envs\python2 にインストールされています。 詳細な手順については、[PTVS のドキュメント](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters)を参照してください。

## <a name="the-jupyter-notebook"></a>Jupyter Notebook

Anaconda ディストリビューションは、コードと分析を共有するための環境である Jupyter Notebook にも付属しています。 Jupyter Notebook サーバーには、Python 2.7、Python 3.x、PySpark、Julia、および R カーネルがあらかじめ構成されています。 Jupyter サーバーを開始し、ブラウザーを開いて Notebook サーバーにアクセスするには、 **[Jupyter Notebook]** デスクトップ アイコンを使用します。

Python および R には、複数のサンプル ノートブックがパッケージングされています。Jupyter にアクセスすると、以下のテクノロジの使用方法がノートブックに表示されます。

* Machine Learning Server
* SQL Server Machine Learning Services、データベース内分析
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* その他の Azure テクノロジ

先ほど作成したパスワードを使用して Jupyter Notebook を認証すると、ノートブックのホーム ページにサンプルへのリンクがあります。

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM には Visual Studio Community が含まれています。 これは Microsoft の定評ある IDE の無料版であり、評価用または小規模なチーム用に使用できます。 [Microsoft ソフトウェア ライセンス条項](https://www.visualstudio.com/support/legal/mt171547)を参照してください。

デスクトップ アイコンまたは **[スタート]** メニューを使用して Visual Studio を開きます。 プログラムを探し (Windows ロゴ キーを押しながら S キーを押します)、 **[Visual Studio]** を探します。 そこから、C#、Python、R、Node.js などの言語でプロジェクトを作成できます。 インストールされているプラグインでは、次の Azure サービスを簡単に使用できます。

* Azure Data Catalog
* Hadoop および Spark 用 Azure HDInsight
* Azure Data Lake

このほか、Azure Machine Learning for Visual Studio Code というプラグインも Azure Machine Learning と統合し、AI アプリケーションの迅速な構築に役立ちます。

> [!NOTE]
> 評価期間が終了したことを示すメッセージが表示される場合があります。 ご利用の Microsoft アカウントの資格情報を入力してください。 または、新しい無料アカウントを作成して Visual Studio Community にアクセスします。

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer エディション

DSVM には、SQL Server 2017 Developer エディションと Machine Learning Services が付属します。 このエディションの SQL Server は R または Python のどちらかで提供され、データベース内分析を実行できます。 

Machine Learning Services はインテリジェント アプリケーションを開発、デプロイするためのプラットフォームです。 これらの言語とコミュニティの多くのパッケージを使用して、SQL Server データを使ったモデルの作成や予測の生成ができます。 Machine Learning Services (データベース内) は SQL Server 内で R と Python の両方の言語を統合するので、分析とデータを緊密に保つことができます。 この統合により、データ移動に伴うコストとセキュリティ リスクの低減ができます。

> [!NOTE]
> SQL Server Developer エディションは、開発およびテスト目的専用です。 運用環境で実行するためにはライセンスが必要です。

SQL Server には、Microsoft SQL Server Management Studio を開くことでアクセスできます。 VM の名前が**サーバー名**として設定されます。 Windows の管理者としてサインインする場合は、Windows 認証を使用します。 SQL Server Management Studio にアクセスできたら、他のユーザーの作成、データベースの作成、データのインポート、SQL クエリの実行などを行うことができます。

SQL Server Machine Learning Services を使ったデータベース内分析を有効にするには、SQL Server Management Studio でサーバー管理者としてサインインした後、次のコマンドを、1 回限りのアクションとして実行します。

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

`%COMPUTERNAME%` を VM の名前に置き換えます。

## <a name="azure"></a>Azure

VM には複数の Azure ツールがインストールされます。

* デスクトップのショートカットは、Azure SDK のドキュメントに移動します。
* Azure ストレージ アカウントとの間でデータをコピーするには、AzCopy を使用します。 使用方法を表示するには、コマンド プロンプトで「**Azcopy**」と入力します。
* Azure ストレージ アカウントに格納したオブジェクトを参照するには、Azure Storage Explorer を使用します。 Azure Storage との間でデータのコピーも行います。 このツールにアクセスするには、 **[検索]** フィールドに「**Storage Explorer**」と入力します。 または、Windows の **[スタート]** メニューで探します。
* AdlCopy は、Azure Data Lake にデータをコピーします。 使用方法を見るには、コマンド プロンプトで「**adlcopy**」と入力します。
* dtui ツールは、クラウド上の NoSQL データベースである Azure Cosmos DB との間でデータをコピーします。 コマンド プロンプトで「**dtui**」と入力します。
* 統合ランタイムは、オンプレミスのデータ ソースとクラウドとの間でデータをコピーします。 Azure Data Factory のようなツール内で使用されます。
* PowerShell スクリプト言語で Azure リソースを管理するには、Azure PowerShell を使用します。 やはり VM にインストールされます。

## <a name="power-bi"></a>Power BI

ダッシュボードの構築と高度な視覚化を支援するために、Power BI Desktop が DSVM に付属してインストールされます。 このツールを使用すると、他のソースからデータを抽出し、ダッシュボードとレポートを作成し、それらをクラウドに公開することができます。 詳細については、[Power BI のサイト](https://powerbi.microsoft.com)を参照してください。 Power BI Desktop は **[スタート]** メニューにあります。

> [!NOTE]
> Power BI にアクセスするには、Microsoft Office 365 アカウントが必要です。

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK for Python

データ サイエンティストと AI 開発者は、Python 用 Azure Machine Learning SDK を使用して、[Azure Machine Learning service](../service/overview-what-is-azure-ml.md) で機械学習のワークフローをビルドして実行します。 TensorFlow や scikit-learn などのオープンソース フレームワークを使用して、Jupyter Notebooks または別の Python IDE でサービスとやりとりできます。

Python SDK は、Microsoft Data Science Virtual Machine にプレインストールされています。 Python SDK の利用を開始するには、[Python を使用して Azure Machine Learning の利用を開始する方法](../service/quickstart-create-workspace-with-python.md)に関するページを参照してください。

## <a name="more-microsoft-development-tools"></a>その他の Microsoft 開発ツール

[Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) を使用して、他の Microsoft 開発ツールを探してダウンロードできます。 Microsoft Data Science Virtual Machine のデスクトップにはツールのショートカットもあります。  

## <a name="important-directories-on-the-virtual-machine"></a>Virtual Machine 上の重要なディレクトリ

| Item | Directory |
| --- | --- |
| Jupyter Notebook サーバーの構成 | C:\ProgramData\jupyter |
| Jupyter Notebook のサンプルのホーム ディレクトリ | C:\dsvm\notebooks および c:\users\\<username\>\notebooks |
| その他のサンプル | C:\dsvm\samples |
| Anaconda、既定: Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7 環境 | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (スタンドアロン) for Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| 既定の R インスタンス、Machine Learning Server (スタンドアロン) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Server Machine Learning Services のデータベース内インスタンスのディレクトリ | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| その他のツール | C:\dsvm\tools |

> [!NOTE]
> DSVM の Windows Server 2012 エディションおよび 2018 年 3 月以前の Windows Server 2016 エディションでは、既定の Anaconda 環境は Python 2.7 です。 セカンダリ環境は、C:\Anaconda\envs\py35 に配置されている Python 3.5 です。

## <a name="next-steps"></a>次の手順

その他の質問がある場合は、 [サポート チケット](https://azure.microsoft.com/support/create-ticket/)の作成を検討してください。
