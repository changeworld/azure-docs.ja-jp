---
title: チュートリアル - Web アプリでアプリとして Microsoft Graph にアクセスする | Azure
description: このチュートリアルでは、マネージド ID を使用して Microsoft Graph のデータにアクセスする方法について学習します。
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: 70b180efa35d6310735f045a85103719b17c8555
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428256"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>チュートリアル: セキュリティで保護されたアプリからアプリとして Microsoft Graph にアクセスする

Azure App Service で実行されている Web アプリから Microsoft Graph にアクセスする方法について説明します。

:::image type="content" alt-text="Microsoft Graph へのアクセス" source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Web アプリに代わって Microsoft Graph を呼び出すとします。  Web アプリにデータへのアクセスを提供するより安全な方法は、[システム割り当てマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview) を使用することです。 Azure AD のマネージド ID を使用すると、App Services は、アプリの資格情報を必要とすることなく、ロールベースのアクセス制御 (RBAC) を介してリソースにアクセスできます。 マネージド ID を対象の Web アプリに割り当てると、Azure では証明書の作成と配布が行われます。  シークレットまたはアプリの資格情報の管理について心配する必要はありません。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Web アプリ上でシステム割り当てマネージド ID を作成する
> * Microsoft Graph API のアクセス許可をマネージド ID に追加する
> * マネージド ID を使用して Web アプリから Microsoft Graph を呼び出す

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* [App Service の認証および承認モジュールが有効になっている](scenario-secure-app-authentication-app-service.md) Azure App Service で実行されている Web アプリケーション。

## <a name="enable-managed-identity-on-app"></a>アプリのマネージド ID を有効にする

Visual Studio を使用して Web アプリを作成して発行すると、アプリでマネージド ID が有効になります。 ご利用のアプリ サービスで、左側のナビゲーション ペインの **[ID]** を選択し、 **[システム割り当て済み]** を選択します。  **[状態]** が **[オン]** に設定されていることを確認します。  そうでない場合は、 **[保存]** 、 **[はい]** の順にクリックして、システム割り当てマネージド ID を有効にします。  マネージド ID が有効になっている場合は、その状態が *[オン]* に設定されており、オブジェクト ID が使用可能です。

**オブジェクト ID** をメモしておきます。これは、次の手順で必要になります。

:::image type="content" alt-text="システム割り当て ID" source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Microsoft Graph へのアクセス権を付与する

Microsoft Graph にアクセスする場合、実行する操作に対する適切なアクセス許可がマネージド ID に与えられている必要があります。 現時点では、Azure portal でこのようなアクセス許可を割り当てるオプションはありません。 次のスクリプトを使用すると、要求された Microsoft Graph API のアクセス許可をマネージド ID サービス プリンシパル オブジェクトに追加できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module (You need admin on the machine)
#Install-Module AzureAD 

# Your tenant id (in Azure Portal, under Azure Active Directory -> Overview )
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get ID of the managed identity for the web app
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph"

# Assign permissions to managed identity service principal
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spID/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

スクリプトを実行した後、[Azure portal](https://portal.azure.com) で、要求された API のアクセス許可がマネージド ID に割り当てられていることを確認できます。  **[Azure Active Directory]** に移動し、 **[エンタープライズ アプリケーション]** を選択します。  このブレードには、対象のテナント内のすべてのサービス プリンシパルが表示されます。  **[すべてのアプリケーション]** で、マネージド ID のサービス プリンシパルを選択します。  このチュートリアルに従っている場合は、表示名が同じ 2 つのサービス プリンシパルがあります ("SecureWebApp2020094113531" など)。  "*ホームページ URL*" を持つサービス プリンシパルは、対象のテナント内の Web アプリを表します。  "*ホームページ URL*" を持たないサービス プリンシパルは、対象の Web アプリのシステム割り当てマネージド ID を表します。 マネージド ID のオブジェクト ID は、以前に作成したマネージド ID のオブジェクト ID と一致します。  

マネージド ID のサービス プリンシパルを選択します。

:::image type="content" alt-text="すべてのアプリケーション" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

**[概要]** で **[アクセス許可]** を選択すると、Microsoft Graph に対する追加のアクセス許可が表示されます。

:::image type="content" alt-text="アクセス許可" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Microsoft Graph を呼び出す (.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) クラスは、Azure Storage に対する要求をコードで承認するためにトークン資格情報を取得する際に使用されます。  [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) クラスのインスタンスを作成します。これは、マネージド ID を使用し、トークンを取得してサービス クライアントにアタッチします。 次のコード例では、認証済みのトークン資格情報を取得し、それを使用して、グループ内のユーザーを取得するサービス クライアント オブジェクトを作成します。  

### <a name="install-microsoftgraph-client-library-package"></a>Microsoft.Graph クライアント ライブラリ パッケージをインストールする

.NET Core コマンドライン インターフェイスまたは Visual Studio のパッケージ マネージャー コンソールを使用して、[Microsoft.Graph NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Graph)を対象のプロジェクトにインストールします。

# <a name="command-line"></a>[コマンド ライン](#tab/command-line)

コマンド ラインを開き、プロジェクト ファイルが含まれるディレクトリに切り替えます。

インストール コマンドを実行します。

```dotnetcli
dotnet add package Microsoft.Graph
```

# <a name="package-manager"></a>[パッケージ マネージャー](#tab/package-manager)

Visual Studio でプロジェクトまたはソリューションを開き、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** コマンドを使用してコンソールを開きます。

インストール コマンドを実行します。
```powershell
Install-Package Microsoft.Graph
```

---

### <a name="example"></a>例

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Graph service client with a DefaultAzureCredential which gets an access token using the available Managed Identity
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了し、Web アプリまたは関連するリソースが不要になった場合は、[作成したリソースをクリーンアップ](scenario-secure-app-clean-up-resources.md)します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
>
> * Web アプリ上でシステム割り当てマネージド ID を作成する
> * Microsoft Graph API のアクセス許可をマネージド ID に追加する
> * マネージド ID を使用して Web アプリから Microsoft Graph を呼び出す

[.NET Core アプリ](tutorial-dotnetcore-sqldb-app.md)、[Python アプリ](tutorial-python-postgresql-app.md)、[Java アプリ](tutorial-java-spring-cosmosdb.md)、または [Node.js アプリ](tutorial-nodejs-mongodb-app.md)をデータベースに接続する方法について学習します。