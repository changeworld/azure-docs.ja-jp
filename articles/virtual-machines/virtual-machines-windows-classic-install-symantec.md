---
title: "VM に Symantec Endpoint Protection をインストールする | Microsoft Docs"
description: "クラシック デプロイメント モデルで作成された新しいまたは既存の Azure VM に Symantec Endpoint Protection のセキュリティ拡張機能をインストールして構成する方法を説明します。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 6739f6cad794665b949bc89372085a2e96100af4
ms.openlocfilehash: 1989e2b1d1285cb62076cce2a0bc02b68f20f281


---

# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Windows VM に Symantec Endpoint Protection をインストールし、構成する方法
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

この記事では、Windows Server を実行している既存の仮想マシン (VM) に Symantec Endpoint Protection クライアントをインストールし、構成する方法を説明します。 このクライアントには、ウイルスおよびスパイウェア対策、ファイアウォール、侵入防止などのサービスが含まれています。 このクライアントは、VM エージェントを使用することにより、セキュリティ拡張機能としてインストールできます。

Symantec のオンプレミス ソリューション用サブスクリプションが既にある場合には、それを使用して Azure の仮想マシンを保護できます。 サブスクリプションがない場合には、サインアップして試用サブスクリプションを利用できます。 このソリューションについて詳しくは、[Microsoft Azure プラットフォームでの Symantec Endpoint Protection][Symantec] に関するブログをご覧ください。 このページには、Symantec の顧客向けのライセンス情報とクライアントをインストールする方法の説明へのリンクもあります。

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>既存の VM に Symantec Endpoint Protection をインストールする
インストールを開始するには、次の条件を満たしている必要があります。

* Azure PowerShell モジュール Version 0.8.2 以降がコンピューター上に存在すること。 インストールした Azure PowerShell のバージョンは、 **Get-Module azure | format-table version** コマンドで確認できます。 最新バージョンの説明とダウンロード用リンクについては、[Azure PowerShell のインストールおよび構成方法][PS]に関する記事をご覧ください。 `Add-AzureAccount`を使用して Azure サブスクリプションにログインします。
* Azure 仮想マシンで実行されている VM エージェント。

最初に、VM エージェントが仮想マシンに既にインストールされていることを確認します。 クラウド サービス名と仮想マシン名を入力して、管理者レベルの Azure PowerShell のコマンド プロンプトで、次のコマンドを実行します。 引用符内のすべての文字 (< および > を含む) を置き換えます。

> [!TIP]
> クラウド サービスや仮想マシンの名前がわからない場合は、**Get-AzureVM** を実行します。現在のサブスクリプションに含まれるすべての仮想マシンの名前が表示されます。
> 
> 

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

**write-host** コマンドで **True** が表示される場合は、VM エージェントがインストールされています。 **False** が表示される場合は、Azure ブログの[VM エージェントと拡張機能に関する記事のパート 2][エージェント] で、手順とダウンロード用リンクをご確認ください。

VM エージェントがインストールされている場合、これらのコマンドを実行して Symantec Endpoint Protection エージェントをインストールします。

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Symantec のセキュリティ拡張機能がインストールされ、最新の状態であることを確認するには:

1. 仮想マシンにログオンします。 詳しくは、[Windows Server が実行されている仮想マシンにログオンする方法][ログオン]に関する記事をご覧ください。
2. Windows Server 2008 R2 の場合は、**[スタート]、[Symantec Endpoint Protection]** の順にクリックします。 Windows Server 2012 または Windows Server 2012 R2 で、スタート画面から、「**Symantec**」と入力し、**[Symantec Endpoint Protection]** をクリックします。
3. **[状態: Symantec Endpoint Protection]** ウィンドウの **[状態]** タブで、更新を適用するか、必要に応じて再起動します。

## <a name="additional-resources"></a>その他のリソース
[Windows Server が実行されている仮想マシンにログオンする方法][ログオン]

[Azure VM 拡張機能とその機能][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[ポータル]: http://manage.windowsazure.com

[作成]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[エージェント]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[ログオン]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493



<!--HONumber=Nov16_HO3-->


