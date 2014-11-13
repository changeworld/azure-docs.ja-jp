<properties urlDisplayName="Create a virtual machine" pageTitle="Azure 上で Windows を実行する仮想マシンの作成" metaKeywords="Azure capture image vm, capturing vm" description="Azure で Windows 仮想マシン (VM) を作成し、ログオンしてデータ ディスクを接続する方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav, rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="kathydav" />

# Windows を実行する仮想マシンの作成

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/virtual-machines-windows-tutorial/" title="Azure ポータル" class="current">Azure ポータル</a><a href="/ja-jp/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure プレビュー ポータル">Azure プレビュー ポータル</a></div>

このチュートリアルは、Azure の管理ポータルのイメージ ギャラリーに用意されている Windows Server イメージを例として使用して、Windows を実行する Azure 仮想マシン (VM) を簡単に作成できることを示します。イメージ ギャラリーは、Windows オペレーティング システム、Linux ベースのオペレーティング システム、アプリケーション イメージなどさまざまなイメージを提供します。

> [WACOM.NOTE] このチュートリアルは、Azure VM の使用経験がなくても完了できます。ただし、Azure アカウントが必要です。数分で無料の試用アカウントを作成することができます。詳細については、[Azure アカウントの作成に関するページ][Azure アカウントの作成に関するページ]を参照してください。

このチュートリアルは、以下について説明します。

-   [仮想マシンの作成方法][仮想マシンの作成方法]
-   [仮想マシンを作成後、ログオンする方法][仮想マシンを作成後、ログオンする方法]
-   [新しい仮想マシンにデータ ディスクを接続する方法][新しい仮想マシンにデータ ディスクを接続する方法]

さらに詳しい内容は、「[仮想マシン][仮想マシン]」を参照してください。

## <span id="createvirtualmachine"></span> </a>仮想マシンの作成方法

このセクションでは、管理ポータルのオプションの **[ギャラリーから]** を使用して仮想マシンを作成する方法について説明します。このオプションは、**[簡易作成]** オプションよりも多数の構成の選択肢があります。たとえば、仮想マシンを仮想ネットワークに参加させる場合、**[ギャラリーから]** オプションを使用する必要があります。

> [WACOM.NOTE] ギャラリーから利用できるイメージの数と種類は、お持ちのサブスクリプションの種類によって異なります。このチュートリアルでは Windows Server イメージを使用しますが、MSDN サブスクリプションがあれば、デスクトップ イメージを含め、さらに多くのイメージを利用できます。

> さらに、より機能が豊富でカスタマイズも可能な [Azure プレビュー ポータル][1]を使用すれば、仮想マシンの作成、複数マシンのアプリケーション テンプレートのデプロイの自動化、VM の強化された監視や診断の機能の使用など、さまざまな操作を試すことができます。この 2 つのポータルにある利用可能な VM の構成オプションには、性質上、重複するものもありますが、まったく同じではありません。

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <span id="logon"></span> </a>仮想マシンを作成後、ログオンする方法

このセクションでは、仮想マシンをログオンする方法について説明します。これによって、仮想マシン上で実行する設定とアプリケーションを管理できます。

