<properties
   pageTitle="StorSimple Virtual Array をデプロイする - VMware でプロビジョニングする"
   description="StorSimple Virtual Array のデプロイ シリーズの 2 番目のチュートリアルには、VMware での仮想デバイスのプロビジョニングが含まれます。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="alkohli"/>


# StorSimple Virtual Array をデプロイする - VMware で Virtual Array をプロビジョニングする (プレビュー)

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## 概要 
このプロビジョニング チュートリアルは、v 1.1.1.0 (パブリック プレビュー) を実行する StorSimple Virtual Array (StorSimple オンプレミス仮想デバイスまたはStorSimple 仮想デバイスとも呼ばれます) にのみ適用されます。このチュートリアルでは、VMware ESXi 5.5 以降を実行するホスト システム上に StorSimple Virtual Array をプロビジョニングして接続する方法について説明します。

仮想デバイスをプロビジョニングして接続するには、管理者特権が必要です。プロビジョニングと初期セットアップは、完了するまでに約 10 分かかることがあります。

> [AZURE.IMPORTANT]
> 
> このパブリック プレビューは、評価のみを目的としています。このプレビューの運用環境へのインストールはサポートされていません。

## プロビジョニングの前提条件

ここでは、VMware ESXi 5.5 を実行しているホスト システム上に仮想デバイスをプロビジョニングするための前提条件について説明します。

### StorSimple Manager サービスの場合

開始する前に次の点を確認します。

-   [StorSimple Virtual Array のポータルの準備](storsimple-ova-deploy1-portal-prep.md)に関するページの手順がすべて完了していること。

-   Azure ポータルから VMware の仮想デバイスのイメージをダウンロードしていること。詳細については、「[手順 3: 仮想デバイスのイメージをダウンロードする](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image)」を参照してください。

### StorSimple 仮想デバイスの場合 

仮想デバイスをデプロイする前に次の点を確認します。

-   Hyper-V (2008 R2 以降) を実行し、デバイスのプロビジョニングに使用できるホスト システムへのアクセス権があること。

-   ホスト システムで、次のリソースを仮想デバイスのプロビジョニング専用に使用できること。

	-   最小で 4 コア。

	-   少なくとも 8 GB の RAM。

	-   ネットワーク インターフェイス 1 つ。

	-   システム データ用の 500 GB の仮想ディスク。

### データセンターのネットワークの場合 

開始する前に次の点を確認します。

-   StorSimple 仮想デバイスをデプロイするためのネットワーク要件を確認し、その要件に従ってデータセンター ネットワークを構成していること。詳細については、Microsoft Azure StorSimple Virtual Array のシステム要件ガイドを参照してください。

## プロビジョニングの手順 

仮想デバイスをプロビジョニングして接続するには、次の手順を実行する必要があります。

1.  ホスト システムに仮想デバイスの最小要件を満たすための十分なリソースがあることを確認します。

2.  ハイパーバイザーで仮想デバイスをプロビジョニングします。

3.  仮想デバイスを起動し、IP アドレスを取得します。

## 手順 1: ホスト システムが仮想デバイスの最小要件を満たしていることを確認する

仮想デバイスを作成するには、次の要件が必要です。

-   VMware ESXi サーバー 5.5 以降を実行しているホスト システムへのアクセス権があること。

-   システムに、ESXi ホストを管理するための VMware vSphere client があること。

	-   最小で 4 コア。

	-   少なくとも 8 GB の RAM。

	-   トラフィックをインターネットにルーティングできるネットワークに接続している 1 つのネットワーク インターフェイス。インターネットの最小帯域幅は、デバイスが最適に動作するように 5 Mbps にする必要があります。

	-   データ用の 500 GB の仮想ディスク。

## 手順 2: ハイパーバイザーで仮想デバイスをプロビジョニングする

ハイパーバイザーで仮想デバイスをプロビジョニングするには、次の手順を実行します。

1.  システム上にある仮想デバイスのイメージをコピーします。これは、Azure クラシック ポータルからダウンロードしたイメージです。このイメージは手順の後半で使用するため、コピーした場所をメモしておきます。

