<properties
	pageTitle="Ubuntu の Docker VM イメージから Docker をすばやく使用する方法"
	description="Azure イメージ ギャラリーから直接、すばやく Ubuntu Server で Docker を使用する方法について詳しく説明します"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure"
	ms.date="01/04/2016"
	ms.author="rasquill"/>

# Azure Marketplace で Docker をすばやく開始する方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。
 

[Docker] を最もすばやく使用する方法は、Azure Marketplace に移動し、[Canonical] と [MSOpenTech] により作成された **Ubuntu Server の Docker** イメージ テンプレートを使用して VM を作成するものです。これにより Ubuntu Server VM が作成され、[Docker VM 拡張機能](virtual-machines-linux-dockerextension.md)のインストールが、**最新の** Docker Engine のインストールと、Azure での実行が完了した状態で自動的に行われます。

すぐに SSH で VM を接続し、そのまま直接 Docker で作業を始められます。

> [AZURE.NOTE]Azure Marketplace テンプレートで作成された VM は、リモートの Docker クライアントによる管理目的の Docker リモート API をホストしません。リモートでこの VM の Docker ホストの管理を有効にするには、[HTTPS による Docker の実行](https://docs.docker.com/articles/https/)に関するページを参照するか、「[Azure クラシック ポータルでの Docker VM 拡張機能の使用](virtual-machines-linux-classic-portal-use-docker.md)」または「[Azure CLI での Docker VM 拡張機能の使用](virtual-machines-linux-classic-cli-use-docker.md)」の手順に従ってください。
<!-- -->
Windows からの Azure Docker VM を自動化する場合は、[Docker Toolbox](https://docs.docker.com/installation/windows/) をインストールするか、[Chocolatey から](https://chocolatey.org/packages/docker) Docker.exe を取得してください。

## ポータルへのログオン

この作業は Azure アカウントさえあれば、問題ありません。[アカウントがない場合も簡単に取得できます](https://azure.microsoft.com/pricing/free-trial/)。

## Canonical と MSOpenTech の Docker イメージを使用した VM の作成

1. ログオンしたら、左下にある **[新規]** をクリックして新しい VM イメージを作成します。すぐにバナーに適切なイメージが表示されます。

> ![バナーの Docker Ubuntu イメージの選択](./media/virtual-machines-linux-classic-docker-quickstart/CreateNewDockerBanner.png)

2. 表示されない場合は、イメージ ギャラリーで検索します。

> ![イメージ ギャラリーでのイメージの検索](./media/virtual-machines-linux-classic-docker-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. インスタンスのユーザー名とパスワードを入力するか、**.pub** ファイル (ssh-rsa format) のコンテンツを指定して SSH で証明書を使用できるようにします(以下の図はユーザー名とパスワードの組み合わせの入力画面を示しています)。 次に、下部にある **[作成]** をクリックします。

> ![VM インスタンスの構成](./media/virtual-machines-linux-classic-docker-quickstart/CreateVMDockerUbuntuPwd.png)

4. 稼働するまで待ちます。通常、長くはかかりません。

> ![ポータルでの Docker イメージの実行](./media/virtual-machines-linux-classic-docker-quickstart/DockerUbuntuRunning.png)

## SSH による接続と簡単な設定

次は楽しい作業です。SSH ですぐに VM に接続できます。

> ![SSH との接続](./media/virtual-machines-linux-classic-docker-quickstart/SSHToDockerUbuntu.png)

Docker コマンドの発行を開始しますが、この Azure VM では既定の構成に **`sudo`** が必要なことに注意してください。

> ![イメージの取得](./media/virtual-machines-linux-classic-docker-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

[Docker] の使用を開始してください。

<!--Anchors-->
[Log on to the Portal]: #logon
[Create a VM with the Docker Image from Canonical and MSOpenTech]: #createvm
[Connect with SSH and Have Fun]: #havingfun
[Next steps]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Docker scratch image]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/
 

<!---HONumber=AcomDC_0323_2016-->