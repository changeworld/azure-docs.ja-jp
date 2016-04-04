<properties
	pageTitle="Azure コマンド ライン インターフェイスのインストール | Microsoft Azure"
	description="Mac、Linux、および Windows に Azure CLI をインストールして Azure サービスの利用を開始する"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="danlep"/>

# Azure CLI のインストール

Azure コマンド ライン インターフェイス (Azure CLI) を簡単にインストールすると、コマンド ライン シェルからオープン ソースのコマンドを使って Microsoft Azure 上のリソースを作成したり管理したりすることができます。いくつかの方法があります。提供されているいずれかのインストーラー パッケージを使用してオペレーティング システムに Azure CLI をインストールするか、Node.js と **npm** を使って CLI をインストールするか、Docker ホストのコンテナーとして Azure CLI をインストールします。その他のオプションとバックグラウンドについては、[GitHub](https://github.com/azure/azure-xplat-cli) のプロジェクト リポジトリを参照してください。


Azure CLI をインストールした後、[Azure サブスクリプションに接続](xplat-cli-connect.md)し、コマンド ライン インターフェイス (Bash、ターミナル、コマンド プロンプトなど) から **azure** コマンドを実行すると、Azure のリソースを操作できるようになります。




## インストーラーの使用

次のインストーラー パッケージを使用できます。

* [Windows インストーラー][windows-installer]

* [OS X インストーラー](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Linux インストーラー][linux-installer]


## Node.js と npm のインストールと使用

また、Node.js が既にシステムにインストールされている場合は、次のコマンドを使用して Azure CLI をインストールします。

	npm install azure-cli -g

> [AZURE.NOTE] Linux ディストリビューションの場合、__npm__ コマンドを正常に実行するには `sudo` を使用することが必要になる場合があります。

### [dpkg](http://en.wikipedia.org/wiki/Dpkg) パッケージ管理を使用する Linux ディストリビューションへの Node.js と npm のインストール

このようなディストリビューションでは、[Advanced Packaging Tool (APT)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) や `.deb` パッケージ形式に基づく他のツールの使用が最も一般的です。例としては、Ubuntu や Debian が該当します。

このようなディストリビューションの中でも新しいものではほとんどのケースで、適切に構成された **npm** ツールを取得して Azure CLI をインストールするために、**nodejs-legacy** をインストールする必要があります。次のコードは、**npm** を Ubuntu 14.04 に適切にインストールするコマンドです。

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Ubuntu 12.04 など以前のディストリビューションでは、現在の Node.js のバイナリ ディストリビューションのインストールが必要です。次のコードは、**curl** をインストールして使用することでこれを行う方法を示しています。

>[AZURE.NOTE] ここに示したコマンドは、[こちら](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager)のインストール手順から引用されたものです。ただし、**sudo** をパイプ ターゲットとして使う場合は、インストールするスクリプトを常に確認し、適切に動作することを検証した後で、**sudo** で実行してください。強力な機能だけに、慎重に使用することが重要です。

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager) パッケージ管理を使用する Linux ディストリビューションへの Node.js と npm のインストール

RPM ベースのディストリビューションで Node.js をインストールするには、EPEL リポジトリを有効にする必要があります。次のコードは、CentOS 7 上でのインストールのベスト プラクティスを示しています (下の最初の行の '-' (ハイフン) が重要ですので注意してください)。

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Windows および Mac OS X への Node.js および npm のインストール

Node.js と npm を Windows および OS X にインストールするには、[Nodejs.org](https://nodejs.org/en/download/) で提供されているインストーラーを使用します。インストールを完了するために、コンピューターを再起動する必要がある場合があります。Node.js と npm が正しくインストールされていることを確認するには、コマンド プロンプトまたはターミナル ウィンドウを開き、次のように入力します。

	npm -v

インストールされている npm のバージョンが表示された場合は、次のコマンドを使用して Azure CLI をインストールします。

	npm install -g azure-cli

インストールの最後に、次のような情報が表示されます。

	azure-cli@0.8.0 ..\node_modules\azure-cli
	|-- easy-table@0.0.1
	|-- eyes@0.1.8
	|-- xmlbuilder@0.4.2
	|-- colors@0.6.1
	|-- node-uuid@1.2.0
	|-- async@0.2.7
	|-- underscore@1.4.4
	|-- tunnel@0.0.2
	|-- omelette@0.1.0
	|-- github@0.1.6
	|-- commander@1.0.4 (keypress@0.1.0)
	|-- xml2js@0.1.14 (sax@0.5.4)
	|-- streamline@0.4.5
	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

>[AZURE.NOTE] Linux システムでは、[ソース](http://go.microsoft.com/fwlink/?linkid=253472)から構築することで Azure CLI をインストールすることもできます。ソースからの構築の詳細については、アーカイブにある INSTALL ファイルを参照してください。

## Docker コンテナーの使用

Docker ホストで、次のコマンドを実行します。

```
docker run -it microsoft/azure-cli
```

## Azure CLI コマンドの実行
Azure CLI をインストールすると、コマンド ライン ユーザー インターフェイス (Bash、ターミナル、コマンド プロンプトなど) から **azure** コマンドを実行できるようになります。たとえば、ヘルプ コマンドを実行するには、次のように入力します。

```
azure help
```

インストールした Azure CLI のバージョンを確認するには、次のように入力します。

```
azure --version
```

これで準備が整いました。 CLI コマンドを使って必要なリソースを操作するためには、[Azure CLI から Azure サブスクリプションに接続](xplat-cli-connect.md)します。

## CLI の更新

マイクロソフトは、Azure CLI の更新バージョンを頻繁にリリースしています。ご利用のオペレーティング システム用のインストーラーを使用して CLI を再インストールするか、Node.js と npm がインストールされている場合は、次のコマンドを入力して更新してください (Linux ディストリビューションでは、**sudo** の使用が必要になる場合があります)。

```
npm update -g azure-cli
```

## その他のリソース

* [Azure Resource Manager (arm) モードでの Azure CLI コマンド][cliarm]

* [Azure サービス管理 (asm) モードでの Azure CLI コマンド][cliasm]

* Azure CLI の詳細、ソース コードのダウンロード、問題のレポート、プロジェクトへの協力については、[GitHub リポジトリの Azure CLI](https://github.com/azure/azure-xplat-cli) のページを参照してください。

* Azure CLI または Azure の使用に関してご不明な点がある場合は、[Azure のフォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=azurescripting)をご覧ください。



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0323_2016-->