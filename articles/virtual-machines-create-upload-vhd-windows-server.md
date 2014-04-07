<properties linkid="manage-windows-common-task-upload-vhd" urlDisplayName="VHD のアップロード" pageTitle="Windows Server VHD の作成と Windows Azure へのアップロード" metaKeywords="Azure VHD, VHD のアップロード" description="Windows Server オペレーティング システムを格納した仮想ハード ディスク (VHD) を作成して Windows Azure にアップロードする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード" authors=""  solutions="" writer="kathydav" manager="jeffreyg" editor="tysonn"  />





#Windows Server オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード#

Windows Azure の仮想マシンでは、仮想マシンの作成時に選択したオペレーティング システムが実行されます。Windows Azure は、仮想マシンのオペレーティング システムを VHD 形式 (.vhd ファイル) で仮想ハード ディスクに格納します。複製用に準備されたオペレーティング システムの VHD はイメージと呼ばれます。この記事では、インストールおよび一般化したオペレーティング システムの .vhd ファイルをアップロードすることで、独自のイメージを作成する方法について説明します。Windows Azure でのディスクとイメージの詳細については、「[ディスクおよびイメージの管理](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj672979.aspx)」を参照してください。

**注**: 仮想マシンを作成するときに、オペレーティング システムの設定をカスタマイズして、アプリケーションを実行しやすくできます。設定した構成は、その仮想マシンのディスクに格納されます。詳細については、「[カスタム仮想マシンを作成する方法](/ja-jp/manage/windows/how-to-guides/custom-create-a-vm/)」を参照してください。

##前提条件##
この記事では、次の項目があることを前提としています。

**管理証明書** - VHD をアップロードするサブスクリプションの管理証明書を作成し、その証明書を .cer ファイルにエクスポートした。証明書の作成方法の詳細については、「[Windows Azure の管理証明書の作成とアップロード](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg551722.aspx)」を参照してください。

**.vhd ファイルに格納された、サポートされている Windows オペレーティング システム** - サポートされている Windows Server オペレーティング システムを仮想ハード ディスクにインストールした。.vhd ファイルを作成するツールはいくつかあります。Hyper-V などの仮想化ソリューションを使用して .vhd ファイルを作成し、オペレーティング システムをインストールすることができます。詳細については、「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/ja-jp/library/hh846766.aspx)」を参照してください。

**重要**: 新しい VHDX 形式は、Windows Azure ではサポートされていません。Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。
 
- **Windows Server オペレーティング システムのメディア。**このタスクでは、Windows Server オペレーティング システムを格納した .iso ファイルが必要です。次の Windows Server のバージョンがサポートされています。
<P>
  <TABLE BORDER="1" WIDTH="600">
  <TR BGCOLOR="#E9E7E7">
    <TH>OS</TH>
    <TH>SKU</TH>
    <TH>Service Pack</TH>
    <TH>アーキテクチャ</TH>
  </TR>
  <TR>
    <TD>Windows Server 2012</TD>
    <TD>すべてのエディション</TD>
    <TD>該当なし</TD>
    <TD>x64</TD>
  </TR>
  <TR>
    <TD>Windows Server 2008 R2</TD>
    <TD>すべてのエディション</TD>
    <TD>SP1</TD>
    <TD>x64</TD>
  </TR>
  </TABLE>
</P>

