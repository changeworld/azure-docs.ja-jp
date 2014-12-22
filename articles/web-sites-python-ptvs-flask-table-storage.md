<properties linkid="web-sites-python-ptvs-flask-table-storage" title="Flask and Azure Table Storage on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Python Tools 2.1 for Visual Studio を使用した Azure 上の Flask と Azure テーブル ストレージ" description="Learn how to use the Python Tools for Visual Studio to create a Flask application that stores data in Azure Table Storage and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Python Tools 2.1 for Visual Studio を使用した Azure 上の Flask と Azure テーブル ストレージ 

このチュートリアルでは、PTVS サンプル テンプレートのいずれかを使用して、簡単な投票アプリケーションを作成します。

この投票アプリケーションでは、リポジトリの抽象化を定義します。そのため、異なる種類のリポジトリ (メモリ内、Azure テーブル ストレージ、MongoDB) を簡単に切り替えることができます。

ここでは、Azure ストレージ アカウントを作成する方法、アプリケーションが Azure テーブル ストレージを使用するよう構成する方法、および Azure Web サイトにアプリケーションを発行する方法を学習します。

Bottle、Flask および Django Web フレームワークと MongoDB、Azure テーブル ストレージ、MySQL および SQL Database サービスを使用した、PTVS による Azure Web サイトの開発について説明するその他の記事については、[Python デベロッパー センター][]を参照してください。この記事では Azure Web サイトに焦点を当てますが、手順は [Azure クラウド サービス][]を開発する場合と似ています。

