<properties
   pageTitle="Azure Marketplace 向けの仮想マシン イメージの作成 | Microsoft Azure"
   description="顧客が購入できるように Azure Marketplace 向けの仮想マシン イメージを作成する方法について詳しく説明します。"
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio; v-divte"/>

# Azure Marketplace 向け仮想マシン イメージ作成ガイド

この**手順 2** では、Azure Marketplace にデプロイする SKU の基盤となる VHD を準備する方法を説明します。プロセスは、Linux ベースの SKU または Windows ベースの SKU のどちらを提供するかによって異なります。このセクションでは、両方のシナリオについて説明します。このプロセスは、[アカウントの作成および登録][link-acct-creation]と並行して実行できます。

## 1\.プランと SKU の定義

このセクションでは、プランおよびプランに属する SKU を定義します。

プランは、そのプランに属するすべての SKU の "親" です。複数のプランを定義できます。プランをどのように構成するかは、それぞれの状況によって異なります。プランをステージングにプッシュすると、そのすべての SKU も一緒にプッシュされます。URL に表示されるため、SKU の ID を慎重に検討します。

- Azure.com – http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}

- Azure プレビュー ポータル - https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}

SKU は、VM イメージの取引名です。ＶＭ イメージには、1 個の OS ディスクと 0 個以上のデータ ディスクが含まれます。これは、仮想マシンに欠かせない完全なストレージ プロファイルです。ディスクあたり 1 つの VHD が必要で、空のデータ ディスクにも VHD を作成する必要があります。

どのオペレーティング システムを使っているかにかかわらず、SKU に必要な最小数のデータ ディスクのみを追加します。エンド ユーザーはデプロイ時にイメージの一部であるディスクを削除できませんが、必要に応じて、デプロイ中またはデプロイ後にディスクを追加できます。

### 1\.1 プランの追加

1. 販売者アカウントを使用して、[発行ポータル][link-pubportal]にログインします。
2. 発行ポータルの **[Virtual Machines]** タブに移動します。フィールドにプラン名を入力して作成します。
3. 通常、プラン名は Azure Marketplace で販売する計画の製品/サービスの名前です。

### 1\.2 SKU の定義
プランを追加したら、SKU を定義/特定する必要があります。複数のプランを作成し、各プランに複数の SKU を定義できます。プランをステージングにプッシュすると、そのすべての SKU も一緒にプッシュされます。

1. **SKU を追加します。** ID が必要です。これは、URL で使用します。これは、発行プロファイル内で一意である必要がありますが、他の発行元との ID の競合が発生するリスクはありません。

> [AZURE.NOTE]プランと SKU の ID は、Marketplace でプランの URL に表示されます。

2. **SKU の概要説明を追加します。** これは、UX で人が読み取るため、わかりやすい内容にすることをお勧めします。この情報は、"ステージングにプッシュ" するまでロックする必要はありません。それまでは、自由に編集できます。
3. Windows ベースの SKU を使用する場合、表示されたリンクに従って、Windows Server の承認されたバージョンを取得します。

## 2\.Azure と互換性のある VHD の作成 (Linux ベース)
次のセクションでは、Microsoft Azure Marketplace 用 Linux ベースの VM イメージを作成するためのベスト プラクティスについて説明します。詳細な手順については、「[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード][link-azure-vm-1]」を参照してください。

> [AZURE.TIP]次の手順の多く (例: エージェントのインストール、カーネルのブート パラメーター) は既に、Microsoft Azure イメージ ギャラリーから使用できる Linux イメージ対応になっています。したがって、ベースとしてこれらのイメージのいずれかを使用して始めれば、Azure 対応ではない Linux イメージを構成する場合と比較して、時間を節約できます。

### 2\.1 適切な VHD サイズの選択
発行済み SKU (VM イメージ) は、SKU に対してディスクをサポートしたあらゆる VM サイズに対応するよう設計する必要があります。推奨サイズに関するガイダンスを提供できますが、これらは強制ではなく、推奨事項として取り扱ってください。

1. Linux OS VHD: VM イメージ内の Linux OS VHD は、30 GB ～ 50 GB の固定形式の VHD として作成する必要があります。30 GB を下回らないようにしてください。物理サイズが VHD サイズより小さいと、VHD が疎になります。ケースバイケースで、Linux VHD を 50 GB より大きくすることも検討します。別の形式で VHD を既に設定している場合は、[Convert-VHD PowerShell コマンドレットを使用して形式を変更できます][link-technet-1]。
2. データ ディスク VHD: データ ディスクは 1 TB にすることができます。データ ディスク VHD は固定形式の VHD として作成する必要がありますが、この方法では疎になることに注意してください。ディスク サイズを決めるとき、エンド ユーザーはイメージ内の VHD をサイズ変更できないことに注意してください。

