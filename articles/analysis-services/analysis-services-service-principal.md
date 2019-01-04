---
title: サービス プリンシパルを使用して Azure Analysis Services タスクを自動化する | Microsoft Docs
description: Azure Analysis Services タスクを自動化するためのサービス プリンシパルを作成する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2acf39a7537d25b05999b7b2813fa66d7f778724
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995795"
---
# <a name="automation-with-service-principals"></a>サービス プリンシパルによる自動化

サービス プリンシパルは、リソース/サービス レベルの無人操作を実行する目的でテナント内で作成する Azure Active Directory アプリケーション リソースです。 アプリケーション ID とパスワードまたは証明書が与えられた、独自の*ユーザー ID* です。 サービス プリンシパルには、割り当てられたロールとアクセス許可によって定義されるタスクを実行するために必要な権限のみが与えられます。 

Analysis Services では、サービス プリンシパルは Azure Automation、PowerShell 無人モード、カスタム クライアント アプリケーション、Web アプリと共に使用し、共通タスクを自動化します。 たとえば、サーバーのプロビジョニング、モデルのデプロイ、データ更新、拡大縮小、一時停止/再開をすべて、サービス プリンシパルを利用することで自動化できます。 権限は、通常の Azure AD UPN アカウントとほぼ同じように、ロール メンバーシップを介してサービス プリンシパルに割り当てられます。

## <a name="create-service-principals"></a>サービス プリンシパルの作成
 
サービス プリンシパルは Azure Portal で作成するか、PowerShell を利用して作成できます。 詳細については、次を参照してください。

[サービス プリンシパルを作成する - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[サービス プリンシパルを作成する - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Azure Automation に資格情報と証明書資産を保存する

Runbook 操作のために、サービス プリンシパルの資格情報と証明書を Azure Automation に安全に格納できます。 詳細については、次を参照してください。

[Azure Automation の資格情報資産](../automation/automation-credentials.md)   
[Azure Automation の証明書資産](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>サービス プリンシパルをサーバー管理者ロールに追加する

Analysis Services サーバー管理操作のためにサービス プリンシパルを使用するには、最初にサービス プリンシパルをサーバー管理者ロールに追加する必要があります。 詳細については、「[サーバー管理者ロールへのサービス プリンシパルの追加](analysis-services-addservprinc-admins.md)」を参照してください。

## <a name="service-principals-in-connection-strings"></a>接続文字列のサービス プリンシパル

サービス プリンシパル appID とパスワードまたは証明書は、UPN とほぼ同じように接続文字列で使用できます。

### <a name="powershell"></a>PowerShell

[AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) モジュールによるリソース管理操作でサービス プリンシパルを使用するとき、`Login-AzureRmAccount` コマンドレットを使用します。 [SQLServer](https://www.powershellgallery.com/packages/SqlServer) モジュールによるサーバー操作のためにサービス プリンシパルを使用するとき、`Add-AzureAnalysisServicesAccount` コマンドレットを使用します。 

次の例では、appID とパスワードを使用し、モデル データベース更新操作を実行します。

```PowerShell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Add-AzureAnalysisServicesAccount -Credential $Credential -ServicePrincipal -TenantId $TenantId -RolloutEnvironment "westcentralus.asazure.windows.net"

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full"
```

### <a name="amo-and-adomd"></a>AMO と ADOMD 

クライアント アプリケーションや Web アプリと接続するとき、[AMO と ADOMD のクライアント ライブラリ](analysis-services-data-providers.md) バージョン 15.0.2 以降の、NuGet からインストールできるパッケージでは、接続文字列にサービス プリンシパルを指定できます。構文 `app:AppID` とパスワードまたは `cert:thumbprint` を利用します。 

次の例では、`appID` と `password` を使用し、モデル データベース更新操作を実行します。

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>次の手順
[Azure PowerShell を使用してサインインする](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[サーバー管理者ロールへのサービス プリンシパルの追加](analysis-services-addservprinc-admins.md)   