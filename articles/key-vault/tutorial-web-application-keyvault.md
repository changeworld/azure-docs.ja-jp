---
title: キー コンテナーからシークレットを読み取るように Azure Web アプリケーションを構成するためのチュートリアル | Microsoft Docs
description: 'チュートリアル: キー コンテナーからシークレットを読み取るように ASP.Net Core アプリケーションを構成する'
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 91e2047998d6e743691821c631e15c94cd63cf15
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "41917605"
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>チュートリアル: キー コンテナーからシークレットを読み取るように Azure Web アプリケーションを構成する

このチュートリアルでは、Azure Web アプリケーションを取得し、マネージド サービス ID を使用してキー コンテナーから情報を読み取るために必要な手順を学習します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * キー コンテナーを作成する
> * キー コンテナーにシークレットを格納する
> * Azure AD Web アプリケーションを作成する
> * マネージド サービス ID を有効にする
> * アプリケーションに必要なアクセス許可を付与して、キー コンテナーからデータを読み取る

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

CLI を使用して Azure にログインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*ContosoResourceGroup* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "ContosoResourceGroup" --location "East US"
```

作成したリソース グループは、このチュートリアルの全体を通して使用します。

## <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する

次に、前の手順で作成したリソース グループにキー コンテナーを作成します。 このチュートリアル全体でキー コンテナーの名前として "ContosoKeyVault" を使用していますが、一意の名前を使用する必要があります。 次の情報を指定します。

* コンテナー名: **ContosoKeyVault**
* リソース グループ名: **ContosoResourceGroup**
* 場所: **米国東部**

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East US"
```

このコマンドの出力では、新しく作成したキー コンテナーのプロパティが表示されます。 次の 2 つのプロパティをメモしておきます。

* **Vault Name**: この例では、これは **ContosoKeyVault** です。 すべての Key Vault コマンドに対して、自分のキー コンテナーの名前を使用します。
* **Vault URI**: この例では、これは https://<YourKeyVaultName>.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

>[!IMPORTANT]
> "パラメーター 'vault_name' は '^[a-zA-Z0-9-]{3,24}$' というパターンに準拠する必要があります" エラーを取得した場合、-name パラメーター値が一意でなかったか、文字列が長さ 3 ～ 24 文字の英数字での構成に準拠していなかったことが原因です。

この時点で、自分の Azure アカウントが唯一、この新しいコンテナーで任意の操作を実行することを許可されています。

## <a name="add-a-secret-to-key-vault"></a>キー コンテナーにシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 SQL 接続文字列やその他の情報を機密として保持する必要があるのに、アプリケーションで使用可能になるように保管している場合があります。 このチュートリアルでは、パスワードを **AppSecret** と呼び、このパスワード内に **MySecret** という値を格納します。

次のコマンドを入力して、値 **MySecret** を保存する **AppSecret** というシークレットをキー コンテナーに作成します。

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "AppSecret" --value "MySecret"
```

シークレットに格納されている値をプレーンテキストとして表示するには:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "ContosoKeyVault"
```

このコマンドは、URI を含むシークレットの情報を表示します。 これらの手順を完了すると、Azure Key Vault のシークレットへの URI がわかります。 この情報をメモしてください。 後の手順で必要になります。

## <a name="create-a-web-app"></a>Web アプリを作成する

このセクションでは、ASP.NET MVC アプリケーションを作成して、Web アプリとして Azure にデプロイします。 Azure Web Apps の詳細については、 [Web Apps の概要](../app-service/app-service-web-overview.md)に関するページを参照してください。

1. Visual Studio で、 **[ファイル]、[新規作成]、[プロジェクト]** の順にクリックして、プロジェクトを作成します。 

