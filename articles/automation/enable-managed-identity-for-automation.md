---
title: Azure Automation アカウントのマネージド ID を有効にする (プレビュー)
description: この記事では、Azure Automation アカウントのマネージド ID を設定する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 04/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1bdba095c18943be5b367bd605d1a22d6eb6499f
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108323676"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>Azure Automation アカウントのマネージド ID を有効にする (プレビュー)

このトピックでは、Azure Automation アカウントに対してマネージド ID を作成し、それを使用して他のリソースにアクセスする方法を説明します。 マネージド ID と Azure Automation の連携方法の詳細については、[マネージド ID](automation-security-overview.md#managed-identities-preview) に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure アカウントとサブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 マネージド ID と、その ID を使用して Runbook が管理するターゲット Azure リソースの両方が、同じ Azure サブスクリプションに存在する必要があります。

- Azure アカウント モジュールの最新バージョン。 現在 2.2.8 です。 (このバージョンの詳細は [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/) をご覧ください。)

- Automation Runbook からアクセスする必要がある Azure リソース。 このリソースには、マネージド ID に対して定義されたロールが必要です。これにより、Automation Runbook はリソースへのアクセスを認証できます。 ロールを追加するには、対応する Azure AD テナントのリソースの所有者である必要があります。

- マネージド ID を使用してハイブリッド ジョブを実行する場合は、Hybrid Runbook Worker を最新バージョンに更新します。 最低限必要なバージョンは次のとおりです。

   - Windows Hybrid Runbook Worker: バージョン 7.3.1125.0
   - Linux Hybrid Runbook Worker: バージョン 1.7.4.0

## <a name="enable-system-assigned-identity"></a>システム割り当て ID を有効にする

>[!IMPORTANT]
>新しい Automation アカウントレベルの ID によって、以前の VM レベルのシステム割り当て ID (「[マネージド ID で Runbook 認証を使用する](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)」で説明されている) がオーバーライドされます。 VM のシステム割り当て ID を使用して Runbook リソースにアクセスする Azure VM でハイブリッド ジョブを実行している場合は、そのハイブリッド ジョブに対して Automation アカウント ID が使用されます。 これは、Automation アカウントのカスタマー マネージド キー (CMK) 機能を使用している場合に、既存のジョブの実行が影響を受ける可能性があることを意味します。<br/><br/>VM のマネージド ID を引き続き使用する場合は、Automation アカウント レベルの ID を有効にしないでください。 それを既に有効にしている場合は、Automation アカウントのマネージド ID を無効にできます。 [Azure Automation アカウントのマネージド ID の無効化](./disable-managed-identity-for-automation.md)に関する記事を参照してください。

Azure Automation に対するシステム割り当て ID の設定は、2 つの方法のいずれかで実行できます。 Azure portal または Azure REST API を使用できます。

>[!NOTE]
>ユーザー割り当て ID はまだサポートされていません。

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Azure portal でシステム割り当て ID を有効にする

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Automation アカウントに移動し、 **[アカウント設定]** で **[ID]** を選択します。

1. **[システム割り当て済み]** オプションを **[オン]** に設定し、 **[保存]** を押します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Azure portal でのシステム割り当て ID の有効化。":::

これで、Automation アカウントはシステム割り当て ID を使用できるようになりました。これは Azure Active Directory (Azure AD) に登録され、オブジェクト ID で表されます。

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="マネージド ID であるオブジェクト ID。":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>REST API を使用してシステム割り当て ID を有効にする

次の REST API 呼び出しを使用して、Automation アカウントに対してシステム割り当てマネージド ID を構成できます。

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

要求本文
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|プロパティ (JSON) | 値 | 説明|
|----------|-----------|------------|
| principalid | \<principal-ID\> | Azure AD テナント内の Automation アカウントを表すマネージド ID に対するサービス プリンシパル オブジェクトのグローバル一意識別子 (GUID)。 この GUID は、"オブジェクト ID" (objectID) として表されることがあります。 |
| tenantid | \<Azure-AD-tenant-ID\> | Automation アカウントが現在メンバーである Azure AD テナントを表すグローバル一意識別子 (GUID)。 Azure AD テナント内では、サービス プリンシパルは Automation アカウントと同じ名前です。 |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>トークンの取得によって ID が Azure リソースにアクセスできるようにする

Azure Key Vault など、Azure AD によって保護されているその他のリソースにアクセスするために、Automation アカウントはそのマネージド ID を使用してトークンを取得できます。 これらのトークンは、アプリケーションの特定のユーザーを表すものではありません。 代わりに、それらは、リソースにアクセスしているアプリケーションを表します。 たとえば、今回の場合、トークンは Automation アカウントを表します。

システム割り当てマネージド ID を認証に使用するには、その ID を使用する予定の Azure リソースに対する ID のアクセスを先に設定します。 このタスクを完了するには、ターゲットの Azure リソース上でその ID に適切な役割を割り当てます。

この例では、Azure PowerShell を使用して、サブスクリプションの共同作成者ロールをターゲットの Azure リソースに割り当てる方法を示します。 共同作成者ロールは例として使用されます。ご自身のケースに必要な場合と不要な場合があります。

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>マネージド ID を利用してアクセスを認証する

Automation アカウントのマネージド ID を有効にし、ID がターゲット リソースにアクセスできるようにした後、マネージド ID をサポートするリソースに対して Runbook 内でその ID を指定できます。 ID のサポートのために、Az cmdlet コマンドレットの `Connect-AzAccount` コマンドレットを使用します。 PowerShell リファレンスの「[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount)」を参照してください。

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>非推奨の AzureRM コマンドレットを組織でまだ使用している場合は、`Connect-AzureRMAccount -Identity` を使用できます。

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Azure コマンドレットを使用せずにアクセス トークンを生成する

HTTP エンドポイントに対して、次のことを確認してください。
- メタデータ ヘッダーが存在し、"true" に設定されている必要があります。
- リソースを GET 要求の場合はクエリ パラメーターとして、POST 要求の場合はフォーム データとして、要求と共に渡す必要があります。
- X-IDENTITY-HEADER は、Hybrid Runbook Worker の環境変数 IDENTITY_HEADER の値に設定する必要があります。 
- Post 要求のコンテンツの種類は 'application/x-www-form-urlencoded' である必要があります。 

### <a name="sample-get-request"></a>サンプルの GET 要求

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>サンプルの POST 要求
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>マネージド ID を使用したサンプル Runbook

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>Azure コマンドレットを使用せずに SQL データベースにアクセスするためのサンプル Runbook

ID を有効にしたことを確認してから、このスクリプトを試します。 「[システム割り当て ID を有効にする](#enable-system-assigned-identity)」を参照してください。

Azure SQL データベースへのアクセス権のプロビジョニングの詳細は、[Azure AD 管理者のプロビジョニング (SQL Database)](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) に関するセクションを参照してください。

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>Azure コマンドレットを使用してキー コンテナーにアクセスするためのサンプル Runbook

ID を有効にしたことを確認してから、このスクリプトを試します。 「[システム割り当て ID を有効にする](#enable-system-assigned-identity)」を参照してください。

詳しくは「[Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret)」をご覧ください。

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="sample-python-runbook-to-get-a-token"></a>トークンを取得するためのサンプル Python Runbook

ID を有効にしたことを確認してから、この Runbook を試します。 「[システム割り当て ID を有効にする](#enable-system-assigned-identity)」を参照してください。

```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

## <a name="next-steps"></a>次の手順

- Runbook が正常に完了しない場合は、「[Azure Automation マネージド ID に関する問題のトラブルシューティング (プレビュー)](troubleshoot/managed-identity.md)」を参照してください。

- マネージド ID を無効にする必要がある場合は、「[Azure Automation アカウントのマネージド ID を無効にする (プレビュー)](disable-managed-identity-for-automation.md)」を参照してください。

- Azure Automation アカウントのセキュリティの概要については、[Automation アカウントの認証の概要](automation-security-overview.md)に関する記事を参照してください。
