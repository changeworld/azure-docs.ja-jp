<properties urlDisplayName="IPython Notebook" pageTitle="IPython Notebook - Azure チュートリアル" metaKeywords="" description="Linux または Windows の仮想マシン (VM) を使用して Azure 上に IPython Notebook をデプロイする方法について説明するチュートリアルです。" metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Azure での IPython Notebook" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo" />

# Azure での IPython Notebook

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p><a href="http://ipython.org">IPython プロジェクト</a>には、科学技術計算用ツールのコレクションが用意されています。このコレクションには、強力な対話型のシェル、高いパフォーマンスを実現する使いやすい並列ライブラリ、IPython Notebook と呼ばれる Web ベースの環境が含まれています。この Notebook よって、コードの実行とリアルタイムなコンピューティング ドキュメントの作成を統合した、対話型コンピューティング向けの作業環境が実現されます。作成されるノートブック ファイルには、任意のテキスト、数式、入力コード、結果、グラフィックス、ビデオ、その他の種類のメディアなど、最新の Web ブラウザーで表示できるデータを含めることができます。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">5:22:00</span></div>

</div>

Python を初めて利用し、対話形式の環境で楽しく Python を
学習する場合でも、本格的な並列計算や技術計算をする場合でも、
IPython Notebook は適切な選択です。機能を説明するために、次のスクリーンショットでは、
IPython Notebook を SciPy および matplotlib パッケージと
組み合わせて使用し、録音物の構造を分析する
方法を示します。

![スクリーンショット][スクリーンショット]

このドキュメントでは、Linux または Windows の仮想マシン (VM) を使用して Microsoft
Azure 上に IPython Notebook をデプロイする方法について説明します。Azure で IPython
Notebook を使用すると、Web 経由でアクセスできるインターフェイスを
拡張性の高いコンピューティング リソースに提供できます。これにより、Python とその多くのライブラリ
が持つ機能をすべて利用することができます。すべてのインストールはクラウド内で完了しているため、
ユーザーは最新の Web ブラウザーでローカルに構成しなくても、
これらのリソースにアクセスできます。

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Azure での VM の作成と構成

まず、Azure で動作する仮想マシン (VM) を作成します。
この VM はクラウドで動作する完全なオペレーティング システムであり、
IPython Notebook を実行するために使用されます。Azure では Linux と Windows のどちらの
仮想マシンも実行できます。また、これらの種類の仮想マシン上に IPython を設定することもできます。

### Linux VM

[ここ][ここ]に記載されている手順に従って、*OpenSUSE* または *Ubuntu* ディストリビューションの仮想マシンを作成します。このチュートリアルでは、OpenSUSE 13.1 と Ubuntu Server 14.04 LTS を使用します。既定のユーザー名が *azureuser* であると想定しています。

### Windows VM

[ここ][1]に記載されている手順に従って、*Windows Server 2012 Datacenter* ディストリビューションの仮想マシンを作成します。このチュートリアルでは、ユーザー名が *azureuser* であると想定しています。

## IPython Notebook 用のエンドポイントの作成

この手順は Linux と Windows の両方の VM に適用されます。後で、IPython の Notebook サーバーが
ポート 9999 で実行するように IPython を構成します。このポートが
パブリックに利用できるように、Azure 管理ポータルでエンドポイントを作成する必要があります。この
エンドポイントは Azure ファイアウォールでポートを開き、パブリック ポート (HTTPS、
443) を VM のプライベート ポート (9999) にマップします。

エンドポイントを作成するには、VM ダッシュボードに移動し、[エンドポイント]、[エンドポイントの
追加] の順にクリックして、新しいエンドポイント (この例では `ipython_nb`) を作成します。プロトコルに TCP
を選択し、パブリック ポートには 443、プライベート ポートには 9999 を指定します。

![スクリーンショット][2]

この順が完了すると、ダッシュボードの [エンドポイント] タブが次のように表示されます。

![スクリーンショット][3]

## VM への必要なソフトウェアのインストール

