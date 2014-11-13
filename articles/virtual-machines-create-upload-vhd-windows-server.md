<properties urlDisplayName="Upload a VHD" pageTitle="Windows Server VHD の作成と Azure へのアップロード" metaKeywords="Azure VHD, uploading VHD" description="Windows Server オペレーティング システムを格納した仮想ハード ディスク (VHD) を作成して Azure にアップロードする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Windows Server VHD の作成と Azure へのアップロード" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="kathydav" />

# Windows Server VHD の作成と Azure へのアップロード

この記事では、イメージを使用してそのイメージを基にした仮想マシンを作成するための、オペレーティング システムの仮想ハード ディスク (VHD) をアップロードする方法について説明します。Microsoft Azure でのディスクとイメージの詳細については、「[Azure でのディスクおよびイメージについて][Azure でのディスクおよびイメージについて]」を参照してください。

**メモ**:仮想マシンを作成するときに、オペレーティング システムの設定をカスタマイズして、アプリケーションを実行しやすくできます。設定した構成は、その仮想マシンのディスクに格納されます。手順については、「[カスタム仮想マシンの作成方法][カスタム仮想マシンの作成方法]」を参照してください。

## 前提条件

この記事では、次の項目があることを前提としています。

**Azure サブスクリプション** - アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure アカウントの作成に関するページ][Azure アカウントの作成に関するページ]を参照してください。

**Microsoft Azure PowerShell** - Microsoft Azure PowerShell モジュールをインストールしておきます。このモジュールをダウンロードするには、[Azure のダウンロード ページ][Azure のダウンロード ページ]にアクセスしてください。PowerShell のインストールと Azure サブスクリプションでの構成に関するチュートリアルは、[こちら][こちら]をご覧ください。

-   Microsoft Azure PowerShell モジュールの一部である [Add-AzureVHD][Add-AzureVHD] コマンドレット。このコマンドレットは VHD のアップロードに使用します。

**.vhd ファイルに格納された、サポートされている Windows オペレーティング システム** - サポートされている Windows Server オペレーティング システムを仮想ハード ディスクにインストールした。.vhd ファイルを作成するツールはいくつかあります。Hyper-V などの仮想化ソリューションを使用して .vhd ファイルを作成し、オペレーティング システムをインストールすることができます。詳細については、「[Hyper-V の役割のインストールと仮想マシンの構成][Hyper-V の役割のインストールと仮想マシンの構成]」を参照してください。

**重要**: VHDX 形式は、Microsoft Azure ではサポートされていません。Hyper-V マネージャーまたは [Convert-VHD コマンドレット][Convert-VHD コマンドレット]を使用して、ディスクを VHD 形式に変換できます。変換についてのチュートリアルは、[こちら][1]をご覧ください。

**Windows Server オペレーティング システムのメディア。**このタスクでは、Windows Server オペレーティング システムを格納した .iso ファイルが必要です。次の Windows Server のバージョンがサポートされています。

<table border="1" width="600">

<tr bgcolor="#E9E7E7">

<th>
OS

</th>

<th>
SKU

</th>

<th>
Service Pack

</th>

<th>
アーキテクチャ

</th>

</tr>

<tr>

<td>
Windows Server 2012 R2

</td>

<td>
すべてのエディション

</td>

<td>
該当なし

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2012

</td>

<td>
すべてのエディション

</td>

<td>
該当なし

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2008 R2

</td>

<td>
すべてのエディション

</td>

<td>
SP1

</td>

<td>
x64

</td>

</tr>

</table>

</p>
</p>
このタスクの手順は次のとおりです。

-   [手順 1.アップロードするイメージを準備する][手順 1.アップロードするイメージを準備する]
-   [手順 2.Azure にストレージ アカウントを作成する][手順 2.Azure にストレージ アカウントを作成する]
-   [手順 3.Azure への接続を準備する][手順 3.Azure への接続を準備する]
-   [手順 4..vhd ファイルをアップロードする][手順 4..vhd ファイルをアップロードする]

## <span id="prepimage"></span> </a>手順 1.アップロードするイメージを準備する

