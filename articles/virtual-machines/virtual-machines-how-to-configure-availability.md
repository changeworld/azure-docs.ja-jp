<properties
	pageTitle="VM の可用性セットを構成する | Microsoft Azure"
	description="Azure ポータルと Azure PowerShell を使用して新規または既存の仮想マシンに対する可用性セットをクラシック デプロイ モデルで構成します。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="cynthn"/>

# クラシック デプロイ モデルに仮想マシンの可用性セットを構成する方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


可用性セットは、メンテナンス時などのシステム停止時に仮想マシンを利用するのに役立ちます。同じように構成された 2 つ以上の仮想マシンを同じ可用性セットに割り当てることによって、仮想マシンが実行するアプリケーションやサービスの可用性を維持するために必要な冗長性が実現されます。この機能の詳細については、「[仮想マシンの可用性管理][]」を参照してください。

アプリケーションをいつでも利用できるようにするだけでなく、効率的に実行できるようにするためには、可用性セットと負荷分散エンドポイントの両方を使用することをお勧めします。負荷分散されたエンドポイントの詳細については、「[Azure インフラストラクチャ サービスの負荷分散][]」を参照してください。

仮想マシンを可用性セットに割り当てるには、2 つの方法があります。

- [オプション 1: 仮想マシンと可用性セットを同時に作成する][]。この後で、新しい仮想マシンを作成する場合には、ここで作成した可用性セットに追加します。
- [オプション 2: 既存の仮想マシンを可用性セットに追加する][]。

>[AZURE.NOTE]複数の仮想マシンを同じ可用性セットに割り当てる場合には同じクラウド サービスに属している必要があります。

## <a id="createset"> </a>オプション 1: 仮想マシンと可用性セットを同時に作成する##

Azure ポータルを使用する方法と、Azure PowerShell コマンドを使用する方法があります。

Azure ポータルを使用するには。

1. まだサインインしていない場合は、Azure ポータルにサインインします。

2. コマンド バーで、**[新規]** をクリックします。

3. **[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

4. 最初の 2 つの画面で、イメージ、ユーザー名、パスワードなどを選択します。詳細については、「[Windows を実行する仮想マシンの作成][]」を参照してください。

5. 3 番目の画面では、リソースのネットワーク、ストレージ、可用性を構成できます。以下の手順を実行します。

	1. 適切なクラウド サービス名を選択します。ここでは、**[新しいクラウド サービスの作成]** に設定します (ただし、新しい仮想マシンを既存の仮想マシン クラウド サービスに追加する場合を除く)。**[クラウド サービス DNS 名]** で、名前を入力します。DNS 名は、仮想マシンに接続するために使用される URI の一部になります。クラウド サービスは、通信および分離グループとしての機能があります。すべての仮想マシンが同じクラウド サービス内に属していれば、相互の通信、負荷分散設定、同一可用性セットへの割り当てができます。

	2. 仮想ネットワークを使用する場合には、**[リージョン/アフィニティ グループ/仮想ネットワーク]** で、使用する仮想ネットワークを指定します。**重要**: 仮想マシンで仮想ネットワークを使用する場合は、仮想マシンを作成時に仮想ネットワークに参加させる必要があります。作成後に仮想マシンを仮想ネットワークに参加させることはできません。詳細については、「[Virtual Network の概要][]」を参照してください。

	3. 可用性セットを作成します。**[可用性セット]** で、**[可用性セットの作成]** が選択されたままにします。次に、可用性セットの名前を入力します。

	4. 既定のエンドポイントを作成し、必要に応じてエンドポイントを追加します。エンドポイントは後で追加することもできます。

	![新しい仮想マシンの可用性セットを作成する](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png)

6. 4 番目の画面で、インストールする拡張機能をクリックします。拡張機能には、マルウェア対策の実行やパスワードのリセットなどの機能が用意されており、仮想マシンの管理に役立ちます。詳細については、「[Azure VM Agent and VM Extensions (Azure VM エージェントおよび VM 拡張機能)](virtual-machines-extensions-agent-about.md)」を参照してください。

7.	矢印をクリックして、仮想マシンと可用性セットを作成します。

	新しい仮想マシンのダッシュボードで **[構成]** をクリックし、仮想マシンが新しい可用性セットに属していることを確認します。

Azure PowerShell コマンド使用して Azure 仮想マシンを作成し、新規または既存の可用性セットに追加する方法については、次を参照してください。

- [Azure PowerShell を使用して Windows ベースの仮想マシンを作成および事前構成する](virtual-machines-ps-create-preconfigure-windows-vms.md)
- [Azure PowerShell を使用して Linux ベースの仮想マシンを作成と事前構成する](virtual-machines-ps-create-preconfigure-linux-vms.md)

## <a id="addmachine"> </a>オプション 2: 既存の仮想マシンを可用性セットに追加する##

Azure ポータルでは、既存の仮想マシンを既存の可用性セットに追加することも、既存の仮想マシン用に新しい可用性セットを作成することもできます(同じ可用性セット内の仮想マシンは同じクラウド サービスに属する必要がある点に留意してください)。 いずれの場合も、手順はほとんど同じです。Azure PowerShell では、既存の仮想マシンを既存の可用性セットに追加できます。

1. まだサインインしていない場合は、Azure ポータルにサインインします。

2. コマンド バーで **[Virtual Machines]** をクリックします。

3. 仮想マシンの一覧から、既存の可用性セットに追加する仮想マシンの名前を選択します。

4. 仮想マシン名の下のタブの中から、**[構成]** をクリックします。

5. [設定] セクションで、**[可用性セット]** を探します。次のいずれかを実行します。

	A.**[可用性セットの作成]** を選択し、セットの名前を入力します。

	B.**[可用性セットの選択]** を選択し、一覧から目的の可用性セットを選択します。

	![既存の仮想マシンの可用性セットを作成する](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png)

6. **[保存]** をクリックします。

Azure PowerShell コマンドを使用するには、管理者レベルの Azure PowerShell セッションを開き、次のコマンドを実行します。プレース ホルダー (&lt;VmCloudServiceName&gt; など) では、< and > 文字を含む引用符内のすべてを正しい名前に置き換えます。

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE]可用性セットに仮想マシンを追加する手順を完了するには、仮想マシンの再起動が必要になる場合があります。

## その他のリソース

[サービス管理での仮想マシンの操作に関する記事]

<!-- LINKS -->
[オプション 1: 仮想マシンと可用性セットを同時に作成する]: #createset
[オプション 2: 既存の仮想マシンを可用性セットに追加する]: #addmachine

[Azure インフラストラクチャ サービスの負荷分散]: virtual-machines-load-balance.md
[仮想マシンの可用性管理]: virtual-machines-manage-availability.md
[Windows を実行する仮想マシンの作成]: virtual-machines-windows-tutorial.md
[Virtual Network の概要]: virtual-networks-overview.md
[サービス管理での仮想マシンの操作に関する記事]: virtual-machines-service-management-articles.md

<!---HONumber=Nov15_HO3-->