<properties 
	pageTitle="Python と SDK のインストール - Azure" 
	description="Azure で使用するための Python と SDK のインストール方法について説明します。" 
	services="" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>




# Python と SDK のインストール

Python は、Windows でのセットアップが容易であり、Mac および Linux ではプレインストールされています。このガイドでは、インストール方法と Azure で使用するためのコンピューターの準備について説明します。以下の説明が記載されています。

* Python Azure SDK の機能
* 使用する Python とそのバージョン
* Windows でのインストール
* Mac と Linux でのインストール

## Python Azure SDK の機能

Azure SDK for Python には、Azure 向けの Python アプリケーションを開発、デプロイ、管理するためのコンポーネントが用意されています。Azure SDK for Python には次のコンポーネントが用意されています。

* **Azure 用 Python クライアント ライブラリ**です。これらのクラス ライブラリは、ストレージおよび service bus などの Azure の機能にアクセスして、ストレージ アカウント、仮想マシンなどの Azure リソースを管理するためのインターフェイスを提供します。
* **Azure エミュレーター (Windows のみ)**です。コンピューティング エミュレーターとストレージ エミュレーターは、アプリケーションをローカルでテストできるようにするためのクラウド サービスおよびデータ管理サービスのローカル エミュレーターです。Azure エミュレーターは Windows 上でのみ動作します。

## 使用する Python とそのバージョン

利用可能な Python インタープリターにはいくつかの種類があります。以下に例を示します。

* CPython - 標準的な頻繁に使用する Python インタープリター
* IronPython - .NET/CLR 上で実行する Python インタープリター
* Jython - JVM 上で実行する Python インタープリター

のみ**CPython**をテストし、web サイトなどの Python Azure SDK および Azure のサービスやクラウド サービスではサポートされています。Version 2.7 または 3.4 をお勧めします。

## Python を入手するには

CPython はいくつかの方法で入手できます。

* 直接[www.python.org][]
* などのよく知られたディストリビューションから[www.continuum.io][]、 [www.enthought.com][]または[www.activestate.com][]
* ソースからビルド

特定のニーズがない限り、以下に説明するように、最初の 2 つの入手方法をお勧めします。

## Windows でのインストール

Windows では、Web プラットフォーム インストーラーを使用して、インストールを合理化できます。CPython が含まれます[www.python.org][]です。

* [Microsoft Azure SDK for Python 2.7][]
* [Microsoft Azure SDK for Python 3.4][]

**注:**で Windows Server、WebPI インストーラーをダウンロードするためにしなければならない場合 IE ESC 設定の構成 (開始、[管理ツールとサーバー マネージャー/ローカル サーバー] をクリックし、 **IE セキュリティ強化の構成**、設定をオフに)


### Python 2.7

WebPI インストーラーには、Python Azure アプリケーション開発に必要なものがすべて用意されています。

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

完了すると、インストールの選択内容を確認する次の画面が表示されます。

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

インストールが完了した後に入力 `python`ことを確認プロンプトで順調に進みました。インストールの方法によっては、"path" 変数を (正しいバージョンの) Python が検索されるように設定する必要があります。

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

インストール後は、既定の場所から Python とクライアント ライブラリを使用することができます。

		C:\Python27\Lib\site-packages\azure


### Python 3.4

WebPI インストーラーには、Python Azure アプリケーション開発に必要なものがすべて用意されています。

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)
 
完了すると、インストールの選択内容を確認する次の画面が表示されます。

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

インストールの完了後、プロンプトで「python」と入力し、インストールが問題なく実行されたことを確認します。インストールの方法によっては、"path" 変数を (正しいバージョンの) Python が検索されるように設定する必要があります。

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

インストール後は、既定の場所から Python とクライアント ライブラリを使用することができます。

		C:\Python34\Lib\site-packages\azure


### その他のパッケージの入手

[Python Package Index][] (PyPI) が、豊富に Python ライブラリです。ディストリビューションをインストールすると、Web 開発から技術計算にいたるさまざまなシナリオのうち、興味のある多くのシナリオを実施することができます。


### Python Tools for Visual Studio

[Python Tools for Visual Studio][] (PTVS) は、VS を本格的な Python IDE にする Microsoft から無料の OSS プラグイン。

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

PTVS を使用しては省略可能では、Python と Web プロジェクト/ソリューションのサポート、デバッグ、プロファイリング、対話型ウィンドウ、テンプレートの編集と Intellisense することをお勧めします。

PTVS も簡単に配置するためのサポートを Microsoft Azure に展開する[クラウド サービス][]と[web サイト][]です。

PTVS は、既存の Visual Studio 2010、2012、または 2013 インストールに対応しています。ドキュメント、ダウンロード、およびディスカッション、以下を参照してください。 [Python Tools for Visual Studio CodePlex で][]です。  

## Windows でのアンインストール

**Azure SDK for Python** WebPI 製品は、一般的な意味が実際には、個別製品 3.4/32 ビット Python 2.7 などがまとめてバンドルされている Python 用 Azure クライアント ライブラリのコレクション内のアプリケーションでできません。このため、このコレクション自体には、従来のアンインストーラーがありません。インストールされるプログラムを個別に Windows のコントロール パネルから削除する必要があります。  

**Azure SDK for Python** を再インストールする場合は、管理者として PowerShell コマンド プロンプトを開き、次のコマンドを実行するだけです。

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