### 2\.2 最新の Azure Linux エージェントがインストールされていることを確認
OS VHD を準備するときは、[Azure Linux エージェント][link-azure-vm-2]がインストールされていることを確認します。RPM または Deb パッケージを使用します。パッケージの名前は通常は walinuxagent または WALinuxAgent ですが、ディストリビューションを確認してください。エージェントには、Azure で Linux IaaS をデプロイするための主要な機能 (VM プロビジョニング、ネットワーク機能など) が用意されています。

エージェントはさまざまな方法で構成できますが、互換性を最大化するために一般的なエージェント構成方法を使用することをお勧めします。エージェントは手動でインストールできますが、ディストリビューションに事前構成されたパッケージが含まれる場合はそれを使うことを強くお勧めします。

[GitHub リポジトリ][link-github-waagent]からエージェントを手動でインストールすることを選択した場合、まず waagent ファイルを /usr/sbin にコピーし、次のコマンドを root として実行します。

    # chmod 755 /usr/sbin/waagent
    # /usr/sbin/waagent -install

エージェントの構成ファイルが /etc/waagent.conf に配置されます。

### 2\.3 必要なライブラリが含まれていることを確認
Azure Linux エージェントのほか、次のライブラリが含まれている必要があります。

1. [Linux Integration Services][link-intsvc] Version 3.0 以降がカーネルで有効になっている必要があります。「[Linux カーネルの要件](../virtual-machines/virtual-machines-linux-create-upload-vhd-generic/#linux-kernel-requirements)」を参照してください。
2. Azure I/O 安定性向上のための[カーネル パッチ](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c) (最新のカーネルには必要ないと思いますが、確認してください)
3. [Python][link-python] 2.6 以降
4. Python pyasn1 パッケージ (まだインストールされていない場合)
5. [OpenSSL][link-openssl] (v1.0 以降を推奨)

### 2\.4 ディスク パーティションの設定
Logical Volume Manager を**使用しない**ことをお勧めします。OS ディスクの単一のルート パーティションを作成します。OS またはデータ ディスクでスワップ パーティションを使用しないでください。/etc/fstab にマウントされていない場合でも、スワップ パーティションを削除することをお勧めします。必要に応じて、Linux エージェントによりローカル リソース ディスク (/dev/sdb) にスワップ パーティションを作成することもできます。

### 2\.5 カーネル ブート ライン パラメーターの追加
また、次のパラメーターをカーネル ブート ラインに追加する必要があります。

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

これにより、Azure サポートで必要に応じてお客様にシリアル コンソール出力を提供できます。また、クラウド ストレージからの OS ディスクのマウントに対して適切なタイムアウトを指定できます。SKU でエンド ユーザーの仮想マシンへのダイレクト SSHing をブロックする場合でも、シリアル コンソール出力を有効にする必要があります。

### 2\.6 既定で SSH Server を含める
エンド ユーザーに対して SSH を有効にすることを強くお勧めします。SSH Server を有効にした場合、ClientAliveInterval 180 オプションを使って、SSH キープアライブを sshd 構成に追加してください。180 が推奨されますが、許可される範囲は 30 ～ 235 です。すべてのアプリケーションについて、エンド ユーザーに仮想マシンへのダイレクト SSH を許可する必要はありません。SSH を明示的にブロックした場合は、ClientAliveInterval を設定する必要はありません。

### 2\.7 ネットワークの要件を満たす
Azure と互換性のある Linux VM イメージのネットワーク要件は次のとおりです。

- 通常は、NetworkManager を無効にするのが最善です。例外の 1 つは CentOS 7.x ベースのシステム (および派生物) で、NetworkManager を有効にしておく必要があります。
- ネットワーク構成は ifup/ifdown スクリプトから制御できる必要があります。Linux エージェントは、これらのコマンドを使ってプロビジョニング中にネットワークを再起動することがあります。
- カスタム ネットワーク構成は使用しないでください。最後の手順として、resolv.conf ファイルを削除する必要があります。通常、これはプロビジョニング解除の一部として行います (「[Azure Linux エージェント ユーザー ガイド](../virtual-machines/virtual-machines-linux-agent-user-guide/)」を参照)。このステップは、次のコマンドを使って手動で実行することもできます。

        rm /etc/resolv.conf

- ネットワーク デバイスをブート時に起動して、DHCP を使用する必要があります。
- IPv6 は、Azure ではサポートされていません。このプロパティを有効にしても機能しません。

### 2\.8 セキュリティのベスト プラクティスを実装していることを確認
Azure Marketplace の SKU がセキュリティにおけるベスト プラクティスを順守していることが重要です。コーディネートは次のとおりです。

- ディストリビューションのすべてのセキュリティ パッチをインストールします。
- ディストリビューションのセキュリティ ガイドラインに従います。
- 既定のアカウントを作成しないようにします (プロビジョニング インスタンス全体で同じものを使用するなど)。
- bash の履歴エントリを消去します。
- iptables (ファイアウォール) ソフトウェアを組み込みますが、ルールを有効にはしません。これにより、お客様にシームレスな既定の操作性が提供されます。追加の構成として VM ファイアウォールを使用する必要があるお客様は、各社固有のニーズを満たすために iptables ルールを構成することもできます。

### 2\.9 イメージの汎用化
Azure Marketplace 内のすべてのイメージは汎用的な方法で再利用できる必要があります。つまり、特定の構成からそれらを抜き取る必要があります。これを Linux で実現するには、OS VHD をプロビジョニング解除する必要があります。

プロビジョニング解除用の Linux コマンドは次のとおりです。

        # waagent -deprovision

このコマンドは、次の操作を自動的に実行します。

- /etc/resolv.conf でネーム サーバー構成を削除します
- キャッシュされた DHCP クライアントのリースの削除
- ホスト名を localhost.localdomain にリセット

次の操作も確実に実行されるよう構成ファイル (/etc/waagent.conf) を設定することもお勧めします。

- 構成ファイル内の Provisioning.RegenerateSshHostKeyPair を "y" に設定して、すべての SSH ホスト キーを削除します
- 構成ファイル内の Provisioning.DeleteRootPassword を "y" に設定して、/etc/shadow からルート パスワードを削除します。構成ファイルの内容のドキュメントについては、エージェントの Github リポジトリ ページの README ファイルの「CONFIGURATION」セクションを参照してください ([https://github.com/Azure/WALinuxAgent](https://github.com/Azure/WALinuxAgent) で下にスクロール)。  

これで、Linux VM の汎用化は完了です。Azure ポータル、コマンド ライン、または VM 内から VM をシャット ダウンします。シャット ダウンが完了したら、手順 3.4 に進みます。

## 3\.Azure と互換性のある VHD の作成 (Windows ベース)
次のセクションでは、Microsoft Azure Marketplace 用の Windows Server に基づいて SKU を作成するためのステップについて説明します。

### 3\.1 正しい基本 VHD を使用していることを確認
VM イメージの OS VHD は、Microsoft Azure 承認の基本イメージに基づいている必要があります (Windows Server、SQL Server など)。

始めに、[Microsoft Azure ポータル][link-azure-portal]にある次のイメージのいずれかから VM を作成します。

- Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2]、[2012 Datacenter][link-datactr-2012]、[2008 R2 SP1][link-datactr-2008-r2])
- SQL Server 2014 ([Enterprise][link-sql-2014-ent]、[Standard][link-sql-2014-std]、[Web][link-sql-2014-web])
- SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent]、[Standard][link-sql-2012-std]、[Web][link-sql-2012-web])