イメージを Azure にアップロードする前に、Sysprep コマンドを使用してイメージを一般化する必要があります。Sysprep の使い方の詳細については、「[Sysprep の使用方法: 紹介][Sysprep の使用方法: 紹介]」を参照してください。

作成した仮想マシンで、以下の手順を完了します。

1.  オペレーティング システムにログインします。

2.  管理者としてコマンド プロンプト ウィンドウを開きます。ディレクトリを **%windir%\\system32\\sysprep** に変更し、`sysprep.exe` を実行します。

    ![コマンド プロンプト ウィンドウを開く][コマンド プロンプト ウィンドウを開く]

3.  **[システム準備ツール]** ダイアログ ボックスが表示されます。

    ![Sysprep の開始][Sysprep の開始]

4.  **[システム準備ツール]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、[一般化する] チェック ボックスがオンになっていることを確認します。

5.  **[シャットダウン オプション]** の **[シャットダウン]** を選択します。

6.  **[OK]** をクリックします。

## <span id="createstorage"></span> </a>手順 2.Azure でストレージ アカウントを作成する

ストレージ アカウントは、ストレージ サービスにアクセスするための最高レベルの名前空間を表し、Azure サブスクリプションに関連付けられています。仮想マシンの作成に使用できる .vhd ファイルを Azure にアップロードするには、Azure のストレージ アカウントが必要です。Azure の管理ポータルを使用して、ストレージ アカウントを作成できます。

1.  Azure 管理ポータルにサインインします。

2.  コマンド バーで、**[新規]** をクリックします。

3.  **[ストレージ アカウント]**、**[簡易作成]** の順にクリックします。

    ![ストレージ アカウントの簡易作成][ストレージ アカウントの簡易作成]

4.  次のようにフィールドを指定します。

-   **[URL]** で、ストレージ アカウントの URL で使用するサブドメイン名を入力します。文字数は 3 ～ 24 文字で、アルファベット小文字と数字を使用できます。この名前は、対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URL のホスト名になります。

-   ストレージ アカウントの**場所またはアフィニティ グループ**を選択します。アフィニティ グループを指定することで、ストレージと同じデータ センターにクラウド サービスを配置できます。

-   ストレージ アカウントの **Geo (主要地域) レプリケーション**を使用するかどうかを決定します。Geo レプリケーションは既定で有効です。このオプションでは、ユーザーのコスト負担なしで、データが 2 次拠点にコピーされるため、1 次拠点で対処できない大規模な障害が発生した場合に、2 次拠点にストレージをフェールオーバーすることができます。2 次拠点は自動的に割り当てられ、変更することはできません。法律上の要件または組織のポリシー上、クラウド方式のストレージの場所を厳格に管理する必要がある場合は、Geo レプリケーションを無効にすることができます。ただし、後で Geo レプリケーションを有効に戻すと、既存データを 2 次拠点にコピーするためのデータ転送料金が 1 回だけ発生することに注意してください。Geo レプリケーションなしのストレージ サービスも割引価格で提供されています。ストレージ アカウントの Geo レプリケーションの管理についての詳細は、「[ストレージ アカウントの管理方法][ストレージ アカウントの管理方法]」を参照してください。

    ![ストレージ アカウントの詳細の入力][ストレージ アカウントの詳細の入力]

1.  **[ストレージ アカウントの作成]** をクリックします。

    作成したアカウントが **[ストレージ アカウント]** に表示されます。

    ![ストレージ アカウントの作成に成功][ストレージ アカウントの作成に成功]

2.  次に、アップロードした VHD のコンテナーを作成します。**[ストレージ アカウント名]** をクリックし、次に **[コンテナー]** をクリックします。

    ![ストレージ アカウントの詳細][ストレージ アカウントの詳細]

3.  **[コンテナーを作成する]** をクリックします。

    ![ストレージ アカウントの詳細][2]

4.  **[名前]** にコンテナーの名前を入力し、**[アクセス ポリシー]** を選択します。

    ![コンテナー名][コンテナー名]

    > [WACOM.NOTE] 既定では、コンテナーはプライベートであり、アカウント所有者のみがアクセスできます。コンテナー内の BLOB にはパブリック読み取りアクセスを許可し、コンテナーのプロパティやメタデータにはアクセスを許可しない場合は、[パブリック BLOB] オプションを使用します。コンテナーと BLOB に完全パブリック読み取りアクセスを許可するには、[パブリック コンテナー] オプションを使用します。

