<properties
	pageTitle="Linux を実行する仮想マシンのイメージのキャプチャ"
	description="Linux を実行する Azure の仮想マシン (VM) のイメージをキャプチャする方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="dkshir"/>


# テンプレートとして使用するために Linux 仮想マシンをキャプチャする方法

ここでは、Linux を実行する Azure 仮想マシンをキャプチャして、他の仮想マシンを作成する際のテンプレートのように使用する方法を示します。このテンプレートには、仮想マシンに接続された OS ディスクやデータ ディスクが含まれます。ネットワーク構成は含まれないため、テンプレートを使用する他の仮想マシンを作成するときは、ネットワーク構成を行う必要があります。

Azure ではこのテンプレートをイメージとして扱い、**[イメージ]** に格納します。アップロードしたすべてのイメージがこの場所に格納されます。イメージの詳細については、「[Azure の仮想マシン イメージについて][]」を参照してください。

## 開始する前に

これらの手順は、すでにクラシック デプロイ モデルを使用して Azure 仮想マシンを作成し、データ ディスクの接続を含め、オペレーティング システムの構成が完了していることを前提としています。まだ完了していない場合は、下記の手順を確認してください。

- [How to Create a Virtual Machine Running Linux (Linux を実行する仮想マシンの作成方法)][]


## 仮想マシンをキャプチャする

1. 任意の SSH クライアントを使用して、仮想マシンに接続します。詳細については、「[Linux を実行する仮想マシンにログオンする方法][]」を参照してください。

2. SSH のウィンドウで、次のコマンドを入力します。`waagent` からの出力はこのユーティリティのバージョンによって多少異なる場合があることに注意してください。

	`sudo waagent -deprovision`

	このコマンドは、システムをクリーンアップし、再プロビジョニングに適した状態にします。この操作では次のタスクが実行されます。

	- すべての SSH ホスト キーの削除 (構成ファイルで Provisioning.RegenerateSshHostKeyPair が 'y' の場合)
	- /etc/resolv.conf 内のネームサーバー構成の消去
	- /etc/shadow の `root` ユーザーのパスワードの削除 (構成ファイルで Provisioning.DeleteRootPassword が 'y' の場合)
	- キャッシュされた DHCP クライアントのリースの削除
	- ホスト名を localhost.localdomain にリセット
	- (/var/lib/waagent から取得した) 前回プロビジョニングされたユーザー アカウント**および関連データ**の削除

	>[AZURE.NOTE]プロビジョニング解除では、イメージを "一般化" する目的でファイルとデータが削除されます。このコマンドは、必ず新しいイメージ テンプレートとしてキャプチャする仮想マシン上で実行してください。プロビジョニング解除により、イメージからすべての機密情報が削除されることや、イメージが第三者への再配布に適した状態になることが保証されるわけではありません。


3. 「**y**」と入力して続行します。`-force` パラメーターを追加すると、この確認手順を省略できます。

4. 「**Exit**」と入力して、SSH クライアントを閉じます。


	>[AZURE.NOTE]次の手順は、クライアント コンピューターに既に [Azure CLI がインストールされている](../xplat-cli-install.md)ことを前提としています。次の手順はすべて、[管理ポータル][]でも実行できます。

5. クライアント コンピューターから Azure CLI を開き、Azure サブスクリプションにログインします。詳細については、[「Connect to an Azure subscription from the Azure CLI (Azure CLI から Azure サブスクリプションへの接続)」](../xplat-cli-connect.md)を参照してください。

6. サービス管理モードであることを確認します。

	`azure config mode asm`

7. 次のコマンドを実行して、上記の手順で既にプロビジョニングが解除された仮想マシンを終了します。

	`azure vm shutdown <your-virtual-machine-name>`

	>[AZURE.NOTE]`azure vm list` を使用することによって、サブスクリプションで作成されたすべての仮想マシンを確認できます。

8. 仮想マシンが停止したら、次のコマンドを実行してイメージをキャプチャします。

	`azure vm capture -t <your-virtual-machine-name> <new-image-name>`

	_new-image-name_ を使用するイメージ名に置き換えて入力します。このコマンドでは、一般化された OS イメージが作成されます。`-t` サブコマンドによって、元の仮想マシンが削除されます。

9.	新しいイメージが、新しい仮想マシンの構成に使用できるイメージの一覧に表示されるようになりました。次のコマンドを実行すると、新しいイメージを表示できます。

	`azure vm image list`

	[管理ポータル][]の **[イメージ]** 一覧に表示されます。

	![イメージのキャプチャの成功](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)


## 次のステップ
イメージの準備ができましたので、これをテンプレートとして使用して仮想マシンを作成します。Azure CLI コマンド `azure vm create` を使用して、先ほど作成したイメージの名前を指定することができます。コマンドの詳細については、「[Using Azure CLI with Service Management API (サービス管理 API での Azure CLI の使用)](virtual-machines-command-line-tools.md)」をご覧ください。または、[管理ポータル][]を使用し、**[ギャラリーから]** の方法を使用して、先ほど作成したイメージを選択することで、カスタム仮想マシンを作成することもできます。詳細については、[「カスタム仮想マシンの作成方法」][]を参照してください。

**関連項目:** [Azure Linux エージェント ユーザー ガイド](virtual-machines-linux-agent-user-guide.md)

[管理ポータル]: http://manage.windowsazure.com
[Linux を実行する仮想マシンにログオンする方法]: virtual-machines-linux-how-to-log-on.md
[Azure の仮想マシン イメージについて]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[「カスタム仮想マシンの作成方法」]: virtual-machines-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: storage-windows-attach-disk.md
[How to Create a Virtual Machine Running Linux (Linux を実行する仮想マシンの作成方法)]: virtual-machines-linux-tutorial.md

<!---HONumber=August15_HO8-->