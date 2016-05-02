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
	ms.date="04/07/2016"
	ms.author="danlep"/>

# Azure CLI のインストール

Azure コマンド ライン インターフェイス (Azure CLI) を簡単にインストールすると、コマンド ライン シェルからオープン ソースのコマンドを使って Microsoft Azure 上のリソースを作成したり管理したりすることができます。インストールにはいくつかの方法があります。各種オペレーティング システム用に提供されているいずれかのインストーラー パッケージを使用するか、npm パッケージを使用するか、Docker ホストのコンテナーとして Azure CLI をインストールします。その他のオプションとバックグラウンドについては、[GitHub](https://github.com/azure/azure-xplat-cli) のプロジェクト リポジトリを参照してください。


Azure CLI をインストールした後、[Azure サブスクリプションに接続](xplat-cli-connect.md)し、コマンド ライン インターフェイス (Bash、ターミナル、コマンド プロンプトなど) から **azure** コマンドを実行すると、Azure のリソースを操作できるようになります。


## インストーラーの使用

次のインストーラー パッケージを使用できます。

* [Windows インストーラー][windows-installer]

* [OS X インストーラー](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Linux インストーラー][linux-installer]


## npm パッケージのインストール

または、最新の Node.js と npm が既にシステムにインストールされている場合は、次のコマンドを実行して Azure CLI パッケージをインストールします (Linux ディストリビューションの場合、__npm__ コマンドを正常に実行するには **sudo** を使用することが必要になる場合があります)。

	npm install azure-cli -g

> [AZURE.NOTE]ご利用のオペレーティング システム用に Node.js と npm をインストールまたは更新する必要がある場合は、[Nodejs.org](https://nodejs.org/en/download/package-manager/) のドキュメントをご覧ください。最新の Node.js LTS バージョン (4.x) をインストールされることをお勧めします。以前のバージョンを使用すると、インストール エラーが発生する場合があります。


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

マイクロソフトは、Azure CLI の更新バージョンを頻繁にリリースしています。ご利用のオペレーティング システム用のインストーラーを使用して CLI を再インストールするか、最新の Node.js と npm がインストールされている場合は、次のコマンドを入力して更新してください (Linux ディストリビューションでは、**sudo** の使用が必要になる場合があります)。

```
npm update -g azure-cli
```

## 次のステップ 

* [CLI から Azure サブスクリプションへの接続](xplat-cli-connect.md)を行い、Azure リソースを作成および管理します。

* Azure CLI の詳細、ソース コードのダウンロード、問題のレポート、プロジェクトへの協力については、[GitHub リポジトリの Azure CLI](https://github.com/azure/azure-xplat-cli) のページを参照してください。

* Azure CLI または Azure の使用に関してご不明な点がある場合は、[Azure のフォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=azurescripting)をご覧ください。

* Linux システムでは、[ソース](http://aka.ms/linux-azure-cli)から構築することで Azure CLI をインストールすることもできます。ソースからの構築の詳細については、ソース アーカイブにある INSTALL ファイルを参照してください。

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=windowsazurexplatcli&mode=new
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0420_2016-->