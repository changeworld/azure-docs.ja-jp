<properties linkid="develop-python-install-python" urlDisplayName="Python のインストール" pageTitle="Python と SDK のインストール - Azure" metaKeywords="Azure Python SDK" description="Azure で使用するための Python と SDK のインストール方法について説明します。" metaCanonical="" services="" documentationCenter="Python" title="Python と SDK のインストール" authors="" solutions="" manager="" editor="" />




# Python と SDK のインストール
Python は、Windows でのセットアップが容易であり、Mac および Linux ではプレインストールされています。このガイドでは、インストール方法と Azure で使用するためのコンピューターの準備について説明します。以下の説明が記載されています。

* Python Azure SDK の機能
* 使用する Python とそのバージョン
*Windows でのインストール
*Mac と Linux でのインストール

## Python Azure SDK の機能

Azure SDK for Python には、Azure 向けの Python アプリケーションを開発、デプロイ、管理するためのコンポーネントが用意されています。Azure SDK for Python には次のコンポーネントが用意されています。

* **Azure 用 Python クライアント ライブラリ**。これらのクラス ライブラリには、データ管理サービスやクラウド サービスなどの Azure の機能にアクセスするためのインターフェイスが用意されています。
***Mac および Linux 用 Azure コマンド ライン ツール**。これは、Azure の Web サイトや Azure の仮想マシンなどの Azure サービスをデプロイおよび管理するためのコマンド ライン ツールのセットです。これらのツールは、Mac、Linux、Windows など、すべてのプラットフォームで動作します。
* **PowerShell for Azure (Windows のみ)**。これは、クラウド サービスや仮想マシンなどの Azure サービスをデプロイおよび管理するための PowerShell コマンドレットのセットです。
***Azure エミュレーター (Windows のみ)**。コンピューティング エミュレーターとストレージ エミュレーターは、アプリケーションをローカルでテストできるようにするためのクラウド サービスおよびデータ管理サービスのローカル エミュレーターです。Azure エミュレーターは Windows 上でのみ動作します。


このリリースでの主要なシナリオは以下のとおりです。

* **Windows**: クラウド サービス --  Web ロールを使用する Django サイトなど
* **Mac/Linux**: IaaS -- VM で必要なものを実行する (Python を利用した Azure サービスの使用)。

## 使用する Python とそのバージョン

利用可能な Python インタープリターにはいくつかの種類があります。以下に例を示します。

* CPython - 最も一般的に使用されている標準的な Python インタープリター
* IronPython - .Net/CLR 上で実行する Python インタープリター
* Jython - JVM 上で実行する Python インタープリター

このリリースでは、**CPython** のみをテストしサポートします。また、バージョン 2.7 以上の使用をお勧めします。**IronPython** のサポートも、近い将来に追加される予定です。

##Python を入手するには

CPython はいくつかの方法で入手できます。

* www.python.org から直接入手
* www.enthought.com や www.ActiveState.com などの信頼できるディストリビューターから入手
*ソースからビルド

特定のニーズがない限り、以下に説明するように、最初の 2 つの入手方法をお勧めします。

##Windows でのインストール

Windows では、メインの Python デベロッパー センターから入手できる [WebPI インストーラー]を使用して、インストールを合理化できます (このインストーラーにより www.python.org から CPython が取得されます)。

**メモ:** Windows Server では、WebPI インストーラーをダウンロードするには、IE ESC 設定の構成が必要になる場合があります。そのためには、[スタート]、[管理ツール]、[サーバー マネージャー] の順にクリックしてから、**[IE ESC の構成]** をクリックし、[オフ] に設定します。

![how-to-install-python-webpi-1](./media/python-how-to-install/how-to-install-python-webpi-1.png)

WebPI インストーラーには、Python Azure アプリケーションに必要なものがすべて用意されており、Django アプリケーション向けの特定のサポートも提供されます。


![how-to-install-Python-webpi-2](./media/python-how-to-install/how-to-install-python-webpi-2.png)
 

