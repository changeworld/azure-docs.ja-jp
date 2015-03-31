<properties 
	pageTitle="Ubuntu の Docker VM イメージから Docker をすばやく使用する方法" 
	description="Azure イメージ ギャラリーから直接、すばやく Ubuntu Server で Docker を使用する方法について詳しく説明します" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure" 
	ms.date="02/02/2015" 
	ms.author="rasquill"/>

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Azure Marketplace で Docker をすばやく開始する方法 

[Docker] を最もすばやく使用する方法は、Azure Marketplace に移動し、[Canonical] と [MSOpenTech] により作成された **Ubuntu Server の Docker** イメージ テンプレートを使用して VM を作成するものです。これにより Ubuntu Server VM が作成され、[Docker VM 拡張機能](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/)のインストールが、**最新**の Docker Engine のインストールと、Azure での実行が完了した状態で自動的に行われます。  

すぐに SSH で VM を接続し、そのまま直接 Docker で作業を始められます。 

> [AZURE.NOTE]Azure Marketplace テンプレートで作成された VM は、リモートの Docker クライアントによる管理目的の Docker リモート API をホストしません。リモートでこの VM の Docker ホストの管理を有効にするには、[HTTPS による Docker の実行](https://docs.docker.com/articles/https/)に関するページを参照するか、「[Azure ポータルでの Docker VM 拡張機能の使用](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/)」または [azure-cli での Docker VM 拡張機能の使用](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/)に関するページの手順に従ってください。高度な設定をする場合は、Github で [Windows Docker クライアント](https://github.com/ahmetalpbalkan/Docker.DotNet)を構築することもできます ([nuget](https://www.nuget.org/packages/Docker.DotNet/) での取得も可能です)。 

このトピックの内容:

- [ポータルへのログオン]
- [Canonical と MSOpenTech の Docker イメージを使用した VM の作成]
- [SSH による接続と簡単な設定]

## <a id='logon'>ポータルへのログオン</a>

この作業は Azure アカウントさえあれば、問題ありません。[アカウントがない場合も簡単に取得できます](http://azure.microsoft.com/pricing/free-trial/)。

## <a id='createvm'>Canonical と MSOpenTech の Docker イメージを使用した VM の作成</a>

1. ログオンしたら、左下にある **[新規]** をクリックして新しい VM イメージを作成します。すぐにバナーに適切なイメージが表示されます。

> ![Choose the Docker Ubuntu image in the banner](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. 表示されない場合は、イメージ ギャラリーで検索します。 

> ![Locate the image in the Image Gallery](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. インスタンスのユーザー名とパスワードを入力するか、**.pem** ファイルを指定して SSH で証明書を使用できるようにします(以下の図はユーザー名とパスワードの組み合わせの入力画面を示しています)。次に、下部にある **[作成]** をクリックします。

> ![Configure the vm instance](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. 稼働するまで待ちます。通常、長くはかかりません。

> ![Docker image running in portal](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## <a id='havingfun'>SSH による接続と簡単な設定</a>

次は楽しい作業です。SSH ですぐに VM に接続できます。

> ![Connecting with SSH](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

Docker コマンドの発行を開始しますが、この Azure VM では既定の構成に **`sudo`** が必要なことに注意してください。

> ![Pulling images](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

[Docker] の使用を開始してください。 

<!--Anchors-->
[ポータルへのログオン]: #logon
[Canonical と MSOpenTech の Docker イメージを使用した VM の作成]: #createvm
[SSH による接続と簡単な設定]: #havingfun
[次のステップ]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Docker スクラッチ イメージ]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/


<!--HONumber=47-->