[WACOM.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <span id="attachdisk"></span> </a>新しい仮想マシンにデータ ディスクを接続する方法

このセクションでは、空のデータ ディスクを仮想マシンに接続する方法について説明します。空のディスクの接続方法と、既存のディスクの接続方法の詳細については、「[データ ディスクを Virtual Machine に接続する方法][データ ディスクを Virtual Machine に接続する方法]」を参照してください。

1.  [Azure 管理ポータル][Azure 管理ポータル]にサインインします。

2.  **[仮想マシン]** をクリックし、次に **[MyTestVM]** 仮想マシンを選択します。

    ![Select MyTestVM][Select MyTestVM]

3.  最初にクイック スタート ページが表示される場合があります。その場合は、上部にある **[ダッシュボード]** を選択します。

    ![Select Dashboard][Select Dashboard]

4.  コマンド バーで、**[ディスクの接続]** をクリックし、表示される **[空のディスクの接続]** をクリックします。

    ![Select Attach from the command bar][Select Attach from the command bar]

5.  **[仮想マシン名]**、**[ストレージの場所]**、**[ファイル名]**、**[ホスト キャッシュの設定]** の値は既に定義されています。必要なのは、ディスクのサイズを入力することだけです。**[サイズ]** ボックスに「**5**」と入力します。次にチェック マークをクリックして、仮想マシンに空のデータ ディスクを接続します。

    > [WACOM.NOTE] Azure のディスク イメージはページ BLOB として Azure Storage に格納されることに注意してください。Azure 以外では、仮想ハード ディスクには VHD フォーマットまたは VHDX フォーマットを使用でき、固定、動的拡張、差分にも対応しています。Azure は VHD フォーマットの固定ディスクをサポートしています。固定フォーマットの場合、ファイル内で論理ディスクがリニアにレイアウトされるため、ディスク オフセット X は BLOB オフセット X に格納されます。BLOB 末尾の小さなフッターに、VHD のプロパティが記述されます。固定フォーマットの場合、ほとんどのディスクに大きな未使用の範囲が含まれるため、容量が無駄になることがよくあります。しかし、Azure では .vhd ファイルをスパース フォーマットで格納するため、固定ディスクのメリットと動的ディスクのメリットを同時に享受できます。この点の詳細については、「[About VHDs in Azure (Azure の VHD について)][About VHDs in Azure (Azure の VHD について)]」を参照してください。

    ![Specify the size of the empty disk][Specify the size of the empty disk]

    > [WACOM.NOTE] ディスクはすべて、VHD ファイルから Windows Azure のストレージに作成されます。ストレージに追加する VHD ファイルの名前は **[ファイル名]** で指定できますが、ディスクの名前は Azure によって自動的に生成されます。

6.  ダッシュボードに戻り、空のデータ ディスクが仮想マシンに正常に接続されたことを確認します。空のディスクは 2 番目のディスクとして **[ディスク]** 一覧に OS ディスクと共に表示されます。

    ![空のディスクの接続][空のディスクの接続]

    仮想マシンにデータ ディスクを接続した後も、ディスクはオフラインで初期化されていません。データ ディスクを使ってデータを保存する前に、仮想マシンにログオンして、ディスクを初期化する必要があります。

7.  仮想マシンに接続するには、前のセクション「[仮想マシンを作成後、ログオンする方法][仮想マシンを作成後、ログオンする方法]」(\#logon) の手順を使用します。

8.  仮想マシンにログオンした後、**サーバー マネージャー**を開きます。左側のウィンドウで、**[ファイル サービスと記憶域サービス]** を選択します。

    ![Expand File and Storage Services in Server Manager][Expand File and Storage Services in Server Manager]

9.  展開したメニューの **[ディスク]** を選択します。

    ![Expand File and Storage Services in Server Manager][2]

10. **[ディスク]** セクションの一覧には、disk 0、disk 1、disk 2 の 3 つのディスクがあります。disk 0 は OS ディスクで、disk 1 は一時的なリソース ディスク (データ ストレージ用には使用できない) です。disk 2 は仮想マシンに接続したデータ ディスクです。以前に指定したように、データ ディスクは 5 GB の容量があります。disk 2 を右クリックし、**[初期化]** を選択します。

    ![Start initialization][Start initialization]

11. **[はい]** をクリックして初期化処理を開始します。

    ![Continue initialization][Continue initialization]

12. もう一度、disk 2 を右クリックし、**[ボリューム]** を選択します。

    ![Create the volume][Create the volume]

13. 提供される既定の値を使用してウィザードを完了します。ウィザードを終了すると、新しいボリュームが **[ボリューム]** セクションに一覧表示されます。

    ![Create the volume][3]

    これでディスクがオンラインになり、新しいドライブ文字が使用できるようになりました。

## 次のステップ

Azure での Windows 仮想マシンの構成に関する詳細については、次の記事を参照してください。

[クラウド サービス内の仮想マシンを相互に接続する方法][クラウド サービス内の仮想マシンを相互に接続する方法]

[Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード][Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード]

[仮想マシンの可用性管理][仮想マシンの可用性管理]

[Azure VM の構成設定について][Azure VM の構成設定について]

[VIDEO: Getting Started with VHDs - What's Really Happening (ビデオ: VHD の概要 - その実際)][VIDEO: Getting Started with VHDs - What's Really Happening (ビデオ: VHD の概要 - その実際)]

[VIDEO: FAQ with Mark Russinovich - Does Windows Azure run Windows? (ビデオ: Mark Russinovich への質問と回答 - Windows Azure で Windows を実行できるか)][VIDEO: FAQ with Mark Russinovich - Does Windows Azure run Windows? (ビデオ: Mark Russinovich への質問と回答 - Windows Azure で Windows を実行できるか)]

[VIDEO: Adding a new virtual machine to a Web Farm by making reusable images (ビデオ: 再利用可能なイメージを作成して、新しい仮想マシンを Web ファームに追加する)][VIDEO: Adding a new virtual machine to a Web Farm by making reusable images (ビデオ: 再利用可能なイメージを作成して、新しい仮想マシンを Web ファームに追加する)]

[VIDEO: Adding Virtual Hard Drives, Storage Accounts, and Scaling Virtual Machines (ビデオ: 仮想ハード ドライブの追加、ストレージ アカウント、仮想マシンのスケーリング)][VIDEO: Adding Virtual Hard Drives, Storage Accounts, and Scaling Virtual Machines (ビデオ: 仮想ハード ドライブの追加、ストレージ アカウント、仮想マシンのスケーリング)]

[VIDEO: Scott Guthrie starts with Virtual Machines (ビデオ: Scott Guthrie による仮想マシンの解説)][VIDEO: Scott Guthrie starts with Virtual Machines (ビデオ: Scott Guthrie による仮想マシンの解説)]

[VIDEO: Storage and Disk Basics with Azure Virtual Machines (ビデオ: Azure Virtual Machines のストレージとディスクの基本)][VIDEO: Storage and Disk Basics with Azure Virtual Machines (ビデオ: Azure Virtual Machines のストレージとディスクの基本)]

  [Azure プレビュー ポータル]: /ja-jp/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Azure プレビュー ポータル"
  [Azure アカウントの作成に関するページ]: http://www.windowsazure.com/ja-jp/develop/php/tutorials/create-a-windows-azure-account/
  [仮想マシンの作成方法]: #createvirtualmachine
  [仮想マシンを作成後、ログオンする方法]: #logon
  [新しい仮想マシンにデータ ディスクを接続する方法]: #attachdisk
  [仮想マシン]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [1]: https://portal.azure.com
  [データ ディスクを Virtual Machine に接続する方法]: http://www.windowsazure.com/ja-jp/documentation/articles/storage-windows-attach-disk/
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [Select MyTestVM]: ./media/virtual-machines-windows-tutorial/selectvm.png
  [Select Dashboard]: ./media/virtual-machines-windows-tutorial/dashboard.png
  [Select Attach from the command bar]: ./media/virtual-machines-windows-tutorial/commandbarattach.png
  [About VHDs in Azure (Azure の VHD について)]: http://msdn.microsoft.com/ja-jp/library/azure/dn790344.aspx
  [Specify the size of the empty disk]: ./media/virtual-machines-windows-tutorial/emptydisksize.png
  [空のディスクの接続]: ./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png
  [Expand File and Storage Services in Server Manager]: ./media/virtual-machines-windows-tutorial/fileandstorageservices.png
  [2]: ./media/virtual-machines-windows-tutorial/selectdisks.png
  [Start initialization]: ./media/virtual-machines-windows-tutorial/initializedisk.png
  [Continue initialization]: ./media/virtual-machines-windows-tutorial/yesinitialize.png
  [Create the volume]: ./media/virtual-machines-windows-tutorial/initializediskvolume.png
  [3]: ./media/virtual-machines-windows-tutorial/newvolumecreated.png
  [クラウド サービス内の仮想マシンを相互に接続する方法]: http://www.windowsazure.com/ja-jp/documentation/articles/cloud-services-connect-virtual-machine/
  [Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード]: http://www.windowsazure.com/ja-jp/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [仮想マシンの可用性管理]: http://www.windowsazure.com/ja-jp/documentation/articles/manage-availability-virtual-machines/
  [Azure VM の構成設定について]: http://msdn.microsoft.com/library/azure/dn763935.aspx
  [VIDEO: Getting Started with VHDs - What's Really Happening (ビデオ: VHD の概要 - その実際)]: http://azure.microsoft.com/ja-jp/documentation/videos/getting-started-with-azure-virtual-machines
  [VIDEO: FAQ with Mark Russinovich - Does Windows Azure run Windows? (ビデオ: Mark Russinovich への質問と回答 - Windows Azure で Windows を実行できるか)]: http://azure.microsoft.com/ja-jp/documentation/videos/mark-russinovich-windows-on-azure
  [VIDEO: Adding a new virtual machine to a Web Farm by making reusable images (ビデオ: 再利用可能なイメージを作成して、新しい仮想マシンを Web ファームに追加する)]: http://azure.microsoft.com/ja-jp/documentation/videos/adding-virtual-machines-web-farm
  [VIDEO: Adding Virtual Hard Drives, Storage Accounts, and Scaling Virtual Machines (ビデオ: 仮想ハード ドライブの追加、ストレージ アカウント、仮想マシンのスケーリング)]: http://azure.microsoft.com/ja-jp/documentation/videos/adding-drives-scaling-virtual-machines
  [VIDEO: Scott Guthrie starts with Virtual Machines (ビデオ: Scott Guthrie による仮想マシンの解説)]: http://azure.microsoft.com/ja-jp/documentation/videos/virtual-machines-scottgu
  [VIDEO: Storage and Disk Basics with Azure Virtual Machines (ビデオ: Azure Virtual Machines のストレージとディスクの基本)]: http://azure.microsoft.com/ja-jp/documentation/videos/storage-and-disks-virtual-machines
