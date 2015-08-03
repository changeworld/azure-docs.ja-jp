<properties
	pageTitle="Mac、Linux、および Windows 用 Azure CLI での VM 操作に使用するリソース マネージャーおよびサービス管理コマンドの対応"
	description="リソース マネージャー モードとサービス管理モードで Azure 仮想マシンを作成および管理するための、Azure CLI と同等のコマンドを紹介します。"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="04/28/2015"
	ms.author="danlep"/>


# Mac、Linux、および Windows 用 Azure CLI での VM 操作に使用するリソース マネージャーおよびサービス管理コマンドの対応
この記事では、サービス管理 (asm) モードとリソース マネージャー (arm) モードで Azure VM を作成および管理するための Microsoft Azure コマンド ライン インターフェイス (Azure CLI) のコマンドを紹介します。この記事は、スクリプトをコマンド モード間で移行するための簡易ガイドとして使用してください。

* まだ Azure CLI をインストールしてサブスクリプションに接続していない場合は、[Azure CLI のインストール](../xplat-cli-install.md)に関するページと、[Azure CLI から Azure サブスクリプションへの接続](../xplat-cli-connect.md)に関するページをご覧ください。arm モードのコマンドを使用する場合、必ず所定のログイン方法で接続してください。

* Azure CLI で arm モードの使用を開始する方法とコマンド モードの切り替え方法については、[リソース マネージャーでの Azure コマンド ライン インターフェイスの使用](xplat-cli-azure-resource-manager.md)に関するページを参照してください。

* オンライン コマンドのヘルプとオプションについては、「`azure <command> <subcommand> --help`」または「`azure help <command> <subcommand>`」と入力します。

## シナリオ
asm モードと arm モードで Azure CLI コマンドを使用して実行できる一般的な VM 操作を次の表に示します。多くの arm モードのコマンドでは、既存のリソース グループの名前を渡す必要があります。

> [AZURE.NOTE]これらの例には、arm モードでのテンプレート ベースの操作が含まれていません。詳細については、「[Using the Azure Command-Line Interface with the Resource Manager (リソース マネージャーでの Azure コマンドライン インターフェイスの使用)](xplat-cli-azure-resource-manager.md)」を参照してください。

シナリオ | asm モード | arm モード
-------------- | ----------- | -------------------------
最も基本的な VM の作成 | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`
Linux VM の作成 | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Windows VM の作成 | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
VM の一覧表示 | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
VM に関する情報の取得 | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
VM の起動 | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
VM の停止 | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
VM の再起動 | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
VM の削除 | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
VM のキャプチャ | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
ユーザー イメージからの VM の作成 | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
専用ディスクからの VM の作成 | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
VM へのデータ ディスクの追加 | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -または- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
VM からのデータ ディスクの削除 | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
VM への一般的な拡張機能の追加 | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
VM への VM アクセス拡張機能の追加 | 使用できません。 | `azure vm reset-access [options] <resource-group> <name>`
VM への Docker 拡張機能の追加 | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
VM への Chef 拡張機能の追加 | `azure  vm extension get-chef [options] <vm-name>` | 使用できません。
VM 拡張機能の無効化 | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | 使用できません。
VM 拡張機能の削除 | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
VM 拡張機能の一覧表示 | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
VM イメージの一覧表示 | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` -または- <br/> `azure vm image list-publishers [options] <location>` -または- <br/> `azure vm image list-offers [options] <location>` -または- <br/> `azure vm image list-skus [options] <location>`
VM イメージの表示 | `azure vm image show [options]` | 使用できません。


## 次のステップ

* Azure CLI を使用して arm モードでリソースを操作する方法の詳細については、[リソース マネージャーでの Azure コマンド ライン インターフェイスの使用](xplat-cli-azure-resource-manager.md)に関するページと、[Azure コマンド ライン インターフェイスによる役割ベースのアクセス制御の管理](../role-based-access-control-xplat-cli.md)に関するページを参照してください。
* CLI コマンドのその他の例については、[Azure コマンド ライン インターフェイスの使用](../virtual-machines-command-line-tools.md)に関するページと、[Azure リソース マネージャーでの Azure CLI の使用](azure-cli-arm-commands.md)に関するページをご覧ください。

<!---HONumber=July15_HO4-->