---
title: Azure Automation Runbook に JSON オブジェクトを渡す
description: JSON オブジェクトとして Runbook にパラメーターを渡す方法
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: 1bdeef02621924bbb7af1e676d2b275229761081
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "42140924"
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Azure Automation Runbook に JSON オブジェクトを渡す

Runbook に渡すデータを JSON ファイルに格納すると便利な場合があります。
たとえば、Runbook に渡すすべてのパラメーターを含む JSON ファイルを作成できます。
これを行うには、JSON を文字列に変換し、その文字列を PowerShell オブジェクトに変換してからその内容を Runbook に渡します。

この例では、[Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) を呼び出して PowerShell Runbook を起動する PowerShell スクリプトを作成し、JSON の内容を Runbook に渡します。
PowerShell Runbook は、Azure VM を開始し、渡された JSON から VM のパラメーターを取得します。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、<a href="/pricing/free-account/" target="_blank">[無料アカウントにサインアップ](https://azure.microsoft.com/free/)してください。
* [Automation アカウント](automation-sec-configure-azure-runas-account.md)。Runbook の保存と Azure リソースの認証に使用します。  このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Azure 仮想マシン。 マシンを停止して起動するので、運用 VM は使用しないでください。
* ローカル コンピューターにインストールされている Azure Powershell。 Azure PowerShell の取得方法の詳細については、[Azure PowerShell のインストールと構成の方法](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0)に関するページを参照してください。

## <a name="create-the-json-file"></a>JSON ファイルの作成

テキスト ファイルに次のテストを入力し、これをローカル コンピューター上のどこかに `test.json` として保存します。

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Runbook の作成

Azure Automation で "Test-Json" という名前の PowerShell Runbook を作成します。
新しい PowerShell Runbook を作成する方法については、「[初めての PowerShell Runbook](automation-first-runbook-textual-powershell.md)」を参照してください。

JSON データを受け入れるには、Runbook は、入力パラメーターとしてオブジェクトを受け取る必要があります。

Runbook が JSON で定義されたプロパティを使用できるようになります。

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Automation アカウントでこの Runbook を保存して公開します。

## <a name="call-the-runbook-from-powershell"></a>PowerShell からの Runbook の呼び出し

Azure PowerShell を使用して、ローカル コンピューターから Runbook を呼び出すことができるようになりました。
次の PowerShell コマンドを実行します。

1. Azure へのログイン:
   ```powershell
   Connect-AzureRmAccount
   ```
    Azure 資格情報を入力するよう求められます。

   > [!IMPORTANT]
   > これで、**Connect-AzureRmAccount** のエイリアスは **Add-AzureRMAccount** に設定されました。 ライブラリ項目を検索して **Connect-AzureRMAccount** が表示されない場合は、**Add-AzureRmAccount** を使用するか、Automation アカウントでモジュールを更新できます。

1. JSON ファイルの内容を取得して文字列に変換:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` は、JSON ファイルを保存した場所へのパスです。
1. `$json` の文字列の内容を PowerShell オブジェクトに変換:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. `Start-AzureRmAutomationRunbook` のパラメーターのハッシュ テーブルの作成:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   JSON ファイルの値を含む PowerShell オブジェクトに `Parameters` の値を設定していることに注意してください。 
1. Runbook の起動
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

Runbook は、JSON ファイルの値を使用して、VM を開始します。

## <a name="next-steps"></a>次の手順

* テキスト エディターを使用した PowerShell および PowerShell ワークフロー Runbook の編集の詳細については、「 [Azure Automation でのテキスト形式の Runbook の編集](automation-edit-textual-runbook.md) 
* Runbook の作成およびインポートの詳細については、「[Azure Automation での Runbook の作成またはインポート](automation-creating-importing-runbook.md)」を参照してください。


