<properties title="How To Change the Drive Letter of the Windows Temporary Disk" pageTitle="How To Change the Drive Letter of the Windows Temporary Disk" description="Describes how to remap the temporary disk on a Windows VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Windows 一時ディスクのドライブ文字を変更する方法

D ドライブを他の目的に使用する必要がある場合は、一時ディスクのドライブ文字を変更できます。この変更は、常時 D ドライブをストレージとして使用するアプリケーションやサービスをサポートする場合などに実行します。

開始する前に、次の項目が準備されていることを確認してください。

-   この手順で Windows ページ ファイル (pagefile.sys) の保存に使用するデータ ディスクが 1 つ接続されていること。手順については、「[データ ディスクを Virtual Machine に接続する方法][データ ディスクを Virtual Machine に接続する方法]」を参照してください。
-   D ドライブ上にある既存のデータ ディスクの VHD を使用する場合は、ストレージ アカウントに 1 つの VHD がアップロードされていること。手順については、「[Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード][Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード]」のステップ 3 と 4 を参照してください。

## ドライブ文字の変更

1.  仮想マシンにログインします。

2.  pagefile.sys を D ドライブから別のドライブに移動します。

3.  仮想マシンを再起動します。

4.  再びログインして、ドライブ文字を D から E に変更します。

5.  [Azure 管理ポータル][Azure 管理ポータル]から、既存のデータ ディスクか、空のデータ ディスクを接続します。

6.  仮想マシンに再ログインして、直前の手順で接続したディスクを初期化し、ドライブ文字 D を割り当てます。

7.  一時ストレージ ディスクに E がマップされていることを確認します。

8.  pagefile.sys を別のドライブから E ドライブに移動します。

## その他のリソース

[Windows Server が実行されている仮想マシンにログオンする方法][Windows Server が実行されている仮想マシンにログオンする方法]

[データ ディスクを仮想マシンから切断する方法][データ ディスクを仮想マシンから切断する方法]

[ストレージ アカウントとは][ストレージ アカウントとは]

<!--Link references-->

  [データ ディスクを Virtual Machine に接続する方法]: ../storage-windows-attach-disk
  [Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード]: ../virtual-machines-create-upload-vhd-windows-server/
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [Windows Server が実行されている仮想マシンにログオンする方法]: ../virtual-machines-log-on-windows-server/
  [データ ディスクを仮想マシンから切断する方法]: ../storage-windows-detach-disk/
  [ストレージ アカウントとは]: ../storage-whatis-account/