完了すると、インストールの選択内容を確認する次の画面が表示されます。


![how-to-install-python-webpi-3](./media/python-how-to-install/how-to-install-python-webpi-3.png)


インストールの完了後、プロンプトで「python」と入力し、インストールが問題なく実行されたことを確認します。インストールの方法によっては、"path" 変数を (正しいバージョンの) Python が検索されるように設定する必要があります。

![how-to-install-python-win-run](./media/python-how-to-install/how-to-install-python-win-run.png)


このリリースでは Django を使用した Web アプリケーションに焦点を当てていますが、いつでも [Python Package Index (PyPI)][] を参照して、豊富にあるその他のソフトウェアを確認してください。ディストリビューションをインストールすると、Web 開発から技術計算にいたるさまざまなシナリオのうち、興味のある多くのシナリオを実施することができます。

**site-packages** ディレクトリにインストールされている Python パッケージを確認するには、以下のように入力して、そのディレクトリの場所を検索します。

![how-to-install-python-win-site](./media/python-how-to-install/how-to-install-python-win-site.png)

これにより、システムにインストールされているものが一覧表示されます。

インストール後は、既定の場所から Python、Django、クライアント ライブラリを使用することができます。

		C:\Python27\Lib\site-packages\windowsazure
		C:\Python27\Lib\site-packages\django

### Python Tools for Visual Studio

Python Tools for Visual Studio は Microsoft が提供する無料の OSS プラグインで、VS を本格的な Python IDE として使用することができます。

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Python Tools for Visual Studio の使用はオプションですが、使用することをお勧めします。それは、Python Tools for Visual Studio によって、Python と Django についてプロジェクト/ソリューションのサポート、デバッグ、プロファイル、テンプレートの編集と Intellisense、クラウドへのデプロイなどを利用できるためです。このアドインは、既存の VS2010 インストールで動作します。VS2010 がインストールされていない場合、WebPI によって無料の統合シェルと PTVS がインストールされ、**完全に無料**の "VS Python Express" IDE を利用できるようになります。詳細については、「[Python Tools for Visual Studio on CodePlex (CodePlex の Python Tools for Visual Studio)][]」を参照してください。

メモ: PTVS プラグインはサイズが小さいですが、統合シェルによってダウンロードにかかる時間が長くなる場合があります。現時点では、統合シェルのバージョンは "Azure デプロイ プロジェクトの追加" 機能をサポートしていません。

## Windows でのアンインストール

**Azure SDK for Python June 2012** WebPI 製品は、一般的な意味でのアプリケーションではありません。実際には、これは 32 ビット版 Python 2.7、Python 用 Azure クライアント API、Django などの各製品のコレクションです。これらの製品がまとめてバンドルされています。このため、このコレクション自体には、従来のアンインストーラーがありません。インストールされるプログラムを個別に Windows のコントロール パネルから削除する必要があります。

**Azure SDK for Python** を再インストールする場合は、管理者として PowerShell コマンド プロンプトを開き、次のコマンドを実行するだけです。

	rm -force "HKLM:\SOFTWARE\Microsoft\Windows Azure SDK for Python - June 2012"

次に、WebPI を再実行します。

##Linux と MacOS でのインストール

ほとんどの場合、Python は開発コンピューター上に既にインストールされています。次のように入力することで、確認できます。

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

ここでは、Azure Suse VM に CPython 2.7.2 がインストールされており、Azure チュートリアルと Django のサンプルを実行する際にそのまま使用できることがわかります。アップグレードが必要な場合は、OS によって推奨されるパッケージ アップグレードの手順に従います。ただし一般的には、システム上の Python はそのままの状態にしておき (他のコンポーネントはそのバージョン応じてアップグレードが必要になる場合があります)、[Virtualenv][] を利用して新しいバージョンをインストールすることが適しています。

