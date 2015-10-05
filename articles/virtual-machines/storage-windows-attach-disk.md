<properties
	pageTitle="ディスクを仮想マシンにアタッチする | Microsoft Azure"
	description="クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにデータ ディスクをアタッチします。"
	services="virtual-machines, storage"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="cynthn"/>

# クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにデータ ディスクをアタッチする

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、クラシック デプロイメント モデルを使用したリソースの作成について説明します。また、[リソース マネージャー デプロイ モデル](virtual-machines-attach-disk-preview.md)を使用してリソースを作成することもできます。

空のデータ ディスクもデータの入ったディスクも接続できます。どちらの場合も、ディスクは、実際には、Azure ストレージ アカウントに配置されている .vhd ファイルです。また、ディスクを接続した後に、初期化して、使用できる状態にする必要があります。

> [AZURE.NOTE]仮想マシンのデータを格納するには、1 つ以上の個別のディスクを使用することをお勧めします。Azure 仮想マシンを作成する場合は、オペレーティング システムをディスクの C ドライブにマップし、一時ディスクを D ドライブにマップします。**データの保存に D ドライブを使用しないでください。** 名前が示すとおり、D ドライブは一時的なストレージのみを提供します。Azure Storage に配置されていないため、冗長性やバックアップは提供しません。

## ビデオ チュートリアル

以下には、このチュートリアルのステップが記載されています。

[AZURE.VIDEO attaching-a-data-disk-to-a-windows-vm]

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>方法: Windows Server で新しいデータ ディスクを初期化する

1. 仮想マシンへの接続詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法][logon]」を参照してください。

2. 仮想マシンにログオンした後、**サーバー マネージャー**を開きます。左側のウィンドウで、**[ファイル サービスと記憶域サービス]** を選択します。

	![サーバー マネージャーを開く](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. メニューを展開し、**[ディスク]** を選択します。

4. **[ディスク]** セクションには、disk 0、disk 1、disk 2 が一覧表示されます。disk 0 はオペレーティング システム ディスクで、disk 1 は一時的なディスク (データ ストレージ用には使用できない) です。disk 2 は仮想マシンに接続したデータ ディスクです。データ ディスクの容量は、ディスクに指定したものやディスクを接続したタイミングに基づいて、5 GB になります。disk 2 を右クリックし、**[初期化]** を選択します。

5.	ディスクの初期化時にすべてのデータが消去されることが通知されます。**[はい]** をクリックして警告を了解し、ディスクを初期化します。次に、もう一度 disk 2 を右クリックし、**[ボリューム]** を選択します。

6.	既定の値を使用してウィザードを完了します。ウィザードが終了すると、**[ボリューム]** セクションに新しいボリュームが表示されます。ディスクがオンラインになり、データを格納できるようになります。

	![ボリュームの初期化に成功](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE]仮想マシンのサイズによって、アタッチできるディスクの数が決まります。詳細については、「[Sizes for virtual machines (仮想マシンのサイズ)](virtual-machines-size-specs.md)」を参照してください。

## その他のリソース

[Windows 仮想マシンからディスクを切断する方法](storage-windows-detach-disk.md)

[仮想マシン用のディスクと VHD について](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=Sept15_HO4-->