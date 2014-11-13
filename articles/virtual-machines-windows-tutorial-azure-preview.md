<properties urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する" metaKeywords="Azure image gallery vm" description="Windows を実行する Azure 仮想マシン (VM) を、Azure プレビュー ポータルの VM ギャラリーを利用して作成する方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する" authors="danlep,kathydav,rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep,kathydav,rasquill" />

# Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/virtual-machines-windows-tutorial/" title="Azure ポータル">Azure ポータル</a><a href="/ja-jp/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure プレビュー ポータル" class="current">Azure プレビュー ポータル</a></div>

このチュートリアルは、Azure プレビュー ポータルのイメージ ギャラリーに用意されている Windows Server イメージを例として使用して、Windows を実行する Azure 仮想マシン (VM) を簡単に作成できることを示します。このギャラリーは、Windows オペレーティング システム、Linux ベースのオペレーティング システム、アプリケーション イメージなどさまざまなイメージを提供します。

> [WACOM.NOTE] このチュートリアルは、Azure VM の使用経験がなくても完了できます。ただし、Azure アカウントが必要です。数分で無料の試用アカウントを作成することができます。詳細については、[Azure アカウントの作成に関するページ][Azure アカウントの作成に関するページ]を参照してください。

このチュートリアルは、以下について説明します。

-   [仮想マシンの作成方法][仮想マシンの作成方法]
-   [仮想マシンを作成後、ログオンする方法][仮想マシンを作成後、ログオンする方法]

さらに詳しい内容は、「[仮想マシン][仮想マシン]」を参照してください。

## <span id="createvirtualmachine"></span> </a>仮想マシンの作成方法

このセクションでは、Windows Server を例として使用して、プレビュー ポータルで VM を作成する方法について説明します。ほとんどの構成に Azure の既定の設定を使用でき、ほんの数分で VM を作成できます。

> [WACOM.NOTE] ギャラリーから利用できるイメージの数と種類は、お持ちのサブスクリプションの種類によって異なります。このチュートリアルでは Windows Server イメージを使用しますが、MSDN サブスクリプションがあれば、デスクトップ イメージを含め、さらに多くのイメージを利用できます。

1.  [Azure プレビュー ポータル][1]にサインインします。まだサブスクリプションをお持ちでない場合は、[無料評価版][無料評価版]をお試しください。

2.  ハブ メニューの **[新規]** をクリックします。

    ![Select New from the Command Bar][Select New from the Command Bar]

3.  **[新規]** の **[すべて]** をクリックし、**[ギャラリー]** の下の **[仮想マシン]** をクリックします。**[Windows Server 2012 R2 Datacenter]** をクリックします。その画面の **[作成]** をクリックします。

    ![Select a VM image from the Gallery][Select a VM image from the Gallery]

4.  **[VM の作成]** ブレードで、**[ホスト名]** に VM の名前を入力し、管理 **[ユーザー名]** と強力な **[パスワード]** を入力します。

    ![Configure host name and log on credentials][Configure host name and log on credentials]

    > [WACOM.NOTE] **[ユーザー名]** とは、サーバーの管理に使用する管理アカウントの名前です。このアカウント用の一意のパスワードを作成し、忘れないように記憶してください。**仮想マシンにログオンする際に、このユーザー名とパスワードが必要になります。**

5.  残りの VM オプションに既定の設定を使用し、VM の作成を開始するには、**[作成]** をクリックします。必要に応じて、**[作成]** をクリックする前に、**[オプションの構成]** 設定を確認することができます。たとえば、後で VM にオプションの診断を構成し、VM の多数のメトリックを追跡できます。**[オプションの構成]**、**[診断]** の順にクリックし、**[状態]** を **[オン]** に切り替えます。

    ![Turn on VM diagnostics][Turn on VM diagnostics]

    > [WACOM.NOTE] Azure 診断をオンにすると、Azure ストレージ アカウントに診断データが保存され、ストレージ コストが増加します。