## <span id="PrepAzure"></span> </a>手順 3.Microsoft Azure への接続を準備する

.vhd ファイルをアップロードする前に、コンピューターと Microsoft Azure のサブスクリプションの間で、セキュリティで保護された接続を確立する必要があります。接続の確立には、Microsoft Azure Active Directory 方式または証明書方式を使用できます。

### Microsoft Azure AD 方式を使用する

1.  Microsoft Azure PowerShell コンソールを開きます。手順については、「[方法: Microsoft Azure PowerShell のインストール][方法: Microsoft Azure PowerShell のインストール]」を参照してください。

2.  次のコマンドを入力します。
    `Add-AzureAccount`

    このコマンドによりサインイン ウィンドウが開きますので、職場や学校のアカウントを使用してサインインできます。

    ![PowerShell ウィンドウ][PowerShell ウィンドウ]

3.  Microsoft Azure により資格情報が認証および保存され、ウィンドウが閉じます。

### 証明書方式を使用する

1.  Microsoft Azure PowerShell ウィンドウを開きます。

2.  次のコマンドを入力します。
    `Get-AzurePublishSettingsFile`

3.  ブラウザー ウィンドウが開き、.publishsettings ファイルをダウンロードするよう求められます。これには、Microsoft Azure のサブスクリプションについての情報と証明書が含まれています。

    ![ブラウザーのダウンロード ページ][ブラウザーのダウンロード ページ]

4.  .publishsettings ファイルを保存します。

5.  次のコマンドを入力します。
    `Import-AzurePublishSettingsFile <PathToFile>`

    ここで、`<PathToFile>` は .publishsettings ファイルへの完全なパスです。

    詳細については、「[Microsoft Azure コマンドレットの概要][Microsoft Azure コマンドレットの概要]」を参照してください。

    PowerShell のインストールと構成についての詳細は、「[Microsoft Azure PowerShell のインストールおよび構成方法][こちら]」を参照してください。

## <span id="upload"></span> </a>手順 4..vhd ファイルをアップロードする

.vhd ファイルをアップロードするときは、BLOB ストレージ内であればどこにでも .vhd ファイルを置くことができます。以下のコマンドの例では、**BlobStorageURL** は手順 2 で作成したストレージ アカウントの URL であり、**YourImagesFolder** は BLOB ストレージ内でイメージを格納するコンテナーです。**VHDName** は、仮想ハード ディスクを識別するために管理ポータルに表示されるラベルです。**PathToVHDFile** は、.vhd ファイルの完全なパスとファイル名です。

