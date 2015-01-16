<properties title="How to Configure An Availability Set for Virtual Machines" pageTitle="仮想マシンの可用性セットを構成する方法" description="Azure VM の可用性セットを構成する手順について説明します。" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="kathydav" />

#仮想マシンの可用性セットを構成する方法#

可用性セットは、メンテナンス時などのシステム停止時に仮想マシンを利用するのに役立ちます。同じように構成された 2 つ以上の仮想マシンを同じ可用性セットに割り当てることによって、仮想マシンが実行するアプリケーションやサービスの可用性を維持するために必要な冗長性が実現されます。この機能の詳細については、「[仮想マシンの可用性管理]」を参照してください[]。 

アプリケーションをいつでも利用できるようにするだけでなく、効率的に実行できるようにするためには、可用性セットと負荷分散エンドポイントの両方を使用することをお勧めします。負荷分散されたエンドポイントの詳細については、「[Azure インフラストラクチャ サービスの負荷分散]」を参照してください[]。

仮想マシンを可用性セットに割り当てるには、2 つの方法があります。

- [方法 1:仮想マシンと可用性セットを同時に作成する][]。この後で、新しい仮想マシンを作成する場合には、ここで作成した可用性セットに追加します。
- [方法 2:既存の仮想マシンを可用性セットに追加する][]。


>[WACOM.NOTE] 複数の仮想マシンを同じ可用性セットに割り当てる場合には同じクラウド サービスに属している必要があります。   

## <a id="createset"> </a>方法 1:仮想マシンと可用性セットを同時に作成する##

管理ポータルを使用する方法と、Azure PowerShell コマンドレットを使用する方法があります。 

管理ポータルを使用する場合:

