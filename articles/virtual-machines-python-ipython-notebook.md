<properties pageTitle="IPython Notebook - Azure チュートリアル" description="Linux または Windows の仮想マシン (VM) を使用して Azure 上に IPython Notebook をデプロイする方法について説明するチュートリアルです。" services="virtual-machines" documentationCenter="python" authors="huguesv" manager="wpickett" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo"/>





# Azure での IPython Notebook

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p><a href="http://ipython.org">IPython プロジェクト</a>には、科学技術計算用ツールのコレクションが用意されています。このコレクションには、強力な対話型のシェル、高いパフォーマンスを実現する使いやすい並列ライブラリ、IPython Notebook と呼ばれる Web ベースの環境が含まれています。この Notebook よって、コードの実行とリアルタイムなコンピューティング ドキュメントの作成を統合した、対話型コンピューティング向けの作業環境が実現されます。作成されるノートブック ファイルには、任意のテキスト、数式、入力コード、結果、グラフィックス、ビデオ、その他の種類のメディアなど、最新の Web ブラウザーで表示できるデータを含めることができます。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">5:22</span></div>
</div>

Python を初めて利用し、対話形式の環境で楽しく Python を学習する場合でも、本格的な並列計算や技術計算をする場合でも、IPython Notebook はお勧めの選択肢です。機能を説明するために、次のスクリーンショットでは、IPython Notebook を SciPy および matplotlib パッケージと組み合わせて使用し、録音物の構造を分析する方法を示します。

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

このドキュメントでは、Linux または Windows の仮想マシン (VM) を使用して Microsoft Azure 上に IPython Notebook をデプロイする方法について説明します。Azure で IPython Notebook を使用すると、Web 経由でアクセスできるインターフェイスを拡張性の高いコンピューティング リソースに提供できます。これにより、Python とその多くのライブラリが持つ機能をすべて利用できます。すべてのインストールはクラウド内で完了しているため、ユーザーは最新の Web ブラウザーでローカルに構成しなくても、これらのリソースにアクセスできます。

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Azure での VM の作成と構成

最初の手順は Azure で動作する仮想マシン (VM) を作成することです。
この VM は、クラウド内の完全なオペレーティング システムであり、IPython Notebook を実行するために使用されます。Azure では Linux と Windows のどちらの仮想マシンも実行できます。ここでは両方の仮想マシン上での IPython のセットアップについて説明します。

### Linux VM

[ここ][portal-vm-linux]に記載されている手順に従って、 *OpenSUSE* または  *Ubuntu* ディストリビューションの仮想マシンを作成します。このチュートリアルでは、OpenSUSE 13.1 と Ubuntu Server 14.04 LTS を使用します。既定のユーザー名が  *azureuser* であると想定しています。

### Windows VM

[ここ][portal-vm-windows]に記載されている手順に従って、 *Windows Server 2012 R2 Datacenter* ディストリビューションの仮想マシンを作成します。このチュートリアルでは、ユーザー名が  *azureuser* であると想定しています。

## IPython Notebook 用のエンドポイントの作成

この手順は Linux と Windows の両方の VM に適用されます。後で IPython を構成して notebook サーバーをポート 9999 で実行します。このポートを公開するには、Azure 管理ポータルでエンドポイントを作成する必要があります。このエンドポイントは Azure ファイアウォールでポートを開き、パブリック ポート (HTTPS、443) を VM のプライベート ポート (9999) にマップします。

エンドポイントを作成するには、VM ダッシュボードに移動し、[エンドポイント]、[エンドポイントの追加] の順にクリックして、新しいエンドポイント (この例では  `ipython_nb`) を作成します。プロトコルに TCP を選択し、パブリック ポートには 443、プライベート ポートには 9999 を指定します。

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

この順が完了すると、ダッシュボードの [エンドポイント] タブが次のように表示されます。

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

## VM への必要なソフトウェアのインストール

VM で IPython Notebook を実行するには、まず IPython と依存するソフトウェアをインストールする必要があります。

### Linux (OpenSUSE)

IPython と依存するソフトウェアをインストールするには、まず SSH を使用して Linux VM に接続し、次の手順を実行します。

次のコマンドを実行して、[NumPy][numpy]、[Matplotlib][matplotlib]、[Tornado][tornado]、IPython が依存するその他のモジュールをインストールします。

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

IPython と依存するソフトウェアをインストールするには、まず SSH を使用して Linux VM に接続し、次の手順を実行します。

次のコマンドを実行して、[NumPy][numpy]、[Matplotlib][matplotlib]、[Tornado][tornado]、IPython が依存するその他のモジュールをインストールします。

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

IPython と依存モジュールを Windows VM にインストールするには、リモート デスクトップから VM に接続します。その後で、Windows PowerShell を使用して次の手順を実行し、すべてのコマンド ラインのアクションを実行します。

**注**:Internet Explorer を使用してダウンロードするには、セキュリティの設定を変更する必要があります。**サーバー マネージャー**で、**[ローカル サーバー]**、**[IE セキュリティ強化の構成]** の順にクリックし、Administrators に対して IE セキュリティ強化の構成を無効にします。IPython のインストールが完了したら、その設定を再度有効にできます。

