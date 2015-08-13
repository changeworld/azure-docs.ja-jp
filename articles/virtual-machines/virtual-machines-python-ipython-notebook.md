<properties
	pageTitle="IPython Notebook - Azure チュートリアル"
	description="Linux または Windows の仮想マシン (VM) を使用して Azure 上に IPython Notebook をデプロイする方法について説明するチュートリアルです。"
	services="virtual-machines"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="python"
	ms.topic="article"
	ms.date="05/20/2015"
	ms.author="huvalo"/>


# Azure での IPython Notebook

[IPython プロジェクト](http://ipython.org)には、科学技術計算用ツールのコレクションが用意されています。このコレクションには、強力な対話型のシェル、高いパフォーマンスを実現する使いやすい並列ライブラリ、IPython Notebook と呼ばれる Web ベースの環境が含まれています。この Notebook よって、コードの実行とリアルタイムなコンピューティング ドキュメントの作成を統合した、対話型コンピューティング向けの作業環境が実現されます。作成されるノートブック ファイルには、任意のテキスト、数式、入力コード、結果、グラフィックス、ビデオ、その他の種類のメディアなど、最新の Web ブラウザーで表示できるデータを含めることができます。

Python を初めて利用し、対話形式の環境で楽しく Python を学習する場合でも、本格的な並列計算や技術計算をする場合でも、IPython Notebook はお勧めの選択肢です。機能を説明するために、次のスクリーンショットでは、IPython Notebook を SciPy および matplotlib パッケージと組み合わせて使用し、録音物の構造を分析する方法を示します。

![スクリーンショット](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

この記事では、Linux または Windows の仮想マシン (VM) を使用して Microsoft Azure 上に IPython Notebook をデプロイする方法について説明します。Azure で IPython Notebook を使用すると、Web 経由でアクセスできるインターフェイスを拡張性の高いコンピューティング リソースに提供できます。これにより、Python とその多くのライブラリが持つ機能をすべて利用できます。すべてのインストールはクラウド内で完了しているため、ユーザーは最新の Web ブラウザーでローカルに構成しなくても、これらのリソースにアクセスできます。

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Azure での VM の作成と構成

最初の手順は Azure で動作する仮想マシン (VM) を作成することです。この VM は、クラウド内の完全なオペレーティング システムであり、IPython Notebook を実行するために使用されます。Azure では Linux と Windows のどちらの仮想マシンも実行できます。ここでは両方の仮想マシン上での IPython のセットアップについて説明します。

### Linux VM

[ここ][portal-vm-linux]に記載されている手順に従って、*OpenSUSE* または *Ubuntu* ディストリビューションの仮想マシンを作成します。このチュートリアルでは、OpenSUSE 13.2 と Ubuntu Server 14.04 LTS を使用します。既定のユーザー名が *azureuser* であると想定しています。

### Windows VM

[ここ][portal-vm-windows]に記載されている手順に従って、*Windows Server 2012 Datacenter* ディストリビューションの仮想マシンを作成します。このチュートリアルでは、ユーザー名が *azureuser* であると想定しています。

## IPython Notebook 用のエンドポイントの作成

この手順は Linux と Windows の両方の VM に適用されます。後で IPython を構成して notebook サーバーをポート 9999 で実行します。このポートを公開するには、Azure 管理ポータルでエンドポイントを作成する必要があります。このエンドポイントは Azure ファイアウォールでポートを開き、パブリック ポート (HTTPS、443) を VM のプライベート ポート (9999) にマップします。

エンドポイントを作成するには、VM ダッシュボードに移動し、**[エンドポイント]**、**[エンドポイントの追加]** の順にクリックして、新しいエンドポイント (この例では `ipython_nb`) を作成します。プロトコルに **TCP** を選択し、パブリック ポートには **443**、プライベート ポートには **9999** を指定します。

![スクリーンショット](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

この手順が完了すると、ダッシュボードの **[エンドポイント]** タブが次のスクリーンショットのように表示されます。

![スクリーンショット](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

## VM への必要なソフトウェアのインストール

VM で IPython Notebook を実行するには、まず IPython と依存するソフトウェアをインストールする必要があります。

### Linux (OpenSUSE)

IPython と依存するソフトウェアをインストールするには、まず SSH を使用して Linux VM に接続し、次の完了を実行します。

次のコマンドを実行し、[NumPy][NumPy]、[Matplotlib][Matplotlib]、[Tornado][Tornado]、IPython が依存するその他のモジュールをインストールします。

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

IPython と依存するソフトウェアをインストールするには、まず SSH を使用して Linux VM に接続し、次の手順を実行します。

最初に、次のコマンドでパッケージの新しいリストを取得します。

    sudo apt-get update

次のコマンドを実行し、[NumPy][NumPy]、[Matplotlib][Matplotlib]、[Tornado][Tornado]、IPython が依存するその他のモジュールをインストールします。

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

IPython とその依存モジュールを Windows VM にインストールするには、リモート デスクトップを利用し、VM に接続します。その後で、Windows PowerShell を使用して次の手順を実行し、すべてのコマンド ラインのアクションを実行します。

**注**: Internet Explorer を使用してダウンロードするには、セキュリティの設定を変更する必要があります。**サーバー マネージャー**で、**[ローカル サーバー]**、**[IE セキュリティ強化の構成]** の順にクリックし、Administrators に対して IE セキュリティ強化の構成を無効にします。IPython をインストールした後に再度有効にできます。

1.  [Python 2.7][] の最新の 32 ビット バージョンをダウンロードしてインストールします。`PATH` 環境変数に `C:\Python27` と `C:\Python27\Scripts` を追加する必要があります。

1.  次のコマンドを実行し、[Tornado][Tornado]、[PyZMQ][PyZMQ]、IPython が依存するその他のモジュールをインストールします。

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  [NumPy][NumPy] をダウンロードし、その提供元の Web サイトで入手できる `.exe` バイナリ インストーラーを使ってインストールします。この記事の執筆時点で最新バージョンは numpy-1.9.1-win32-superpack-python2.7.exe です。

1.  次のコマンドで [Matplotlib][Matplotlib] をインストールします。

        pip install matplotlib==1.4.2

1.  [OpenSSL][] をダウンロードしてインストールします。

	* 同じダウンロード ページで提供されている、Visual C++ 2008 Redistributable もインストールする必要があります。

	* `PATH` 環境変数に `C:\OpenSSL-Win32\bin` を追加する必要があります。

> [AZURE.NOTE]OpenSSL をインストールする際は、ハートブリードのセキュリティ脆弱性の修正を含む、バージョン 1.0.1g 以降のバージョンを使用してください。

1.  次のコマンドで IPython をインストールします。

        pip install ipython==2.4

1.  Windows ファイアウォールでポートを開きます。Windows Server 2012 では、ファイアウォールにより、着信接続が既定でブロックされます。ポート 9999 を開くには、次の手順に従います。

    - スタート画面から **[セキュリティが強化された Windows ファイアウォール]** を開きます。

    - 左側のパネルで **[受信の規則]** をクリックします。

	- [操作] パネルで **[新しい規則]** をクリックします。

	- 新規の受信の規則ウィザードで **[ポート]** を選択します。

	- 次の画面で、**[TCP]** を選択し、**[特定のローカル ポート]** に「**9999**」と入力します。

	- 既定値をそのまま使用し、規則の名前を指定して、**[完了]** をクリックします。

### IPython Notebook の構成

次に、IPython Notebook を構成します。最初の手順では、カスタムの IPython 構成プロファイルを作成し、構成情報をカプセル化します。次のコマンドを使用します。

    ipython profile create nbserver

次に、プロファイル ディレクトリに移動して (`cd` コマンドの使用)、SSL 証明書を作成し、プロファイルの構成ファイルを編集します。

Linux で次のコマンドを使用します。

    cd ~/.ipython/profile_nbserver/

Windows で次のコマンドを使用します。

    cd \users\azureuser.ipython\profile_nbserver

次のコマンドを使用し、SSL 証明書を作成します (Linux と Windows)。

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

ここでは自己署名 SSL 証明書を作成しているため、ノートブックに接続するときに、ブラウザーでセキュリティに関する警告が表示されます。長期間にわたって製品を使用するには、組織に関連付けられている適切に署名された証明書を使用する必要があります。証明書の管理はこのデモの範囲外のため、ここでは自己署名証明書を引き続き使用します。

証明書を使用するだけでなく、許可されてない使用からノートブックを保護するためにパスワードを指定する必要もあります。セキュリティ上の理由から、IPython は暗号化されたパスワードを構成ファイルで使用するため、最初にパスワードを暗号化する必要があります。IPython には暗号化のためのユーティリティが用意されています。コマンド プロンプトで次のコマンドを実行します。

    python -c "import IPython;print IPython.lib.passwd()"

このコマンドでは、パスワードとその確認が要求され、次のようにパスワードが出力されます。

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)
    
次に、プロファイルの構成ファイルを編集します。このファイルは、現在のプロファイル ディレクトリにある `ipython_notebook_config.py` ファイルです。このファイルは存在しないことがあります。その場合は作成してください。このファイルにはさまざまなフィールドが含まれ、既定ではいずれもコメント アウトされています。このファイルは、よく使用するテキスト エディターで開くことができます。少なくとも次の内容が含まれていることを確認してください。

    c = get_config()
    
    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'
    
    # You must give the path to the certificate file.
    
    # If using a Linux VM:
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'
    
    # And if using a Windows VM:
    c.NotebookApp.certfile = r'C:\Users\azureuser\.ipython\profile_nbserver\mycert.pem'
    
    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '
    
    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### IPython Notebook の実行

この時点で、IPython Notebook を開始する準備が整っています。IPython Notebook を実行するには、ノートブックを保存するディレクトリに移動して、IPython Notebook サーバーを起動します。次のコマンドを使用します。

    ipython notebook --profile=nbserver

これで、アドレス `https://[Your Chosen Name Here].cloudapp.net` で IPython Notebook にアクセスすることができます。

ノートブックに初めてアクセスするとき、ログイン ページでパスワードが要求されます。

![スクリーンショット](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

ログインすると、[IPython Notebook ダッシュボード] が表示されます。これは、ノートブックに関するすべての操作のコントロール センターとなります。このページから、新しいノートブックを作成し、既存のノートブックを開くことができます。

![スクリーンショット](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

**[New Notebook]** ボタンをクリックすると、次のオープニング ページが表示されます。

![スクリーンショット](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

`In []:` プロンプトがある領域に適切な Python コードを入力します。`Shift-Enter` キーを押すか、[実行] アイコン (ツール バーにある右向き三角形) をクリックすると、ここに入力したコードが実行されます。

NumPy と matplotlib のサポートで自動的に開始されるようにノートブックを構成してあるので、次のスクリーンショットのような図を作成することもできます。

![スクリーンショット](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

## 強力なパラダイム: リッチ メディアを含んだリアルタイムなコンピューティング ドキュメント

ノートブック自体は、Python とワード プロセッサを使用したことがあるユーザーにとっては違和感のないドキュメントです。これは、Python とワード プロセッサの両方の特徴を持っているためです。ノートブックでは、Python コードのブロックを実行できます。また、ツール バーのドロップダウン メニューでセルの形式を [Code] から [Markdown] に変更して、メモなどのテキストを保存することもできます。

![スクリーンショット](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)


IPython Notebook はワード プロセッサよりも優れています。IPython Notebook では、計算処理とリッチ メディア (テキスト、グラフィックス、ビデオなど、最新の Web ブラウザーで表示できるほとんどのデータ) を混在させることができます。たとえば、教育用に説明ビデオと計算処理を混在させることができます。

![スクリーンショット](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)

あるいは、次のスクリーンショットのように、ノートブック ファイル内に外部の Web サイトを埋め込んで、リアルタイムに表示し、利用できます。

![スクリーンショット](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

Python に関連する多数の優れた科学技術計算用ライブラリを使用することで、次のスクリーンショットのように、1 つの環境で複雑なネットワーク分析も単純な計算も同じように簡単に実行することができます。

![スクリーンショット](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

最新の Web の機能とリアルタイムな計算処理を混在させるというパラダイムは、多くの可能性を示すものです。このパラダイムは、クラウドに適した理想的なパラダイムです。Notebook の用途には次のようなものがあります。

* 問題に対する調査作業を記録するためのコンピューティング スクラッチパッドとして使用。

* "リアルタイムな" コンピューティング フォームまたはハードコピーの形式 (HTML や PDF) で、同僚と作業結果を共有するために使用。

* 計算処理を含んだライブ教材の配布や提示のために使用。これにより、生徒はすぐに実際のコードを試行したり、変更したり、あるいは対話形式で再実行したりすることができます。

* 直ちに再現したり、検証したり、他のユーザーが拡張したりできる方法で調査結果を示す "実行可能な用紙" を提供するために使用。

* コラボレーション コンピューティング用のプラットフォームとして使用。複数のユーザーが 同じ Notebook サーバーにログインして、ライブのコンピューティング セッションを共有できます。



IPython のソース コード [リポジトリ][]にアクセスすると、ノートブックのサンプルが保存されているディレクトリ全体を確認できます。これらのサンプルをダウンロードして、ユーザー独自の Azure IPython VM で試用することができます。このサイトから `.ipynb` ファイルをダウンロードし、それらのファイルをユーザーのノートブックに関する Azure VM のダッシュボードにアップロードしてください (直接 VM にダウンロードしてもかまいません)。

## まとめ

IPython Notebook には、Azure 上で Python エコシステムの機能に対話的にアクセスするための強力なインターフェイスが用意されています。このインターフェイスは幅広い用途 (簡単な調査、Python の学習、データの分析と表示、シミュレーション、並列コンピューティングなど) に対応しています。作成される Notebook のドキュメントには実行された計算処理の完全な記録が含まれており、このドキュメントは他の IPython ユーザーと共有できます。IPython Notebook はローカル アプリケーションとして使用できますが、Azure でのクラウドのデプロイに最適です。

IPython の主要機能は、[Python Tools for Visual Studio][] (PTVS) を介して Visual Studio 内部で使用することもできます。PTVS は、Microsoft が提供する無料のオープン ソース プラグインです。このプラグインによって、Visual Studio を Python の高度な開発環境として利用することができます。この開発環境には、IntelliSense を備えた高度なエディター、デバッグ、プロファイル、並列コンピューティング統合の機能が含まれています。




[Tornado]: http://www.tornadoweb.org/ "Tornado"
[PyZMQ]: https://github.com/zeromq/pyzmq "PyZMQ"
[NumPy]: http://www.numpy.org/ "NumPy"
[Matplotlib]: http://matplotlib.sourceforge.net/ "Matplotlib"
[portal-vm-windows]: /manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /manage/linux/tutorials/virtual-machine-from-gallery/

[リポジトリ]: https://github.com/ipython/ipython
[python Tools for visual studio]: http://aka.ms/ptvs

[Python 2.7]: http://www.python.org/download
[OpenSSL]: http://slproweb.com/products/Win32OpenSSL.html
 

<!--------HONumber=July15_HO5-->