これらのリンクは、SKU ページの発行ポータルにもあります。

> [AZURE.TIP]現行の Microsoft Azure 管理ポータルまたは PowerShell を使用している場合は、2014 年 9 月 8 日以降に発行された Windows Server イメージが承認されます。


### 3\.2 Windows VM の作成
Microsoft Azure ポータルから承認された基本イメージに基づいて、簡単な数ステップ実行して VM を作成できます。大まかな手順を以下に示します。

1. 基本イメージ ページで、[[VM の作成]][link-azure-portal] を選択して新しい **Microsoft Azure ポータル**に移動します。

    ![図][img-acom-1]

2. 使用する Azure サブスクリプションの Microsoft アカウント (MSA) とパスワードでポータルにログインします。
3. プロンプトに従って、選択した基本イメージを使って VM を作成します。最低限の情報として、ホスト名 (コンピューターの名前)、ユーザー名 (登録管理ユーザー)、VM のパスワードを入力する必要があります。

    ![図][img-portal-vm-create]

4. デプロイする VM のサイズを選択します。

    a.VHD をオンプレミスで開発する場合は、サイズの選択は必要ありません。小さいサイズの VM を使用することを検討してください。

    b.Azure でイメージを開発する場合は、選択したイメージに推奨される VM サイズを使用することを検討してください。

    c.価格情報については、ポータルに表示される推奨される価格レベルのセレクターを参照してください。発行元が指定できるサイズとして、3 つのサイズが推奨されています。(この場合、発行元はマイクロソフト。)

    ![図][img-portal-vm-size]

