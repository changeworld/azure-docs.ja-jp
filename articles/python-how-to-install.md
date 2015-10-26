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
	ms.date="08/31/2015"
	ms.author="huvalo"/>

# Python と SDK のインストール

Python は、Windows でのセットアップが容易であり、Mac および Linux ではプレインストールされています。このガイドでは、インストール方法と Azure で使用するためのコンピューターの準備について説明します。

## Python Azure SDK の機能

Azure SDK for Python には、Azure 向けの Python アプリケーションを開発、デプロイ、管理するためのコンポーネントが用意されています。Azure SDK for Python には次のコンポーネントが用意されています。

* **Azure 用 Python クライアント ライブラリ**。これらのクラス ライブラリには、ストレージやサービス バスなどのAzure の機能にアクセスするためのインターフェイスや、ストレージ アカウントや仮想マシンなどの Azure リソースを管理するためのインターフェイスが用意されています。
* **Azure エミュレーター (Windows のみ)**。コンピューティング エミュレーターとストレージ エミュレーターは、アプリケーションをローカルでテストできるようにするための Cloud Services およびデータ管理サービスのローカル エミュレーターです。Azure エミュレーターは Windows 上でのみ動作します。

## 使用する Python とそのバージョン

利用可能な Python インタープリターにはいくつかの種類があります。以下に例を示します。

* CPython - 最も一般的に使用されている標準的な Python インタープリター
* IronPython - .Net/CLR 上で実行する Python インタープリター
* Jython - JVM 上で実行する Python インタープリター

このリリースでは、Python Azure SDK と Websites や Cloud Services などの Azure サービス向けの **CPython** のみをテストしサポートします。Version 2.7 または 3.4 をお勧めします。

## Python を入手するには

CPython はいくつかの方法で入手できます。

* [www.python.org][] から直接入手
* [www.continuum.io][]、[www.enthought.com][]、[www.activestate.com][] などの信頼できるディストリビューターから入手
* ソースからビルド

特定のニーズがない限り、以下に説明するように、最初の 2 つの入手方法をお勧めします。

## Windows、Linux、および MacOS (クライアント ライブラリのみ) でのインストール

既に Python をインストールしている場合は、pip を使用して、既存の Python 2.7 または Python 3.3 以降の環境ですべてのクライアント ライブラリのバンドルをインストールできます。これにより、[Python Package Index][] (PyPI) からパッケージがダウンロードされます。

Linux および MacOS で `sudo` コマンドを使用する必要がある場合があります。`sudo pip install azure`

	pip install azure

バージョン 1.0.0 以降では、ライブラリは複数のパッケージに分割されています。このため、必要なパッケージまたはバンドルのみをインストールできます。

Azure Storage ランタイム クライアント ライブラリをインストールするには、次の手順に従います。

	pip install azure-storage

Azure Service Bus ランタイム クライアント ライブラリをインストールするには、次の手順に従います。

	pip install azure-servicebus

Azure リソース マネージャー (ARM) クライアント ライブラリをインストールするには、次の手順に従います。

	pip install azure-mgmt

Azure サービス管理 (ARM) クライアント ライブラリをインストールするには、次の手順に従います。

	pip install azure-servicemanagement-legacy


## Windows (Python、Azure エミュレーター、クライアント ライブラリ) でのインストール

Web プラットフォーム インストーラーを使用して、インストールを合理化できます。これらには、[www.python.org][] にある CPython などがあります。

* [Microsoft Azure SDK for Python 2.7][]
* [Microsoft Azure SDK for Python 3.4][]

**注:** Windows Server では、WebPI インストーラーをダウンロードするため、IE ESC 設定の構成が必要になる場合があります。設定するには、[スタート]、[管理ツール]、[サーバー マネージャー]、[ローカル サーバー] の順にクリックしてから、[**IE セキュリティ強化の構成**] をクリックし、[オフ] に設定します。

### Python 2.7

WebPI インストーラーには、Python Azure アプリケーション開発に必要なものがすべて用意されています。

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

インストールの完了後、プロンプトで「`python`」と入力し、インストールが問題なく実行されたことを確認します。インストールの方法によっては、"path" 変数を (正しいバージョンの) Python が検索されるように設定する必要があります。

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

インストール後は、既定の場所から Python とクライアント ライブラリを使用することができます。

		C:\Python27\Lib\site-packages\azure


### Python 3.4

WebPI インストーラーには、Python Azure アプリケーション開発に必要なものがすべて用意されています。

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)

インストールの完了後、プロンプトで「python」と入力し、インストールが問題なく実行されたことを確認します。インストールの方法によっては、"path" 変数を (正しいバージョンの) Python が検索されるように設定する必要があります。

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

インストール後は、既定の場所から Python とクライアント ライブラリを使用することができます。

		C:\Python34\Lib\site-packages\azure

### Windows でのアンインストール

**Azure SDK for Python** WebPI 製品は、一般的な意味でのアプリケーションではありません。実際には、これは 32 ビット版 Python 2.7/3.4、Python 用 Azure クライアント ライブラリなどの各製品のコレクションです。これらの製品がまとめてバンドルされています。このため、このコレクション自体には、従来のアンインストーラーがありません。インストールされるプログラムを個別に Windows のコントロール パネルから削除する必要があります。

**Azure SDK for Python** を再インストールする場合は、管理者として PowerShell コマンド プロンプトを開き、次のコマンドを実行するだけです。

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