+ [前提条件](#prerequisites)
+ [プロジェクトを作成する](#create-the-project)
+ [Azure のストレージ アカウントの作成](#create-an-azure-storage-account)
+ [プロジェクトを構成する](#configure-the-project)
+ [Azure テーブル ストレージを調査する](#explore-the-azure-table-storage)
+ [Azure Web サイトへ発行する](#publish-to-an-azure-website)
+ [Azure Web サイトを構成する](#configure-the-azure-website)
+ [次のステップ](#next-steps)

##<a name="prerequisites"></a>前提条件

 - Visual Studio 2012 または 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Python Tools 2.1 for Visual Studio サンプル VSIX][]
 - [Azure SDK Tools for VS 2013][] または [Azure SDK Tools for VS 2012][]
 - [Python 2.7 (32 ビット)][] または [Python 3.4 (32 ビット)][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>プロジェクトを作成する

このセクションでは、サンプル テンプレートを使用して、Visual Studio プロジェクトを作成します。仮想環境を作成し、必要なパッケージをインストールします。その後、既定のメモリ内リポジトリを使用してローカルでアプリケーションを実行します。

1.  Visual Studio で、**[ファイル]**、**[新しいプロジェクト]** を順に選択します。

1.  PTVS サンプル VSIX のテンプレートは、**[Python]**、**[サンプル]** の下にあります。**[投票 Flask Web プロジェクト]** を選択し、[OK] をクリックしてプロジェクトを作成します。

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  外部のパッケージをインストールするように促されます。**[仮想環境にインストールする]** を選択します。

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  基になる Python インタープリターとして **[Python 2.7]** または **[Python 3.4]** を選択します。

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  <kbd>F5</kbd> キーを押して、アプリケーションが動作することを確認します。既定では、アプリケーションは、構成を必要としないメモリ内リポジトリを使用します。Web サーバーが停止すると、すべてのデータが失われます。

1.  **[サンプル投票の作成]** をクリックし、投票をクリックして投票します。

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

##<a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

ストレージ操作を行うには、Azure のストレージ アカウントが必要です。ストレージ アカウントを作成するには、次のステップを実行します。

1.  [Azure の管理ポータル][]にログインします。

1.  ナビゲーション ウィンドウの下部にある **[新規]** をクリックします。

  	![New Button](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png)

1.  **[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

  	![Quick Create](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)

1.    [URL] で、ストレージ アカウントの URI で使用する    サブドメイン名を入力します。文字数は 3 ～ 24 文字で、アルファベット小文字と数字を使用できます。この値は、対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URL のホスト名になります。

1.    ストレージの配置先となるリージョンまたは    アフィニティ グループを選択します。Azure アプリケーションからストレージを使用する場合は、アプリケーションをデプロイするリージョンと同じリージョンを選択します。

1.  必要に応じて、Geo レプリケーションを有効にできます。Geo レプリケーションにより、Azure Storage では、2 つの場所で高いデータ持続性を維持できるようになりました。両方の場所で、Azure Storage は常にデータの正常なレプリカを複数維持します。

1.  **[ストレージ アカウントの作成]** をクリックします。

##<a name="configure-the-project"></a>プロジェクトを構成する

このセクションでは、先ほど作成したストレージ アカウントを使用するようアプリケーションを構成します。Azure ポータルから接続の設定を取得する方法を見ていきます。その後、アプリケーションをローカルで実行します。

1.  [Azure の管理ポータル][]で、前のセクションで作成したストレージ アカウントをクリックします。

1.  **[アクセス キーの管理]** をクリックします。

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  Visual Studio のソリューション エクスプローラーで、使用するプロジェクト ノードを右クリックし、**[プロパティ]** を選択します。[デバッグ]**** タブをクリックします。

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  **[サーバー コマンドのデバッグ]**、**[環境]**で、アプリケーションに必要な環境変数の値を設定します。

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    これにより、**デバッグを開始する**ときに、環境変数が設定されます。**デバッグしないで開始する**ときに変数を設定する場合は、**[サーバー コマンドの実行]** にも同じ値を設定します。

    または、Windows のコントロール パネルを使用して、環境変数を定義できます。ソース コードまたはプロジェクト ファイルに資格情報を格納するのを避ける場合には、これがより優れた方法です。アプリケーションで新しい環境値を使用可能にするには、Visual Studio を再起動する必要があります。

1.  Azure テーブル ストレージ リポジトリを実装するコードは、**models/azuretablestorage.py** にあります。Python から Table サービスを使用する方法の詳細については、[ドキュメント]を参照してください。

1.  <kbd>F5</kbd> キーを使用してアプリケーションを実行します。**[サンプル投票の作成]** で作成された投票と、投票によって送信されたデータは、Azure テーブル ストレージでシリアル化されます。

1.  アプリケーションが、**Azure テーブル ストレージ** リポジトリを使用していることを確認するには、**[情報]** ページを参照します。

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

##<a name="explore-the-azure-table-storage"></a>Azure テーブル ストレージを調査する

ストレージのテーブルは、Visual Studio のサーバー エクスプローラーを使用して、簡単に表示し、編集できます。このセクションでは、投票アプリケーションのテーブルの内容を表示するのにサーバー エクスプローラーを使用します。

> [WACOM.NOTE]これには、Azure Tools をインストールする必要があります。このツールは、[Azure SDK for .NET][] の一部として使用できます。

1.  **サーバー エクスプローラー**を開きます。**[Azure]**、**[ストレージ]**、使用するストレージ アカウント、**[テーブル]** の順に展開します。

  	![Server Explorer](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png)

1.  **投票**テーブルまたは**選択肢**テーブルをダブルクリックすると、ドキュメント ウィンドウでテーブルの内容を表示し、エンティティを追加、削除、編集できます。

  	![Table Query Results](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png)

##<a name="publish-to-an-azure-website"></a>Azure Web サイトへ発行する

PTVS は、Azure の Web サイトに Web アプリケーションをデプロイする簡単な方法を提供します。

1.  **ソリューション エクスプローラー**で、プロジェクト ノードを右クリックして **[発行]** を選択します。

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  **Microsoft Azure Websites** をクリックします。

1.  **[新規]** をクリックして新しいサイトを作成します。

1.  **[サイト名]** と **[リージョン]** を選択し、**[作成]** をクリックします。

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png)

1.  その他のすべての既定値をそのまま使用し、**[発行]** をクリックします。

1.  Web ブラウザーが、発行されたサイトを自動的に開きます。[情報] ページを参照すると、**Azure テーブル ストレージ** リポジトリではなく、**メモリ内リポジトリ**を使用していることが確認できます。

    これは、Azure の Web サイトでは環境変数が設定されていないので、**settings.py** で指定されている既定値を使うためです。

##<a name="configure-the-azure-website"></a>Azure Web サイトを構成する

このセクションでは、サイト用の環境変数を構成します。

1.  [Azure の管理ポータル][]で、前のセクションで作成したサイトをクリックします。

1.  上部のメニューで、**[構成]** をクリックします。

  	![Top Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png)

1.  下方向へ **[アプリケーション設定]** セクションまでスクロールし、前のセクションで説明したように **REPOSITORY\_NAME**、**STORAGE\_NAME** および **STORAGE\_KEY** の値を設定します。

  	![App Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. 下部のメニューで **[保存]**、**[再起動]**、最後に **[参照]** の順にクリックします。

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  想定どおりにアプリケーションが **Azure テーブル ストレージ** リポジトリを使用して動作していることが確認できます。

    ご利用ありがとうございます。

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>次のステップ

Python Tools for Visual Studio、Flask および Azure テーブル ストレージの詳細については、以下のリンクを参照してください。

- [Python Tools for Visual Studio のドキュメント][]
  - [Web プロジェクト][]
  - [クラウド サービス プロジェクト][]
  - [Microsoft Azure でのリモート デバッグ][]
- [Flask のドキュメント][]
- [Azure Storage (Azure Storage)][]
- [Azure SDK for Python][]
- [Python からテーブル ストレージ サービスを使用する方法][]


<!--Link references-->
[Python デベロッパー センター]: /ja-jp/develop/python/
[Azure Cloud Services (Azure Cloud Services)]: ../cloud-services-python-ptvs/
[ドキュメント]: ../storage-python-how-to-use-table-storage/
[Python からテーブル ストレージ サービスを使用する方法]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Azure 管理ポータル]: https://manage.windowsazure.com
[Azure SDK for .NET]: http://azure.microsoft.com/ja-jp/downloads/
[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 for Visual Studio サンプル VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 ビット)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32 ビット)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio のドキュメント]: http://pytools.codeplex.com/documentation
[Flask のドキュメント]: http://flask.pocoo.org/
[Microsoft Azure でのリモート デバッグ]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Web プロジェクト]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[クラウド サービス プロジェクト]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Azure Storage (Azure Storage)]: http://azure.microsoft.com/ja-jp/documentation/services/storage/
[Azure SDK for Python]: https://github.com/Azure/azure-sdk-for-python