1.  前の手順で使用した Microsoft Azure PowerShell ウィンドウで、次のように入力します。

    `Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

    ![PowerShell Add-AzureVHD][PowerShell Add-AzureVHD]

    Add-AzureVhd コマンドレットの詳細については、[Add-AzureVhd に関するページ][Add-AzureVhd に関するページ]を参照してください。

## カスタム イメージの一覧にイメージを追加する

.vhd をアップロードしたら、サブスクリプションに関連付けられたカスタム イメージの一覧に、その .vhd をイメージとして追加します。

1.  管理ポータルで、**[すべてのアイテム]** の **[仮想マシン]** をクリックします。

2.  [仮想マシン] で、**[イメージ]** をクリックします。

3.  次に、**[イメージの作成]** をクリックします。

    ![PowerShell Add-AzureVHD][3]

4.  **[VHD からのイメージの作成]** で、以下の手順を実行します。

    -   **[名前]** を指定します
    -   **[説明]** を指定します
    -   **[VHD の URL]** を指定するには、フォルダー ボタンをクリックして、次のダイアログ ボックスを起動します

    ![VHD の選択][VHD の選択]

    -   VHD があるストレージ アカウントを選択し、**[開く]** をクリックします。これにより、**[VHD からのイメージの作成]** ウィンドウに戻ります。
    -   **[VHD からのイメージの作成]** ウィンドウに戻ったら、[オペレーティング システム ファミリ] を選択します。
    -   **[この VHD に関連付けられた仮想マシンで Sysprep を実行しました]** をクリックして、手順 1. でオペレーティング システムを一般化したことを確認し、**[OK]** をクリックします。

    ![イメージの追加][イメージの追加]

5.  **省略可能:** Azure PowerShell の Add-AzureVMImage コマンドレットを使用して、VHD をイメージとして追加することもできます。

    Microsoft Azure PowerShell ウィンドウで、次のように入力します。

    `Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`

    ![PowerShell Add-AzureVMImage][PowerShell Add-AzureVMImage]

6.  ここまでの手順を完了すると、**[イメージ]** タブを選択したときに、新しいイメージが一覧に表示されます。

    ![カスタム イメージ][カスタム イメージ]

    新しい仮想マシンを作成するときに、この新しいイメージを使用できるようになりました。新しいイメージを表示するには、**[マイ イメージ]** を選択します。手順については、「[Windows Server を実行する仮想マシンの作成][カスタム仮想マシンの作成方法]」を参照してください。

    ![カスタム イメージからの VM の作成][カスタム イメージからの VM の作成]

## 次のステップ

仮想マシンを作成したら、SQL Server 仮想マシンを作成してみてください。手順については、「[Microsoft Azure での SQL Server 仮想マシンのプロビジョニング][Microsoft Azure での SQL Server 仮想マシンのプロビジョニング]」を参照してください。

  [Azure でのディスクおよびイメージについて]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj672979.aspx
  [カスタム仮想マシンの作成方法]: http://www.windowsazure.com/ja-jp/documentation/articles/virtual-machines-windows-tutorial/
  [Azure アカウントの作成に関するページ]: http://www.windowsazure.com/ja-jp/develop/php/tutorials/create-a-windows-azure-account/
  [Azure のダウンロード ページ]: http://www.windowsazure.com/ja-jp/downloads/
  [こちら]: http://www.windowsazure.com/ja-jp/documentation/articles/install-configure-powershell/
  [Add-AzureVHD]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn205185.aspx
  [Hyper-V の役割のインストールと仮想マシンの構成]: http://technet.microsoft.com/ja-jp/library/hh846766.aspx
  [Convert-VHD コマンドレット]: http://technet.microsoft.com/ja-jp/library/hh848454.aspx
  [1]: http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx
  [手順 1.アップロードするイメージを準備する]: #prepimage
  [手順 2.Azure にストレージ アカウントを作成する]: #createstorage
  [手順 3.Azure への接続を準備する]: #prepAzure
  [手順 4..vhd ファイルをアップロードする]: #upload
  [Sysprep の使用方法: 紹介]: http://technet.microsoft.com/ja-jp/library/bb457073.aspx
  [コマンド プロンプト ウィンドウを開く]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png
  [Sysprep の開始]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png
  [ストレージ アカウントの簡易作成]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png
  [ストレージ アカウントの管理方法]: http://www.windowsazure.com/ja-jp/documentation/articles/storage-manage-storage-account/
  [ストレージ アカウントの詳細の入力]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png
  [ストレージ アカウントの作成に成功]: ./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png
  [ストレージ アカウントの詳細]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png
  [2]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png
  [コンテナー名]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png
  [方法: Microsoft Azure PowerShell のインストール]: #Install
  [PowerShell ウィンドウ]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png
  [ブラウザーのダウンロード ページ]: ./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png
  [Microsoft Azure コマンドレットの概要]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554332.aspx
  [PowerShell Add-AzureVHD]: ./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png
  [Add-AzureVhd に関するページ]: http://msdn.microsoft.com/ja-jp/library/dn495173.aspx
  [3]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png
  [VHD の選択]: ./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png
  [イメージの追加]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png
  [PowerShell Add-AzureVMImage]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png
  [カスタム イメージ]: ./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png
  [カスタム イメージからの VM の作成]: ./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png
  [Microsoft Azure での SQL Server 仮想マシンのプロビジョニング]: http://www.windowsazure.com/ja-jp/documentation/articles/virtual-machines-provision-sql-server/