VM で IPython Notebook を実行するには、最初に、IPython とその依存するソフトウェアをインストールする
必要があります。

### Linux (OpenSUSE)

IPython とその依存するソフトウェアをインストールするには、最初に、SSH を使用して Linux VM に接続し、
次の手順を実行します。

次のコマンドを実行して、[NumPy][NumPy]、[Matplotlib][Matplotlib]、[Tornado][Tornado]、IPython が依存するその他のモジュールをインストールします。

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

IPython とその依存するソフトウェアをインストールするには、最初に、SSH を使用して Linux VM に接続し、
次の手順を実行します。

次のコマンドを実行して、[NumPy][NumPy]、[Matplotlib][Matplotlib]、[Tornado][Tornado]、IPython が依存するその他のモジュールをインストールします。

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

IPython とその依存モジュールを Windows VM にインストールするには、リモート デスクトップから VM に接続します。その後で、Windows PowerShell を使用して
次の手順を実行し、すべてのコマンド ラインのアクションを実行します。

**メモ**:Internet Explorer を使用してダウンロードするには、セキュリティの設定を変更する必要があります。**サーバー マネージャー**で、**[ローカル サーバー]**、**[IE セキュリティ強化の構成]** の順にクリックし、Administrators に対して IE セキュリティ強化の構成を無効にします。IPython のインストールが完了したら、その設定を再度有効にできます。

1.  [python.org][python.org] から Python 2.7.8 (32 ビット) をインストールします。
    `C:\Python27` と `C:\Python27\Scripts` を `PATH`
    環境変数に追加する必要もあります。

2.  **get-pip.py**
    ファイルを [][]<https://pip.pypa.io/en/latest/installing.html></a> からダウンロードして次のコマンドを実行し、
    pip と setuptools をインストールします。

        python get-pip.py

3.  次のコマンドを実行して、[Tornado][Tornado]、[PyZMQ][PyZMQ]、および IPython が依存するその他のモジュールをインストールします。

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

4.  [NumPy][NumPy] をダウンロードし、その提供元の Web サイトで入手できる
    `.exe` バイナリ インストーラーを使ってインストールします。この記事の執筆時点で最新バージョンは **numpy-1.90.0-win32-superpack-python2.7.exe** です。

5.  [Matplotlib][Matplotlib] をダウンロードし、その提供元の Web サイトで入手できる
    `.exe` バイナリ インストーラーを使ってインストールします。この記事の執筆時点で最新バージョンは **matplotlib-1.40.0.win32-py2.7.exe** です。

6.  OpenSSL をダウンロードしインストールします。Windows 版 OpenSSL は [][4]<http://slproweb.com/products/Win32OpenSSL.html></a> から入手できます。

    -   **Light** バージョンをインストールする場合、同じページで提供されている **Visual C++ 2008 Redistributable** もインストールする必要があります。

    -   `PATH` 環境変数に `C:\OpenSSL-Win32\bin` を追加する必要があります。

    > [WACOM.NOTE] OpenSSL をインストールする際は、ハートブリードのセキュリティ脆弱性の修正を含む、バージョン 1.0.1g 以降のバージョンを使用してください。

7.  次のコマンドを使用して IPython をインストールします。

        easy_install ipython

8.  Windows ファイアウォールでポートを開きます。Windows Server 2012 では、ファイアウォールにより、着信接続が既定でブロックされます。ポート 9999 を開くには、次の手順に従います。

    -   スタート画面から **[セキュリティが強化された Windows ファイアウォール]** を開きます。

    -   左側のパネルで **[受信の規則]** をクリックします。

    -   [操作] パネルで **[新しい規則]** をクリックします。

    -   新規の受信の規則ウィザードで **[ポート]** を選択します。

    -   次の画面で、**[TCP]** を選択し、**[特定のローカル ポート]** に「**9999**」と入力します。

    -   既定値をそのまま使用し、規則の名前を指定して、[完了] をクリックします。

### IPython Notebook の構成

