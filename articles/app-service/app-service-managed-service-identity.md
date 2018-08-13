---
title: App Service および Azure Functions での管理対象のサービス ID | Microsoft Docs
description: Azure App Service および Azure Functions での管理対象のサービス ID の概念と設定に関するガイドです
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/25/2018
ms.author: mahender
ms.openlocfilehash: 2e392a3a50cda3daacb5bc358baaea2627eeafc0
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578832"
---
# <a name="how-to-use-azure-managed-service-identity-in-app-service-and-azure-functions"></a>App Service および Azure Functions で Azure マネージド サービス ID を使用する方法

> [!NOTE] 
> App Service on Linux と Web App for Containers は、現時点ではマネージド サービス ID をサポートしていません。

> [!Important] 
> アプリがサブスクリプションやテナント間で移行された場合、App Service および Azure Functions での管理対象のサービス ID は想定されたとおりに動作しません。 アプリでは、機能を無効にしてから再度有効にすることで、新しい ID を取得する必要があります。 以下の「[ID の削除](#remove)」を参照してください。 また、ダウンストリーム リソースでは、新しい ID を使用するようにアクセス ポリシーを更新する必要があります。

このトピックでは、App Service および Azure Functions アプリケーション用の管理対象アプリ ID を作成し、それを使って他のリソースにアクセスする方法を説明します。 アプリで Azure Active Directory の管理対象のサービス ID を使うと、他の AAD で保護されたリソース (Azure Key Vault など) に簡単にアクセスできます。 ID は Azure プラットフォームによって管理され、ユーザーがシークレットをプロビジョニングまたはローテーションする必要はありません。 管理対象のサービス ID について詳しくは、「[Managed Service Identity overview](../active-directory/managed-service-identity/overview.md)」(管理対象のサービス ID の概要) をご覧ください。

## <a name="creating-an-app-with-an-identity"></a>ID を持つアプリの作成

ID を持つアプリを作成するには、アプリケーションで追加のプロパティを設定する必要があります。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

ポータルで管理対象サービス ID を設定するには、最初に通常の方法でアプリケーションを作成した後、機能を有効にします。

1. ポータルを使って通常の方法でアプリを作成します。 ポータルでアプリに移動します。

2. 関数アプリを使っている場合は、**[プラットフォーム機能]** に移動します。 他のアプリの種類の場合は、左側のナビゲーションを下にスクロールして **[設定]** グループに移動します。

3. 
  **[Managed service identity]\(管理対象のサービス ID\)** を選びます。

4. **[Register with Azure Active Directory]\(Azure Active Directory に登録\)** を **[オン]** に切り替えます。 **[Save]** をクリックします。

![App Service での管理対象のサービス ID](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Azure CLI の使用

Azure CLI を使用して、管理対象のサービス ID を設定するには、既存のアプリケーションに対して `az webapp identity assign` コマンドを使用する必要があります。 このセクションの例を実行するためのオプションとして次の 3 つがあります。

- Azure Portal から [Azure Cloud Shell](../cloud-shell/overview.md) を使用する。
- 以下の各コード ブロックの右上隅にある [試してみる] を利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
- ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョン (2.0.31 以降) をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。 

次の手順では、CLI を使用して、Web アプリを作成し、ID を割り当てる方法について説明します。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 次のように、アプリケーションをデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

    ```azurecli-interactive
    az login
    ```
2. CLI を使用して Web アプリケーションを作成します。 App Service で CLI を使用する方法の他の例については、[App Service の CLI のサンプル](../app-service/app-service-cli-samples.md)に関するページを参照してください。

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. `identity assign` コマンドを実行してこのアプリケーションの ID を作成します。

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

次の手順では、Azure PowerShell を使用して、Web アプリを作成し、ID を割り当てる方法について説明します。

1. 必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Login-AzureRmAccount` を実行して、Azure との接続を作成します。

2. Azure PowerShell を使用して Web アプリケーションを作成します。 App Service で Azure PowerShell を使用する方法の他の例については、[App Service の PowerShell のサンプル](../app-service/app-service-powershell-samples.md)に関するページを参照してください。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzureRmResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzureRmAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzureRmWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. `identity assign` コマンドを実行してこのアプリケーションの ID を作成します。

    ```azurepowershell-interactive
    Set-AzureRmWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

Azure Resource Manager テンプレートを使って、Azure リソースのデプロイを自動化できます。 App Service および Functions へのデプロイについて詳しくは、「[Automating resource deployment in App Service](../app-service/app-service-deploy-complex-application-predictably.md)」(App Service でのリソースのデプロイの自動化) および「[Azure Functions の関数アプリのリソース デプロイを自動化](../azure-functions/functions-infrastructure-as-code.md)」をご覧ください。

種類が `Microsoft.Web/sites` であるすべてのリソースは、リソース定義に次のプロパティを含めることにより、ID を使って作成できます。
```json
"identity": {
    "type": "SystemAssigned"
}    
```

これは、Azure に対してアプリケーション用の ID を作成して管理するように指示します。

たとえば、Web アプリは次のようになります。
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

作成されるサイトには、次の追加プロパティが設定されます。
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` と `<PRINCIPALID>` は GUID に置き換えられます。 tenantId プロパティは、ID が属している AAD テナントを示します。 principalId は、アプリケーションの新しい ID の一意識別子です。 AAD でのサービス プリンシパルの名前は、App Service または Azure Functions のインスタンスに指定したものと同じです。

## <a name="obtaining-tokens-for-azure-resources"></a>Azure リソースのトークンの取得

アプリは、その ID を使って、AAD で保護されている他のリソース (Azure Key Vault など) へのトークンを取得できます。 これらのトークンは、アプリケーションの特定のユーザーではなく、リソースにアクセスしているアプリケーションを表します。 

> [!IMPORTANT]
> アプリケーションからのアクセスを許可するように、対象のリソースを構成することが必要な場合があります。 たとえば、Key Vault に対するトークンを要求する場合、アプリケーションの ID を含むアクセス ポリシーを追加する必要があります。 追加しないと、トークンを含めた場合でも、Key Vault の呼び出しは拒否されます。 管理対象のサービス ID のトークンをサポートしているリソースについて詳しくは、「[Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-service-identity/overview.md#which-azure-services-support-managed-service-identity)」をご覧ください。

App Service と Azure Functions には、トークンを取得するための簡単な REST プロトコルがあります。 .NET アプリケーションの場合は、Microsoft.Azure.Services.AppAuthentication ライブラリがこのプロトコルの抽象化を提供し、ローカル開発エクスペリエンスをサポートします。

### <a name="asal"></a>.NET 用の Microsoft.Azure.Services.AppAuthentication ライブラリの使用

.NET アプリケーションと Functions の場合、管理対象のサービス ID を使う最も簡単な方法は、Microsoft.Azure.Services.AppAuthentication パッケージを利用することです。 このライブラリを使うと、Visual Studio、[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)、または Active Directory 統合認証のユーザー アカウントを使って、開発用コンピューターでローカルにコードをテストすることもできます。 このライブラリでのローカル開発オプションについて詳しくは、[Microsoft.Azure.Services.AppAuthentication のリファレンス]に関するページをご覧ください。 このセクションでは、コードでライブラリを使い始める方法を示します。

1. [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) および [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet パッケージに対する参照をアプリケーションに追加します。

2.  次のコードをアプリケーションに追加します。

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Microsoft.Azure.Services.AppAuthentication およびそれによって公開される操作について詳しくは、[Microsoft.Azure.Services.AppAuthentication のリファレンス]に関するページおよび「[App Service and KeyVault with MSI .NET sample](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)」(MSI .NET での App Service と KeyVault のサンプル) をご覧ください。

### <a name="using-the-rest-protocol"></a>REST プロトコルの使用

管理対象のサービス ID を使うアプリでは、2 つの環境変数を定義します。
- MSI_ENDPOINT
- MSI_SECRET

**MSI_ENDPOINT** は、アプリがトークンを要求できるローカル URL です。 リソースのトークンを取得するには、次のパラメーターを指定して、このエンドポイントに HTTP GET 要求を行います。

> [!div class="mx-tdBreakAll"]
> |パラメーター名|場所|説明|
> |-----|-----|-----|
> |resource|クエリ|トークンを取得する必要のあるリソースの AAD リソース URI。|
> |api-version|クエリ|使うトークン API のバージョン。 現在サポートされているバージョンは "2017-09-01" だけです。|
> |secret|ヘッダー|MSI_SECRET 環境変数の値。|


正常終了の応答である 200 OK には、JSON 本文と次のプロパティが含まれています。

> [!div class="mx-tdBreakAll"]
> |プロパティ名|説明|
> |-------------|----------|
> |access_token|要求されたアクセス トークン。 呼び出し元の Web サービスは、このトークンを使用して受信側の Web サービスに対する認証処理を行うことができます。|
> |expires_on|アクセス トークンの有効期限が切れる日時。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 この値は、キャッシュされたトークンの有効期間を調べるために使用されます。|
> |resource|受信側の Web サービスのアプリケーション ID URI。|
> |token_type|トークン タイプ値を指定します。 Azure AD でサポートされるのは Bearer タイプのみです。 ベアラー トークンについて詳しくは、「[OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)」(OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750)) をご覧ください。|


この応答は、[AAD のサービス間アクセス トークン要求に対する応答](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)と同じです。

> [!NOTE] 
> プロセスが初めて開始されるときに環境変数が設定されるため、お使いのアプリケーションの管理対象サービス ID を有効にした後、`MSI_ENDPOINT` および `MSI_SECRET` がコードに対して利用可能になる前にアプリケーションを再起動するか、またはアプリケーションのコードを再デプロイする必要が生じる場合があります。

### <a name="rest-protocol-examples"></a>REST プロトコルの例
要求の例を次に示します。
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
応答の例は次のようになります。
```
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>コード例
<a name="token-csharp"></a>この要求を C# で行うには:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> .NET 言語では、この要求を自作する代わりに、[Microsoft.Azure.Services.AppAuthentication](#asal) を使うこともできます。

<a name="token-js"></a>Node.JS の例:
```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-powershell"></a>PowerShell の例:
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>ID の削除

ID は、ポータル、PowerShell、または CLI を使用して、作成時と同じ方法で機能を無効にすることで、削除できます。 REST/ARM テンプレート プロトコルでは、種類を "なし" に設定することで、この削除を実行します。

```json
"identity": {
    "type": "None"
}    
```

この方法で ID を削除すると、AAD からプリンシパルも削除されます。 システム割り当て ID は、アプリ リソースが削除されるときに、AAD から自動的に削除されます。

> [!NOTE] 
> また、単純にローカル トークン サービスを無効にする、設定可能なアプリケーション設定 WEBSITE_DISABLE_MSI もあります。 ただし、ID はその場所に残り、ツールには引き続き MSI が "オン" または "有効" と表示されます。 そのため、この設定の使用は推奨しません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [マネージド サービス ID を使用して SQL データベースに安全にアクセスする](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication のリファレンス]: https://go.microsoft.com/fwlink/p/?linkid=862452
