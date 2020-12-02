---
title: チュートリアル - .NET で Azure Web アプリを使用して Azure Key Vault を使用する | Microsoft Docs
description: このチュートリアルでは、キー コンテナーからシークレットを読み取るように ASP.NET Core アプリケーションの Azure webapp を構成します。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 4ed999e282aa9bcd80b000f3db2ecf9a8386a489
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95537953"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>チュートリアル:マネージド ID と .NET を使用して Key Vault を Azure Web アプリに接続する

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) は、資格情報やその他のシークレットを安全に保管する方法を提供しますが、コードで Key Vault に対して認証を行い、それらを取得する必要があります。 [Azure リソースのマネージド ID の概要](../../active-directory/managed-identities-azure-resources/overview.md)に関するページは、Azure AD で自動的に管理されているマネージド ID を Azure サービスに付与することで、この問題を解決するうえで役立ちます。 この ID を使用して、コードに資格情報が表示されていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

このチュートリアルでは、マネージド ID と Azure Key Vault を使用して Azure Web アプリを認証します。 この手順では、[.NET 用 Azure Key Vault v4 クライアント ライブラリ](/dotnet/api/overview/azure/key-vault)と [Azure CLI](/cli/azure/get-started-with-azure-cli) を使用していますが、各種の開発言語や Azure PowerShell、Azure portal を使用している場合でも、基本的な原則は同じです。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.Net Core 3.1 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* [Git をインストールします](https://www.git-scm.com/downloads)。
* [Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/)。
* [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview)。 [Azure portal](quick-create-portal.md)、[Azure CLI](quick-create-cli.md)、または [Azure PowerShell](quick-create-powershell.md) を使用して、キー コンテナーを作成できます。
* Key Vault [シークレット](https://docs.microsoft.com/azure/key-vault/secrets/about-secrets)。 シークレットは、[Azure portal](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal)、[PowerShell](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-powershell)、[Azure CLI](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli) のいずれかを使用して作成できます

## <a name="create-a-net-core-app-and-deploy-it-to-azure"></a>.NET Core アプリを作成して Azure にデプロイする
この手順では、ローカル .NET Core プロジェクトを設定します。

コンピューターのターミナル ウィンドウで、`akvwebapp` という名前のディレクトリを作成し、現在のディレクトリをそのディレクトリに変更します。

```bash
mkdir akvwebapp
cd akvwebapp
```

次に、[dotnet new web](/dotnet/core/tools/dotnet-new) コマンドを使用して新しい .NET Core アプリを作成します。

```bash
dotnet new web
```

アプリケーションをローカルで実行すると、アプリケーションを Azure にデプロイするとどう表示されるかを把握できます。 

```bash
dotnet run
```

Web ブラウザーを開き、`http://localhost:5000` のアプリに移動します。

ページに表示されているサンプル アプリの **Hello World** メッセージが表示されます。

## <a name="deploy-app-to-azure"></a>アプリを Azure にデプロイする

この手順では、ローカル Git を使用して、App Service に .NET Core アプリケーションをデプロイします。 アプリケーションを作成してデプロイする方法について詳しくは、[Azure に ASP.NET Core Web アプリを作成する](https://docs.microsoft.com/azure/app-service/quickstart-dotnetcore)方法に関するページを参照してください。

### <a name="configure-local-git-deployment"></a>ローカル Git デプロイを構成する

ターミナル ウィンドウで **Ctrl + C** キーを押して、Web サーバーを終了します。  .NET Core プロジェクト用の Git リポジトリを初期化します。

```bash
git init
git add .
git commit -m "first commit"
```

FTP およびローカルの Git では、"*デプロイ ユーザー*" を使用して Azure Web アプリにデプロイできます。 デプロイ ユーザーを構成すると、すべての Azure デプロイでこのユーザーを使用できます。 アカウントレベルのデプロイのユーザー名とパスワードは、Azure サブスクリプションの資格情報とは異なります。 

デプロイ ユーザーを構成するには、[az webapp deployment user set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) コマンドを実行します。 次のガイドラインに準拠したユーザー名とパスワードを選択してください。 

- ユーザー名は、Azure 内で一意である必要があり、ローカル Git プッシュの場合は "\@" シンボルを含めることはできません。 
- パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON 出力には、パスワードが `null` として表示されます。 `'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 `'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。 

Web アプリのデプロイに使用するユーザー名とパスワードを記録します。

### <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 キー コンテナーと Web アプリの配置先となるリソース グループを [az group create](/cli/azure/group?#az-group-create) コマンドで作成します。

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

Azure CLI の [az appservice plan create](/cli/azure/appservice/plan) コマンドで、[App Service プラン](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)を作成します。 次の例では、**Free** 価格レベルの `myAppServicePlan` という名前の App Service プランを作成します。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

App Service プランの管理の詳細については、「[Azure で App Service プランを管理する](https://docs.microsoft.com/azure/app-service/app-service-plan-manage)」を参照してください。

### <a name="create-a-web-app"></a>Web アプリを作成する

`myAppServicePlan` App Service プランに [Azure Web アプリ](../../app-service/overview.md)を作成します。 

> [!Important]
> Key Vault と同様、Azure Web アプリにも一意の名前を付ける必要があります。 次の例の \<your-webapp-name\> は、実際の Web アプリの名前に置き換えてください。


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


Git リモートの URL は `deploymentLocalGitUrl` プロパティに `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` 形式で出力されます。 この URL は後で必要になるので保存しておいてください。

新しく作成されたアプリに移動します。 _&lt;your-webapp-name>_ は、お使いのアプリの名前に置き換えてください。

```bash
https://<your-webapp-name>.azurewebsites.net
```

新しく作成した Azure Web アプリの既定の Web ページが表示されます。

### <a name="deploy-your-local-app"></a>ローカル アプリをデプロイする

ローカルのターミナル ウィンドウに戻り、Azure リモートをローカル Git リポジトリに追加します。 *\<deploymentLocalGitUrl-from-create-step>* は、[Web アプリの作成](#create-a-web-app)手順で保存した Git リモートの URL に置き換えてください。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 Git Credential Manager によって資格情報の入力を求めるメッセージが表示されたら、[ローカル Git デプロイの構成](#configure-local-git-deployment)手順で作成した資格情報を使用してください。

```bash
git push azure master
```

このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Web ブラウザーを使用して、デプロイされたアプリケーションにアクセス (またはブラウザーを最新の情報に更新) します。

```bash
http://<your-webapp-name>.azurewebsites.net
```

先ほど  にアクセスしたときに表示された "Hello World!" というメッセージが表示されます。
 
## <a name="configure-web-app-to-connect-to-key-vault"></a>Key Vault に接続するように Web アプリを構成する

このセクションでは、キー コンテナーへの Web アクセスを構成し、キー コンテナーからシークレットを取得するようにアプリケーション コードを更新します。

### <a name="create-and-assign-a-managed-identity"></a>マネージド ID を作成して割り当てる

このチュートリアルでは、アプリケーションの[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用してキー コンテナーに対する認証を行い、アプリケーションの資格情報を自動的に管理します。

このアプリケーションの ID を作成するために、Azure CLI で [az webapp-identity assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) コマンドを実行します。

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

この操作からは、次の JSON スニペットが返されます。

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

キー コンテナーに対する **get** 操作と **list** 操作のアクセス許可を Web アプリに与えるため、Azure CLI の [az keyvault set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) コマンドに principalID を渡します。

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

[Azure portal](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal) または [PowerShell](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-powershell) を使用してアクセス ポリシーを割り当てることもできます。

### <a name="modify-the-app-to-access-your-key-vault"></a>キー コンテナーにアクセスするようアプリを変更する

#### <a name="install-the-packages"></a>パッケージのインストール

ターミナル ウィンドウから、.NET 用 Azure Key Vault クライアント ライブラリ パッケージをインストールします。

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>コードを更新する

akvwebapp プロジェクトから Startup.cs ファイルを見つけて開きます。 

ヘッダーに次の 2 行を追加します。

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

`app.UseEndpoints` 呼び出しの前に以下の行を追加します。URI は、実際のキー コンテナーの `vaultUri` に合わせて更新してください。 以下のコードでは、キー コンテナーに対する認証に "[DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential)" が使用されています。この場合、アプリケーションのマネージド ID からのトークンが認証に使用されます。 キー コンテナーに対する認証について詳しくは、[開発者ガイド](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)を参照してください。 また、キー コンテナーがスロットルされている場合の再試行にはエクスポネンシャル バックオフが使用されています。 キー コンテナーのトランザクション制限について詳しくは、「[Azure Key Vault のスロットル ガイダンス](https://docs.microsoft.com/azure/key-vault/general/overview-throttling)」を参照してください。

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

`await context.Response.WriteAsync("Hello World!");` という行を次のように更新します。

```csharp
await context.Response.WriteAsync(secretValue);
```

次の手順に進む前に、必ず変更内容を保存してください。

#### <a name="redeploy-your-web-app"></a>Web アプリを再デプロイする

コードを更新したら、次の Git コマンドを使用して、そのコードを Azure に再デプロイすることができます。

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

### <a name="visit-your-completed-web-app"></a>完成した Web アプリにアクセスする

```bash
http://<your-webapp-name>.azurewebsites.net
```

**Hello World** が表示される前に、シークレットの値が表示されるはずです。**成功です**

## <a name="next-steps"></a>次のステップ

- [仮想マシンにデプロイされたアプリケーションで Azure Key Vault を使用する (.NET)](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-virtual-machine)
- 詳細については、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) について学びます。
- [App Service のマネージド ID](../../app-service/overview-managed-identity.md?tabs=dotnet) について学びます。
- [開発者ガイド](https://docs.microsoft.com/azure/key-vault/general/developers-guide)
- [キー コンテナーへのアクセスをセキュリティで保護する](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault)