次に、WebPI を再実行します。

## その他のパッケージの入手

「[Python Package Index][] (Python パッケージ インデックス) (PyPI)」では、Python ライブラリから豊富なリソースを選べます。ディストリビューションをインストールすると、Web 開発から技術計算にいたるさまざまなシナリオのうち、興味のある多くのシナリオを実施することができます。


## Python Tools for Visual Studio

[Python Tools for Visual Studio][] (PTVS) は Microsoft が提供する無料の OSS プラグインで、VS を本格的な Python IDE として使用することができます。

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

PTVS の使用はオプションですが、使用することをお勧めします。この PTVS によって、Python と Web のプロジェクト/ソリューション サポート、デバッグ、プロファイル、対話型ウィンドウ、テンプレートの編集、Intellisense を利用できるためです。

PTVS では、[Cloud Services][]と [Websites][] へのデプロイをサポートしており、Microsoft Azure へのデプロイも容易になります。

PTVS は、既存の Visual Studio 2013 または 2015 インストールに対応しています。ダウンロードなどの詳細については、[Python Tools for Visual Studio] に関するページをご覧ください。

## Linux や MacOS 向けの Python Azure

Mac/Linux で開発する場合、次の方法がサポートされています。

1. Python クライアント ライブラリを使用して、Azure サービスを利用する

2. Linux VM でアプリケーションを実行する

3. Git を使って Azure Websites を開発して公開する

最初のシナリオでは、Azure REST API 用の Python ラッパーを介して Azure の PaaS 機能 ([BLOB ストレージ][]、[キュー ストレージ][]、[テーブル ストレージ][]など) を利用する優れた Web アプリケーションを作成できます。これらは、Windows、Mac、Linux とまったく同じように動作します。また、ローカルの開発マシンや Linux 仮想マシンからこれらのクライアント ライブラリを使うこともできます。

VM のシナリオでは、選択した Linux VM (Ubuntu、CentOS、Suse) を起動し、必要なものを実行または管理するだけです。たとえば、Windows/Mac/Linux コンピューター上で [IPython][] REPL/Notebook を実行し、Azure 上で IPython エンジンを実行している Linux または Windows の複数プロセッサの VM をブラウザーで指定することができます。詳細については、「[Azure での IPython Notebook][]」チュートリアルをご覧ください。

Linux 仮想マシンのセットアップの詳細については、「[Linux を実行する仮想マシンの作成][]」チュートリアルをご覧ください。

Git のデプロイを使って Python Web アプリケーションを開発できます。また、どのオペレーティング システムからでも Azure Web サイトを公開できます。ご利用のリポジトリを Azure にプッシュする場合、自動的に仮想環境が作られ、pip によって必要なパッケージがインストールされます。

Azure Websites を開発して公開する詳細については、「[Creating Websites with Django (Django を使った Websites の作成)][]」、「[Creating Websites with Bottle (Bottle を使った Websites の作成)][]」、「[Flask を使った Websites の作成][]」をご覧ください。さまざまな WSGI 互換フレームワークの使用についての一般的な情報は、「[Azure Websites での Python の構成][]」をご覧ください。


## その他のソフトウェアとリソース:

* [Continuum Analytics Python Distribution][] (Continuum Analytics Python Distribution ディストリビューション)
* [Enthought Python Distribution][] (Enthought の Python ディストリビューション)
* [ActiveState Python Distribution][] (ActiveState の Python ディストリビューション)
* [SciPy - A suite of Scientific Python libraries][] (SciPy - Python の科学技術計算ライブラリのパッケージ)
* [NumPy - A numerics library for Python][] (NumPy - Python の数値計算ライブラリ)
* [Django Project - A mature web framework/CMS][] (Django Project - 進化した Web フレームワーク/CMS)
* [IPython - an advanced REPL/Notebook for Python][] (IPython - 高度な Python 用 REPL/Notebook)
* [Azure での IPython Notebook][]
* [Python Tools for Visual Studio on GitHub][] に関するページ
* [Python デベロッパー センター](/develop/python/)

[Continuum Analytics Python Distribution]: http://continuum.io
[Enthought Python Distribution]: http://www.enthought.com
[ActiveState Python Distribution]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries]: http://www.scipy.org
[NumPy - A numerics library for Python]: http://www.numpy.org
[Django Project - A mature web framework/CMS]: http://www.djangoproject.com
[IPython - an advanced REPL/Notebook for Python]: http://ipython.org
[IPython]: http://ipython.org
[Azure での IPython Notebook]: virtual-machines-python-ipython-notebook.md
[Cloud Services]: cloud-services-python-ptvs.md
[Websites]: web-sites-python-ptvs-django-mysql.md
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio on GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Linux を実行する仮想マシンの作成]: virtual-machines-linux-tutorial.md
[Creating Websites with Django (Django を使った Websites の作成)]: web-sites-python-create-deploy-django-app.md
[Creating Websites with Bottle (Bottle を使った Websites の作成)]: web-sites-python-create-deploy-bottle-app.md
[Flask を使った Websites の作成]: web-sites-python-create-deploy-flask-app.md
[Azure Websites での Python の構成]: web-sites-python-configure.md
[テーブル ストレージ]: storage-python-how-to-use-table-storage.md
[キュー ストレージ]: storage-python-how-to-use-queue-storage.md
[BLOB ストレージ]: storage-python-how-to-use-blob-storage.md

<!---HONumber=Oct15_HO3-->