次に、IPython Notebook を構成します。最初の手順では、カスタムの
IPython 構成プロファイルを作成して、構成情報をカプセル化します。

    ipython profile create nbserver

次に、プロファイル ディレクトリに移動して (`cd` コマンドの使用)、SSL 証明書を作成し、
プロファイルの構成ファイルを編集します。

Linux (OpenSUSE):

    cd ~/.config/ipython/profile_nbserver/

Linux (Ubuntu):

    cd ~/.ipython/profile_nbserver/

Windows:

    cd \users\azureuser\.ipython\profile_nbserver

どちらのプラットフォームの場合も、次のようにして SSL 証明書を作成します。

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

ここでは自己署名 SSL 証明書を作成しているので、
ノートブックに接続するときに、ブラウザーでセキュリティに関する警告が表示されます。長期間にわたって
製品を使用するには、組織に関連付けられている適切に署名された証明書を
使用する必要があります。証明書の管理はこのデモの範囲を越えているので、
ここでは自己署名証明書を引き続き使用します。

証明書を使用するだけでなく、許可されてない使用からノートブックを
保護するためにパスワードを指定する必要もあります。セキュリティ上の理由から、IPython は
暗号化されたパスワードを構成ファイルで使用するため、最初に
パスワードを暗号化する必要があります。IPython には暗号化のためのユーティリティが用意されています。コマンド プロンプトで次を実行してください。

    python -c "import IPython;print IPython.lib.passwd()"

このコマンドでは、パスワードとその確認が要求され、次のように
パスワードが出力されます。

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

次に、プロファイルの構成ファイルを編集します。
このファイルは、現在のプロファイル ディレクトリにある `ipython_notebook_config.py` ファイルです。このファイルは存在しないことがあります。その場合は作成してください。この
ファイルにはさまざまなフィールドが含まれ、既定ではいずれもコメント アウトされています。このファイルは、よく使用する
テキスト エディターで開くことができます。少なくとも次の内容が
含まれていることを確認してください。

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

この時点で、IPython Notebook を開始する準備が整っています。IPython Notebook を実行するには、
ノートブックを保存するディレクトリに移動して、
IPython Notebook サーバーを起動します。

    ipython notebook --profile=nbserver

これで、アドレス
`https://[Your Chosen Name Here].cloudapp.net` で IPython Notebook にアクセスすることができます。

ノートブックに初めてアクセスするとき、ログイン ページでパスワードが要求されます。

![スクリーンショット][5]

ログインすると、"IPython Notebook ダッシュボード" が表示されます。これは、ノートブックに関するすべての操作の
コントロール センターとなります。このページから、新しいノートブックを作成する、
既存のノートブックを開くなどの操作を実行できます。

![スクリーンショット][6]

[New Notebook] ボタンをクリックすると、次のように
ページが開きます。

![スクリーンショット][7]

`In []:` プロンプトがある領域に適切な Python コードを入力します。
`Shift-Enter` キーを押すか、[実行] アイコン (ツール バーにある右向き三角形) を
クリックすると、ここに入力したコードが実行されます。

NumPy と matplotlib のサポートで自動的に開始されるように
ノートブックを構成してあるので、次の例のように、図を作成できます。

![スクリーンショット][8]

## 強力なパラダイム: リッチ メディアを含んだリアルタイムなコンピューティング ドキュメント

ノートブック自体は、Python とワード プロセッサを使用したことがある
ユーザーにとっては違和感のないドキュメントです。これは、Python とワード プロセッサの両方の特徴を持っているためです。ノートブックでは、Python コードの
ブロックを実行できます。また、ツール バーのドロップダウン メニュー
でセルの形式を [Code] から [Markdown] に変更して、メモなどの
テキストを保存することもできます。

![スクリーンショット][9]

IPython Notebook はワード プロセッサよりも優れています。
IPython Notebook では、計算処理とリッチ メディア (テキスト、グラフィックス、ビデオなど、最新の Web ブラウザーで
表示できるほとんどのデータ) を混在させることができます。たとえば、教育用に説明ビデオと
計算処理を混在させることができます。