2. **[新しいプロジェクト]** ダイアログ ボックスで、**[Visual C#]、[Web]、[ASP.NET Core Web アプリケーション]** の順にクリックします。

3. アプリケーションに **WebKeyVault** という名前を付けて、**[OK]** を選択します。
   >[!IMPORTANT]
   > コピーして貼り付けるコードが名前空間と一致するように、アプリの名前を WebKeyVault にする必要があります。 サイトを他の名前にした場合、サイト名と一致するようにコードを変更する必要があります。

    ![[新しい ASP.NET プロジェクト] ダイアログ ボックス](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. 任意の種類の ASP.NET Core Web アプリを Azure にデプロイできます。 このチュートリアルでは、**[Web アプリケーション]** テンプレートを選択し、認証が **[認証なし]** に設定されていることを確認してください。

    ![ASPNET の [認証なし] ダイアログ ボックス](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. **[OK]** を選択します。

6. ASP.NET Core プロジェクトが作成されると、ASP.NET Core ウェルカム ページが開き、作業を開始する際に役立つリソースへのリンクが多数表示されます。

7. メニューから、**[デバッグ]、[デバッグなしで開始]** の順にクリックし、ローカルで Web アプリを実行します。

## <a name="modify-the-web-app"></a>Web アプリを変更する

Web アプリケーションでインストールしておく必要のある NuGet パッケージは 2 つあります。 それらをインストールするには、次の手順に従います。

1. ソリューション エクスプローラーで、Web サイト名を右クリックします。
2. **[ソリューションの NuGet パッケージの管理...]** を選択します。
3. 検索ボックスの横にある  **[プレリリースを含める]** のチェック ボックスをオンにします。
4. 以下に示す 2 つの NuGet パッケージを検索して、ソリューションへの追加を許可します。

    * [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) - サービスから Azure サービスへの認証シナリオでのアクセス トークンのフェッチを容易にします。 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) - Key Vault と対話するためのメソッドが含まれています。

5. ソリューション エクスプローラーを使用して `Program.cs` を開き、Program.cs ファイルの内容を次のコードに置き換えます。 ```<YourKeyVaultName>``` には、お使いのキー コンテナーの名前を指定してください。

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
    namespace WebKeyVault
    {
       public class Program
       {
           public static void Main(string[] args)
           {
               BuildWebHost(args).Run();
           }

           public static IWebHost BuildWebHost(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>()
             .Build();

           private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
         }
    }
    ```

6. ソリューション エクスプローラーを使用して **[ページ]** セクションに移動し、`About.cshtml` を開きます。 **About.cshtml.cs** の内容を次のコードに置き換えます。

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. メイン メニューから、**[デバッグ]** > **[デバッグなしで開始]** を選択します。 ブラウザーが表示されたら、**[About]\(詳細\)** ページに移動します。 AppSecret の値が表示されます。

>[!IMPORTANT]
> HTTP エラー 502.5 が発生した場合 - エラー メッセージを処理し、
> > `Program.cs` に指定されたキー コンテナーの名前を確認します。

## <a name="publish-the-web-application-to-azure"></a>Azure に Web アプリケーションを発行する

1. エディターの上にある **[WebKeyVault]** を選択します。
2. **[発行]**、**[開始]** の順に選択します。
3. 新しい **App Service** を作成し、**[発行]** を選択します。
4. **作成**を選択します。

>[!IMPORTANT]
> ブラウザー ウィンドウが開き、502.5 のプロセス エラー メッセージが表示されます。 これは予期されることです。 キー コンテナーからシークレットを読み取るために、アプリケーション ID の権限を付与する必要があります。

## <a name="enable-managed-service-identity"></a>マネージド サービス ID を有効にする

Azure Key Vault は、資格情報およびその他のキーやシークレットを安全に保管する方法を提供しますが、コードは Key Vault に認証してそれらを取得する必要があります。 管理対象サービス ID (MSI) は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、この問題を簡単に解決します。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

1. Azure CLI に戻ります。
2. assign-identity コマンドを実行して、このアプリケーションの ID を作成します。

```azurecli
az webapp identity assign --name "WebKeyVault" --resource-group "ContosoResourcegroup"
```

>[!NOTE]
>このコマンドは、ポータルに移動して、Web アプリケーション プロパティの **[マネージド サービス ID]** を **[オン]** に切り替えることと同等です。

## <a name="grant-rights-to-the-application-identity"></a>アプリケーション ID に権限を付与する

Azure Portal を使用して、キー コンテナーのアクセス ポリシーに移動し、自身にキー コンテナーへのシークレット管理アクセス権を付与します。 これにより、ローカル開発コンピューター上でアプリケーションを実行できるようになります。

1. Azure Portal の **[リソースの検索]** ダイアログ ボックスで、自分のキー コンテナーを検索します。
2. **[アクセス ポリシー]** を選択します。
3. [**シークレットのアクセス許可**] セクションで **[新規追加]** を選択し、**[取得]** および **[一覧]** を選択します。
4. **[プリンシパルの選択]** を選択して、アプリケーション ID を追加します。 これは、アプリケーションと同じ名前になります。
5. **[OK]** を選択します。

これで、Azure の自分のアカウントとアプリケーション ID に、キー コンテナーからの情報を読み取る権限が付与されました。 ページを更新すると、サイトのランディング ページが表示されます。 **[About]\(詳細\)** を選択すると、キー コンテナーに格納した値が表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループとそのすべてのリソースを削除するには、**az group delete** コマンドを使用します。

  ```azurecli
  az group delete -n "ContosoResourceGroup"
  ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Key Vault 開発者ガイド](key-vault-developers-guide.md)