1. まだサインインしていない場合は、[Azure の管理ポータル](http://manage.windowsazure.com)にサインインします。

2. コマンド バーで、**[新規]** をクリックします。

3. **[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

4. 最初の 2 つの画面で、イメージ、ユーザー名、パスワードなどを選択または入力します。詳細については、「[Windows を実行する仮想マシンの作成][]」を参照してください。
 
5. 3 番目の画面では、リソースのネットワーク、ストレージ、可用性を構成できます。以下の手順を実行します。
	 
	1. クラウド サービスを選択します。ここでは、**[新しいクラウド サービスの作成]** に設定します (ただし、新しい仮想マシンを既存の VM クラウド サービスに追加する場合を除く)。**[クラウド サービス DNS 名]** で、名前を入力します。DNS 名は、仮想マシンに接続するために使用される URI の一部になります。クラウド サービスは、通信および分離グループとしての機能があります。すべての仮想マシンが同じクラウド サービス内に属していれば、相互の通信、負荷分散設定、同一可用性セットへの割り当てができます。 

	2. 仮想ネットワークを使用する場合には、**[リージョン/アフィニティ グループ/仮想ネットワーク]** で、使用する仮想ネットワークを指定します。**重要**:仮想マシンで仮想ネットワークを使用する場合は、仮想マシンの作成時に仮想ネットワークに参加させる必要があります。仮想マシンの作成後に仮想マシンを仮想ネットワークに参加させることはできません。詳細については、「[仮想ネットワークの概要][]」を参照してください。 
	
	3. 可用性セットを作成します。**[可用性セット]** で、**[可用性セットの作成]** が選択されたままにします。次に、可用性セットの名前を入力します。 
	4. 既定のエンドポイントを作成し、必要に応じてエンドポイントを追加します。エンドポイントは後で追加することもできます。 

	![Create an availabililty set for a new VM](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png) 

6. 4 番目の画面で、インストールする拡張機能を選択します。拡張機能には、マルウェア対策の実行やパスワードのリセットなどの機能が用意されており、仮想マシンの管理に役立ちます。詳細については、「[Azure VM Agent and VM Extensions (Azure VM エージェントおよび VM 拡張機能)](http://go.microsoft.com/fwlink/p/?LinkId=XXX)」を参照してください。

7.	矢印をクリックして、仮想マシンと可用性セットを作成します。

	新しい仮想マシンのダッシュボードで **[構成]** をクリックし、仮想マシンが新しい可用性セットに属していることを確認します。

Azure のコマンドレットを使用する場合:

1.	Azure PowerShell セッションを開き、次の例のようなコマンドを実行します。これらの例は、仮想マシン、クラウド サービス、および可用性セットを作成中であることが想定されています。

2.	仮想マシンを作成するときに使用するイメージの名前を取得して変数に格納します。次の例のコマンドでは、イメージ オブジェクトのインデックス番号を使用して ImageName プロパティを取得します。目的のイメージの現在のインデックス番号を知っていることと、&lt;index_number&gt; の代わりにその番号を使用することを前提とします。 <br>

	`C:\PS> $image = (Get-AzureVMImage)[<index_number>].ImageName`

	>[WACOM.NOTE] サブスクリプションに適用されるすべてのイメージの一覧を取得するには、パラメーターなしで、`Get-AzureVMImage` を実行します。この結果、大規模な一覧が返される場合があります。一覧を短くするには、イメージのファミリ名などのプロパティを使用します。これを使用して、指定したイメージを見つける方法のヒントと例については、「[Windows PowerShell を使用してイメージを管理する](http://msdn.microsoft.com/ja-jp/library/azure/dn790330.aspx)」を参照してください。

3.	新しい仮想マシンの構成を指定します。次に、パイプラインを使用して、VM を作成するコマンドレットに構成オブジェクトを渡します。&lt;VmName&gt; や &lt;VmSize&gt; などのプレースホルダーは、必ず実際の値に置き換えてください。

	`C:\PS> New-AzureVMConfig -Name "<VmName>" -InstanceSize <VmSize> -AvailabilitySetName "<SetName>" -ImageName $image | Add-AzureProvisioningConfig -Windows -AdminUsername "<UserName>" -Password "<MyPassword>" | Add-AzureDataDisk -CreateNew -DiskSizeInGB 50 -DiskLabel 'datadisk1' -LUN 0 | New-AzureVM -ServiceName "<MySvc1>" `

## <a id="addmachine"> </a>方法 2:既存の仮想マシンを可用性セットに追加する##

管理ポータルでは、既存の仮想マシンを既存の可用性セットに追加することも、既存の仮想マシン用に新しい可用性セットを作成することもできます(仮想マシンは同じクラウド サービスに属している必要があります)。いずれの場合も、手順はほとんど同じです。Azure PowerShell では、既存の仮想マシンを既存の可用性セットに追加することができます。 

1. まだサインインしていない場合は、[Azure の管理ポータル](http://manage.windowsazure.com)にサインインします。

2. ナビゲーション バーで、**[仮想マシン]** をクリックします。

3. 仮想マシンの一覧から、既存の可用性セットに追加する仮想マシンを選択します。仮想マシンの行をクリックすると、ダッシュボードが表示されます。

4. 仮想マシン名の下のタブの中から、**[構成]** をクリックします。 

5. [設定] セクションで、**[可用性セット]** を探します。次のいずれかを実行します。

	A. **[可用性セットの作成]** を選択し、その名前を入力します。

	B. **[可用性セットの選択]** を選択し、一覧から目的の可用性セットを選択します。

	![Create an availabililty set for an existing VM](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png) 

6. **[保存]** をクリックします。

Azure のコマンドレットを使用する場合:

Azure PowerShell セッションを開き、次のコマンドを実行します。&lt;VmCloudServiceName&gt; や &lt;VmName&gt; などのプレースホルダーは、必ず実際の値に置き換えてください。

	C:\PS> Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<MyAvSet>" | Update-AzureVM

>[WACOM.NOTE] 可用性セットに仮想マシンを追加する手順を完了するには、仮想マシンを再起動する必要があります。


##その他のリソース
[Azure VM の構成設定について]

[方法 1:仮想マシンと可用性セットを同時に作成する]: #createset
[方法 2:既存の仮想マシンを可用性セットに追加する]: #addmachine

<!-- LINKS -->
[Azure インフラストラクチャ サービスの負荷分散]: ../virtual-machines-load-balance
[仮想マシンの可用性管理]: ../virtual-machines-manage-availability
[Windows を実行する仮想マシンの作成]: ../virtual-machines-windows-tutorial
[仮想ネットワークの概要]: http://go.microsoft.com/fwlink/p/?linkid=294063
[About Affinity Groups for Virtual Network (仮想ネットワークのアフィニティ グループについて)]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx
[How to connect virtual machines in a cloud service (クラウド サービス内の仮想マシンを接続する方法)]: ../virtual-machines-connect-cloud-service
[Azure VM の構成設定について]: http://msdn.microsoft.com/ja-jp/library/azure/dn763935.aspx


<!--HONumber=35.1-->
