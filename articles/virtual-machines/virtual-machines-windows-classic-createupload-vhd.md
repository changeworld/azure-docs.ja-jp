<properties
	pageTitle="Powershell を使用した VM イメージの作成とアップロード | Microsoft Azure"
	description="従来のデプロイ モデルと Azure Powershell を使用し、一般化された Windows Server イメージ (VHD) を作成してアップロードする方法について説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# Windows Server VHD の作成と Azure へのアップロード

この記事では、仮想マシンを作成できるように、独自の一般化された VM イメージを仮想ハードディスク (VHD) としてアップロードする方法について説明します。Microsoft Azure でのディスクと VHD の詳細については、「[Virtual Machines 用のディスクと VHD について](virtual-machines-linux-about-disks-vhds.md)」を参照してください。


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)].Resource Manager モデルを使用して、仮想マシンを[キャプチャ](virtual-machines-windows-capture-image.md)および[アップロード](virtual-machines-windows-upload-image.md)することもできます。

## 前提条件

この記事では、以下のことを前提としています。

- **Azure サブスクリプション** - サブスクリプションがない場合は、[Azure アカウントを無料で作成](/pricing/free-trial/?WT.mc_id=A261C142F)できます。

- **[Microsoft Azure PowerShell](../powershell-install-configure.md)** - Microsoft Azure PowerShell モジュールをインストールし、サブスクリプションを使用するように構成しておきます。

- **.vhd ファイル** - .vhd ファイルに格納され、仮想マシンに接続された、Windows オペレーティング システムでサポートされている。VHD で実行されているサーバー ロールが Sysprep でサポートされていることを確認してください。詳しくは、「[Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)」 (サーバー ロールに対する Sysprep サポート) をご覧ください。

> [AZURE.IMPORTANT] VHDX 形式は、Microsoft Azure ではサポートされていません。Hyper-V マネージャーまたは [Convert-VHD コマンドレット](http://technet.microsoft.com/library/hh848454.aspx)を使用して、ディスクを VHD 形式に変換できます。詳細については、この[ブログの投稿](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)を参照してください。

## 手順 1: VHD を準備する 

VHD を Azure にアップロードする前に、Sysprep ツールを使用して一般化する必要があります。一般化によって、VHD をイメージとして使用できるように準備します。Sysprep の詳細については、「[Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。Sysprep を実行する前に、VM をバックアップします。

オペレーティング システムがインストールされている仮想マシンから、次の手順を実行します。

1. オペレーティング システムにサインインします。

2. 管理者としてコマンド プロンプト ウィンドウを開きます。ディレクトリを **%windir%\\system32\\sysprep** に変更し、`sysprep.exe` を実行します。

	![コマンド プロンプト ウィンドウを開く](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.	**[システム準備ツール]** ダイアログ ボックスが表示されます。

	![Sysprep の開始](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  **[システム準備ツール]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。

5.  **[シャットダウン オプション]** の **[シャットダウン]** を選択します。

6.  **[OK]** をクリックします。

## 手順 2: ストレージ アカウントとコンテナーを作成する

.vhd ファイルをアップロードする場所を用意するために、Azure のストレージ アカウントが必要です。この手順では、アカウントを作成する方法と、既存のアカウントから必要な情報を取得する方法について説明します。&lsaquo; 括弧 &rsaquo; 内の変数は、自分の情報に置き換えてください。

1. ログイン

		Add-AzureAccount

1. Azure サブスクリプションを設定します。

    	Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. 新しいストレージ アカウントを作成します。ストレージ アカウントの名前は、一意の 3 ～ 24 文字にする必要があります。この名前は、文字と数字の任意の組み合わせとすることができます。"米国東部" などの場所を指定する必要があります。
    	
		New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. 新しいストレージ アカウントを既定に設定します。
    	
		Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. 新しいコンテナーを作成します。

    	New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## 手順 3: .vhd ファイルをアップロードする

[Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) を使用して VHD をアップロードします。

前の手順で使用した Azure PowerShell ウィンドウから次のコマンドを入力し、&lsaquo; 括弧 &rsaquo; 内の変数を自分の情報に置き換えます。

		Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## 手順 4: カスタム イメージの一覧にイメージを追加する

[Add-AzureVMImage] (https://msdn.microsoft.com/library/mt589167.aspx) コマンドレットを使用して、カスタム イメージの一覧にイメージを追加します。

		Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## 次のステップ

アップロードしたイメージを使用して、[カスタム VM を作成](virtual-machines-windows-classic-createportal.md)できるようになりました。

<!---HONumber=AcomDC_0831_2016-->