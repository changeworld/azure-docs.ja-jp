<properties 
	pageTitle="Windows 一時ディスクのドライブ文字を変更する方法" 
	description="Azure の Windows VM で一時ディスクを再マップする方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="kathydav"/>

#Windows 一時ディスクのドライブ文字を変更する方法

D ドライブを使用してデータを格納する必要がある場合、次の手順に従って一時ディスク用の別のドライブを使用します。維持する必要があるデータは、一時ドライブに格納しないでください。

始める前に、この手順の間に Windows ページファイル (pagefile.sys) を保存できるように、仮想マシンにデータ ディスクを接続しておく必要があります。接続していない場合は、[データ ディスクを Windows 仮想マシンに接続する方法][Attach]に関するページを参照してください。接続されているディスクを調べる手順については、[Azure の仮想マシンのディスク][Disks]に関するページのディスクの管理に関するセクションを参照してください。

D ドライブの既存のデータ ディスクを使用する場合は、そのストレージ アカウントに VHD をアップロード済みであることもご確認ください。手順については、「[Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード][VHD]」のステップ 3 と 4 を参照してください。

> [AZURE.WARNING]仮想マシンのサイズを変更して別のホストに移動する場合は、一時ドライブは再度 D ドライブに変更されます。

##ドライブ文字の変更

1. 仮想マシンにログインします。詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法][Logon]」を参照してください。

2. pagefile.sys を D ドライブから別のドライブに移動します。

3. 仮想マシンを再起動します。

4. 再びログインして、ドライブ文字を D から E に変更します。

5. [Azure 管理ポータル](http://manage.windowsazure.com)から、既存のデータ ディスクか、空のデータ ディスクを接続します。

6.	仮想マシンに再ログインして、直前の手順で接続したディスクを初期化し、ドライブ文字 D を割り当てます。

7.	一時ストレージ ディスクに E がマップされていることを確認します。

8.	pagefile.sys を別のドライブから E ドライブに移動します。

## その他のリソース
[Windows Server が実行されている仮想マシンにログオンする方法][Logon]

[データ ディスクを仮想マシンから切断する方法][Detach]

[Azure ストレージ アカウントについて][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md

[Disks]: ../dn790303.aspx

[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md


 

<!---HONumber=July15_HO3-->