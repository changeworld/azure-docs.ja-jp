<properties pageTitle="Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する" description="Windows を実行する Azure 仮想マシン (VM) を、Azure プレビュー ポータルの Azure Marketplace を利用して作成する方法について説明します。" services="virtual-machines" documentationCenter="" authors="dlepow" manager="timlt"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/13/2015" ms.author="danlep,kathydav,rasquill"/>

<!---Workflow can be confusing...have to select the correct size with **Browse all pricing tiers** then click Select on that pane and the **Recommended pricing tiers** pane to apply. But even after that, it didn't seem to pick the Premium storage account...showed up as 'standard GRS' when I inspected what type of storage account would be used for the VM. 
-->

<!-- Preview portal screenshots getting a little out of date. Please refresh at next update.
-->

# Azure プレビュー ポータルで仮想マシンを作成する#

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal">Azure ポータル</a><a href="/ja-jp/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal" class="current">Azure プレビュー ポータル</a></div>

このチュートリアルは、Azure プレビュー ポータルの Azure Marketplace に用意されている Windows Server イメージを例として使用しながら、Windows を実行する Azure 仮想マシン (VM) をいかに簡単に作成できるかを説明します。Marketplace では、Windows オペレーティング システム、Linux ベースのオペレーティング システム、アプリケーション イメージなどさまざまなイメージを提供しています。 

> [AZURE.NOTE] このチュートリアルは、Azure VM の使用経験がなくても完了できます。ただし、Azure アカウントが必要です。数分で無料の試用アカウントを作成することができます。詳細については、「[Azure アカウントの作成](http://www.windowsazure.com/ja-jp/develop/php/tutorials/create-a-windows-azure-account/)」を参照してください。 

このチュートリアルは、以下について説明します。

- [仮想マシンの作成方法](#createvirtualmachine)
- [仮想マシンを作成後、ログオンする方法](#logon)

さらに詳しい内容は、「[仮想マシン](http://go.microsoft.com/fwlink/p/?LinkID=271224)」を参照してください。


##<a id="createvirtualmachine"> </a>仮想マシンの作成方法##

このセクションでは、Windows Server 2012 R2 Datacenter を例として使用して、プレビュー ポータルで VM を作成する方法について説明します。ほとんどの構成に Azure の既定の設定を使用でき、ほんの数分で VM を作成できます。

> [AZURE.NOTE] 使用できるイメージは、サブスクリプションによって異なります。このチュートリアルでは Windows Server イメージを使用しますが、MSDN サブスクリプションがあれば、デスクトップ イメージを含め、さらに多くのイメージを利用できます。 
 

1. [Azure プレビュー ポータル](https://portal.azure.com)にサインインします。まだサブスクリプションをお持ちでない場合は、[無料評価版](http://www.windowsazure.com/ja-jp/pricing/free-trial/)をお試しください。

2. ハブ メニューの **[新規]** をクリックします。

	![Select New from the Command Bar](./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png)

3. **[新規]** ブレードで、**[すべて]**、**[仮想マシン]**、**[Windows Server 2012 R2 Datacenter]**、**[作成]** の順にクリックします。

	![Select a VM image from the Gallery](./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png)
	
4. **[VM の作成]** ブレードで、**[ホスト名]** に VM の名前を入力し、管理 **[ユーザー名]** と強力な **[パスワード]** を入力します。

	>[AZURE.NOTE] **[ユーザー名]** とは、サーバーの管理に使用する管理アカウントの名前です。このアカウント用の一意のパスワードを作成し、忘れないように記憶してください。**仮想マシンにログオンする際に、このユーザー名とパスワードが必要になります**。
	

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_name_pwd_preview_portal.png)
	
	
5. **[料金レベル]**、**[オプションの構成]**、**[場所]** などの既定の設定を確認します。これらの選択は、ドメイン メンバーシップなどのネットワークのオプションと VM のサイズに影響します。たとえば、仮想マシンでプレミアム ストレージを使用するには、プレミアム ストレージをサポートするリージョンとサイズを選択する必要があります。 

	>[AZURE.NOTE] プレミアム ストレージはプレビューでは、特定のリージョンの DS シリーズの仮想マシンで使用できます。詳細については、「[Premium Storage:High-Performance Storage for Azure Virtual Machine Workloads (Premium Storage: Azure 仮想マシンのワークロード向け高パフォーマンス ストレージ)](http://azure.microsoft.com/ja-jp/documentation/articles/storage-premium-storage-preview-portal/)」を参照してください。

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png)
	
6. 設定の確認または更新を終了したら、**[作成]** をクリックします。	

7. Azure が VM を作成している間の進捗状況は、ハブ メニューの **[通知]** で確認できます。作成された VM は、スタートボードに表示されます。

	![VM appears on the Startboard](./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png)

## <a id="logon"> </a>仮想マシンを作成後、ログオンする方法 ##

このセクションでは、VM をログオンする方法について説明します。これによって、仮想マシン上で実行する設定とアプリケーションを管理できます。

>[AZURE.NOTE] 要件やトラブルシューティングのヒントについては、「[RDP または SSH で Azure 仮想マシンに接続する](http://go.microsoft.com/fwlink/p/?LinkId=398294)」を参照してください。

1. まだサインインしていない場合は、[Azure プレビュー ポータル](https://portal.azure.com) にサインインします。

2. スタートボードの VM をクリックします。VM を探す場合は、**[参照]** をクリックし、次に **[仮想マシン]** をクリックします。 

	![Browse to find the VM](./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png)

3. その後、一覧から VM を選択します。 

	![Select your VM name from the list](./media/virtual-machines-windows-tutorial-azure-preview/vm_select_preview_portal.png)

4. VM ブレードで、上部の **[接続]** をクリックします。

	![Log on to the virtual machine](./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png)

5. **[開く]** をクリックして、仮想マシン用に自動的に作成されたリモート デスクトップ プロトコル ファイルを使用します。
	
6. **[接続]** をクリックして接続処理を続行します。

	![Continue with connecting](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

7. 仮想マシンで管理アカウントのユーザー名とパスワードを入力して、**[OK]** をクリックします。
	
8. **[はい]** をクリックして、目的の仮想マシンであることを確認します。

	![Verify the identity of the machine](./media/virtual-machines-log-on-windows-server/connectverify.png)

	これで、仮想マシンを他のサーバーとまったく同様に扱うことができます。

##次のステップ 

Azure での Windows 仮想マシンの構成に関する詳細については、次の記事を参照してください。

[クラウド サービス内の仮想マシンを相互に接続する方法](http://www.windowsazure.com/ja-jp/documentation/articles/cloud-services-connect-virtual-machine/)

[Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](http://www.windowsazure.com/ja-jp/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[データ ディスクを仮想マシンに接続する方法](http://www.windowsazure.com/ja-jp/documentation/articles/storage-windows-attach-disk/)

[仮想マシンの可用性管理](http://www.windowsazure.com/ja-jp/documentation/articles/manage-availability-virtual-machines/)

[Azure VM 構成設定について](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[仮想マシンの作成方法]: #custommachine
[仮想マシンを作成後、ログオンする方法]: #logon


<!--HONumber=42-->