6.  Azure が VM を作成している間の進捗状況は、ハブ メニューの **[通知]** で確認できます。作成された VM は、スタートボードに表示されます。

    ![VM appears on the Startboard][VM appears on the Startboard]

## <span id="logon"></span> </a>仮想マシンを作成後、ログオンする方法

このセクションでは、VM をログオンする方法について説明します。これによって、仮想マシン上で実行する設定とアプリケーションを管理できます。

> [WACOM.NOTE] 要件とトラブルシューティングのヒントについては、「[RDP または SSH を使用した Azure 仮想マシンへの接続][RDP または SSH を使用した Azure 仮想マシンへの接続]」を参照してください。

1.  まだサインインしていない場合は、[Azure プレビュー ポータル][1]にサインインします。

2.  スタートボードの VM をクリックします。VM を探す場合は、**[参照]** をクリックし、次に **[仮想マシン]** をクリックします。

    ![Browse to find the VM][Browse to find the VM]

3.  VM ブレードで、上部の **[接続]** をクリックします。

    ![仮想マシンへのログオン][仮想マシンへのログオン]

4.  **[開く]** をクリックして、仮想マシン用に自動的に作成されたリモート デスクトップ プロトコル ファイルを使用します。

5.  **[接続]** をクリックして接続処理を続行します。

    ![接続の続行][接続の続行]

6.  仮想マシンで管理アカウントのユーザー名とパスワードを入力して、**[OK]** をクリックします。

7.  **[はい]** をクリックして、目的の仮想マシンであることを確認します。

    ![目的の仮想マシンであることを確認][目的の仮想マシンであることを確認]

    これで、仮想マシンを他のサーバーとまったく同様に扱うことができます。

## 次のステップ

Azure での Windows 仮想マシンの構成に関する詳細については、次の記事を参照してください。

[クラウド サービス内の仮想マシンを相互に接続する方法][クラウド サービス内の仮想マシンを相互に接続する方法]

[Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード][Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード]

[データ ディスクを Virtual Machine に接続する方法][データ ディスクを Virtual Machine に接続する方法]

[仮想マシンの可用性管理][仮想マシンの可用性管理]

[Azure VM の構成設定について][Azure VM の構成設定について]

  [Azure プレビュー ポータル]: /ja-jp/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Azure プレビュー ポータル"
  [Azure アカウントの作成に関するページ]: http://www.windowsazure.com/ja-jp/develop/php/tutorials/create-a-windows-azure-account/
  [仮想マシンの作成方法]: #createvirtualmachine
  [仮想マシンを作成後、ログオンする方法]: #logon
  [仮想マシン]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [1]: https://portal.azure.com
  [無料評価版]: http://www.windowsazure.com/ja-jp/pricing/free-trial/
  [Select New from the Command Bar]: ./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png
  [Select a VM image from the Gallery]: ./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png
  [Configure host name and log on credentials]: ./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png
  [Turn on VM diagnostics]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_diagnostics_status_preview_portal.png
  [VM appears on the Startboard]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png
  [RDP または SSH を使用した Azure 仮想マシンへの接続]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Browse to find the VM]: ./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png
  [仮想マシンへのログオン]: ./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png
  [接続の続行]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [目的の仮想マシンであることを確認]: ./media/virtual-machines-log-on-windows-server/connectverify.png
  [クラウド サービス内の仮想マシンを相互に接続する方法]: http://www.windowsazure.com/ja-jp/documentation/articles/cloud-services-connect-virtual-machine/
  [Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード]: http://www.windowsazure.com/ja-jp/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [データ ディスクを Virtual Machine に接続する方法]: http://www.windowsazure.com/ja-jp/documentation/articles/storage-windows-attach-disk/
  [仮想マシンの可用性管理]: http://www.windowsazure.com/ja-jp/documentation/articles/manage-availability-virtual-machines/
  [Azure VM の構成設定について]: http://msdn.microsoft.com/library/azure/dn763935.aspx