5. プロパティを設定します。

    a.迅速にデプロイするには、**[オプションの構成]** と **[リソース グループ]** のプロパティの既定値をそのまま使用します。

    b.必要に応じて、[ストレージ アカウント] で OS VHD を格納するストレージのアカウントを選択できます。

    c.必要に応じて、[リソース グループ] で VM を配置する論理グループを選択できます。
6. デプロイ先の**場所**を選択します。

    a.VHD をオンプレミスで開発する場合は、後でイメージを Azure にアップロードするため、場所を指定する必要はありません。

    b.Azure でイメージで開発する場合は、最初から US ベースの Microsoft Azure リージョンのいずれかを使用することを検討してください。これにより認定でイメージを送信した際に、お客様に代わりマイクロソフトが実行することで VHD コピー プロセスが高速化されます。

    ![図][img-portal-vm-location]

7. **[作成]** をクリックします。VM のデプロイが開始されます。数分でデプロイが完了し、SKU のイメージの作成を開始できます。

### 3\.3 クラウドでの VHD の開発
リモート デスクトップ プロトコル (RDP) を使用してクラウドで VHD を開発することを強くお勧めします。プロビジョニングではユーザー名とパスワードを指定して RDP に接続します。

> [AZURE.IMPORTANT]\: オンプレミスで VHD を開発する場合は (推奨されません)、「[Creating a Virtual Machine Image on-premise (オンプレミスでの仮想マシン イメージの作成)](marketplace-publishing-vm-image-creation-on-premise.md)」を参照してください。**クラウドで開発する場合は、VHD をダウンロードする必要はありません**。


**[Microsoft Azure ポータル][link-azure-portal]を使用した RDP 経由の接続**

1. **[参照]**、**[VM]** の順に選択します。
2. [VM] ブレードが開きます。接続する VM が実行していることを確認し、デプロイ済み VM の一覧からその VM を選択します。
3. ブレードが開き、選択した VM の詳細が表示されます。上部で、**[接続]** をクリックします。
4. プロビジョニング時に指定したユーザー名とパスワードを入力するように求められます。

**PowerShell を使用した RDP 経由の接続**

リモート デスクトップ ファイルをローカル マシンにダウンロードするには、[Get-AzureRemoteDesktopFile コマンドレット][link-technet-2]を使用します。このコマンドレットを使用するには、サービス名と VM 名が必要です。[Microsoft Azure ポータル][link-azure-portal]から VM を作成した場合は、この情報を [VM プロパティ] で探すことができます。

1. Microsoft Azure ポータルで **[参照]**、**VM** の順に選択します。
2. **[Virtual Machines]** ブレードが開きます。VM の一覧からデプロイする VM を選びます。
3. ブレードが開き、選択した VM の詳細が表示されます。
4. **[プロパティ]** をクリックします。
5. ドメイン名の最初の部分はサービス名です。ホスト名は、VM の名前です。

    ![図][img-portal-vm-rdp]

6. 作成した VM の RDP ファイルを管理者のローカル デスクトップにダウンロードするコマンドレットは次のとおりです。

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

