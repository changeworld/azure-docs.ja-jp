<properties
	pageTitle="Azure コマンド ライン インターフェイスのインストール | Microsoft Azure"
	description="Mac、Linux、および Windows に Azure コマンド ライン インターフェイス (CLI) をインストールして Azure サービスの利用を開始する"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="danlep"/>
    
# Azure CLI のインストール

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli-install.md)

Azure コマンド ライン インターフェイス (Azure CLI) を簡単にインストールすると、コマンド ライン シェルからオープン ソースのコマンドを使って Microsoft Azure 上のリソースを作成したり管理したりすることができます。最新バージョンをインストールする方法はいくつかあります。

* npm パッケージからインストールする (Node.js と npm が必要)
* さまざまなオペレーティング システム用に提供されたインストーラー パッケージのいずれかを使用する
* Docker ホストのコンテナーとして Azure CLI をインストールする
    
その他のオプションと背景については、[GitHub](https://github.com/azure/azure-xplat-cli) のプロジェクト リポジトリを参照してください。

Azure CLI をインストールした後、[Azure サブスクリプションに接続](xplat-cli-connect.md)し、コマンド ライン インターフェイス (Bash、ターミナル、コマンド プロンプトなど) から **azure** コマンドを実行して、Azure リソースを操作します。



## 方法 1.npm パッケージのインストール

CLI を npm パッケージからインストールするには、ご使用のシステムに最新の Node.js と npm がインストールされている必要があります。そのうえで、次のコマンドを使用して Azure CLI パッケージをインストールします (Linux ディストリビューションの場合、__npm__ コマンドを正常に実行するには **sudo** の使用が必要になる場合があります)。

	npm install azure-cli -g

> [AZURE.NOTE]ご使用のオペレーティング システム用に Node.js と npm をインストールまたは更新する必要がある場合は、[Nodejs.org](https://nodejs.org/en/download/package-manager/) のドキュメントを参照してください。最新の Node.js LTS バージョン (4.x) をインストールされることをお勧めします。以前のバージョンを使用すると、インストール エラーが発生する場合があります。npm について詳しくは、[npmjs.com](https://www.npmjs.com/) をご覧ください。

## 方法 2.インストーラーの使用

次の CLI インストーラー パッケージをダウンロードして利用することもできます。


* [Mac OS X インストーラー][mac-installer]

* [Windows インストーラー][windows-installer]

* [Linux tar ファイル][linux-installer] \(Node.js と npm が必要) - `sudo npm install -g <path to downloaded tar file>` を実行してインストールします。


## 方法 3.Docker コンテナーの使用

Docker ホストを設定すると、Docker コンテナーで Azure CLI を実行できるようになります。次のコマンドを実行します。

```
docker run -it microsoft/azure-cli
```

Docker の詳細については、[docker.com](https://docs.docker.com/engine/understanding-docker/) をご覧ください。

## Azure CLI コマンドの実行
Azure CLI をインストールした後、コマンド ライン ユーザー インターフェイス (Bash、ターミナル、コマンド プロンプトなど) から **azure** コマンドを実行します。たとえば、ヘルプ コマンドを実行するには、次のように入力します。

```
azure help
```
> [AZURE.NOTE]一部の Linux ディストリビューションでは、`/usr/bin/env: ‘node’: No such file or directory` のようなエラーが表示されることがあります。このエラーは、/usr/bin/nodejs に最近インストールされた nodejs が原因で発生します。このエラーを修正するには、次のコマンドを実行して /usr/bin/node へのシンボリック リンクを作成します。

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

インストールした Azure CLI のバージョンを確認するには、次のように入力します。

```
azure --version
```

これで準備が整いました。 すべての CLI コマンドにアクセスして独自のリソースを操作するには、[Azure CLI から Azure サブスクリプションに接続](xplat-cli-connect.md)します。

>[AZURE.NOTE] Azure CLI を初めて使用する場合、Microsoft が CLI の使用状況についての情報を収集することを許可するかどうかをたずねるメッセージが表示されます。参加は任意です。参加した後でも、`azure telemetry --disable` を実行するといつでも停止できます。参加を有効にするには、任意のタイミングで `azure telemetry --enable` を実行します。


## CLI の更新

マイクロソフトは、Azure CLI の更新バージョンを頻繁にリリースしています。ご使用のオペレーティング システム用のインストーラーを使用するか、最新の Docker コンテナーを実行して、CLI を再インストールします。または、最新の Node.js と npm がインストールされている場合は、次のコマンドを入力して更新します (Linux ディストリビューションでは、**sudo** の使用が必要になる場合があります)。

```
npm update -g azure-cli
```

## タブ補完の有効化

Mac と Linux では、CLI コマンドのタブ補完がサポートされます。

zsh で有効化する場合は、次のコマンドを実行します。

```
echo '. <(azure --completion)' >> .zshrc
```

bash で有効化する場合は、次のコマンドを実行します。

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## 次のステップ 

* [CLI から Azure サブスクリプションへの接続](xplat-cli-connect.md)を行い、Azure リソースを作成および管理します。

* Azure CLI の詳細、ソース コードのダウンロード、問題のレポート、プロジェクトへの協力については、[GitHub リポジトリの Azure CLI](https://github.com/azure/azure-xplat-cli) のページを参照してください。

* Azure CLI または Azure の使用に関してご不明な点がある場合は、[Azure のフォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=azurescripting)をご覧ください。

* Linux システムでは、[ソース](http://aka.ms/linux-azure-cli)から構築することで Azure CLI をインストールすることもできます。ソースからの構築の詳細については、ソース アーカイブにある INSTALL ファイルを参照してください。

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0907_2016-->