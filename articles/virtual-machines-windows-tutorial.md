<properties 
	pageTitle="Azure 上で Windows を実行する仮想マシンの作成" description="Azure で Windows 仮想マシン (VM) を作成し、ログオンしてデータ ディスクを接続する方法について説明します" 
	services="virtual-machines" 
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



# Windows を実行する仮想マシンの作成 #

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal" class="current">Azure ポータル</a><a href="/ja-jp/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal">Azure プレビュー ポータル</a></div>

このチュートリアルでは、Azure 仮想マシン (VM) を簡単に作成する方法を示します。ここでは Windows Server イメージを使用しますが、Azure では他にも使用可能なイメージが多数あります。使用可能なイメージには、Windows オペレーティング システム、Linux ベースのオペレーティング システム、プレインストールされているアプリケーションのイメージなどがあります。選択できるイメージは、ご利用のサブスクリプションの種類によって異なります。たとえば、デスクトップ イメージは MSDN サブスクリプション会員のみが使用できますです。

> [AZURE.NOTE] このチュートリアルは、Azure VM の使用経験がなくても完了できますが、Azure アカウントが必要です。数分で無料の試用アカウントを作成することができます。詳細については、「[Azure アカウントの作成](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/)」を参照してください。 

このチュートリアルは、以下について説明します。

