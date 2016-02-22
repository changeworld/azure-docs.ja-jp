<properties
	pageTitle="ディスクを VM にアタッチする | Microsoft Azure"
	description="クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにデータ ディスクをアタッチし、初期化します。"
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
	ms.date="02/03/2016"
	ms.author="cynthn"/>

# クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにデータ ディスクをアタッチする

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-attach-disk-preview.md)。

データ ディスクを追加する必要がある場合は、空のディスクまたはデータを含む既存のディスクを仮想マシンにアタッチできます。どちらの場合も、ディスクは Azure ストレージ アカウントにある .vhd ファイルです。新しいディスクの場合は、ディスクをアタッチした後で、Windows VM で使用できるように初期化する必要もあります。

仮想マシンのデータを格納するには、1 つ以上の個別のディスクを使用することをお勧めします。Azure 仮想マシンを作成する場合は、オペレーティング システムのディスクを C ドライブにマップし、一時ディスクを D ドライブにマップします。**データの保存に一時ディスクを使用しないでください**。名前が示すとおり、一時ディスクは一時的なストレージのみを提供します。Azure Storage に配置されていないため、冗長性やバックアップは提供しません。

## ビデオ チュートリアル

以下には、このチュートリアルのステップが記載されています。

[AZURE.VIDEO attaching-a-data-disk-to-a-windows-vm]

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>方法: Windows Server で新しいデータ ディスクを初期化する

1. 仮想マシンへの接続詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法][logon]」を参照してください。

2. 仮想マシンにログオンした後、**サーバー マネージャー**を開きます。左側のウィンドウで、**[ファイル サービスと記憶域サービス]** を選択します。

	![サーバー マネージャーを開く](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. メニューを展開し、**[ディスク]** を選択します。

4. **[ディスク]** セクションにディスクが一覧表示されます。普通、disk 0、disk 1、disk 2 といった名前です。disk 0 はオペレーティング システム ディスク、disk 1 は一時ディスク、disk 2 は VM にアタッチしたデータ ディスクです。新しいデータ ディスクのパーティションは **[不明]** と表示されます。ディスクを右クリックし、**[初期化]** を選択します。

5.	ディスクの初期化時にすべてのデータが消去されることが通知されます。**[はい]** をクリックして警告を了解し、ディスクを初期化します。完了すると、パーティションは **[GPT]** と表示されます。もう一度ディスクを右クリックし、**[新しいボリューム]** を選択します。

6.	既定の値を使用してウィザードを完了します。ウィザードが終了すると、**[ボリューム]** セクションに新しいボリュームが表示されます。ディスクがオンラインになり、データを格納できるようになります。

	![ボリュームの初期化に成功](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] VM のサイズによって、アタッチできるディスクの数が決まります。詳細については、「[仮想マシンのサイズ](virtual-machines-size-specs.md)」を参照してください。

## その他のリソース

[Windows 仮想マシンからディスクを切断する方法](storage-windows-detach-disk.md)

[仮想マシン用のディスクと VHD について](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=AcomDC_0211_2016-->