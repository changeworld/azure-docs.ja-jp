---
title: PowerShell を使用して Linux Azure Automation State Configuration を適用する
description: この記事では、Azure Automation State Configuration で PowerShell を使用して、Linux 仮想マシンをあるべき状態に構成する方法を紹介します。
ms.topic: conceptual
services: automation
ms.subservice: dsc
ms.date: 08/31/2021
ms.openlocfilehash: e4352c5c2cca0391e4e795b537af16c28ded6f6e
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123354393"
---
# <a name="configure-linux-desired-state-with-azure-automation-state-configuration-using-powershell"></a>Azure Automation State Configuration で PowerShell を使用して Linux をあるべき状態に構成する

このチュートリアルでは、PowerShell を使用して Azure Automation State Configuration を Azure Linux 仮想マシンに適用して、これがあるべき状態に準拠しているかどうかを確認します。 あるべき状態は、apache2 サービスがノード上に存在するかどうかを識別することです。
Azure Automation State Configuration を使うと、マシンの構成を指定し、時間が経過してもそのマシンが指定した状態を保つようにすることができます。 State Configuration の詳細については、[Azure Automation State Configuration の概要](./automation-dsc-overview.md)に関するページをご覧ください。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - Azure Automation DSC によって管理する Azure Linux VM をオンボードする
> - 構成を作成する
> - Automation 用の PowerShell モジュールをインストールする
> - Azure Automation に構成をインポートする
> - 構成をノードの構成としてコンパイルする
> - ノードの構成を管理対象ノードに割り当てる
> - ノード構成マッピングを変更する
> - 管理対象ノードの準拠状態を確認する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

- Azure Automation アカウント。 Automation アカウントの詳細については、「[Automation アカウントの認証の概要](./automation-security-overview.md)」を参照してください。
- Ubuntu 18.04 LTS 以降を実行している Azure Resource Manager 仮想マシン (VM)。 Azure Linux VM を作成する手順については、[PowerShell を使用して Azure に Linux 仮想マシンを作成する](../virtual-machines/windows/quick-create-powershell.md)方法に関する記事を参照してください。
- ターゲットの Azure Linux VM に状態構成を書き込み、コンパイルし、適用するために使用する、PowerShell [Az モジュール](/powershell/azure/new-azureps-module-az)がインストールされたマシン。 最新バージョンを使用していることを確認します。 必要であれば、`Update-Module -Name Az` を実行します。

## <a name="create-a-configuration"></a>構成を作成する

次のコードに、`IsPresent` と `IsNotPresent` という 2 つのノード[構成](/powershell/scripting/dsc/configurations/configurations)が含まれていることを確認してください。 この構成では、各ノード ブロック内で 1 つのリソース ([nxPackage リソース](/powershell/scripting/dsc/reference/resources/linux/lnxpackageresource)) が呼び出されます。 このリソースは、**apache2** パッケージの存在を管理します。 次に、テキスト エディターで以下のコードをローカル ファイルにコピーし、`LinuxConfig.ps1` という名前を付けます。

```powershell
Configuration LinuxConfig
{
    Import-DscResource -ModuleName 'nx'

    Node IsPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Present'
            PackageManager    = 'Apt'
        }
    }

    Node IsNotPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Absent'
        }
    }
}
```

## <a name="sign-in-to-azure"></a>Azure へのサインイン

マシンから、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) PowerShell コマンドレットを使用して Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

## <a name="initialize-variables"></a>変数の初期化

効率的にコマンドレットを実行し、エラーの確率を下げるために、以下の PowerShell コードを必要に応じて修正してから実行するようにしてください。

