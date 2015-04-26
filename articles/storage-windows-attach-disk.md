<properties 
	pageTitle="仮想マシンへのディスクの接続 | Azure" 
	description="データ ディスクを Azure 仮想マシンに接続し、初期化して、使用できる状態にする方法について説明します。" 
	services="virtual-machines, storage" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2015" 
	ms.author="kathydav"/>

#データ ディスクを Windows 仮想マシンに接続する方法

空のディスクと、データが含まれているディスクのどちらも接続できます。どちらの場合も、ディスクは、実際には、Azure ストレージ アカウントに配置されている .vhd ファイルです。また、ディスクを接続した後に、初期化して、使用できる状態にする必要があります。 

> [AZURE.NOTE] 仮想マシンのデータを格納するには、1 つ以上の個別のディスクを使用することをお勧めします。Azure の仮想マシンを作成する場合は、オペレーティング システムをディスクの C ドライブにマップし、一時ディスクを D ドライブにマップします。**データの格納に D ドライブを使用しないでください。**名前が示すとおり、D ドライブは一時的なストレージのみを提供します。Azure Storage に配置されていないため、冗長性やバックアップは提供しません。

- [方法:空のディスクの接続](#attachempty)
- [方法:既存のディスクの接続](#attachexisting)
- [方法:Windows Server での新しいデータ ディスクの初期化](#initializeinWS)


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

##<a id="initializeinWS"></a>方法:Windows Server での新しいデータ ディスクの初期化

1. 仮想マシンへの接続詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法][logon]」を参照してください。

2. マシンにログオンしたら、左側のウィンドウで**サーバー マネージャー**を開き、**[ストレージ]** を展開して、**[ディスクの管理]** をクリックします。



	![Open Server Manager](./media/storage-windows-attach-disk/ServerManager.png)



3. **[ディスク 2]** を右クリックして、**[ディスクの初期化]** をクリックし、**[OK]** をクリックします。



	![Initialize the disk](./media/storage-windows-attach-disk/InitializeDisk.png)


4. ディスク 2 のスペース割り当て領域を右クリックして、**[新しいシンプル ボリューム]** をクリックし、既定値を使ってウィザードを終了します。
 

	![Initialize the volume](./media/storage-windows-attach-disk/InitializeDiskVolume.png)


[logon]: ../virtual-machines-log-on-windows-server/



	これでディスクがオンラインになり、新しいドライブ文字が使用できるようになりました。



	![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

> [AZURE.NOTE] 仮想マシンに接続できるディスクの最大数は、仮想マシンのサイズによって変化します。たとえば、標準 A2 には 4 つのディスクのみを接続できますが、標準 D14 には 32 のディスク、標準 G5 には 64 のディスクを接続できます。仮想マシンのサイズによって接続できるディスク数の詳細は、[こちら](https://msdn.microsoft.com/ja-jp/library/azure/dn197896.aspx)をご覧ください。



<!--HONumber=42-->
