---
title: コマンドライン ツールを使用した VM の開始と停止
description: コマンドライン ツールを使用して、Azure DevTest Labs 内の仮想マシンを開始および停止する方法について説明します。
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 58440d00f888816f95aac0159063a7b6d6fc5289
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430193"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>コマンドライン ツールを使用した Azure DevTest Labs 仮想マシンの開始と停止

この記事では、Azure DevTest Labs でラボ仮想マシンを開始または停止する方法について説明します。 Azure PowerShell または Azure CLI スクリプトを作成してこの操作を自動化することができます。 

## <a name="prerequisites"></a>前提条件
- PowerShell を使用している場合は、[Az モジュール](/powershell/azure/new-azureps-module-az)がワークステーションにインストールされている必要があります。 最新バージョンを使用していることを確認します。 必要であれば、`Update-Module -Name Az` を実行します。

- Azure CLI を使用したいが、まだインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

- DevTest Labs ラボの仮想マシン。

## <a name="overview"></a>概要

Azure DevTest Labs を使用すると、高速で簡単な無駄のない Dev/Test 環境を作成する手段が得られます。 ラボを使用すると、コストを管理し、VM をすばやく作成し、無駄を最小限に抑えることができます。 Azure portal の機能を使用して、特定の時点で VM を自動的に開始および停止することができます。 ただし、スクリプトから VM の開始と停止を自動化したい場合があります。 スクリプトを使用してタスクを実行した方が便利なケースを、次にいくつか示します。

- 3 層アプリケーションをテスト環境の一部として使用している場合、各層を順番に開始する必要があります。 
- コスト削減のため、カスタム条件が満たされたときに VM をオフにします。 
- 継続的インテグレーションと継続的デリバリー ワークフロー内のタスクとして、最初から開始して、プロセスの完了時に VM を停止します。 このワークフローの例に、Azure DevTest Labs を使用したカスタム イメージ ファクトリがあります。  

## <a name="azure-powershell"></a>Azure PowerShell

次の PowerShell スクリプトでは、ラボ内の VM を開始または停止できます。 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) が、このスクリプトの主な目的です。 **ResourceId** パラメーターは、ラボ内の VM の完全修飾リソース ID です。 **Action** パラメーターには、**Start** または **Stop** オプションが必要に応じて設定されます。

1. ワークステーションから、PowerShell [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットを使用して Azure サブスクリプションにログインし、画面上の指示に従います。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Set-AzContext -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. 変数に適切な値を指定し、スクリプトを実行します。

    ```powershell
    $devTestLabName = "yourlabname"
    $vMToStart = "vmname"
    
    # The action on the virtual machine (Start or Stop)
    $vmAction = "Start"
    ```

1. `$vmAction` に渡された値に基づいて VM を開始または停止します。

    ```powershell
    # Get the lab information
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName
    
    # Start or stop the VM and return a succeeded or failed status
    $returnStatus = Invoke-AzResourceAction `
                        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                        -Action $vmAction `
                        -Force
    
    if ($returnStatus.Status -eq 'Succeeded') {
        Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
    }
    else {
        Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
    }
    ```

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/get-started-with-azure-cli) は、DevTest Labs の VM を自動的に開始および停止するための、もう 1 つの方法です。 次のスクリプトは、ラボ内の VM を開始および停止するコマンドを示しています。 このセクションの変数の使用は、Windows 環境を前提としています。 bash やその他の環境では、若干の調整が必要となります。

1. `SubscriptionID`、`yourlabname`、 `yourVM`、および `action` を適切な値に置き換えます。 次に、スクリプトを実行します。

    ```azurecli
    set SUBSCIPTIONID=SubscriptionID
    set DEVTESTLABNAME=yourlabname
    set VMNAME=yourVM
    
    REM The action on the virtual machine (Start or Stop)
    set ACTION=action
    ```

1. Azure サブスクリプションにサインインし、ラボが含まれているリソース グループの名前を取得します。

    ```azurecli
    az login
    
    REM If you have multiple subscriptions, set the one to use
    REM az account set --subscription %SUBSCIPTIONID%

    az resource list --resource-type "Microsoft.DevTestLab/labs" --name %DEVTESTLABNAME% --query "[0].resourceGroup"
    ```

1. `resourceGroup` は、前のステップで取得した値に置き換えます。 次に、スクリプトを実行します。

    ```azurecli
    set RESOURCEGROUP=resourceGroup
    ```

1. `ACTION` に渡された値に基づいて VM を開始または停止します。

    ```azurecli
    az lab vm %ACTION% --lab-name %DEVTESTLABNAME% --name %VMNAME% --resource-group %RESOURCEGROUP%
    ```

## <a name="next-steps"></a>次のステップ

Azure portal を使用してこれらの操作を行う方法については、[VM の再起動](devtest-lab-restart-vm.md)に関する記事を参照してください。