RDP について詳しくは、MSDN の記事「[RDP または SSH を使用した Azure 仮想マシンへの接続](http://msdn.microsoft.com/library/azure/dn535788.aspx)」をご覧ください。

**VM の構成と SKU の作成**

OS VHD がダウンロードされたら、Hyper-V を使って、SKU の作成を開始するように VM を構成します。詳細な手順については、TechNet リンク「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

### 3\.4 適切な VHD サイズの選択
VM イメージ内の Windows OS VHD は、128 GB 固定形式 VHD として作成する必要があります。

物理サイズが 128 GB 未満の場合、VHD が疎になります。Windows と SQL Server の基本イメージがこれらの要件を既に満たしている場合は、取得された VHD の形式またはサイズを変更しないでください。

データ ディスクは 1 TB にすることができます。ディスク サイズを決めるときに、エンド ユーザーはデプロイ時にイメージ内の VHD をサイズ変更できないことに注意してください。データ ディスク VHD は固定形式の VHD として作成する必要がありますが、この方法では疎になることに注意してください。データ ディスクは空にすることも、データを含むこともできます。


### 3\.5 最新の Windows 更新プログラムのインストール
基本イメージには、発行日の時点における最新の更新プログラムが含まれています。作成した OS VHD を発行する前に、Windows Update が実行されていること、すべての最新の "重要な" セキュリティ更新プログラムがインストールされていることを確認してください。

### 3\.6 必要に応じて追加の構成およびタスクのスケジュールを実行
追加の構成が必要な場合は、デプロイ時に最終的な変更が VM に反映されるように、起動時にスケジュールされたタスクを使用することを検討してください。

- 実行が正常に完了したら、タスクが自動的に削除されるように設定するのがベスト プラクティスです。
- ドライブ C:\\ または D:\\ は常設の唯一のドライブであるため、構成はこれら以外を使用して設定するようにしてください。C:\\ は OS ディスクで、D:\\ は一時的なローカル ディスクです。

### 3\.7 イメージの汎用化
Azure Marketplace のすべてのイメージは汎用的な方法で再利用できる必要があります。つまり、OS VHD を一般化する必要があります。

- Windows では、イメージが sysprep 済みで、sysprep コマンドをサポートしない構成を設定しないことが必要です。
- ディレクトリ %windir%\\System32\\Sysprep から次のコマンドを実行できます。

        sysprep.exe /generalize /oobe /sshutdown

  OS を sysprep する方法のガイダンスについては、MSDN 記事「[Windows Server VHD の作成と Azure へのアップロード](../virtual-machines/virtual-machines-create-upload-vhd-windows-server/)」の手順を参照してください。

## 4\.VHD からの VM のデプロイ
VHD、汎用化された OS VHD、および 0 または 1 つ以上のデータ ディスク VHD が Azure Storage アカウントにアップロードされたら、これらをテスト用のユーザー VM イメージとして登録できます。OS VHD は汎用化されるため、VHD URL を指定して VM を直接デプロイすることはできません。

VM イメージの詳細については、次のブログを参照してください。

- [VM イメージ](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell How To (VM イメージ PowerShell ハウツー)](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
- [Azure のバーチャル マシン イメージについて](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### 4\.1 ユーザー VM イメージの作成
SKU からユーザー VM イメージを作成して複数の VM のデプロイを開始するには、[VM イメージ作成 Rest API](http://msdn.microsoft.com/library/azure/dn775054.aspx) を使用して VHD を VM イメージとして登録する必要があります。

PowerShell から VM イメージを作成するには、Invoke-WebRequest コマンドレットを使用します。以下の PowerShell スクリプトは、単一の OS ディスクおよびデータ ディスクを使って VM イメージを作成する方法を示しています。PowerShell セッションとサブスクリプションが既に設定されている必要があることに注意してください。

        # Image Parameters to Specify
        $ImageName=’ENTER-YOUR-OWN-IMAGE-NAME-HERE’
        $Label='ENTER-YOUR-LABEL-HERE'
        $Description='DESCRIBE YOUR IMAGE HERE’
        $osCaching='ReadWrite' $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'https://mystorageaccount.blob.core.windows.net/vhds/myosvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $dataMediaLink='http://mystorageaccount.blob.core.windows.net/vhds/mydatavhd.vhd'
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription -Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" + Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages" $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body
        $body -Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        {
        echo "Accepted"
        } else {
        echo "Not Accepted" }
        $opId = $response.Headers.'x-ms-request-id'
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + $opId $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" -
        Certificate $certificate -Headers $headers -Method GET
        $response2.RawContent


このスクリプトを実行すると、ImageName パラメーターに指定した名前 myVMIMage でユーザー VM イメージが作成されます。これは、1 つの OS ディスクと 1 つのデータ ディスクから成ります。

この API は非同期処理を実行し、202 を承認して応答します。VM イメージが作成されたことを確認するには、クエリを実行して処理の状態を問い合わせる必要があります。返される応答内の x-ms-request-id は処理 ID です。この ID を次の $opId に設定する必要があります。

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "opId"
        $response2 = Invoke‐WebRequest ‐Uri $uri2 ‐ContentType "application/xml" ‐Certificate $certificate ‐Headers $headers ‐Method GET

OS VHD や追加の空のデータ ディスクから VM イメージを作成するには (このディスクの VHD を作成していない場合)、VM イメージ作成 API で次のスクリプトを使用します。

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get‐AzureSubscription –Current ‐ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =
        "<VMImage xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema‐instance">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" +
        "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x‐ms‐version"="2014‐06‐01"}
        $response = Invoke‐WebRequest ‐Uri $uri ‐ContentType "application/xml" ‐Body $body ‐Certificate $certificate ‐Headers $headers ‐Method POST
        if ($response.StatusCode ‐ge 200 ‐and $response.StatusCode ‐lt 300)
        {
        echo "Accepted"
        }
        else
        {
        echo "Not Accepted"
        }

このスクリプトを実行すると、ImageName パラメーターに指定した名前 myVMImage でユーザー VM イメージが作成されます。これは、1 つの OS ディスクと 1 つのデータ ディスクから成ります。

この API は非同期処理を実行し、202 を承認して応答します。VM イメージが作成されたことを確認するには、クエリを実行して処理の状態を問い合わせる必要があります。返される応答内の x-ms-request-id は処理 ID です。この ID を次の $opId に設定する必要があります。

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "$opId"
        $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" Certificate $certificate -Headers $headers -Method GET

OS VHD や追加の空のデータ ディスクから VM イメージを作成するには (このディスクの VHD を作成していない場合)、VM イメージ作成 API で次のスクリプトを使用します。

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite' $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink =
        'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription –Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" + "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" + "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body $body Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        { echo "Accepted"
        } else
        { echo "Not Accepted"
        }

このスクリプトを実行すると、ImageName パラメーターに指定した名前 "myVMImage" でユーザー VM イメージが作成されます。これは、渡した VHD をベースとした 1 つの OS ディスクと 1 つの空の 32 GB データ ディスクから成ります。

### 4\.2 ユーザー VM イメージからの VM のデプロイ
ユーザー VM イメージから VM をデプロイするには、既存の [Microsoft Azure 管理ポータル](https://manage.windowsazure.com)または PowerShell を使用します。

**既存の Microsoft Azure 管理ポータルからの VM のデプロイ**

1. **[新規]、[Compute]、[VM]、[ギャラリーから]** の順に移動します。

    ![図][img-manage-vm-new]

2. **[マイ イメージ]** に移動し、VM のデプロイ元の VM イメージを選択します。
  1. [マイ イメージ] ビューには OS イメージと VM イメージの両方が表示されるため、イメージを選択する際には注意が必要です。
  2. ほとんどの VM イメージに 1 つ以上のディスクが含まれるため、ディスクの数に注目すると、デプロイするイメージの種類を特定する際に役立ちます。ただし、"ディスク数" が 1 に設定された、OS ディスクが 1 つのみの VM イメージを選択することもできます。

    ![図][img-manage-vm-select]

3. VM 作成ウィザードに従って、VM 名、VM サイズ、場所、ユーザー名、パスワードを指定します。

**PowerShell からの VM のデプロイ**

作成したばかりの汎用化された VM イメージから大規模な VM をデプロイするには、次のコマンドレットを使用します。

    $img = Get‐AzureVMImage ‐ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New‐AzureVMConfig ‐Name "VMImageVM" ‐InstanceSize "Large" ‐ImageName $img.ImageName | Add‐AzureProvisioningConfig ‐Windows ‐AdminUsername $user ‐Password $pass
    New‐AzureVM ‐ServiceName "VMImageCloudService" ‐VMs $myVM ‐Location "West US" ‐WaitForBoot

## 5\.VM イメージの証明書の取得
Azure ストア用の VM イメージを準備するための次のステップは、イメージの認定です。

このプロセスには、特別な認定ツールの実行、検証結果の Azure コンテナー (VHD が配置された) へのアップロード、プランの追加、SKU の定義、認定のための VM イメージの送信などがあります。

### 5\.1 Microsoft Azure 認定ツールのダウンロードと実行
Microsoft Azure 認定ツールを、VM イメージからプロビジョニングされた VM の実行に対して実行することで、VM イメージが Microsoft Azure と互換性があることが証明されます。VHD の準備に関するガイダンスと要件を満たしているかどうかを検証します。ツールの出力の互換性レポートを、認定要求のときに発行ポータルにアップロードする必要があります。

認定ツールは、Windows および Linux VM の両方で使用できます。Windows VM には PowerShell から接続し、Linux VM には SSH.Net から接続します。

1. 最初に、[Microsoft ダウンロード サイト][link-msft-download]で認定ツールをダウンロードします。
2. 認定ツールを開き、**[新規テストの開始]** ボタンをクリックします。
3. **[テスト情報]** 画面で、実行するテストの名前を入力します。
4. Linux VM か Windows VM かを選択します。選択内容に基づいて、後続のオプションを選択します。

### **認定ツールを Linux VM イメージに接続する**

1. SSH 認証モード (パスワードまたはキー ファイル) を選択します。
2. パスワード ベースの認証を使う場合は、DNS 名、ユーザー名、およびパスワードを入力します。
3. キー ファイル認証を使う場合は、DNS、ユーザー名、および秘密キーの場所を入力します。

  ![Linux VM イメージのパスワード認証][img-cert-vm-pswd-lnx]

  ![Linux VM イメージのキー ファイル認証][img-cert-vm-key-lnx]

### **認定ツールを Windows VM イメージに接続する**

1. MyVMName.Cloudapp.net などの完全修飾 VM ドメイン名システム (DNS) を入力します。
2. ユーザー名とパスワードを入力します。

  ![Windows VM イメージのパスワード認証][img-cert-vm-pswd-win]

Linux または Windows VM イメージに適したオプションを選択した後、**[接続のテスト]** をクリックして、SSH.Net または PowerShell にテスト用の有効な接続が確立されたことを確認します。接続が確立されたら、**[次へ]** をクリックしてテストを開始します。

テストが完了すると、各テスト要素の結果 (合格/不合格/警告) が届きます。

![Linux VM イメージのテスト ケース][img-cert-vm-test-lnx]

![Windows VM イメージのテスト ケース][img-cert-vm-test-win]

いずれかのテストが失敗した場合、イメージは認定されません。要件を確認し、必要な変更を行ってください。

自動テストが済むと、アンケート画面で VM イメージへの追加入力を求められます。質問に回答し、**[次へ]** をクリックします。

![認定ツールのアンケート][img-cert-vm-questionnaire]

![認定ツールのアンケート][img-cert-vm-questionnaire-2]

アンケートに回答した後は、Linux VM イメージの SSH アクセス情報や、失敗した評価の説明などの他の情報を提供できます。実行されたテスト ケースのテスト結果とログ ファイルおよびアンケートへの回答をダウンロードできます。VHD と同じコンテナーに結果を保存します。

![認定テスト結果の保存][img-cert-vm-results]

### 5\.2 VM イメージの Shared Access Signature の URI (Uniform Resource Identifier) の取得

発行プロセスにおいて、SKU として作成した各 VHD にリンクされた URI を指定します。マイクロソフトは、認定プロセスでこれらの VHD にアクセスします。このため、各 VHD の Shared Access Signature (SAS) の URI (Uniform Resource Identifier) を作成する必要があります。これは、発行ポータルの **[イメージ]** タブに入力する URI です。

作成した SAS URI は次の要件を満たす必要があります。

- VHD の SAS URI の作成時に、一覧表示および読み取り専用アクセス許可が付与されていること。書き込みまたは削除アクセス権を付与しないでください。
- アクセス許可の期間は、SAS URI の作成時から最低 7 営業日数必要です。
- クロックスキューによるエラーを回避するには、時刻を現在の時刻の 15 分前に指定します。

SAS URI を作成するには、「[共有アクセス署名、パート 1:SAS モデルについて][link-azure-1]」、「[パート 2: BLOB サービスによる SAS の作成および使用][link-azure-2]」に記載された手順を使用できます。

コードを使用して共有アクセス キーを生成する代わりに、[Azure Storage エクスプローラー][link-azure-codeplex]などのストレージ ツールを使用することもできます。

**Azure Storage エクスプローラーを使用した共有アクセス キーの生成**

1. [Azure Storage エクスプローラー][link-azure-codeplex] 6 以降を CodePlex からダウンロードします。
2. インストールした後、アプリケーションを開きます。
3. **[アカウントの追加]** をクリックします。

    ![図][img-azstg-add]

4. ストレージ アカウント名、ストレージ アカウント キー、およびストレージ エンドポイントのドメインを指定します。[HTTPS の使用] は**選択しない**でください。

    ![図][img-azstg-setup-1]

5. 上記の手順で、Azure Storage エクスプローラーは特定のストレージ アカウントに接続されています。ストレージ アカウント内のすべてのコンテナーの表示が開始されます。OS ディスクの VHD ファイルを (シナリオによってはデータ ディスクも) コピーしたコンテナーを選択します。

    ![図][img-azstg-setup-2]

6. BLOB コンテナーを選択すると、Azure Storage エクスプローラー アプリケーションはコンテナー内のファイルの表示を始めます。送信する必要があるイメージ ファイル (.vhd) を選択します。

    ![図][img-azstg-setup-3]

7. コンテナーで .vhd ファイルを選択した後、以下で強調表示されている **[セキュリティ]** タブをクリックします。

    ![図][img-azstg-setup-4]

8. **[セキュリティ]** タブをクリックすると、次のダイアログ ボックスが表示されます。[アクセス レベル] タブは既定値のままにして、[共有アクセス署名] タブをクリックします。

    ![図][img-azstg-setup-5]

9. このタブで、次の手順に従って、.vhd イメージの SAS URL を生成します。

    ![図][img-azstg-setup-6]

    a.[アクセスを許可: 開始] -> UTC 時刻を保護するだけの場合は、当日より前の日付を選択します。たとえば、当日が 10/6/2014 の場合は、ここで 10/5/2014 を選択します。

    b.[アクセスを許可] -> 少なくとも 7、8 日を指定します。

    c.[許可する操作] -> [一覧表示] および [読み取り] アクセス許可を許可します。

    d..vhd ファイルを正しく選択した場合、BLOB 名の下に拡張子 .vhd の付いたファイルが表示されます。

    e.[署名の生成] をクリックします。

    f.[生成された共有アクセス署名 URI] で、上の図で強調表示されている部分について以下をチェックします。

    - i.URL が https で始まっていないことを確認します。
    - ii.URI にイメージ ファイル名 .vhd が含まれることを確認します。
    - iii.署名の最後に =rl があることを確認します (これは読み取り/一覧表示アクセスが正常に提供されたことを示します)。 g.生成された SAS URI の動作を確認するには、[ブラウザーでテストする] をクリックします。ダウンロード プロセスが開始します。
10. SAS URI をコピーします。この URI を発行ポータルに貼り付けます。
11. SKU 内の各 VHD に対してこれらのステップを繰り返します。

### 5\.3 VM イメージに関する情報の指定と発行ポータルでの証明書の要求
プランと SKU を作成したら、その SKU に関連するイメージの詳細を入力する必要があります。

1. [発行ポータル][link-pubportal]に移動し、販売者アカウントでログインします。
2. **[VM イメージ]** タブを選択します。
3. ページの上部に一覧表示された ID はプラン ID で、SKU ID ではありません。
4. [SKU] セクションでプロパティを入力します。

    ![図][img-pubportal-vm-skus]

5. **[オペレーティング システム ファミリ]** で OS VHD に関連付けられた OS の種類を選択します。
6. **[オペレーティング システム]** にオペレーティング システムの詳細を入力します。オペレーティング システム ファミリ、種類、バージョン、更新の形式で記載してください。たとえば、Windows Server Datacenter 2014 R2 と記載します。
7. 推奨された 3 つの仮想マシンのサイズを選択します。これらは、イメージの購入およびデプロイ時に Microsoft Azure 管理ポータルの [価格レベル] ブレードでエンド ユーザーに表示されるものです。

  >[AZURE.NOTE]これらは単なる推奨サイズです。エンド ユーザーはイメージに指定されたディスクを収容できる任意の VM サイズを選択できます。

8. バージョンを入力します。バージョン フィールドには、製品とその更新プログラムを識別するためのセマンティック バージョンがカプセル化されています。
  -	バージョンの形式は X.Y.Z で、X、Y、Z は整数です。
  -	異なる SKU のイメージは、メジャー バージョンおよびマイナー バージョンが異なっていてもかまいません。
  -	SKU 内のバージョンはパッチ バージョン (X.Y.Z の Z) が変化する増分変更のみでなければなりません。
9. **OS VHD URL** に、OS VHD に作成した SAS URI を入力します。
10. この SKU に関連付けられたデータ ディスクがある場合は、デプロイ時のこのデータ ディスクのマウント先である論理ユニット番号 (LUN) を選択します。
11. LUN X VHD URL に、最初のデータ VHD に作成した SAS URI を入力します。
12.	検証/テスト結果を認定ツールからアップロードします。
13.	**[証明書の要求]** をクリックします。
14.	追加の各データ ディスク VHD について、ステップ 11、12、13 を繰り返します。

    ![図][img-pubportal-vm-skus-2]

## 次のステップ
認定のための仮想マシン イメージ SKU を送信した後は、「[Azure Marketplace Marketing Content Guide (Azure Marketplace マーケティング コンテンツ ガイド)][link-pushstaging]」に進むことができます。発行プロセスのこのステップでは、**ステップ 3.ステージングでの VM プランのテスト**の前に必要なマーケティング コンテンツ、価格、その他の情報を提供します。ステップ 3 では、プランを Azure Marketplace にデプロイして一般に公開して購入できるようにする前にさまざまなユース ケース シナリオをテストします。

## 関連項目
- [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)

[img-acom-1]: media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]: media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]: media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]: media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]: media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]: media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-azstg-setup-1]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup.png
[img-azstg-setup-2]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-2.png
[img-azstg-setup-3]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-3.png
[img-azstg-setup-4]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-4.png
[img-azstg-setup-5]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-5.png
[img-azstg-setup-6]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-6.png
[img-manage-vm-new]: media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]: media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]: media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-github-waagent]: https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]: https://azurestorageexplorer.codeplex.com/
[link-azure-2]: ../storage/storage-dotnet-shared-access-signature-part-2/
[link-azure-1]: ../storage/storage-dotnet-shared-access-signature-part-1/
[link-msft-download]: http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]: https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]: https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]: https://portal.azure.com
[link-pubportal]: https://publish.windowsazure.com
[link-sql-2014-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md
[link-azure-vm-1]: ../virtual-machines/virtual-machines-linux-create-upload-vhd/
[link-technet-1]: https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]: ../virtual-machines/virtual-machines-linux-agent-user-guide/
[link-openssl]: https://www.openssl.org/
[link-intsvc]: http://www.microsoft.com/download/details.aspx?id=41554
[link-python]: https://www.python.org/

<!---HONumber=Nov15_HO3-->