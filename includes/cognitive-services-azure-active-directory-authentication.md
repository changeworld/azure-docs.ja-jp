---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262558"
---
## <a name="authenticate-with-azure-active-directory"></a>Azure Active Directory を使用して認証する

> [!IMPORTANT]
> 1. 現時点では、Computer Vision API、Face API、Text Analytics API、Immersive Reader、Form Recognizer、Anomaly Detector、Bing Custom Search を除くすべての Bing **のみ**において、Azure Active Directory (AAD) を使用した認証がサポートされています。
> 2. AAD 認証は常に、Azure リソースのカスタム サブドメイン名と共に使用される必要があります。 [リージョン エンドポイント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints)では、AAD 認証はサポートされません。

前のセクションでは、Azure Cognitive Services に対して単一サービスまたはマルチサービスのサブスクリプション キーを使用して認証する方法を説明しました。 これらのキーを使用すると、開発を迅速かつ簡単に開始できますが、ロールベースのアクセス制御を必要とするより複雑なシナリオでは不十分です。 Azure Active Directory (AAD) を使用して認証を行うために何が必要なのかを確認しましょう。

以降のセクションでは、Azure Cloud Shell 環境または Azure CLI を使用してサブドメインを作成し、ロールを割り当てて、Azure Cognitive Services を呼び出すベアラー トークンを取得します。 問題が発生した場合は、各セクションに、Azure Cloud Shell/Azure CLI の各コマンドで使用可能なすべてのオプションへのリンクが提供されています。

### <a name="create-a-resource-with-a-custom-subdomain"></a>カスタム サブドメインを使用してリソースを作成する

最初の手順で、カスタム サブドメインを作成します。 カスタム サブドメイン名のない既存の Cognitive Services リソースを使用する場合は、[Cognitive Services カスタム サブドメイン](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources)に関する記事の手順に従って、リソースのカスタム サブドメインを有効にします。

1. まず、Azure Cloud Shell を開きます。 [サブスクリプションを選択](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)します。

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. 次に、カスタム サブドメインを使用して [Cognitive Services リソースを作成](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0)します。 サブドメイン名は、グローバルに一意である必要があり、"."、"!"、"," などの特殊文字を含めることはできません。

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. 成功すると、**エンドポイント**にリソースに固有のサブドメイン名が表示されます。


### <a name="assign-a-role-to-a-service-principal"></a>サービス プリンシパルにロールを割り当てる

これで、カスタム サブドメインがリソースに関連付けられたので、ロールをサービス プリンシパルに割り当てる必要があります。

> [!NOTE]
> AAD ロールの割り当ての反映には最大で 5 分かかる場合があることに留意してください。

1. まず、[AAD アプリケーション](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)を登録してみましょう。

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   次の手順では、**ApplicationId** が必要になります。

2. 次に、AAD アプリケーションの[サービス プリンシパルを作成](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0)する必要があります。

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Azure portal でアプリケーションを登録したら、この手順は完了です。

3. 最後の手順では、(リソースにスコープが設定された) サービス プリンシパルに ["Cognitive Services ユーザー" ロールを割り当て](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0)ます。 ロールを割り当てて、このリソースにサービス プリンシパル アクセス権を付与します。 同じサービス プリンシパル アクセスをサブスクリプション内の複数のリソースに対して許可できます。
   >[!NOTE]
   > アプリケーションの ObjectId ではなく、サービス プリンシパルの ObjectId が使用されます。
   > ACCOUNT_ID は、作成した Cognitive Services アカウントの Azure リソース ID になります。 Azure portal でリソースの "プロパティ" から Azure リソース ID を検索できます。

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>要求のサンプル

このサンプルでは、サービス プリンシパルの認証にパスワードが使用されています。 次に、指定されたトークンを使用して Computer Vision API を呼び出します。

1. **TenantId** を取得します。
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. トークンを取得します。
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Computer Vision API を呼び出します。
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

または、証明書を使用してサービス プリンシパルを認証することもできます。 サービス プリンシパルの他に、別の AAD アプリケーションから委任されたアクセス許可を持つことで、ユーザー プリンシパルもサポートされます。 この場合、パスワードまたは証明書の代わりに、ユーザーはトークンを取得するときに 2 要素認証が求められます。