![スクリーンショット][10]

また、ノートブック ファイル内に外部の Web サイトを埋め込んで、リアルタイムに表示し
利用することができます。

![スクリーンショット][11]

Python に関連する多数の優れた科学技術計算用
ライブラリを使用することで、1 つの環境で、複雑なネットワーク分析も単純な
計算も同じように簡単に実行することができます。

![スクリーンショット][12]

最新の Web の機能とリアルタイムな計算処理を混在させるというパラダイムは、多くの可能性を示すものです。このパラダイムは、クラウドに適した理想的なパラダイムです。Notebook の用途には次のようなものがあります。

-   問題に対する調査作業を記録するためのコンピューティング スクラッチパッドとして使用。

-   "リアルタイムな" コンピューティング フォームまたはハードコピーの形式 (HTML や PDF) で、同僚と作業結果を共有するために使用。

-   計算処理を含んだライブ教材の配布や提示のために使用。これにより、生徒は実際のコードの試行、コードの変更、対話形式によるコードの再実行をすぐに行うことができます。

-   直ちに再現したり、検証したり、他のユーザーが拡張したりできる方法で調査結果を示す "実行可能な用紙" を提供するために使用。

-   コラボレーション コンピューティング用のプラットフォームとして使用。複数のユーザーが同じ Notebook
    サーバーにログインして、ライブのコンピューティング セッションを共有できます。

-   その他にも用途はあります。

IPython のソース コード リポジトリにアクセスすると、
[ノートブックの
サンプル][ノートブックの
サンプル]
が保存されているディレクトリ全体を確認できます。これらのサンプルをダウンロードして、独自の Azure IPython VM で試すことができます。
このサイトから `.ipynb` ファイルをダウンロードし、
ノートブック用の Azure VM のダッシュボードにアップロードしてください (直接 VM にダウンロードしてもかまいません)。

## まとめ

IPython Notebook には、Azure 上で Python エコシステムの機能に対話的に
アクセスするための強力なインターフェイスが用意されています。このインターフェイスは幅広い
用途 (簡単な調査、Python の学習、データの分析と表示、
シミュレーション、並列コンピューティングなど) に対応しています。作成される Notebook のドキュメント
には実行された計算処理の完全な記録が含まれており、
このドキュメントは他の IPython ユーザーと共有できます。IPython Notebook はローカル アプリケーション
として使用できますが、Azure でのクラウドの展開に最適です。

IPython の主要機能は、
[Python Tools for Visual Studio][Python Tools for Visual Studio](PTVS) を介して Visual Studio 内部で使用することもできます。PTVS は、Microsoft が提供する無料の
オープン ソース プラグインです。このプラグインによって、Visual Studio を Python の
高度な開発環境として利用することができます。この開発環境には、IntelliSense を備えた高度なエディター、デバッグ、
プロファイル、並列コンピューティング統合の機能が含まれています。

  [スクリーンショット]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png
  [ここ]: /ja-jp/manage/linux/tutorials/virtual-machine-from-gallery/
  [1]: /ja-jp/manage/windows/tutorials/virtual-machine-from-gallery/
  [2]: ./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png
  [3]: ./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png
  [NumPy]: http://www.numpy.org/ "NumPy"
  [Matplotlib]: http://matplotlib.sourceforge.net/ "Matplotlib"
  [Tornado]: http://www.tornadoweb.org/ "Tornado"
  [python.org]: http://www.python.org/download
  []: https://pip.pypa.io/en/latest/installing.html
  [PyZMQ]: https://github.com/zeromq/pyzmq "PyZMQ"
  [4]: http://slproweb.com/products/Win32OpenSSL.html
  [5]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png
  [6]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png
  [7]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png
  [8]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png
  [9]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png
  [10]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png
  [11]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png
  [12]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png
  [Python Tools for Visual Studio]: http://pytools.codeplex.com
