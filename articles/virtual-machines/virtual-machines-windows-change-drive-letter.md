<properties
	pageTitle="一時ディスクのドライブ文字を変更する | Microsoft Azure"
	description="クラシック デプロイメント モデルで作成された Windows 仮想マシンの一時ディスクのドライブ文字を変更する"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn
"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="cynthn"/>

#クラシック デプロイメント モデルで作成された仮想マシンの Windows 一時ディスクのドライブ文字を変更する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、クラシック デプロイメント モデルを使用したリソースの作成について説明します。

D ドライブを使用してデータを格納する必要がある場合、次の手順に従って一時ディスク用の別のドライブを使用します。維持する必要があるデータは、一時ディスクに格納しないでください。

始める前に、この手順の間に Windows ページファイル (pagefile.sys) を保存できるように、仮想マシンにデータ ディスクを接続しておく必要があります。接続していない場合は、[データ ディスクを Windows 仮想マシンに接続する方法][Attach]に関するページを参照してください。どのようなディスクが接続されているかを確認する方法については、「[Windows 仮想マシンからデータ ディスクを切断する方法][Detach]」の「ディスクの検索」を参照してください。

D ドライブの既存のデータ ディスクを使用する場合は、その Storage アカウントに VHD をアップロード済みであることもご確認ください。手順については、「[Windows Server VHD の作成と Azure へのアップロード][VHD]」の手順 3 と 4 を参照してください。

> [AZURE.WARNING]仮想マシンをサイズ変更する、または "停止 (割り当て解除)" する場合、新しいハイパーバイザーに対するその仮想マシンの配置がトリガーされる可能性があります。計画済みまたは計画外メンテナンス イベントでも、この配置がトリガーされる可能性があります。このシナリオで、一時ディスクに最初に利用可能なドライブ文字が再割り当てされます。特に "D" ドライブを必要とするアプリケーションがある場合は、ページファイルを移動した後に新しい永続ディスクを割り当て、これにドライブ文字 D を割り当てます。Azure で文字 D が使用されなくなります。

> [AZURE.WARNING]ページファイルを明示的に移動した後に仮想マシンのサイズを変更する場合、新しい仮想マシンの一時ディスクに、元の VM のサイズのページファイルを格納するのに十分な大きさがないと、起動時にエラーが発生する場合があります。このエラーは次に使用できるドライブ文字が一時ドライブに設定されていないため、Azure が次に使用できるドライブ文字の一時ドライブを作成する間に Windows がページファイル構成で無効なドライブ文字を参照した場合に発生する可能性があります。

##ドライブ文字の変更

1. 仮想マシンにログオンします。詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法][Logon]」を参照してください。

2. pagefile.sys を D ドライブから別のドライブに移動します。

3. 仮想マシンを再起動します。

4. 再びログインして、ドライブ文字を D から E に変更します。

5. [Azure ポータル](http://manage.windowsazure.com)から、既存のデータ ディスクか、空のデータ ディスクを接続します。

6.	仮想マシンに再ログインして、直前の手順で接続したディスクを初期化し、ドライブ文字 D を割り当てます。

7.	一時ディスクに E がマップされていることを確認します。

8.	pagefile.sys を別のドライブから E ドライブに移動します。

9.	仮想マシンを再起動します。



## その他のリソース
[Windows Server が実行されている仮想マシンにログオンする方法][Logon]

[Windows 仮想マシンからディスクを切断する方法][Detach]

[Azure ストレージ アカウントについて][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md



[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=Oct15_HO1-->