- [仮想マシンの作成方法](#createvirtualmachine)
- [仮想マシンを作成後、ログオンする方法](#logon)
- [データ ディスクを仮想マシンに接続する方法](#attachdisk)

さらに詳しい内容は、「[仮想マシン](http://go.microsoft.com/fwlink/p/?LinkID=271224)」を参照してください。


##<a id="createvirtualmachine"> </a>仮想マシンの作成方法##

このセクションでは、管理ポータルの **[ギャラリーから]** オプションを使用して仮想マシンを作成する方法について説明します。このオプションは、**[簡易作成]** オプションよりも多数の構成の選択肢があります。たとえば、仮想マシンを仮想ネットワークに参加させる場合は、**[ギャラリーから]** オプションを使用する必要があります。

> [AZURE.NOTE] さらに、より機能が豊富でカスタマイズも可能な [Azure プレビュー ポータル](https://portal.azure.com) を使用すれば、仮想マシンの作成、複数の仮想マシンのアプリケーション テンプレートのデプロイの自動化、VM の強化された監視や診断の機能の使用など、さまざまな操作を試すことができます。この 2 つのポータルにある利用可能な VM の構成オプションには、性質上、重複するものもありますが、まったく同じではありません。  

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>仮想マシンを作成後、ログオンする方法 ##

このセクションでは、仮想マシンにログオンする方法について説明します。ログオンすると、仮想マシン上で実行する設定とアプリケーションを管理できます。

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <a id="attachdisk"> </a>データ ディスクを仮想マシンに接続する方法 ##

このセクションでは、空のデータ ディスクを仮想マシンに接続する方法について説明します。既存のディスクの接続方法など、詳細については、「[データ ディスクを仮想マシンに接続する方法](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)」を参照してください。

1. Azure [管理ポータル](http://manage.windowsazure.com) にサインインします。

2. **[仮想マシン]** をクリックし、次に **[MyTestVM]** 仮想マシンを選択します。

	![Select MyTestVM](./media/virtual-machines-windows-tutorial/selectvm.png)
	
3. 最初にクイック スタート ページが表示される場合があります。その場合は、上部にある **[ダッシュボード]** を選択します。

	![Select Dashboard](./media/virtual-machines-windows-tutorial/dashboard.png)

4. コマンド バーで、**[ディスクの接続]** をクリックし、表示される **[空のディスクの接続]** をクリックします。

	![Select Attach from the command bar](./media/virtual-machines-windows-tutorial/commandbarattach.png)	

5. **[仮想マシン名]**、**[ストレージの場所]**、**[ファイル名]**、**[ホスト キャッシュの設定]** の値は既に定義されています。指定する必要があるのは、ディスクのサイズです。たとえば、**[サイズ]** ボックスに「**5**」と入力します。チェック マークをクリックして、ディスクを接続します。


	>[AZURE.NOTE] ディスクはすべて、.vhd ファイルから Azure ストレージに作成されます。**ファイル名 ** を、ディスクが使用している .vhd ファイルの名前にはできますが、ディスク名にはできません。ディスク名は Azure が自動的に割り当てます。 

	![Specify the size of the empty disk](./media/virtual-machines-windows-tutorial/emptydisksize.png)	
	
	>[AZURE.NOTE] .Vhd ファイルは、Azure ストレージにページ blob として格納されます。Azure 以外では、仮想ハード ディスクには VHD フォーマットまたは VHDX フォーマットを使用でき、固定、動的拡張、差分にも対応しています。Azure は VHD フォーマットの固定ディスクをサポートしています。詳細については、「[Azure の VHD について](http://msdn.microsoft.com/library/azure/dn790344.aspx)」を参照してください。  

6. ダッシュボードに戻り、空のデータ ディスクが仮想マシンに正常に接続されたことを確認します。OS ディスクの後に **[ディスク]** リストに表示されます。

	![Attach empty disk](./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png)

	データ ディスクを接続するときは、ディスクはオフラインになり初期化されません。データ ディスクを使ってデータを保存する前に、仮想マシンにログオンして、ディスクを初期化する必要があります。

7. 仮想マシンに接続してログオンするには、前のセクション「[仮想マシンを作成後、ログオンする方法]」の手順を使用します。

8. 仮想マシンにログオンした後、**サーバー マネージャー**を開きます。左側のウィンドウで、**[ファイル サービスと記憶域サービス]** を選択します。

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/fileandstorageservices.png)

9. 展開したメニューの **[ディスク]** を選択します。

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/selectdisks.png)	
	
10.	**[ディスク]** セクションには、disk 0、disk 1、disk 2 が一覧表示されます。disk 0 は OS ディスク、disk 1 は一時リソース ディスク (データ ストレージとして使用しないでください) です。disk 2 は、仮想マシンに接続したデータ ディスクです。データ ディスクの容量は、ディスクに指定したものやディスクを接続したタイミングに基づいて、5 GB になります。disk 2 を右クリックし、**[初期化]** を選択します。

	![Start initialization](./media/virtual-machines-windows-tutorial/initializedisk.png)

11. **[はい]** をクリックします。

	![Continue initialization](./media/virtual-machines-windows-tutorial/yesinitialize.png)

12. もう一度、disk 2 を右クリックし、**[新しいボリューム]** を選択します。 

	![Create the volume](./media/virtual-machines-windows-tutorial/initializediskvolume.png)

13. 既定の値を使用してウィザードを完了します。ウィザードが終了すると、**[ボリューム]** セクションに新しいボリュームが表示されます。ディスクがオンラインになり、データを格納できるようになります。 

	![Create the volume](./media/virtual-machines-windows-tutorial/newvolumecreated.png)
	
##次のステップ 

Azure での Windows 仮想マシンの構成に関する詳細については、次の記事を参照してください。

[クラウド サービス内の仮想マシンを相互に接続する方法](http://azure.microsoft.com/documentation/articles/cloud-services-connect-virtual-machine/)

[Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](http://azure.microsoft.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[仮想マシンの可用性管理](http://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/)

[Azure VM 構成設定について](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[ビデオ:Getting Started with VHDs - What's Really Happening (VHD の概要 - その実際)](http://azure.microsoft.com/documentation/videos/getting-started-with-azure-virtual-machines)

[ビデオ:FFAQ with Mark Russinovich - Does Windows Azure run Windows? (Mark Russinovich への質問と回答 - Windows Azure で Windows を実行できるか)](http://azure.microsoft.com/documentation/videos/mark-russinovich-windows-on-azure)

[ビデオ:Adding a new virtual machine to a Web Farm by making reusable images (再利用可能なイメージを作成して、新しい仮想マシンを Web ファームに追加する)](http://azure.microsoft.com/documentation/videos/adding-virtual-machines-web-farm)

[ビデオ:Adding Virtual Hard Drives, Storage Accounts, and Scaling Virtual Machines (仮想ハード ドライブの追加、ストレージ アカウント、仮想マシンのスケーリング)](http://azure.microsoft.com/documentation/videos/adding-drives-scaling-virtual-machines)

[ビデオ:Scott Guthrie starts with Virtual Machines (Scott Guthrie による仮想マシンの解説)](http://azure.microsoft.com/documentation/videos/virtual-machines-scottgu)

[ビデオ:Storage and Disk Basics with Azure Virtual Machines (Azure Virtual Machines のストレージとディスクの基本)](http://azure.microsoft.com/documentation/videos/storage-and-disks-virtual-machines)



[Azure での仮想マシンについて]: #virtualmachine
[仮想マシンの作成方法]: #custommachine
[仮想マシンを作成後、ログオンする方法]: #logon
[データ ディスクを仮想マシンに接続する方法]: #attachdisk
[仮想マシン間の通信をセットアップする方法]: #endpoints


<!--HONumber=42-->
