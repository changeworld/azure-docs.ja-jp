<properties title="Create a Virtual Machine Running Windows Server" pageTitle="Windows Server を実行する仮想マシンを作成する方法" description="Describes how to create a Windows virtual machine, add a data disk, and log on remotely" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

# Windows Server を実行する仮想マシンの作成 #

このチュートリアルは、Windows Azure の管理ポータルのイメージ ギャラリーを使用すると Windows Server を実行する Azure 仮想マシン (VM) を簡単に作成できることを示します。イメージ ギャラリーは、Windows オペレーティング システム、Linux ベースのオペレーティング システム、アプリケーション イメージなどさまざまなイメージを提供します。 

> [WACOM.NOTE]このチュートリアルは、Azure VM の使用経験がなくても完了できます。ただし、Azure アカウントが必要です。数分で無料の試用アカウントを作成することができます。詳細については、[Azure アカウントの作成に関するページ](http://www.windowsazure.com/ja-jp/develop/php/tutorials/create-a-windows-azure-account/)を参照してください。 

このチュートリアルは、以下について説明します。

- [仮想マシンの作成方法](#createvirtualmachine)
- [仮想マシンを作成後、ログオンする方法](#logon)
- [新しい仮想マシンにデータ ディスクを接続する方法](#attachdisk)

さらに詳しい内容は、「[仮想マシン](http://go.microsoft.com/fwlink/p/?LinkID=271224)」を参照してください。


##<a id="createvirtualmachine"> </a>仮想マシンの作成方法##

このセクションでは、管理ポータルの **[ギャラリーから]** オプションを使用して仮想マシンを作成する方法について説明します。このオプションは、**[簡易作成]** オプションよりも多数の構成の選択肢があります。たとえば、仮想マシンを仮想ネットワークに参加させる場合、**[ギャラリーから]** オプションを使用する必要があります。

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>仮想マシンを作成後、ログオンする方法 ##

このセクションでは、仮想マシンをログオンする方法について説明します。これによって、仮想マシン上で実行する設定とアプリケーションを管理できます。

1. Azure の[管理ポータル](http://manage.windowsazure.com)にサインインします。

2. **[仮想マシン]** をクリックし、**MyTestVM** 仮想マシンを選択します。

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)

3. コマンド バーで、**[接続]** をクリックします。

	![Connect to MyTestVM](./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png)
	
4. **[開く]** をクリックして、仮想マシンに自動的に作成されたリモート デスクトップ プロトコル ファイルを使用します。

	![Open the rdp file](./media/CreateVirtualMachineWindowsTutorial/openrdp.png)
	
5. **[接続]** をクリックします。

	![Continue with connecting](./media/CreateVirtualMachineWindowsTutorial/connectrdc.png)

6. 仮想マシンの作成時に指定したユーザー名とパスワードを入力し、**[OK]** をクリックします。

7. **[はい]** をクリックして、目的の仮想マシンであることを確認します。

	![Verify the identity of the machine](./media/CreateVirtualMachineWindowsTutorial/certificate.png)

	これで仮想マシンをオフィス内のサーバーとまったく同様に扱うことができます。

## <a id="attachdisk"> </a>新しい仮想マシンにデータ ディスクを接続する方法 ##

このセクションでは、空のデータ ディスクを仮想マシンに接続する方法について説明します。空のディスクの接続方法と、既存のディスクの接続方法の詳細については、「[データ ディスクを Windows 仮想マシンに接続する方法](http://www.windowsazure.com/ja-jp/documentation/articles/storage-windows-attach-disk/)」を参照してください。

1. Azure の[管理ポータル](http://manage.windowsazure.com)にサインインします。

2. **[仮想マシン]** をクリックし、**MyTestVM** 仮想マシンを選択します。

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)
	
3. 最初にクイック スタート ページが表示される場合があります。その場合は、上部にある **[ダッシュボード]** を選択します。

	![Select Dashboard](./media/CreateVirtualMachineWindowsTutorial/dashboard.png)

4. コマンド バーで、**[ディスクの接続]** をクリックし、表示される **[空のディスクの接続]** をクリックします。

	![Select Attach from the command bar](./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png)	

5. **[仮想マシン名]**、**[ストレージの場所]**、**[ファイル名]**、**[ホスト キャッシュの設定]** の値は既に定義されています。必要なのは、ディスクのサイズを入力することだけです。**[サイズ]** ボックスに「**5**」と入力します。次にチェック マークをクリックして、仮想マシンに空のデータ ディスクを接続します。

	![Specify the size of the empty disk](./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png)	
	
	>[WACOM.NOTE] ディスクはすべて、VHD ファイルから Windows Azure ストレージに作成されます。ストレージに追加する VHD ファイルの名前は **[ファイル名]** で指定できますが、ディスクの名前は Azure によって自動的に生成されます。

6. ダッシュボードに戻り、空のデータ ディスクが仮想マシンに正常に接続されたことを確認します。空のディスクは 2 番目のディスクとして **[ディスク]** 一覧に OS ディスクと共に表示されます。

	![Attach empty disk](./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png)

仮想マシンにデータ ディスクを接続した後も、ディスクはオフラインで初期化されていません。データ ディスクを使ってデータを保存する前に、仮想マシンにログオンして、ディスクを初期化する必要があります。

7. 仮想マシンに接続するには、前のセクション「[仮想マシンを作成後、ログオンする方法]」の手順を使用します。(#logon)

8. 仮想マシンにログオンした後、**サーバー マネージャー**を開きます。左側のウィンドウで、**[ファイル サービスと記憶域サービス]** を選択します。

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png)

9. 展開したメニューの **[ディスク]** を選択します。

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/selectdisks.png)	
	
10. **[ディスク]** セクションには、[disk 0]、[disk 1]、[disk 2] の 3 種類のディスクが一覧表示されます。disk 0 は OS ディスク、disk 1 は一時リソース ディスク (データ ストレージとして使用しないでください) です。disk 2 は、仮想マシンに接続したデータ ディスクです。以前に指定したように、データ ディスクは 5 GB の容量があります。disk 2 を右クリックし、**[初期化]** を選択します。

	![Start initialization](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

11. **[はい]** をクリックして初期化処理を開始します。

	![Continue initialization](./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png)

12. もう一度、disk 2 を右クリックし、**[新しいボリューム]** を選択します。 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

13. 提供される既定の値を使用してウィザードを完了します。ウィザードを終了すると、新しいボリュームが **[ボリューム]** セクションに一覧表示されます。 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png)

	これでディスクがオンラインになり、新しいドライブ文字が使用できるようになりました。 
	
##次のステップ 

Azure での Windows 仮想マシンの構成に関する詳細については、次の記事を参照してください。

[クラウド サービス内の仮想マシンを相互に接続する方法](http://www.windowsazure.com/ja-jp/documentation/articles/cloud-services-connect-virtual-machine/)

[Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](http://www.windowsazure.com/ja-jp/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[データ ディスクを Virtual Machine に接続する方法](http://www.windowsazure.com/ja-jp/documentation/articles/storage-windows-attach-disk/)

[仮想マシンの可用性管理](http://www.windowsazure.com/ja-jp/documentation/articles/manage-availability-virtual-machines/)

[Azure での仮想マシンについて]: #virtualmachine
[仮想マシンの作成方法]: #custommachine
[仮想マシンを作成後、ログオンする方法]: #logon
[新しい仮想マシンにデータ ディスクを接続する方法]: #attachdisk
[仮想マシン間の通信をセットアップする方法]: #endpoints


