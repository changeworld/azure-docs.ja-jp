<properties urlDisplayName="Attach a disk" pageTitle="仮想マシンへのディスクの接続 | Azure" metaKeywords="Azure Windows virtual machine, Azure attach disk, Azure initialize disk" description="データ ディスクを Azure 仮想マシンに接続し、初期化して、使用できる状態にする方法について説明します。" metaCanonical="" services="virtual-machines,storage" documentationCenter="" title="データ ディスクを仮想マシンに接続する方法" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="7/29/2014" ms.author="kathydav" />

# データ ディスクを Windows 仮想マシンに接続する方法

空のディスクと、データが含まれているディスクのどちらも接続できます。どちらの場合も、ディスクは、実際には、Azure ストレージ アカウントに配置されている .vhd ファイルです。また、ディスクを接続した後に、初期化して、使用できる状態にする必要があります。

> [WACOM.NOTE] 仮想マシンのデータを格納するには、1 つ以上の個別のディスクを使用することをお勧めします。Azure の仮想マシンを作成する場合は、オペレーティング システムをディスクの C ドライブにマップし、一時ディスクを D ドライブにマップします。**データの格納に D ドライブを使用しないでください。**名前が示すとおり、D ドライブは一時的なストレージのみを提供します。Azure Storage に配置されていないため、冗長性やバックアップは提供しません。

-   [方法: 空のディスクの接続][方法: 空のディスクの接続]
-   [方法: 既存のディスクの接続][方法: 既存のディスクの接続]
-   [方法: Windows Server での新しいデータ ディスクの初期化][方法: Windows Server での新しいデータ ディスクの初期化]

[WACOM.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

## <span id="initializeinWS"></span></a>方法: Windows Server での新しいデータ ディスクの初期化

1.  「[Windows Server を実行する仮想マシンにログオンする方法][Windows Server を実行する仮想マシンにログオンする方法]」に示された手順に従って仮想マシンに接続します。

2.  マシンにログオンしたら、左側のウィンドウで**サーバー マネージャー**を開き、**[ストレージ]** を展開して、**[ディスクの管理]** をクリックします。

    ![サーバー マネージャーを開く][サーバー マネージャーを開く]

3.  **[ディスク 2]** を右クリックして、**[ディスクの初期化]** をクリックし、**[OK]** をクリックします。

    ![ディスクの初期化][ディスクの初期化]

4.  ディスク 2 のスペース割り当て領域を右クリックして、**[新しいシンプル ボリューム]** をクリックし、既定値を使ってウィザードを終了します。

    ![ボリュームの初期化][ボリュームの初期化]

<!-- -->

    The disk is now online and ready to use with a new drive letter.



    ![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

  [方法: 空のディスクの接続]: #attachempty
  [方法: 既存のディスクの接続]: #attachexisting
  [方法: Windows Server での新しいデータ ディスクの初期化]: #initializeinWS
  [Windows Server を実行する仮想マシンにログオンする方法]: ../virtual-machines-log-on-windows-server/
  [サーバー マネージャーを開く]: ./media/storage-windows-attach-disk/ServerManager.png
  [ディスクの初期化]: ./media/storage-windows-attach-disk/InitializeDisk.png
  [ボリュームの初期化]: ./media/storage-windows-attach-disk/InitializeDiskVolume.png
