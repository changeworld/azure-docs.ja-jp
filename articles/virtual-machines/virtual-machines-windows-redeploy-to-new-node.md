<properties 
	pageTitle="Windows 仮想マシンの再デプロイ |Microsoft Azure" 
	description="RDP 接続の問題を軽減するために、Windows 仮想マシンを再デプロイする方法について説明します。" 
	services="virtual-machines-windows" 
	documentationCenter="virtual-machines" 
	authors="iainfoulds" 
	manager="timlt"
	tags="azure-resource-manager,top-support-issue" 
/>
	

<tags 
	ms.service="virtual-machines-windows" 
	ms.devlang="na" 
	ms.topic="support-article" 
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure" 
	ms.date="09/19/2016" 
	ms.author="iainfou" 
/>


# 新しい Azure ノードへの仮想マシンの再デプロイ

Windows ベースの Azure 仮想マシン (VM) へのリモート デスクトップ (RDP) 接続またはアプリケーション アクセスに関するトラブルシューティングで問題が発生している場合、VM の再デプロイが有効な場合があります。VM を再デプロイするときは、Azure インフラストラクチャ内の新しいノードに VM を移動してから、電源をオンにすると、すべての構成オプションと関連するリソースが保持されます。この記事では、Azure PowerShell または Azure ポータルを使用して VM を再デプロイする方法について説明します。

> [AZURE.NOTE] VM を再デプロイすると、一時ディスクが失われ、仮想ネットワーク インターフェイスに関連付けられている動的 IP アドレスが更新されます。

## Azure PowerShell の使用

マシンに最新の Azure PowerShell 1.x がインストールされていることを確認します。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

次の Azure PowerShell コマンドを使用して仮想マシンを再デプロイします。

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## 次のステップ
VM への接続に関する問題が発生した場合は、[RDP 接続のトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)または[RDP の詳細なトラブルシューティング手順](virtual-machines-windows-detailed-troubleshoot-rdp.md)に関するページをご覧ください。VM で実行されるアプリケーションにアクセスできない場合は、[アプリケーションの問題のトラブルシューティング](virtual-machines-windows-troubleshoot-app-connection.md)に関するページもご覧ください。

<!---HONumber=AcomDC_0921_2016-->