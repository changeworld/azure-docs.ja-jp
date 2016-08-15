<properties
	pageTitle="Linux VM への SSH 接続の拒否、失敗、エラー | Microsoft Azure"
	description="Linux を実行する Azure 仮想マシンに対する SSH 接続の失敗や拒否などの SSH エラーをトラブルシューティングして修正します。"
	keywords="ssh 接続が拒否される, ssh エラー, azure ssh, SSH 接続に失敗する"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# Azure Linux VM に対する SSH 接続の失敗、エラー、拒否のトラブルシューティング

Linux ベースの Azure 仮想マシン (VM) に接続しようとしたときに、さまざまな理由で Secure Shell (SSH) エラーが発生したり SSH 接続が失敗したり拒否されたりすることがあります。この記事は、問題を特定して修正するために役立ちます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](http://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](http://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。Azure サポートの使用方法の詳細については、「[Microsoft Azure サポートに関する FAQ](http://azure.microsoft.com/support/faq/)」を参照してください。

## Resource Manager デプロイ モデルを使用して作成された VM

Azure CLI コマンドを直接使用するか [Azure VMAccessForLinux 拡張機能](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)を使用して、資格情報また SSHD をリセットすることができます。トラブルシューティングの各手順を実行した後、再度 VM に接続してみてください。

### Azure CLI の前提条件

まだインストールしていない場合は、[Azure CLI をインストールし、Azure サブスクリプションに接続します](../xplat-cli-install.md)。`azure login` コマンドを使用してサインインし、Resource Manager モードであることを確認します (`azure config mode arm`)。

[Microsoft Azure Linux エージェント](virtual-machines-linux-agent-user-guide.md)のバージョン 2.0.5 以降がインストールされていることを確認します。

### SSHD のリセット
SSHD 構成自体が正しく構成されていない、またはサービスでエラーが発生した可能性があります。SSHD をリセットして、SSH 構成自体が有効かを確認します。

#### Azure CLI
```bash
azure vm reset-access -g <resource group> -n <vm name> -r
```

#### VM アクセス拡張機能
アクセス拡張機能は、SSH のリセット、SSH キーの再設定、新しいユーザーの追加など、実行するアクションを定義する json ファイルを読み込みます。まず、次の内容を含む PrivateConf.json という名前のファイルを作成します。

```bash
{  
	"reset_ssh":"True"
}
```

その後、`VMAccessForLinux` 拡張機能を手動で実行して SSHD 接続をリセットします。

```bash
azure vm extension set <resource group> <vm name> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### ユーザーの SSH 資格情報をリセットする
SSHD が正しく機能している場合は、特定のユーザーのパスワードをリセットできます。

#### Azure CLI
```bash
azure vm reset-access -g <resource group> <vm name> -u <username> -p <new password>
```

SSH キー認証を使用する場合は、特定のユーザーの SSH キーをリセットすることができます。

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <username> -M <~/.ssh/azure_id_rsa.pub>
```

#### VM アクセス拡張機能
次の内容を含む PrivateConf.json という名前のファイルを作成します。

```bash
{
	"username":"Username", "password":"NewPassword"
}
```

または、特定のユーザー用の SSH キーをリセットするには、次の内容を含む PrivateConf.json という名前のファイルを作成します。

```bash
{
	"username":"Username", "ssh_key":"ContentsOfNewSSHKey"
}
```

上記の手順のいずれかを実行してから、手動で `VMAccessForLinux` 拡張機能を実行し、SSH ユーザー資格情報をリセットします。

```
azure vm extension set <resource group> <vmname> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### VM を再デプロイする
Azure 内で VM を別のノードに再デプロイすると、基になるネットワーク問題を修正する場合があります。Azure ポータルを使用して VM を再デプロイするには、**[参照]**、**[仮想マシン]**、*ご使用の Linux 仮想マシン*、**[再デプロイ]** の順にクリックします。これを行う方法については、「[新しい Azure ノードへの仮想マシンの再デプロイ](virtual-machines-windows-redeploy-to-new-node.md)」を参照してください。現時点では、Azure CLI を使用して VM を再デプロイすることはできません。

> [AZURE.NOTE] この操作を行うと、一時ディスクのデータが失われ、仮想マシンに関連付けられている動的 IP アドレスが更新されることに注意してください。


## クラシック デプロイ モデルを使用して作成された VM

クラシック デプロイ モデルを使用して作成された VM の一般的な SSH 接続エラーを解決するには、次の手順を試してください。各手順を実行した後、VM に再接続してみてください。

- [Azure ポータル](https://portal.azure.com)からリモート アクセスをリセットします。Azure ポータルから、**[参照]**、**[仮想マシン (クラシック)]**、*ご使用の Linux 仮想マシン*、**[リモートのリセット...]** の順にクリックします。

- VM を再起動します。[Azure ポータル](https://portal.azure.com)から、**[参照]**、**[仮想マシン (クラシック)]**、*ご使用の Linux 仮想マシン*、**[再起動]** の順にクリックします。

	- または -

	[Azure クラシック ポータル](https://manage.windowsazure.com)で、**[仮想マシン]**、**[インスタンス]**、**[再起動]** の順にクリックします。

- 仮想マシンを新しい Azure ノードに VM を再デプロイします。これを行う方法については、「[新しい Azure ノードへの仮想マシンの再デプロイ](virtual-machines-windows-redeploy-to-new-node.md)」を参照してください。

	この操作を行うと、一時ディスクのデータが失われ、仮想マシンに関連付けられている動的 IP アドレスが更新されることに注意してください。

- [Linux ベースの仮想マシンのパスワードまたは SSH をリセットする方法](virtual-machines-linux-classic-reset-access.md)の指示に従って仮想マシンを操作します。
	- パスワードまたは SSH キーをリセットする
	- 新しい _sudo_ ユーザー アカウントを作成する。
	- SSH 構成をリセットする

- プラットフォームの問題の有無について VM のリソースの状態を確認します。<br> **[参照]**、**[仮想マシン (クラシック)]**、*ご使用の Linux 仮想マシン*、**[設定]**、**[正常性の確認]** の順に選択します。


## その他のリソース

- 上記の手順を実行しても VM に SSH を使用できない場合は、[詳細なトラブルシューティング手順](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md)に従って、追加のネットワーク構成と、問題解決の手順を確認します。

- アプリケーションへのアクセスのトラブルシューティングに関する詳細については、「[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-linux-troubleshoot-app-connection.md)」を参照してください。

- クラシック デプロイ モデルを使用して作成された仮想マシンのトラブルシューティングの詳細については、[Linux ベースの仮想マシンのパスワードまたは SSH をリセットする方法](virtual-machines-linux-classic-reset-access.md)に関するページを参照してください。

<!---HONumber=AcomDC_0803_2016-->