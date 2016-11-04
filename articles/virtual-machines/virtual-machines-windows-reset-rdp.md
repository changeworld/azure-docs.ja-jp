---
title: Windows VM でパスワードまたはリモート デスクトップの構成をリセットする | Microsoft Docs
description: Azure Portal または Azure PowerShell を使用して、Windows VM でアカウントのパスワードまたはリモート デスクトップ サービスをリセットする方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: iainfou

---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Windows VM でリモート デスクトップ サービスまたはそのログイン パスワードをリセットする方法
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Windows 仮想マシン (VM) に接続できない場合、ローカル管理者パスワードをリセットすることも、リモート デスクトップ サービスの構成をリセットすることもできます。 Azure ポータルまたは Azure PowerShell で VM アクセス拡張機能を使用して、パスワードをリセットできます。 PowerShell を使用する場合は、最新の PowerShell モジュールが作業コンピューターにインストールされており、Azure サブスクリプションにサインインしていることを確認します。 詳細な手順については、「 [Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」をご覧ください。

> [!TIP]
> インストールされている PowerShell のバージョンを確認するには、`Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version` を使用します。
> 
> 

## <a name="windows-vms-in-resource-manager-deployment-model"></a>リソース マネージャー デプロイ モデルにおける Windows VM
### <a name="azure-portal"></a>Azure ポータル
**[参照]** > **[仮想マシン]** > *ご使用の Windows 仮想マシン* > **[すべての設定]** > **[パスワードのリセット]** の順にクリックして VM を選択します。 パスワード リセット ブレードが表示されます。

![パスワード リセット ページ](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

ユーザー名と新しいパスワードを入力し、 **[保存]**をクリックします。 VM への接続を再度試してみます。

### <a name="vmaccess-extension-and-powershell"></a>VMAccess 拡張機能と PowerShell
Azure PowerShell 1.0 以降がインストールされ、 `Login-AzureRmAccount` コマンドレットを使用してアカウントにサインインしていることを確認します。

#### <a name="**reset-the-local-administrator-account-password**"></a>**ローカル管理者アカウント パスワードのリセット**
管理者パスワードまたはユーザー名をリセットするには、 [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell コマンドを使用します。

次のコマンドを使用してローカル管理者アカウントの資格情報を作成します。

    $cred=Get-Credential

現在のアカウントと異なる名前を入力すると、以下の VMAccess 拡張機能コマンドによってローカル管理者アカウントの名前が変更されて、そのアカウントにパスワードが割り当てられます。さらに、リモート デスクトップのログオフ イベントが発行されます。 ローカル管理者アカウントが無効になっている場合は、VMAccess 拡張機能によって有効に設定されます。

VM アクセス拡張機能を使用して、次のように新しい資格情報を設定します。

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
        -Location WestUS -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


`myRG`、`myVM`、`myVMAccess`、および場所を、セットアップに関連する値に置き換えます。

#### <a name="**reset-the-remote-desktop-service-configuration**"></a>**リモート デスクトップ サービスの構成のリセット**
VM へのリモート アクセスをリセットするには、次のように [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) または [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) を使用します。 (`myRG`、`myVM`、`myVMAccess`、および場所は、実際の値で置き換えます)

    Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
        -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

または <br>

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [!TIP]
> どちらのコマンドでも、新しい名前の VM アクセス エージェントが仮想マシンに追加されます。 同時に VM に存在する VM アクセス エージェント数は 1 つのみです。 続けて VM アクセス エージェントのプロパティを設定するには、`Remove-AzureRmVMAccessExtension` または `Remove-AzureRmVMExtension` を使用して以前に設定したアクセス エージェントを削除します。 Azure PowerShell バージョン 1.2.2 以降では、`-ForceRerun` オプションを指定した `Set-AzureRmVMExtension` を実行することで、この手順を回避できます。 `-ForceRerun`を使用するときに、必ず以前のコマンドで設定したものと同じ名前を VM アクセス エージェントに使用します。
> 
> 

それでも仮想マシンにリモート接続できない場合は、 [Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)に関するページの手順をご覧ください。

## <a name="windows-vms-in-the-classic-deployment-model"></a>クラシック デプロイ モデルにおける Windows VM
### <a name="azure-portal"></a>Azure ポータル
クラシック デプロイメント モデルを使用して仮想マシンを作成した場合、 [Azure ポータル](https://portal.azure.com) を使用してリモート デスクトップ サービスをリセットできます。 **[参照]** > **[仮想マシン (クラシック)]** > *ご使用の Windows 仮想マシン* > **[リモートのリセット]** の順にクリックします。 次のページが表示されます。

![RDP 構成のリセット ページ](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

ローカル管理者アカウントの名前とパスワードをリセットすることもできます。 **[参照]** > 、**[仮想マシン (クラシック)]**、 > *ご使用の Windows 仮想マシン*、 > **[すべての設定]** > 、**[パスワードのリセット]** の順にクリックします。 次のページが表示されます。

![パスワード リセット ページ](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

新しいユーザー名とパスワードを入力して、 **[保存]**をクリックします。

### <a name="vmaccess-extension-and-powershell"></a>VMAccess 拡張機能と PowerShell
仮想マシンに VM エージェントがインストールされていることを確認します。 VM エージェントを使用するには、VMAccess 拡張機能を使用前にインストールしておく必要があります。 次のコマンドを使用して VM エージェントがインストールされていることを確認します。 ("myCloudService" と "myVM" は、それぞれ実際のクラウド サービスの名前と VM に置き換えます。 これらの名前を確認するには、パラメーターを指定せずに `Get-AzureVM` を実行します)。

    $vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
    write-host $vm.VM.ProvisionGuestAgent

**write-host** コマンドで **True** が表示される場合は、VM エージェントがインストールされています。 **False**が表示される場合は、Azure ブログの [VM エージェントと拡張機能に関する記事のパート 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) で手順とダウンロード用リンクをご確認ください。

ポータルで仮想マシンを作成した場合、 `$vm.GetInstance().ProvisionGuestAgent` を実行して **True**が返されるかどうかを確認します。 作成していない場合は、次のコマンドを使用して設定できます。

    $vm.GetInstance().ProvisionGuestAgent = $true

このコマンドは、次のステップで **Set-AzureVMExtension** コマンドを実行する場合、"IaaS VM アクセス拡張機能を設定する前に、VM オブジェクトでゲスト エージェントのプロビジョニングを有効にする必要がある" というエラーを防ぐことができます。

#### <a name="**reset-the-local-administrator-account-password**"></a>**ローカル管理者アカウント パスワードのリセット**
現在のローカル管理者アカウント名と新しいパスワードでサインイン資格情報を作成してから、次のように `Set-AzureVMAccessExtension` を実行します。

    $cred=Get-Credential
    Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

現在のアカウントと異なる名前を入力すると、VMAccess 拡張機能によってローカル管理者アカウントの名前が変更されて、そのアカウントにパスワードが割り当てられます。さらに、リモート デスクトップのサインアウトが発行されます。 ローカル管理者アカウントが無効になっている場合は、VMAccess 拡張機能によって有効に設定されます。

これらのコマンドはリモート デスクトップ サービスの構成もリセットします。

#### <a name="**reset-the-remote-desktop-service-configuration**"></a>**リモート デスクトップ サービスの構成のリセット**
リモート デスクトップ サービスの構成をリセットするには、次のコマンドを実行します。

    Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

VMAccess 拡張機能によって仮想マシンで次の 2 つのコマンドが実行されます。

* `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

このコマンドで、組み込みの Windows ファイアウォール グループが有効になり、TCP ポート 3389 を使用するリモート デスクトップの着信トラフィックが許可されます。

* `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

このコマンドは fDenyTSConnections レジストリ値を 0 に設定します。これでリモート デスクトップ接続が有効になります。

## <a name="next-steps"></a>次のステップ
Azure VM アクセス拡張機能が応答せず、パスワードをリセットできない場合は、オフラインの[ローカル Windows パスワードをリセット](virtual-machines-windows-reset-local-password-without-agent.md)します。 この方法はより高度なプロセスであるため、問題のある VM の仮想ハード ディスクを別の VM に接続する必要があります。 最初に、この記事に記載されている手順を実行し、オフラインのパスワードをリセットする方法は最後の手段としてのみ実行してください。

[Azure VM 拡張機能とその機能](virtual-machines-windows-extensions-features.md)

[RDP または SSH を使用した Azure Virtual Machines への接続](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Windows ベースの Azure Virtual Machines へのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!--HONumber=Oct16_HO2-->


