---
title: チュートリアル - .NET で Azure Web アプリを使用して Azure Key Vault を使用する - Azure Key Vault | Microsoft Docs
description: チュートリアル - キー コンテナーからシークレットを読み取るように ASP.Net Core アプリケーションを構成する
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: b6dbae0f721983920c2073927fff74100528678e
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998797"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>チュートリアル:.NET で Azure Web アプリを使用して Azure Key Vault を使用する

Azure Key Vault は、API キーやデータベース接続文字列などのシークレットを保護するのに役立ちます。 アプリケーション、サービス、IT リソースへのアクセス手段が得られます。

このチュートリアルでは、Azure キー コンテナーから情報を読み取ることができる Azure Web アプリケーションを作成する方法について説明します。 このプロセスでは、Azure リソースのマネージド ID を使用します。 Azure Web アプリケーションについて詳しくは、[Azure App Service](../app-service/overview.md) に関する記事をご覧ください。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * Key Vault を作成します。
> * キー コンテナーにシークレットを格納する。
> * キー コンテナーからシークレットを取得する。
> * Azure AD Web アプリケーションを作成する。
> * Web アプリの[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする
> * Web アプリケーションに必要なアクセス許可を付与して、キー コンテナーからデータを読み取る
> * Azure 上で Web アプリケーションを実行する。

先に進む前に、[Key Vault の基本的な概念](key-vault-whatis.md#basic-concepts)を確認してください。

## <a name="prerequisites"></a>前提条件

* Windows の場合:
  * [.NET Core 2.1 SDK 以降](https://www.microsoft.com/net/download/windows)

* Mac の場合:
  * [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)

* すべてのプラットフォーム:
  * [Git](https://git-scm.com/downloads)
  * Azure サブスクリプション <br />(Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。)
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) バージョン 2.0.4 以降 (Windows、Mac、Linux で利用可能)
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="managed-service-identity-and-how-it-works"></a>マネージド サービス ID とそのしくみ

Azure Key Vault は資格情報を安全に格納します。そのためコードには資格情報が含まれません。 ただし、キーを取得するためには Azure Key Vault に対して認証を行う必要があります。 Key Vault に対して認証を行うには、資格情報が必要となります。 これは従来からあるブートストラップ問題のジレンマです。 マネージド サービス ID (MSI) は、このプロセスを簡素化する "_ブートストラップ ID_" を提供することによって、この問題を解決します。

Azure サービス (例:Virtual Machines、App Service、Functions) に対して MSI を有効にすると、Azure によって[サービス プリンシパル](key-vault-whatis.md#basic-concepts)が作成されます。 MSI は、サービスのインスタンスのプリンシパルを Azure Active Directory (Azure AD) に作成し、サービス プリンシパルの資格情報をそのインスタンスに挿入します。

![MSI ダイアグラム](media/MSI.png)

次に、ユーザーのコードが、Azure リソース上で利用可能なローカル メタデータ サービスを呼び出してアクセス トークンを取得します。 コードは、ローカルの MSI_ENDPOINT から取得したアクセス トークンを使用して、Azure Key Vault サービスに対して認証します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

1. [az group create](/cli/azure/group#az-group-create) コマンドを使ってリソース グループを作成します。
1. リソース グループ名を選択し、プレース ホルダーを入力します。 次の例では、米国西部の場所にリソース グループを作成します。

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

このリソース グループは、このチュートリアルの全体を通して使用します。

## <a name="create-a-key-vault"></a>Key Vault を作成します

ご利用のリソース グループにキー コンテナーを作成するには、次の情報を入力します。

* キー コンテナー名: 数字、文字、ハイフンのみを含んだ 3 から 24 文字の文字列 (例:0 から 9、a から z、A から Z、-)。
* リソース グループ名
* 場所:**[米国西部]**

Azure CLI で、次のコマンドを入力します。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

この時点で、使用している Azure アカウントのみが、この新しいコンテナーで任意の操作を実行することが許可されます。

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

以上で、シークレットを追加できる状態になりました。 その候補としては、SQL 接続文字列など、安全性と、アプリケーションから利用できる状態の両方を維持する必要のある情報が考えられます。

次のコマンドを入力して、**AppSecret** というキー コンテナーにシークレットを作成します。 このシークレットには、**MySecret** という値が格納されます。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

シークレットに含まれている値をプレーンテキストとして表示するには、次のコマンドを入力します。

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

このコマンドは、URI を含むシークレットの情報を表示します。 これらの手順を完了すると、キー コンテナーのシークレットへの URI がわかります。 この情報をメモしてください。 後の手順で必要になります。

## <a name="create-a-net-core-web-app"></a>.NET Core Web アプリを作成する

この[チュートリアル](../app-service/app-service-web-get-started-dotnet.md)に従って .NET Core Web アプリを作成し、それを Azure に**発行**します。 次のビデオをご覧いただくこともできます。

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>ソリューションを開いて編集する

1. **[ページ]** > **[About.cshtml.cs]** ファイルの順に移動します。
2. 次の NuGet パッケージをインストールします。
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. About.cshtml.cs ファイルに次のコードをインポートします。

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

4. AboutModel クラスのコードは次のようになります。

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-app"></a>アプリの実行

1. Visual Studio 2017 のメイン メニューで、**[デバッグ]** > **[開始]** の順に選択します ("デバッグあり" と "デバッグなし" のどちらでもかまいません)。 
1. ブラウザーが表示されたら、**[バージョン情報]** ページに移動します。
1. **AppSecret** の値が表示されます。

## <a name="enable-a-managed-identity-for-the-web-app"></a>Web アプリのマネージド ID を有効にする

Azure Key Vault は、資格情報やその他のシークレットを安全に保管する方法を提供しますが、コードで Key Vault に対して認証を行い、それらを取得する必要があります。 [Azure リソースのマネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)に関するページは、Azure AD で自動的に管理されているマネージド ID を Azure サービスに付与することで、この問題を解決するうえで役立ちます。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

1. Azure CLI で assign-identity コマンドを実行して、このアプリケーションの ID を作成します。

   ```azurecli

   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"

   ```

   >[!NOTE]
   >\<YourAppName\> は、Azure 上の発行済みアプリの名前に置き換える必要があります。 たとえば、その発行済みアプリの名前が **MyAwesomeapp.azurewebsites.net** である場合は、\<YourAppName\> を **MyAwesomeapp** に置き換えます。

1. アプリケーションを Azure に発行するときの `PrincipalId` をメモします。 手順 1. で実行したコマンドの出力形式は次のとおりです。

   ```json
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
   ```

>[!NOTE]
>この手順のコマンドは、[ポータル](https://portal.azure.com)に移動して、Web アプリケーション プロパティの **[ID] / [システム割り当て済み]** を **[オン]** に切り替えることと同等です。

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>アプリケーションにアクセス許可を割り当ててキー コンテナーからシークレットを読み取る

次のコマンドの \<YourKeyVaultName\> は実際のキー コンテナーの名前に、\<PrincipalId\> は該当する **PrincipalId** の値に置き換えてください。

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

このコマンドでは、App Service の ID (MSI) に、キー コンテナーに対する **get** および **list** 操作を行うためのアクセス許可を付与しています。

## <a name="publish-the-web-application-to-azure"></a>Azure に Web アプリケーションを発行する

ライブ Web アプリからシークレット値をフェッチできることを確認するために、もう一度 Web アプリを Azure に発行します。

1. Visual Studio で、**key-vault-dotnet-core-quickstart** プロジェクトを選択します。
2. **[発行]** > **[開始]** の順に選択します。
3. **作成**を選択します。

アプリケーションを実行すると、シークレット値を取得できることが確認できます。

これで、シークレットを保存してキー コンテナーから取得する Web アプリを .NET で正常に作成できました。

## <a name="next-steps"></a>次の手順

>[!div class="nextstepaction"]
>[Azure Key Vault 開発者ガイド](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