1.  [python.org](http://www.python.org/download) から Python 2.7.8 (32 ビット) をインストールします。 
     `C:\Python27` と `C:\Python27\Scripts` を  `PATH` 
    環境変数に追加する必要もあります。

1.  **get-pip.py** ファイルを
    [https://pip.pypa.io/en/latest/installing.html](https://pip.pypa.io/en/latest/installing.html) からダウンロードして、次のコマンドを実行して Pip および setuptools をインストールします
    。

        python get-pip.py

1.  次のコマンドを実行して、[Tornado][tornado]、[PyZMQ][pyzmq]、IPython が依存するその他のモジュールをインストールします。

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  [NumPy][numpy] を
   Web サイトで利用可能な  `.exe` バイナリ インストーラーを使用して、ダウンロードしインストールします。この記事の執筆時点で最新バージョンは **numpy-1.9.0-win32-superpack-python2.7.exe** です。

1.  [Matplotlib][matplotlib] を
   Web サイトで利用可能な  `.exe` バイナリ インストーラーを使用して、ダウンロードしインストールします。この記事の執筆時点で最新バージョンは **matplotlib-1.4.0.win32-py2.7.exe** です。

1.  OpenSSL をダウンロードしてインストールします。Windows 版 OpenSSL は [http://slproweb.com/products/Win32OpenSSL.html](http://slproweb.com/products/Win32OpenSSL.html) から入手できます。

	* **Light** 版をインストールする場合は、**Visual C++ 2008 再頒布可能パッケージ** (同じページから入手可能) も併せてインストールする必要があります。

	*  `PATH` 環境変数に  `C:\OpenSSL-Win32\bin` を追加する必要があります。

	> [AZURE.NOTE] OpenSSL をインストールする際は、ハートブリードのセキュリティ脆弱性の修正を含む、バージョン 1.0.1g 以降のバージョンを使用してください。

1.  次のコマンドを使用して IPython をインストールします。

        easy_install ipython

1.  Windows ファイアウォールでポートを開きます。Windows Server 2012 では、ファイアウォールにより、着信接続が既定でブロックされます。ポート 9999 を開くには、次の手順に従います。

    - スタート画面から **[セキュリティが強化された Windows ファイアウォール]** を開きます。

    - 左側のパネルで **[受信の規則]** をクリックします。

	- [操作] パネルで **[新しい規則]** をクリックします。

	- 新規の受信の規則ウィザードで **[ポート]** を選択します。

	- 次の画面で、**[TCP]** を選択し、**[特定のローカル ポート]** に**「9999」**と入力します。

	- 既定値をそのまま使用し、規則の名前を指定して、[完了] をクリックします。

### IPython Notebook の構成

次に、IPython Notebook を構成します。まず、カスタム 
IPython 構成プロファイルを作成して、構成情報をカプセル化します。

    ipython profile create nbserver

次に、プロファイル ディレクトリに移動して ( `cd` コマンドの使用)、SSL 証明書を作成し、プロファイルの構成ファイルを編集します。

Linux (OpenSUSE):

    cd ~/.config/ipython/profile_nbserver/

Linux (Ubuntu):

    cd ~/.ipython/profile_nbserver/

Windows:

    cd \users\azureuser\.ipython\profile_nbserver

どちらのプラットフォームの場合も、次のようにして SSL 証明書を作成します。

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

ここでは自己署名 SSL 証明書を作成しているため、ノートブックに接続するときに、ブラウザーでセキュリティに関する警告が表示されます。長期間にわたって製品を使用するには、組織に関連付けられている適切に署名された証明書を使用する必要があります。証明書の管理はこのデモの範囲外のため、ここでは自己署名証明書を引き続き使用します。

証明書を使用するだけでなく、許可されてない使用からノートブックを保護するためにパスワードを指定する必要もあります。セキュリティ上の理由から、IPython は暗号化されたパスワードを構成ファイルで使用するため、最初にパスワードを暗号化する必要があります。IPython には暗号化のためのユーティリティが用意されています。コマンド プロンプトで次を実行してください。

    python -c "import IPython;print IPython.lib.passwd()"

このコマンドでは、パスワードとその確認が要求され、次のようにパスワードが出力されます。

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)
    
次に、プロファイルの構成ファイル (現在のプロファイル ディレクトリ内の `ipython_notebook_config.py` ファイル) を編集します。このファイルが存在しない場合には、作成します。このファイルにはさまざまなフィールドが含まれ、既定ではいずれもコメント アウトされています。このファイルは、よく使用するテキスト エディターで開くことができます。少なくとも次の内容が含まれていることを確認してください。

    c = get_config()
    
    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'
    
    # You must give the path to the certificate file.
    
    # If using a Linux VM (OpenSUSE):
    c.NotebookApp.certfile = u'/home/azureuser/.config/ipython/profile_nbserver/mycert.pem'

    # If using a Linux VM (Ubuntu):
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

この時点で、IPython Notebook を開始する準備が整っています。IPython Notebook を実行するには、ノートブックを保存するディレクトリに移動して、IPython Notebook サーバーを起動します。

    ipython notebook --profile=nbserver

これで、
`https://[Your Chosen Name Here].cloudapp.net` のアドレスで IPython Notebook にアクセスすることができます。

ノートブックに初めてアクセスするとき、ログイン ページでパスワードが要求されます。

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

ログインすると、[IPython Notebook ダッシュボード] が表示されます。これは、ノートブックに関するすべての操作のコントロール センターとなります。このページから、新しいノートブックを作成する、既存のノートブックを開くなどの操作を実行できます。

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

[New Notebook] ボタンをクリックすると、次のようにページが開きます。

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

 `In []:`  プロンプトでマークされた領域は、入力領域です。ここでは、
有効な Python コードを入力でき、 `Shift-Enter` キーを押すか、[Play] アイコン (ツールバーにある右向きの三角形) をクリックするときにそのコードが実行されます。

NumPy と matplotlib のサポートで自動的に開始されるようにノートブックを構成してあるので、次の例のように、図を作成できます。

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

## 強力なパラダイム: リッチ メディアを含んだリアルタイムなコンピューティング ドキュメント

ノートブック自体は、Python とワード プロセッサを使用したことがあるユーザーにとっては違和感のないドキュメントです。これは、Python とワード プロセッサの両方の特徴を持っているためです。ノートブックでは、Python コードのブロックを実行できます。また、ツール バーのドロップダウン メニューでセルの形式を [Code] から [Markdown] に変更して、メモなどのテキストを保存することもできます。

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)


IPython Notebook はワード プロセッサよりも優れています。IPython Notebook では、計算処理とリッチ メディア (テキスト、グラフィックス、ビデオなど、最新の Web ブラウザーで表示できるほとんどのデータ) を混在させることができます。たとえば、教育用に説明ビデオと計算処理を混在させることができます。

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)
 また、ノートブック ファイル内に外部の Web サイトを埋め込んで、リアルタイムに表示し利用することができます。

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