2.  vSphere client を使用して、ESXi サーバーにログインします。仮想マシンを作成するには、管理者特権を持っている必要があります。

	![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  vSphere client の左側のウィンドウのインベントリ セクションで、ESXi サーバーを選択します。

	![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  まず、VMDK を ESXi サーバーにアップロードします。右側のウィンドウの **[Configuration]** タブに移動します。**[Hardware]** で **[Storage]** を選択します。

	![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  右側のウィンドウの **[Datastores]** で VMDK をアップロードするデータストアを選択します。データストアには、OS とデータ ディスク用の十分な空き領域が必要です。

	![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  右クリックして **[Browse Datastore]** を選択します。

	![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  A **Datastore Browser** のウィンドウが表示されます。

	![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  ツール バーで ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) アイコンをクリックして新しいフォルダーを作成します。フォルダー名を指定して、メモしておきます。このフォルダー名は後で仮想マシンを作成するときに使用します (推奨されるベスト プラクティス)。**[OK]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  **Datastore Browser** の左側のウィンドウに新しいフォルダーが表示されます。

	![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  アップロード アイコン ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) をクリックして、**[Upload File]** を選択します。

	![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  ここで、ダウンロードした VMDK を参照してポイントする必要があります。

	![](./media/storsimple-ova-deploy2-provision-vmware/image12.png)

1.  **[Open]** をクリックします。指定されたデータストアへの VMDK ファイルのアップロードが開始されます。

	![](./media/storsimple-ova-deploy2-provision-vmware/image13.png)

1.  ファイルのアップロードには数分かかる場合があります。アップロードが完了すると、作成したフォルダー内にデータストアのファイルが表示されます。

	![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

1.  vSphere client のウィンドウに戻ります。ESXi サーバーを選択して右クリックし、**[新しい仮想マシン]** を選択します。

	![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  A **[New Virtual Machine]** ウィンドウが表示されます。**[Configuration]** ページで **[Custom]** を選択します。**[Next]** をクリックします。![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  **[Name and Location]** ページで、仮想マシンの名前を指定します。この名前は、手順 8 で指定したフォルダー名と一致する必要があります (推奨されるベスト プラクティス)。

	![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  **[Storage]** ページで、VM のプロビジョニングに使用するデータストアを選択します。

	![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  **[Virtual Machine Version]** ページで **[Virtual Machine Version: 8]** を選択します。今回のリリースでは、このオプションのみがサポートされていることに注意してください。

	![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  **[Guest Operating System]** ページで、**[Guest Operating System]** に **[Windows]** を選択します。**[Version]** には、ドロップダウンリストから **[Microsoft Windows Server 2012 (64-bit)]** を選択します。

	![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  **[CPUs]** ページで、**[Number of virtual sockets]** と **[Number of cores per virtual socket]** を **[Total number of cores]** が 4 (またはそれ以上) になるように調整します。**[Next]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  **[Memory]** ページで RAM を 8 GB (またはそれ以上) に指定します。**[Next]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  **[Network]** ページでネットワーク インターフェイスの数を指定します。ネットワーク インターフェイスの最小要件は 1 つです。

	![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  **[SCSI Controller]** ページで、規定値の **[LSI Logic SAS]** をそのまま使用します。

	![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  **[Select a Disk]** ページで **[Use an existing virtual disk]** を選択します。**[Next]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  **[Select Existing Disk]** ページの **[Disk File Path]** で **[Browse]** をクリックします。**[Browse Datastores]** ダイアログ ボックスが開きます。VMDK をアップロードした場所に移動します。ファイルを選択し、**[OK]** をクリックします。**[Next]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  **[Advanced Options]** ページで既定値をそのまま使用して、**[Next]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  **[Ready to Complete]** ページで、新しい仮想マシンに関連するすべての設定を確認します。**[Edit the virtual machine settings before completion]** をオンにします。**[Continue]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  **[Virtual Machines Properties]** ページの **[Hardware]** タブでデバイス ハードウェアを見つけます。**[New Hard Disk]** を選択します。**[Add]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  **[Add Hardware]** ウィンドウが表示されます。**[Device Type]** ページの **[Choose the type of device you wish to add]** から **[Hard Disk]** を選択して、**[Next]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  **[Select a Disk]** ページで **[Create a new virtual disk]** を選択します。**[Next]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  **[Create a Disk]** ページで **[Disk Size]** を 500 GB (またはそれ以上) に変更します。**[Disk Provisioning]** で **[Thin Provision]** を選択します。**[Next]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  **[Advanced Options]** ページで既定値をそのまま使用します。

	![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  **[Ready to Complete]** ページでディスク オプションを確認します。**[Finish]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  [Virtual Machine Properties] ページに戻ります。新しいハード ディスクが仮想マシンに追加されます。**[Finish]** をクリックします。
  
	![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  仮想マシンを選択した状態で、右側のウィンドウで **[Summary]** タブに移動します。仮想マシンの設定を確認します。

	![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

これで仮想マシンがプロビジョニングされました。次はこのマシンを起動して IP アドレスを取得します。

## 手順 3: 仮想デバイスを起動し、IP アドレスを取得する

仮想デバイスを起動して接続するには、次の手順を実行します。

#### 仮想デバイスを起動するには

1.  仮想デバイスを起動します。vSphere Configuration Manager の左側のウィンドウでデバイスを選択して右クリックし、コンテキスト メニューを表示します。**[Power]**、**[Power on]** の順に選択します。これで、仮想マシンの電源がオンになります。vSphere client のページ下部の **[Recent Tasks]** ウィンドウで状態を確認できます。

	![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  設定タスクの完了には数分かかります。デバイスが起動したら **[Console]** タブに移動します。デバイスにログインするには Ctrl + Alt + Del キーを押します。[Console] ウィンドウにカーソルをポイントして Ctrl + Alt + Insert キーを押すこともできます。既定のユーザーは StorSimpleAdmin で、既定のパスワードは Password1 です。

	![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  セキュリティ上の理由から、デバイス管理者のパスワードは初回ログオン後に有効期限が切れます。パスワードを変更するように促されます。

	![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  8 文字以上を含むパスワードを入力します。パスワードは、4 つの要件 (大文字、小文字、数字、および特殊文字) のうち 3 つを満たす必要があります。確認のためにパスワードを再入力します。パスワードが変更されたことが通知されます。

	![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  パスワードが正常に変更されると、仮想デバイスが再起動することがあります。再起動が完了するのを待ちます。デバイスの Windows PowerShell コンソールが、進行状況バーと共に表示される場合があります。

	![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  手順 6 ～ 8 は、非 DHCP 環境での起動時にのみ適用されます。DHCP 環境の場合は、手順 6 ～ 8 をスキップし、手順 9 に進みます。非 DHCP 環境でデバイスを起動した場合は、次の画面が表示されます。

	![](./media/storsimple-ova-deploy2-provision-vmware/image42.png)

	次にネットワークを構成する必要があります。

1.  `Get-HcsIpAddress` コマンドを使用して、仮想デバイスで有効なネットワーク インターフェイスの一覧を表示します。デバイスで単一のネットワーク インターフェイスが有効になっている場合、このインターフェイスに割り当てられる既定の名前は `Ethernet` です。

	![](./media/storsimple-ova-deploy2-provision-vmware/image43.png)

1.  Set-HcsIpAddress コマンドレットを使用してネットワークを構成します。例を次に示します。


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

	![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  初期セットアップが完了し、デバイスが再起動すると、デバイスのバナー テキストが表示されます。デバイスを管理するため、バナー テキストに表示される IP アドレスと URL をメモしておきます。この IP アドレスを使用して、仮想デバイスの Web UI に接続し、ローカル セットアップと登録を行います。

	![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)

デバイスが最小構成要件を満たしていない場合は、バナー テキストにエラーが表示されます (下記参照)。最小要件を満たすための十分なリソースを確保するようにデバイスの構成を変更する必要があります。その後、再起動し、デバイスに接続します。「[手順 1: ホスト システムが仮想デバイスの最小要件を満たしていることを確認する](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements)」にある最小構成要件を参照してください。

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

ローカル Web UI を使って初期構成を行っている間に他のエラーが発生した場合は、[ローカル Web UI を使った StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)に関するページにある次のワークフローを参照してください。

-   診断テストを実行して [Web UI 設定のトラブルシューティング](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)を行う。

-   [ログ パッケージの生成とログ ファイルの表示](storsimple-ova-web-ui-admin.md#generate-a-log-package)。

## 次のステップ

-   [StorSimple Virtual Array をファイル サーバーとして設定する](storsimple-ova-deploy3-fs-setup.md)

-   [StorSimple Virtual Array を iSCSI サーバーとして設定する](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0121_2016-->