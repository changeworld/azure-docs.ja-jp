---
title: チュートリアル - .NET で Azure Web アプリを使用して Azure Key Vault を使用する方法 | Microsoft Docs
description: 'チュートリアル: キー コンテナーからシークレットを読み取るように ASP.Net Core アプリケーションを構成する'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 6e30a181bc69678051e6d6469a2f1c533de811df
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686213"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>チュートリアル: .NET で Azure Web アプリを使用して Azure Key Vault を使用する方法

Azure Key Vault は、API キーや、アプリケーション、サービス、IT リソースへのアクセスに必要なデータベース接続文字列などのシークレットを保護するのに役立ちます。

このチュートリアルでは、Azure リソースのマネージド ID を使用して Azure Key Vault から情報を読み取るように Azure Web アプリケーションを設定するために必要な手順を学習します。 このチュートリアルは、[Azure Web Apps](../app-service/app-service-web-overview.md) に基づいています。 ここでは、次の操作を行う方法について学習します。

> [!div class="checklist"]
> * Key Vault を作成します。
> * キー コンテナーにシークレットを格納する。
> * キー コンテナーからシークレットを取得する。
> * Azure AD Web アプリケーションを作成する。
> * Web アプリの[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする
> * Web アプリケーションに必要なアクセス許可を付与して、キー コンテナーからデータを読み取る
> * Azure で Web アプリケーションを実行する

先に進む前に、[基本概念](key-vault-whatis.md#basic-concepts)を確認してください。

## <a name="prerequisites"></a>前提条件

* Windows の場合:
  * [.NET Core 2.1 SDK 以降](https://www.microsoft.com/net/download/windows)

* Mac の場合:
  * [Visual Studio for Mac の新機能](https://visualstudio.microsoft.com/vs/mac/)に関するページを参照してください。

* すべてのプラットフォーム:
  * Git ([ダウンロード](https://git-scm.com/downloads))。
  * Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) バージョン 2.0.4 以降。 これは、Windows、Mac、Linux に対応しています。
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>マネージド サービス ID とは何か、およびそのしくみ
本題に入る前に、MSI について理解しましょう。 Azure Key Vault を使用すると、資格情報を安全に保存することができます。したがって、コードには資格情報を含めませんが、資格情報を取得するために Azure Key Vault に対して認証する必要があります。 そして、Key Vault に対して認証するには資格情報が必要になります。 これは、古典的なブートストラップ問題です。 MSI は、Azure と Azure AD を利用して "ブートストラップ ID" を提供します。これにより、物事がはるかに簡単になります。

そのしくみについて説明しましょう。 Virtual Machines、App Service、Functions などの Azure サービスに対して MSI を有効にすると、Azure によってサービス インスタンスの[サービス プリンシパル](key-vault-whatis.md#basic-concepts)が Azure Active Directory に作成され、サービス プリンシパルの資格情報がサービスのインスタンスに挿入されます。 

![MSI](media/MSI.png)

次に、ユーザーのコードが、Azure リソース上で利用可能なローカル メタデータ サービスを呼び出してアクセス トークンを取得します。
コードは、ローカルの MSI_ENDPOINT から取得したアクセス トークンを使用して、Azure Key Vault サービスに対して認証します。 

それでは、チュートリアルを開始しましょう。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure CLI を使用して Azure にログインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドを使ってリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

リソース グループ名を選択し、プレース ホルダーを入力します。
次の例では、米国西部の場所にリソース グループを作成します。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

作成したリソース グループは、この記事の全体を通して使用します。

## <a name="create-a-key-vault"></a>Key Vault を作成します

次に、前の手順で作成したリソース グループにキー コンテナーを作成します。 次の情報を指定します。

* キー コンテナー名: 名前の文字数は 3 から 24 文字です。使用できる文字は 0-9、a-z、A-Z、および - のみです。
* リソース グループ名。
* 場所: **米国西部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

この時点で、使用している Azure アカウントのみが、この新しいコンテナーで任意の操作を実行することが許可されます。

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 SQL 接続文字列やその他の情報を機密として保持する必要があるのに、アプリケーションで使用可能になるように保管している場合があります。

次のコマンドを入力して、**AppSecret** というキー コンテナーにシークレットを作成します。 このシークレットには、値 **MySecret** が格納されます。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

シークレットに格納されている値をプレーンテキストとして表示するには:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

このコマンドは、URI を含むシークレットの情報を表示します。 これらの手順を完了すると、キー コンテナーのシークレットへの URI がわかります。 この情報をメモしてください。 後の手順で必要になります。

## <a name="create-a-net-core-web-app"></a>.NET Core Web アプリを作成する

この[チュートリアル](../app-service/app-service-web-get-started-dotnet.md)に従って .NET Core Web アプリを作成し、それを Azure に**発行**します。**または**、次のビデオを視聴します
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>ソリューションを開いて編集する

1. [ページ] > About.cshtml.cs ファイルの順に移動します。
2. 次の 2 つの Nuget パッケージをインストールします
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. About.cshtml.cs ファイルに次のコードをインポートします

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. AboutModel クラスのコードは次のようになります
    ```
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

Visual Studio 2017 のメイン メニューで、**[デバッグ]** > **[開始]** を選択します ("デバッグあり" と "デバッグなし" のどちらでもかまいません)。 ブラウザーが表示されたら、**[バージョン情報]** ページに移動します。 **AppSecret** の値が表示されます。

## <a name="enable-a-managed-identity-for-the-web-app"></a>Web アプリのマネージド ID を有効にする

Azure Key Vault は、資格情報およびその他のキーやシークレットを安全に保管する方法を提供しますが、コードは Key Vault に認証してそれらを取得する必要があります。 [Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、この問題を簡単に解決します。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

1. Azure CLI に戻ります。
2. assign-identity コマンドを実行して、このアプリケーションの ID を作成します。 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   <YourAppName> を Azure 上に発行されているアプリの名前に置き換える必要があることに注意してください。たとえば、発行されたアプリの名前が MyAwesomeapp.azurewebsites.net の場合は、<YourAppName> を MyAwesomeapp に置き換えてください
 
 上記のコマンドの出力は次のようになります。アプリケーションを Azure に発行するときに PrincipalId をメモしておきます。 次の形式にする必要があります。
   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
  ```
>[!NOTE]
>この手順のコマンドは、[ポータル](https://portal.azure.com)に移動して、Web アプリケーション プロパティの **[ID] / [システム割り当て済み]** を **[オン]** に切り替えることと同等です。

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>アプリケーションにアクセス許可を割り当ててキー コンテナーからシークレットを読み取る
        
次に、キー コンテナーの名前と **PrincipalId** の値を使用してこのコマンドを実行します。

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>Azure に Web アプリケーションを発行する

このアプリをもう一度 Azure に発行した後、Web アプリとして動作していること、およびシークレット値を取得できることを確認します。

1. Visual Studio で、**key-vault-dotnet-core-quickstart** プロジェクトを選択します。
2. **[発行]** > **[開始]** の順に選択します。
3. **作成**を選択します。

上記のコマンドでは、App Service の ID (MSI) に、キー コンテナーに対する **get** および **list** 操作を行うアクセス許可を付与しています。 <br />
これで、アプリケーションを実行すると、取得されたシークレットの値が表示されます。 

これで終了です。 これで、シークレットを保存してキー コンテナーから取得する Web アプリを .NET で正常に作成できました。