次に、WebPI を再実行します。

## Linux と MacOS でのインストール

Python は開発用コンピューターに既にインストールされている可能性が最も高いです。次のように入力することで、確認できます。

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

ここでは、Azure で実行されているこの Ubuntu Server 14.04 LTS VM に CPython 2.7.6 がインストールされていることがわかります。アップグレードが必要な場合は、OS によって推奨されるパッケージ アップグレードの手順に従います。

Python Azure クライアント ライブラリをインストールする次のように使用します。 **pip**から取得する**PyPI**です。

ない場合は、 **pip**をインストールするには、このコマンドを使用してインストールします。

	curl https://bootstrap.pypa.io/get-pip.py | sudo python
	
前のコマンドでは、ルート パスワードが要求されます (パスワードは非表示)。パスワードを入力して Enter キーを押します。  

次に、使用、 `pip` Python Azure クライアント ライブラリをインストールするコマンド。
	
	sudo pip install azure

これで、**site-packages** にインストールされたクライアント ライブラリを確認できます。MacOS では次のように表示されます。

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

## Python Linux と MacOS の Azure のシナリオ

Linux または MacOS では、次の表がサポートされている Azure の主要なシナリオです。

1. Python クライアント ライブラリを使用して、Azure サービスを利用する

2. Linux VM でアプリケーションを実行する

3. 開発と Git を使用して Azure の web サイトへの発行

最初のシナリオなどの Azure の PaaS 機能を利用するリッチ web アプリケーションを作成することができます[blob ストレージ][]、[キュー ストレージ][]、[テーブル ストレージ][]Azure REST API のための Python ラッパーを介してなどです。これらは、Windows、Mac、Linux とまったく同じように動作します。また、ローカル開発用コンピューターまたは Azure で実行されている Linux VM からこれらのクライアント ライブラリを使用することができます。

VM のシナリオでは、選択した Linux VM (Ubuntu、CentOS、Suse) を起動し、必要なものを実行または管理するだけです。たとえば、Windows/Mac/Linux コンピューター上で [IPython][] REPL/Notebook を実行し、Azure 上で IPython エンジンを実行している Linux または Windows の複数プロセッサの VM をブラウザーで指定することができます。参照してください、 [Azure での IPython Notebook][] 」チュートリアルの詳細をします。

Linux VM をセットアップする方法の詳細については、次を参照してください、[実行されている Linux の仮想マシンを作成する][]チュートリアルです。

Git の展開を使用して Python web アプリケーションを開発および任意のオペレーティング システムからの Azure の web サイトに発行できます。Azure に、リポジトリをプッシュするときに、仮想環境が自動的に作成し、pip が、必要なパッケージをインストールします。

開発と Azure の web サイトの公開の詳細については、ためのチュートリアルを参照してください。 [Django を使用した web サイトを作成する][]、 [Bottle での web サイトを作成する][]と[Flask での web サイトを作成する][]です。どの WSGI 準拠のフレームワークを使用して一般的なについては、次を参照してください。 [Azure の web サイトでの Python の構成][]です。


## その他のソフトウェアとリソース:

* [連続性の分析の Python の配布][]
* [Enthought の Python ディストリビューション][]
* [ActiveState の Python ディストリビューション][]
* [SciPy - Python の科学ライブラリのスイート][]
* [NumPy - Python の数値ライブラリ][]
* [Django プロジェクトで、成熟した web フレームワーク/CMS][]
* [IPython -、Python の高度な Repl/notebook][]
* [Azure での IPython Notebook][]
* [Python Tools for CodePlex での Visual Studio][]


[連続性の分析の Python の配布]: http://continuum.io
[Enthought の Python ディストリビューション]: http://www.enthought.com
[ActiveState の Python ディストリビューション]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - Python の科学ライブラリのスイート]: http://www.scipy.org
[NumPy - Python の数値ライブラリ]: http://www.numpy.org
[Django プロジェクトで、成熟した web フレームワーク/CMS]: http://www.djangoproject.com 
[IPython -、Python の高度な Repl/notebook]: http://ipython.org
[IPython]: http://ipython.org
[Azure での IPython Notebook]: ../virtual-machines-python-ipython-notebook/
[クラウド サービス]: ../cloud-services-python-ptvs/
[Web サイト]: ../web-sites-python-ptvs-django-mysql
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for CodePlex での Visual Studio]: http://pytools.codeplex.com 
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Azure ポータルによる Linux VM を設定します。]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Mac および Linux 用 Azure コマンド ライン ツールを使用する方法]: ../../shared/chunks/crossplat-cmd-tools
[Linux を実行する仮想マシンを作成します。]: ../virtual-machines-linux-tutorial/
[Django を使用した web サイトを作成します。]: ../web-sites-python-create-deploy-django-app/
[Bottle を web サイトを作成します。]: ../web-sites-python-create-deploy-bottle-app/
[Flask を web サイトを作成します。]: ../web-sites-python-create-deploy-flask-app/
[Azure の web サイトでの Python の構成]: ../web-sites-python-configure/
[テーブル ストレージ]: ../storage-python-how-to-use-table-storage/
[キュー ストレージ]: ../storage-python-how-to-use-queue-storage/
[BLOB ストレージ]: ../storage-python-how-to-use-blob-storage/

<!--HONumber=47-->