- Windows Azure PowerShell モジュールの一部である [Add-AzureVHD](http://msdn.microsoft.com/ja-jp/library/windowsazure/dn205185.aspx) コマンドレット。このモジュールをダウンロードするには、[Windows Azure のダウンロード ページ](/ja-jp/develop/downloads/)にアクセスしてください。


このタスクの手順は次のとおりです。

- [ステップ 1: アップロードするイメージを準備する] []
- [ステップ 2: Windows Azure にストレージ アカウントを作成する] []
- [ステップ 3: Windows Azure への接続を準備する] []
- [ステップ 4: .vhd ファイルをアップロードする] []

## <a id="prepimage"> </a>ステップ 1: アップロードするイメージを準備する##

イメージを Windows Azure にアップロードする前に、Sysprep コマンドを使用してイメージを一般化する必要があります。Sysprep の使い方の詳細については、「[Sysprep の使用方法: 紹介](http://technet.microsoft.com/ja-jp/library/bb457073.aspx)」を参照してください。

作成した仮想マシンで、以下の手順を完了します。

1. オペレーティング システムにログインします。

2. 管理者としてコマンド プロンプト ウィンドウを開きます。ディレクトリを **%windir%\system32\sysprep** に変更し、`sysprep.exe` を実行します。

	![コマンド プロンプト ウィンドウを開く](./media/virtual-machines-create-upload-vhd-windows-server/sysprepcommand.png)

3.	**[システム準備ツール]** ダイアログ ボックスが表示されます。

	![Sysprep の開始](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4. **[システム クリーンアップ アクション]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。

5. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。

6. **[OK]** をクリックします。


## <a id="createstorage"> </a>ステップ 2: Windows Azure にストレージ アカウントを作成する##

ストレージ アカウントは、ストレージ サービスにアクセスするための最高レベルの名前空間を表し、Windows Azure サブスクリプションに関連付けられています。仮想マシンの作成に使用できる .vhd ファイルを Windows Azure にアップロードするには、Windows Azure のストレージ アカウントが必要です。Windows Azure の管理ポータルを使用して、ストレージ アカウントを作成できます。

1. Windows Azure の管理ポータルにサインインします。

2. コマンド バーで、**[新規]** をクリックします。

3. **[ストレージ アカウント]**、**[簡易作成]** の順にクリックします。

	![ストレージ アカウントの簡易作成](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. 次のようにフィールドを指定します。

	![ストレージ アカウントの詳細の入力](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

- **[URL]** で、ストレージ アカウントの URL で使用するサブドメイン名を入力します。文字数は 3 ～ 24 文字で、アルファベット小文字と数字を使用できます。この名前は、対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URL のホスト名になります。
	
- ストレージ アカウントの場所またはアフィニティ グループを選択します。アフィニティ グループを指定することで、ストレージと同じデータ センターにクラウド サービスを配置できます。
 
- ストレージ アカウントの Geo レプリケーションを使用するかどうかを決定します。Geo レプリケーションは既定で有効です。このオプションでは、ユーザーのコスト負担なしで、データが 2 次拠点にコピーされるため、1 次拠点で対処できない大規模な障害が発生した場合に、2 次拠点にストレージをフェールオーバーすることができます。2 次拠点は自動的に割り当てられ、変更することはできません。法律上の要件または組織のポリシー上、クラウド方式のストレージの場所を厳格に管理する必要がある場合は、Geo レプリケーションを無効にすることができます。ただし、後で Geo レプリケーションを有効に戻すと、既存データを 2 次拠点にコピーするためのデータ転送料金が 1 回だけ発生することに注意してください。Geo レプリケーションなしのストレージ サービスも割引価格で提供されています。

5. **[ストレージ アカウントの作成]** をクリックします。

	作成したアカウントが **[ストレージ アカウント]** に表示されます。

	![ストレージ アカウントの作成に成功](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)


## <a id="PrepAzure"> </a>ステップ 3: Windows Azure への接続を準備する##

.vhd ファイルをアップロードする前に、コンピューターと Windows Azure のサブスクリプションの間で、セキュリティで保護された接続を確立する必要があります。

1. Windows Azure PowerShell ウィンドウを開きます。

2. 次のコマンドを入力します。

	`Get-AzurePublishSettingsFile`

	このコマンドは、ブラウザー ウィンドウを開き、Windows Azure サブスクリプションの情報と証明書が含まれている .publishsettings ファイルを自動的にダウンロードします。

3. publishsettings ファイルを保存します。

4. 次のコマンドを入力します。

	`Import-AzurePublishSettingsFile <PathToFile>`

	ここで、'<PathToFile>' は .publishsettings ファイルへの完全なパスです。

	詳細については、[Windows Azure のコマンドレットの使用に関するページ](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554332.aspx)を参照してください。


## <a id="upload"> </a>ステップ 4: .vhd ファイルをアップロードする##

.vhd ファイルをアップロードするときは、BLOB ストレージ内であればどこにでも .vhd ファイルを置くことができます。以下のコマンドの例では、**BlobStorageURL** はステップ 2 で作成したストレージ アカウントの URL であり、**YourImagesFolder** は BLOB ストレージ内でイメージを格納するコンテナーです。**VHDName** は、仮想ハード ディスクを識別するために管理ポータルに表示されるラベルです。**PathToVHDFile** は、.vhd ファイルの完全なパスとファイル名です。

1. 前の手順で使用した Windows Azure PowerShell ウィンドウで、次のように入力します。

	`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	詳細については、[Add-AzureVhd に関するページ](http://msdn.microsoft.com/ja-jp/library/windowsazure/dn205185.aspx)を参照してください。

##カスタム イメージの一覧にイメージを追加する##
.vhd をアップロードしたら、サブスクリプションに関連付けられたカスタム イメージの一覧に、その .vhd をイメージとして追加します。

1. 管理ポータルで、**[すべてのアイテム]** の **[仮想マシン]** をクリックします。

2. [仮想マシン] で、**[イメージ]**、**[作成]** の順にクリックします。

3. **[VHD からのイメージの作成]** で、アップロードした .vhd の名前と URL を指定します。

4. **[この VHD に関連付けられた仮想マシンで Sysprep を実行しました]** をクリックして、ステップ 2. でオペレーティング システムを一般化したことを確認し、**[OK]** をクリックします。


## 次の手順##
一覧でイメージが利用できるようになったら、そのイメージを使用して仮想マシンを作成できます。手順については、「[Windows Server を実行する仮想マシンの作成](../virtual-machines-windows-tutorial/)」を参照してください。

[ステップ 1: アップロードするイメージを準備する]: #prepimage
[ステップ 2: Windows Azure にストレージ アカウントを作成する]: #createstorage
[ステップ 3: Windows Azure への接続を準備する]: #prepAzure
[ステップ 4: .vhd ファイルをアップロードする]: #upload





