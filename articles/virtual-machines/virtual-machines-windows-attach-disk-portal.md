<properties
	pageTitle="データ ディスクを接続する |Microsoft Azure"
	description="Azure ポータルでリソース マネージャーデプロイ モデルを使用して、新規または既存のデータ ディスクを VM に接続する方法。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Azure ポータルでデータ ディスクを接続する方法

[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a id="initializeinWS"></a>方法: Windows Server で新しいデータ ディスクを初期化する

1. 仮想マシンへの接続詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法](virtual-machines-windows-log-on.md)」を参照してください。

2. 仮想マシンにログオンした後、**サーバー マネージャー**を開きます。左側のウィンドウで、**[ファイル サービスと記憶域サービス]** を選択します。

	![サーバー マネージャーを開く](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. メニューを展開し、**[ディスク]** を選択します。

4. **[ディスク]** セクションにディスクが一覧表示されます。普通、disk 0、disk 1、disk 2 といった名前です。disk 0 はオペレーティング システム ディスク、disk 1 は一時ディスク、disk 2 は VM にアタッチしたデータ ディスクです。新しいデータ ディスクのパーティションは **[不明]** と表示されます。ディスクを右クリックし、**[初期化]** を選択します。

5.	ディスクの初期化時にすべてのデータが消去されることが通知されます。**[はい]** をクリックして警告を了解し、ディスクを初期化します。完了すると、パーティションは **[GPT]** と表示されます。もう一度ディスクを右クリックし、**[新しいボリューム]** を選択します。

6.	既定の値を使用してウィザードを完了します。ウィザードが終了すると、**[ボリューム]** セクションに新しいボリュームが表示されます。ディスクがオンラインになり、データを格納できるようになります。


	![ボリュームの初期化に成功](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] VM のサイズによって、アタッチできるディスクの数が決まります。詳細については、「[仮想マシンのサイズ](virtual-machines-linux-sizes.md)」を参照してください。


## 次のステップ

アプリケーションで D: ドライブを使用してデータを保存する必要がある場合は、Windows 一時ディスクのドライブ文字を変更できます。

<!---HONumber=AcomDC_0323_2016-->