Python に関連する多数の優れた科学技術計算用ライブラリを使用することで、1 つの環境で、複雑なネットワーク分析も単純な計算も同じように簡単に実行することができます。

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

最新の Web の機能とリアルタイムな計算処理を混在させるというパラダイムは、多くの可能性を示すものです。このパラダイムは、クラウドに適した理想的なパラダイムです。Notebook の用途には次のようなものがあります。

* 問題に対する調査作業を記録するためのコンピューティング スクラッチパッドとして使用。

* " 'live'" コンピューティング フォームまたは
  ハードコピーの形式 (HTML や PDF) で、同僚と作業結果を共有するために使用。

* 計算処理を含むライブ教材の資料を配布および表示して、
  受講者が実際のコードをすぐに試したり、変更したり、
  対話形式で再実行したりできるように使用。

* すぐに再現したり、確認したり、他のユーザーが拡張したりできる方法で、
  調査結果を示す「実行可能な用紙」を提供するために使用。

* コラボレーション コンピューティング用のプラットフォームとして: 複数のユーザーが
  同じ notebook サーバーにログインして、リアルタイムなコンピューティング セッションを共有するために使用。

* その他にも用途はあります。

IPython のソース コード リポジトリにアクセスすると、[ノートブックのサンプル](https://github.com/ipython/ipython/tree/master/examples/notebooks)が保存されているディレクトリ全体を確認できます。これらのサンプルをダウンロードして、ユーザー独自の Azure IPython VM で試用することができます。
このサイトから  `.ipynb` ファイルをダウンロードし、それらのファイルをユーザーのノートブックに関する Azure VM のダッシュボードにアップロードするだけです (または VM に直接ダウンロードします)。

## まとめ

IPython Notebook には、Azure 上で Python エコシステムの機能に対話的にアクセスするための強力なインターフェイスが用意されています。このインターフェイスは幅広い用途 (簡単な調査、Python の学習、データの分析と表示、シミュレーション、並列コンピューティングなど) に対応しています。作成される Notebook のドキュメントには実行された計算処理の完全な記録が含まれており、このドキュメントは他の IPython ユーザーと共有できます。IPython Notebook はローカル アプリケーションとして使用できますが、Azure でのクラウドの展開に最適です。

IPython の主要機能は、 
[Python Tools for Visual Studio](http://pytools.codeplex.com) (PTVS) を介して Visual Studio 内部で使用することもできます。PTVS は、Microsoft が提供する無料のオープン ソース プラグインです。このプラグインによって、Visual Studio を Python の高度な開発環境として利用することができます。この開発環境には、IntelliSense を備えた高度なエディター、デバッグ、プロファイル、並列コンピューティング統合の機能が含まれています。



[tornado]:      http://www.tornadoweb.org/          "Tornado"
[PyZMQ]:        https://github.com/zeromq/pyzmq     "PyZMQ"
[NumPy]:        http://www.numpy.org/               "NumPy"
[Matplotlib]:   http://matplotlib.sourceforge.net/  "Matplotlib"

[portal-vm-windows]: /ja-jp/manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /ja-jp/manage/linux/tutorials/virtual-machine-from-gallery/


<!--HONumber=42-->
