<properties
 pageTitle="仮想マシン エージェントおよび拡張機能について | Microsoft Azure"
 description="エージェントおよび拡張機能の概要とエージェントのインストール方法について説明します。"
 services="virtual-machines"
 documentationCenter=""
 authors="squillace"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="09/22/2015"
 ms.author="rasquill"/>
 
#仮想マシンのエージェントおよび拡張機能について
Azure 仮想マシン エージェント (VM エージェント) は、Azure 仮想マシン拡張機能 (VM 拡張機能) のインストール、構成、管理、および実行に使用します。VM 拡張機能により、Microsoft とその他のサード パーティが提供する動的機能を使用できます。エージェントと拡張機能は主に管理ポータルを使用して追加しますが、VM の作成時または既存の VM の使用時に、[PowerShell](../install-configure-powershell.md) コマンドレットまたは [Azure CLI](xplat-install.md) を使用して追加して構成することもできます。

> [AZURE.NOTE]このトピックでは PowerShell と Azure CLI について説明しますが、説明するデプロイ呼び出しは従来のデプロイ モデルのためのものであり、リソース マネージャー デプロイ モデル用ではありません。デプロイ モデルの詳細については、「[Azure リソース マネージャーにおける Azure コンピューティング、ネットワーク、ストレージ プロバイダー](virtual-machines-azurerm-versus-azuresm.md)」を参照してください。


VM 拡張機能により以下を実行できます。

-   セキュリティおよび ID 機能の変更 (アカウント値のリセットやマルウェア対策の使用など)
-   監視および診断の開始、停止、構成
-   接続機能 (RDP や SSH など) のリセットまたはインストール
-   VM の診断、監視、管理

他にも多くの機能があります。VM 拡張機能は定期的にリリースされます。この記事では、Windows および Linux 向けの Azure VM エージェントと、それらのエージェントの VM 拡張機能のサポート方法について説明します。機能カテゴリごとの VM 拡張機能の一覧については、「[Azure VM 拡張機能とその機能](virtual-machines-extensions-features.md)」を参照してください。

##Windows および Linux 用 Azure VM エージェント

Azure Virtual Machines エージェント (VM エージェント) はセキュリティで保護された軽量のプロセスであり、イメージ ギャラリーから Azure Virtual Machines のインスタンスに対して、また VM エージェントをインストール済みのカスタム VM インスタンスに対して、VM 拡張機能のインストール、構成、削除を行います。VM エージェントは、安全な Azure VM のローカル制御サービスとして機能します。エージェントから読み込まれるされる拡張機能によって、インスタンスを使用して生産性を向上させる機能が提供されます。

Azure VM エージェントには、Windows VM 向けと Linux VM 向けの 2 種類があります。既定では、VM エージェントはイメージ ギャラリーから VM を作成したときに自動でインストールされます。ただし、インスタンスの作成後、または自分でアップロードしたカスタム VM イメージへのインスタンスのインストール後に、VM エージェントをインストールすることもできます。

>[AZURE.IMPORTANT]これらの VM エージェントは、仮想マシン インスタンスを安全に管理できるようにする非常に軽量のサービスです。VM エージェントを必要としない場合もあります。この場合には、VM エージェントがインストールされていない VM を作成する必要があります。VM エージェントは物理的に削除できますが、インスタンス上でのすべての VM 拡張機能の動作は定義されていません。このため、インストールされた VM エージェントの削除は、この時点ではサポートされていません。

VM エージェントは、次のような状況で有効化されます。

-   管理ポータルの**簡易作成**の方法を使用して仮想マシンのインスタンスを作成した場合。または、管理ポータルの**カスタム作成**の方法を使用し、**[VM エージェントのインストール]** チェックボックスをオンにして (下記画像を参照) 仮想マシンのインスタンスを作成した場合。詳細については、「[カスタム仮想マシンを作成する方法](virtual-machines-create-custom.md)」を参照してください。

    ![VM エージェントのチェックボックス](./media/virtual-machines-extensions-agent-about/IC719409.png "VM エージェントのチェックボックス")

-   [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) コマンドレットまたは [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx) コマンドレットを使用して仮想マシンのインスタンスを作成した場合。[Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) コマンドレットに **–DisableGuestAgent** パラメーターを追加すると、VM エージェントをインストールせずに VM を作成することができます。

-   VM エージェント (Window バージョンまたは Linux バージョンのいずれか) を手動でダウンロードして既存の VM インスタンスにインストールし、PowerShell または REST 呼び出しで **ProvisionGuestAgent** の値を **true** に設定した場合(VM エージェントの手動インストール後にこの値を設定しない場合、VM エージェントを追加したことが正常に検出されません)。 次のコード例に、`$svc` 引数と `$name` 引数が決定済みの場合に PowerShell を使用してこの処理を行う方法を示します。

        $vm = Get-AzureVM –serviceName $svc –Name $name
        $vm.VM.ProvisionGuestAgent = $TRUE
        Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

-   VM エージェントを Azure へアップロードする前にインストールした VM イメージを作成した場合。Window VM の場合、[Windows VM Agent.msi ファイル](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をダウンロードして、VM エージェントをインストールします。Linux VM の場合、VM エージェントは github リポジトリの <https://github.com/Azure/WALinuxAgent> からインストールします。Linux への VM エージェントのインストール方法の詳細については、「[Azure Linux エージェント ユーザー ガイド](virtual-machines-linux-agent-user-guide.md)」を参照してください。

>[AZURE.NOTE]PaaS では VM エージェントは **GuestAgent** と呼ばれ、Web ロール VM と Worker ロール VM でいつでも使用できます(詳細については、「[Azure Role Architecture](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx)」 (Azure ロールのアーキテクチャ) を参照してください)。 ロール VM 用の VM エージェントで、永続 Virtual Machines の場合と同じ方法でクラウド サービス VM に拡張機能を追加できるようになりました。ロール VM の VM 拡張機能と永続 VM の VM 拡張機能の最も大きな違いは、ロール VM では、拡張機能がクラウド サービス、そのクラウド サービス内のデプロイの順に追加されることです。

>すべての利用可能なロール VM 拡張機能の一覧を表示するには、[Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) コマンドレットを使用します。

##VM 拡張機能の検索、追加、更新、および削除  

これらのタスクの詳細については、「[仮想マシン拡張機能の管理](virtual-machines-extensions-install.md)」を参照してください。

<!---HONumber=Sept15_HO4-->