Python Azure クライアント ライブラリをインストールするには、**pip** を使用して、**PyPI** からクライアント ライブラリを取得します。

	curl https://raw.github.com/pypa/pip/master/contrib/get-pip.py | sudo python
	
前のコマンドでは、ルート パスワードが要求されます (パスワードは非表示)。パスワードを入力して Enter キーを押します。次に、以下を入力します。
	
	sudo /usr/local/bin/pip-2.7 install azure

これで、**site-packages** にインストールされたクライアント ライブラリを確認できます。MacOS では次のように表示されます。

![MacOS の site-packages](./media/python-how-to-install/how-to-install-python-mac-site.png)

Mac/Linux で開発する場合、このリリースでサポートされている 2 つの主要なシナリオがあります。

1. Python クライアント ライブラリを使用して、Azure サービスを利用する

2. Linux VM でアプリケーションを実行する

最初のシナリオでは、REST API 用の Python ラッパーを介して Azure の PaaS 機能 (BLOB ストレージやキューなど) を利用するリッチ Web アプリケーションを作成できます。これらは、Windows、Mac、Linux とまったく同じように動作します。サンプルについては、チュートリアルやハウツー ガイドを参照してください。また、Linux VM からこれらのクライアント ライブラリを使用することもできます。

VM のシナリオでは、選択した Linux VM (Ubuntu、CentOS、Suse) を起動し、必要なものを実行または管理するだけです。たとえば、Windows/Mac/Linux コンピューター上で [IPython](http://ipython.org) REPL/Notebook を実行し、Azure 上で IPython エンジンを実行している Linux または Windows の複数プロセッサの VM をブラウザーで指定することができます。IPython のインストールの詳細については、IPython のチュートリアル参照してください。

Linux VM を設定する方法については、「[Linux Management (Linux の管理)](/ja-jp/manage/linux/)」のセクションを参照してください。

 

##その他のソフトウェアとリソース: 

* [Enthought Python Distribution (Enthought の Python ディストリビューション)][]
* [ActiveState Python Distribution (ActiveState の Python ディストリビューション)][]
* [SciPy - A suite of Scientific Python libraries (SciPy - Python の科学技術計算ライブラリのパッケージ)][]
* [NumPy -  A numerics library for Python (NumPy - Python の数値計算ライブラリ)][]
* [Django Project - A mature web framework/CMS (Django Project - 進化した Web フレームワーク/CMS)][]
* [IPython - an advanced REPL/Notebook for Python (IPython - 高度な Python 用 REPL/Notebook)][]
* [Azure での IPython Notebook][]
* [Python Tools for Visual Studio on CodePlex (CodePlex の Python Tools for Visual Studio)][]
* [Virtualenv][]


[Enthought Python Distribution (Enthought の Python ディストリビューション)]: http://www.enthought.com 
[ActiveState Python Distribution (ActiveState の Python ディストリビューション)]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries (SciPy - Python の科学技術計算ライブラリのパッケージ)]: http://www.scipy.org
[NumPy - A numerics library for Python (NumPy - Python の数値計算ライブラリ)]: http://www.numpy.org
[Django Project - A mature web framework/CMS (Django Project - 進化した Web フレームワーク/CMS)]: http://www.djangoproject.com 
[IPython - an advanced REPL/Notebook for Python (IPython - 高度な Python 用 REPL/Notebook)]: http://ipython.org
[Azure での IPython Notebook]: http://windowsazure.com/ja-jp/documentation/articles/virtual-machines-python-ipython-notebook
[Python Tools for Visual Studio on CodePlex (CodePlex の Python Tools for Visual Studio)]: http://pytools.codeplex.com 
[Python Package Index (PyPI)]: http://pypi.python.org/pypi
[Virtualenv]: http://pypi.python.org/pypi/virtualenv 
[WebPI インストーラー]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Azure ポータルによる Linux VM の設定]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Mac および Linux 用 Azure コマンド ライン ツールの使用方法]: ../../shared/chunks/crossplat-cmd-tools