| 変数 | 値 |
|---|---|
|$resourceGroup| `yourResourceGroup` は、実際のリソース グループの名前に置き換えます。|
|$automationAccount| `yourAutomationAccount` は、実際の Automation アカウントの名前に置き換えます。|
|$VM| `yourVM` は、実際の Azure Linux VM の名前に置き換えます。|
|$configurationName| `LinuxConfig` のままにしておきます。 このチュートリアルで使用する構成の名前。|
|$nodeConfigurationName0|`LinuxConfig.IsNotPresent` のままにしておきます。 このチュートリアルで使用するノード構成の名前。|
|$nodeConfigurationName1|`LinuxConfig.IsPresent` のままにしておきます。 このチュートリアルで使用するノード構成の名前。|
|$moduleName|`nx` のままにしておきます。 このチュートリアルで DSC に使用する PowerShell モジュールの名前。|
|$moduleVersion| [PowerShell ギャラリー`nx`から ](https://www.powershellgallery.com/packages/nx) の最新バージョンを取得します。 このチュートリアルでは、バージョン `1.0` を使用します。|

```powershell
$resourceGroup = "yourResourceGroup"
$automationAccount = "yourAutomationAccount"
$VM = "yourVM"
$configurationName = "LinuxConfig"
$nodeConfigurationName0 = "LinuxConfig.IsNotPresent"
$nodeConfigurationName1 = "LinuxConfig.IsPresent"
$moduleName = "nx"
$moduleVersion = "1.0"
```

## <a name="install-nx-module"></a>nx モジュールをインストールする

Azure Automation では、複数の PowerShell モジュールを使用して、Runbook と DSC 構成の DSC リソースでコマンドレットを有効にすることができます。 **nx** は、Linux 用 DSC リソースを含むモジュールです。 **nx** モジュールは、[New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) コマンドレットでインストールします。 モジュールの詳細については、「[Azure Automation でモジュールを管理する](./shared-resources/modules.md)」を参照してください。 次のコマンドを実行します。

```powershell
New-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName `
    -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

出力は次のようになります。

   :::image type="content" source="media/dsc-linux-powershell/new-azautomationmodule-output.png" alt-text="New-AzAutomationModule コマンドの出力。":::

次のコマンドを実行することで、インストールしたことを確認することができます。

```powershell
Get-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName 
```

## <a name="import-configuration-to-azure-automation"></a>Azure Automation に構成をインポートする

[Import-AzAutomationDscConfiguration](/powershell/module/az.automation/import-azautomationdscconfiguration) コマンドレットを呼び出して、構成を Automation アカウントにアップロードします。 `-SourcePath` の値を実際のパスに変更し、次のコマンドを実行します。

```powershell
Import-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -SourcePath "path\LinuxConfig.ps1" `
   -Published
```

出力は次のようになります。

   :::image type="content" source="media/dsc-linux-powershell/import-azautomationdscconfiguration-output.png" alt-text="Import-AzAutomationDscConfiguration コマンドの出力。":::

以下のコマンドを実行することで、Automation アカウントの構成内容を確認することができます。

```powershell
Get-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $configurationName
```

## <a name="compile-configuration-in-azure-automation"></a>Azure Automation で構成をコンパイルする

あるべき状態をノードに適用するには、その状態を定義する構成を 1 つ以上のノード構成にコンパイルする必要があります。  [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) コマンドレットを呼び出して、`LinuxConfig` 構成を Automation アカウントにコンパイルします。 コンパイルの詳細については、[DSC 構成のコンパイル](./automation-dsc-compile.md)に関するページを参照してください。 次のコマンドを実行します。

```powershell
Start-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

出力は次のようになります。

   :::image type="content" source="media/dsc-linux-powershell/start-azautomationdsccompilationjob-output.png" alt-text="Start-AzAutomationDscCompilationJob コマンドの出力。":::

次のコマンドを使用して、Automation アカウントからコンパイル ジョブを表示できます。

```powershell
Get-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

コンパイル ジョブが完了するのを待ってから続行します。 ノードに構成を割り当てるには、先に構成をノードの構成としてコンパイルする必要があります。 次のコードを実行して、5 秒ごとに状態を確認します。

```powershell
while ((Get-AzAutomationDscCompilationJob `
         -ResourceGroupName $resourceGroup `
         -AutomationAccountName $automationAccount `
         -ConfigurationName $configurationName).Status -ne "Completed") 
{
   Write-Output "Wait"
   Start-Sleep -Seconds 5
}
Write-Output "Compilation complete"
```

コンパイル ジョブが完了したら、次のコマンドを使用することで、ノード構成メタデータを表示することもできます。

```powershell
Get-AzAutomationDscNodeConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount
```

## <a name="register-the-azure-linux-vm-for-an-automation-account"></a>Automation アカウントに Azure Linux VM を登録する

Azure Linux VM を、Azure Automation アカウントの Desired State Configuration (DSC) ノードとして登録します。 [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) コマンドレットは、Windows OS を実行する VM のみをサポートします。 まず、Azure Linux VM を DSC 用に構成する必要があります。 詳細な手順については、「[Linux 用 Desired State Configuration (DSC) の概要](/powershell/scripting/dsc/getting-started/lnxgettingstarted)」を参照してください。

1. 次のコードを実行して、後で Azure Linux VM 上で実行するための、登録コマンドを含む python スクリプトを PowerShell で構築します。

   ```powershell
    $primaryKey = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).PrimaryKey
    
    $URL = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).Endpoint
    
    Write-Output "sudo /opt/microsoft/dsc/Scripts/Register.py $primaryKey $URL"
   ```

   これらのコマンドは、Automation アカウントのプライマリ アクセス キーと URL を取得し、それを登録コマンドに連結します。 出力からキャリッジ リターンを削除してください。 このコマンドは、後の手順で使用します。

1. Azure Linux VM に接続します。 パスワードを使用した場合は、次の構文を使用してください。 公開キーと秘密キーのペアを使用した場合は、[Linux 上の SSH](./../virtual-machines/linux/mac-create-ssh-keys.md) に関するページで詳細な手順を確認してください。 残りのコマンドでは、現在インストールされているパッケージで利用可能な更新の情報を含む、インストール可能なパッケージに関する情報を取得し、Python をインストールします。

   ```cmd
   ssh user@IP

   sudo apt-get update
   sudo apt-get install -y python
   ```

1. Open Management Infrastructure (OMI) をインストールします。 OMI の詳細については、[Open Management Infrastructure](https://github.com/Microsoft/omi) に関するページを参照してください。 最新[リリース](https://github.com/Microsoft/omi/releases)を確認します。 必要に応じて以下のリリース バージョンを修正し、ssh セッションでコマンドを実行してください。

   ```bash
   wget https://github.com/microsoft/omi/releases/download/v1.6.8-0/omi-1.6.8-0.ssl_110.ulinux.x64.deb

   sudo dpkg -i ./omi-1.6.8-0.ssl_110.ulinux.x64.deb
   ```

1. Linux 用 PowerShell Desired State Configuration をインストールします。 詳細については、[Linux での DSC](https://github.com/microsoft/PowerShell-DSC-for-Linux) に関するページを参照してください。 最新[リリース](https://github.com/microsoft/PowerShell-DSC-for-Linux/releases)を確認します。 必要に応じて以下のリリース バージョンを修正し、ssh セッションでコマンドを実行してください。

   ```bash
   wget https://github.com/microsoft/PowerShell-DSC-for-Linux/releases/download/v1.2.1-0/dsc-1.2.1-0.ssl_110.x64.deb

   sudo dpkg -i ./dsc-1.2.1-0.ssl_110.x64.deb
   ```

1. これで、手順 1 で作成した `sudo /opt/microsoft/dsc/Scripts/Register.py <Primary Access Key> <URL>` Python スクリプトを使用して、ノードを登録できるようになりました。 ssh セッションでコマンドを実行すると、次のような出力が表示されます。

   ```output
   instance of SendConfigurationApply
   {
        ReturnValue=0
   }
   
   ```

1. PowerShell で登録を確認するには、次のコマンドを使用します。

   ```powershell
     Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM  
   ```

   出力は次のようになります。

      :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnode-output.png" alt-text="Get-AzAutomationDscNode コマンドの出力。":::

## <a name="assign-a-node-configuration"></a>ノード構成を割り当てる

[Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) コマンドレットを呼び出して、ノード構成マッピングを設定します。 次のコマンドを実行します。

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $VM

# Set node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName0 `
   -NodeId $node.Id `
   -Force
```

出力は次のようになります。

   :::image type="content" source="media/dsc-linux-powershell/set-azautomationdscnode-output.png" alt-text="Set-AzAutomationDscNode コマンドの出力。":::

## <a name="modify-the-node-configuration-mapping"></a>ノード構成マッピングを変更する

[Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) コマンドレットを呼び出して、ノード構成マッピングを変更します。 ここでは、現在のノード構成のマッピングを `LinuxConfig.IsNotPresent` から `LinuxConfig.IsPresent` に変更します。 次のコマンドを実行します。

```powershell
# Modify node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName1 `
   -NodeId $node.Id `
   -Force
```

## <a name="check-the-compliance-status-of-a-managed-node"></a>管理対象ノードの準拠状態を確認する

State Configuration でマネージド ノードの整合性チェックが実行されるたびに、そのノードから状態レポートがプル サーバーに送信されます。 次の例では、[Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) コマンドレットを使用して、管理対象ノードの準拠状態に関するレポートを生成できます。

```powershell
Get-AzAutomationDscNodeReport `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeId $node.Id `
   -Latest
```

出力は次のようになります。

   :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnodereport-output.png" alt-text="Get-AzAutomationDscNodeReport コマンドの出力。":::

最初のレポートはすぐには使用できず、ノードを有効にしてから最大で 30 分かかることがあります。 レポートのデータの詳細については、「[DSC レポート サーバーの使用](/powershell/scripting/dsc/pull-server/reportserver)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

以下の手順は、このチュートリアルのために作成したリソースのうち、不要になったものを削除するのに役立ちます。

1. Automation アカウントの管理対象から DSC ノードを削除します。 PowerShell を使用してノードを登録することはできませんが、PowerShell を使用して登録を解除することはできます。 次のコマンドを実行します。

    ```powershell
    # Get the ID of the DSC node
    $NodeID = (Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM).Id
    
    Unregister-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Id $NodeID `
       -Force

    # Verify using the same command from Register the Azure Linux VM for an Automation account. A blank response indicates success.
    Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM
    ```

1. Automation で DSC ノード構成からメタデータを削除します。 次のコマンドを実行します。

    ```powershell
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0 `
       -IgnoreNodeMappings `
       -Force
    
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1 `
       -IgnoreNodeMappings `
       -Force

    # Verify using the same command from Compile configuration in Azure Automation.
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0
    
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1
    ```

      正常に削除された場合、出力は次のようになります: `Get-AzAutomationDscNodeConfiguration : NodeConfiguration LinuxConfig.IsNotPresent not found`。

1. DSC 構成を Automation から削除します。 次のコマンドを実行します。

    ```powershell
    Remove-AzAutomationDscConfiguration `
       -AutomationAccountName $automationAccount `
       -ResourceGroupName $resourceGroup `
       -Name $configurationName `
       -Force

    # Verify using the same command from Import configuration to Azure Automation.
    Get-AzAutomationDscConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $configurationName
    ```

   正常に削除された場合、出力は次のようになります: `Get-AzAutomationDscConfiguration : Operation returned an invalid status code 'NotFound'`。

1. Automation から nx モジュールを削除します。 次のコマンドを実行します。

    ```powershell
    Remove-AzAutomationModule `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $moduleName -Force

    # Verify using the same command from Install nx module.
    Get-AzAutomationModule `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $moduleName
    ```

   正常に削除された場合、出力は次のようになります: `Get-AzAutomationModule : The module was not found. Module name: nx.`。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、PowerShell を使用して Azure Automation State Configuration を Azure Linux VM に適用して、これがあるべき状態に準拠しているかどうかを確認しました。 構成の生成に関する詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [DSC 構成を作成する](./compose-configurationwithcompositeresources.md)