
<properties
		pageTitle="VM 拡張機能による Linux VM の監視 |Microsoft Azure"
		description="Linux の診断拡張機能を使用して Azure 内の Linux VM のパフォーマンスと診断データを監視する方法を説明します。"
		services="virtual-machines-linux"
		documentationCenter=""
  		authors="NingKuang"
		manager="timlt"
		editor=""
  		tags="azure-service-management"/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="12/15/2015"
		ms.author="Ning"/>


# Linux の診断拡張機能を使用した Linux VM のパフォーマンスと診断データの監視

## はじめに

Linux 診断拡張機能は、Microsoft Azure で実行されている Linux VM を監視するのに役立ちます。次の機能があります。

- Linux VM のシステム パフォーマンス、診断、syslog データを収集して、ユーザーのストレージ テーブルにアップロードします。
- ユーザーは収集およびアップロードされるデータ メトリックをカスタマイズできます。
- ユーザーは指定したログ ファイルを指定したストレージ テーブルにアップロードできます。

バージョン 2.0 では、以下のデータが含まれます。

- システム、セキュリティ、アプリケーション ログを含む、すべての Linux Rsyslog ログ。
- この[ドキュメント](https://scx.codeplex.com/wikipage?title=xplatproviders")で指定されているすべてのシステム データ。
- ユーザーが指定したログ ファイル。

この拡張機能は、クラシック モデルとリソース マネージャーのデプロイ モデルの両方で機能します。


## 拡張機能を有効にする方法
拡張機能は、[Azure ポータル](https://ms.portal.azure.com/#)、Azure PowerShell、または Azure CLI スクリプトから有効にできます。

Azure ポータルから直接、システム データおよびパフォーマンス データを表示および構成するには、こちらの[手順](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/ "Windows ブログの URL"/)に従ってください。


この記事では、Azure CLI コマンドを使用して拡張機能を有効化および構成する方法を説明します。直接ストレージ テーブルからデータを読み取って表示できます。ここで説明する構成方法は、Azure ポータルでは機能しないことに注意してください。Azure ポータルからシステムとパフォーマンス データを直接表示および構成するには、前の段落で説明したように、この拡張機能を Azure ポータルから有効にする必要があります。


## 前提条件
- Microsoft Azure Linux エージェント バージョン 2.0.6 またはそれ以降。大部分の Azure VM Linux ギャラリー イメージにはバージョン 2.0.6 以降が含まれています。**WAAgent -version** を実行して、VM にインストールされているバージョンを確認できます。VM が 2.0.6 より前のバージョンを実行している場合は、[こちらの説明](https://github.com/Azure/WALinuxAgent "説明")に従って更新できます。
- [Azure CLI](../xplat-cli-install.md)。[このガイダンス](../xplat-cli-install.md)に従って、コンピューターに Azure CLI 環境をセットアップしてください。Azure CLI をインストールすると、コマンド ライン インターフェイス (Bash、Terminal、Command プロンプト) から **azure** コマンドを使用して Azure CLI コマンドにアクセスできるようになります。たとえば、詳細な使用法を確認するには **azure vm extension set --help** を、Azure にログインするには **azure login** を、Azure にあるすべての仮想マシンを一覧表示するには **azure vm list** を実行します。
- データを格納するためのストレージ アカウント。データをストレージにアップロードするには、事前にストレージ アカウント名とアクセス キーを作成しておく必要があります。


## Azure CLI コマンドを使用して Linux 診断拡張機能を有効にする

###  シナリオ 1. 既定のデータ セットで拡張機能を有効にする
バージョン 2.0 以降では、以下のデータが既定で収集されます。

- すべての Rsyslog 情報 (システム ログ、セキュリティ ログ、アプリケーション ログ)。  
- 基本システム データのコア セット。完全なデータ セットについては、この[ドキュメント](https://scx.codeplex.com/wikipage?title=xplatproviders)を参照してください。追加のデータを有効にする場合は、シナリオ 2 および 3 の手順を続行してください。

手順 1.次の内容を含む PrivateConfig.json という名前のファイルを作成します。

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account"
	}

手順 2.**azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json** を実行します。


###   シナリオ 2. パフォーマンス モニターのメトリックをカスタマイズする  
このセクションでは、パフォーマンスと診断データのテーブルをカスタマイズする方法について説明します。

手順 1.上のシナリオ 1 で説明した内容を含む PrivateConfig.json という名前のファイルを作成します。さらに、次の例に示されている PrivateConfig.json という名前のファイルを作成します。収集する特定のデータを指定します。

サポートされているすべてのプロバイダーと変数については、この[ドキュメント](https://scx.codeplex.com/wikipage?title=xplatproviders)を参照してください。スクリプトにさらにクエリを追加することにより、複数のクエリを使用して複数のテーブルにそれを格納することができます。

Rsyslog データは既定で常に収集されます。

	{
      	"perfCfg":[
           	{"query":"SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation","table":"LinuxMemory"
           	}
          ]
	}


手順 2.**azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json** を実行します。


###   シナリオ 3. 独自のログ ファイルをアップロードする
このセクションでは、ストレージ アカウントに特定のログ ファイルを収集およびアップロードする方法について説明します。ログ ファイルのパスを指定し、ログを格納するテーブル名を指定する必要があります。複数の file/table エントリをスクリプトに追加することで、複数のログ ファイルを使用できます。

手順 1.シナリオ 1 で説明した内容を含む PrivateConfig.json という名前のファイルを作成します。次の内容を含む PrivateConfig.json という名前の別のファイルを作成します。

	{
      	"fileCfg":[
           	{"file":"/var/log/mysql.err",
             "table":"mysqlerr"
           	}
          ]
	}


手順 2.**azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json** を実行します。


###   シナリオ 4. 拡張機能によるログ収集を停止する
このセクションでは、拡張機能によるログ収集を停止する方法について説明します。この再構成を使用しても、監視エージェント プロセスはまだ稼働していることに注意してください。監視エージェント プロセスを完全に停止するには、現在の拡張機能をアンインストールする必要があります。将来的には、拡張機能全体のアンインストールを必要とせずに、拡張機能を無効にする (監視エージェント プロセスも完全に停止する) 構成プロパティを追加する予定です。

手順 1.シナリオ 1 で説明した内容を含む PrivateConfig.json という名前のファイルを作成します。次の内容を含む PrivateConfig.json という名前の別のファイルを作成します。

	{
     	"perfCfg":[],
     	"enableSyslog":"false"
	}


手順 2.**azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json** を実行します。


## データを確認する
パフォーマンスと診断のデータが Azure Storage テーブルに保存されます。[この記事](../storage/storage-ruby-how-to-use-table-storage.md)を確認して、Azure CLI スクリプトを使用してストレージ テーブル内のデータにアクセスする方法を学習します。

さらに、次の UI ツールを使用してデータにアクセスすることもできます。

1.	Visual Studio のサーバー エクスプローラーを使用します。ストレージ アカウントに移動します。VM を約 5 分間稼働した後、既定のテーブル「LinuxCpu」、「LinuxDisk」、「LinuxMemory」、および「Linuxsyslog」を確認する必要があります。データを表示するには、テーブル名をダブルクリックします。
2.	[Azure ストレージ エクスプローラー](https://azurestorageexplorer.codeplex.com/ "Azure ストレージ エクスプローラー")を使用してデータにアクセスします。

![image](./media/virtual-machines-linux-classic-diagnostic-extension/no1.png)

シナリオ 2 および 3 で指定した fileCfg または perfCfg を有効にした場合、前述のツールを使用して既定以外のデータを表示することもできます。



## 既知の問題
- バージョン 2.0 の場合、Rsyslog 情報およびユーザー指定のログ ファイルには、スクリプトからのみアクセスできます。

<!---HONumber=AcomDC_0525_2016-->