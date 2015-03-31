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
	ms.date="01/15/2015" 
	ms.author="kathydav"/>

# Windows 一時ディスクのドライブ文字を変更する方法

D ドライブを使用してデータを格納する必要がある場合、次の手順に従って一時ディスク用の別のドライブを使用します。維持する必要があるデータは、一時ドライブに格納しないでください。

始める前に、この手順の間に Windows ページファイル (pagefile.sys) を保存できるように、仮想マシンにデータ ディスクを接続しておく必要があります。接続していない場合は、「[データ ディスクを Windows 仮想マシンに接続する方法]」をご覧ください。接続されているディスクを調べるには、「[Azure の仮想マシンのディスクについて]」のディスクの管理セクションをご覧ください。

D ドライブの既存のデータ ディスクを使用する場合は、そのストレージ アカウントに VHD をアップロード済みであることもご確認ください。手順については、「[Windows Server VHD の作成と Azure へのアップロード]」のステップ 3 と 4 をご覧ください。

> [AZURE.WARNING] 仮想マシンのサイズを変更して別のホストに移動する場合は、一時ドライブは再度 D ドライブに変更されます。

## ドライブ文字の変更

1. 仮想マシンにログインします。 

2. pagefile.sys を D ドライブから別のドライブに移動します。

3. 仮想マシンを再起動します。

4. 	再びログインして、ドライブ文字を D から E に変更します。

5.	[Azure 管理ポータル](http://manage.windowsazure.com)から、既存のデータ ディスクか、空のデータ ディスクを接続します。

6.	仮想マシンに再ログインして、直前の手順で接続したディスクを初期化し、ドライブ文字 D を割り当てます。

7.	一時ストレージ ディスクに E がマップされていることを確認します。

8.	pagefile.sys を別のドライブから E ドライブに移動します。

## その他のリソース
[Windows Server が実行されている仮想マシンにログオンする方法]

[データ ディスクを仮想マシンから切断する方法]

[Azure ストレージ アカウントについて]

<!--Link references-->
[データ ディスクを Windows 仮想マシンに接続する方法]: ../storage-windows-attach-disk
[Azure の仮想マシンのディスクについて]: ../http://msdn.microsoft.com/library/azure/dn790303.aspx
[Windows Server VHD の作成と Azure へのアップロード]: ../virtual-machines-create-upload-vhd-windows-server/
[Windows Server が実行されている仮想マシンにログオンする方法]: ../virtual-machines-log-on-windows-server/
[データ ディスクを仮想マシンから切断する方法]: ../storage-windows-detach-disk/
[Azure ストレージ アカウントについて]: ../storage-whatis-account/



<!--HONumber=47-->
