---
title: マネージド ID を使用して App Configuration にアクセスする
titleSuffix: Azure App Configuration
description: マネージド ID を使用して Azure App Configuration に対して認証する
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: ff7c2b6ced87c8254283923a9163e51f06ae6ef6
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114298176"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>マネージド ID を使用して App Configuration にアクセスする

Azure Active Directory [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) によってクラウド アプリケーションのシークレット管理が簡単になります。 マネージド ID を使用すると、コードでは、それが実行される Azure サービス用に作成されたサービス プリンシパルを使用できます。 Azure Key Vault またはローカル接続文字列に格納された別の資格情報の代わりに、マネージド ID を使用します。

Azure App Configuration とその .NET Core、.NET Framework、および Java Spring のクライアント ライブラリには、マネージド ID サポートが組み込まれています。 これの使用は必須ではありませんが、マネージド ID によって、シークレットが含まれるアクセス トークンが不要になります。 コードはサービス エンドポイントのみを使用して App Configuration ストアにアクセスできます。 シークレットの流出させることなく、この URL をコードに直接埋め込むことができます。

この記事では、マネージド ID を活用して App Configuration にアクセスする方法について説明します。 これは、クイック スタートで紹介されている Web アプリに基づいています。 先に進む前に、[App Configuration を使用して ASP.NET Core アプリを作成します](./quickstart-aspnet-core-app.md)。

> [!IMPORTANT]
> マネージド ID を使用して、ローカルで実行されているアプリケーションを認証することはできません。 アプリケーションは、マネージド ID をサポートする Azure サービスにデプロイする必要があります。 この記事では例として Azure App Service を使用しますが、同じ概念は、マネージド ID をサポートする他の Azure サービス ([Azure Kubernetes Service](../aks/use-azure-ad-pod-identity.md)、[Azure 仮想マシン](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)、[Azure Container Instances](../container-instances/container-instances-managed-identity.md) など) にも当てはまります。 ワークロードがこれらのサービスのいずれかでホストされている場合は、そのサービスのマネージド ID サポートも利用できます。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * App Configuration へのマネージド ID アクセスを許可する。
> * App Configuration に接続する際にマネージド ID を使用するようにアプリを構成する。


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [.NET Core SDK](https://dotnet.microsoft.com/download)。
* [構成済みの Azure Cloud Shell](../cloud-shell/quickstart.md)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>マネージド ID の追加

ポータルでマネージド ID を設定するには、最初にアプリケーションを作成した後、フィーチャーを有効にします。

1. [Microsoft Azure portal](https://portal.azure.com)で App Services リソースに移動します。 既存の App Services リソースがない場合は、作成してください。 

1. 左側のウィンドウで **[設定]** グループまで下へスクロールして、 **[ID]** を選択します。

1. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に切り替えて、 **[保存]** を選択します。

1. システムで割り当てられたマネージド ID を有効にするように求められたら、 **[はい]** を指定します。

    ![App Service でのマネージド ID の設定](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>App Configuration へのアクセスの許可

1. [Azure portal](https://portal.azure.com) で **[すべてのリソース]** を選択し、クイック スタートで作成した App Configuration を選択します。

1. **[アクセス制御 (IAM)]** を選択します。

1. **[アクセスの確認]** タブで、 **[ロールの割り当てを追加する]** カード UI の **[追加]** を選択します。

1. **[ロール]** の中から、**App Configuration データ リーダー** を選択します。 **[アクセスの割り当て先]** で、 **[システム割り当てマネージド ID]** の **[App Service]** を選択します。

1. **[サブスクリプション]** で自分の Azure サブスクリプションを選択します。 アプリの App Service リソースを選択します。

1. **[保存]** を選択します。

    ![マネージド ID の追加](./media/add-managed-identity.png)


## <a name="use-a-managed-identity"></a>マネージド ID の使用

1. *Azure.Identity* パッケージの参照を追加します。

    ```bash
    dotnet add package Azure.Identity
    ```

1. App Configuration ストアのエンドポイントを検索します。 この URL は、Azure portal 内のストアの **[アクセスキー]** タブにリストされます。

1. *appsettings.json* を開き、以下のスクリプトを追加します。 *\<service_endpoint>* を、角かっこも含めて、ご利用の App Configuration ストアへの URL に置き換えます。

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. *Program.cs* を開き、`Azure.Identity` および `Microsoft.Azure.Services.AppAuthentication` の名前空間への参照を追加します。

    ```csharp-interactive
    using Azure.Identity;
    ```

1. App Configuration に直接格納されている値にのみアクセスしたい場合は、`config.AddAzureAppConfiguration()` メソッド (`Microsoft.Azure.AppConfiguration.AspNetCore` パッケージにあります) を置換して `CreateWebHostBuilder` メソッドを更新します。

    > [!IMPORTANT]
    > `CreateHostBuilder` により、.NET Core 3.0 の `CreateWebHostBuilder` が置き換えられます。  お使いの環境に応じて適切な構文を選択します。

    ### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ---

    > [!NOTE]
    > **ユーザー割り当てのマネージド ID** を使用する場合、[ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) の作成時に必ず clientId を指定してください。
    >```csharp
    >config.AddAzureAppConfiguration(options =>
    >       {
    >           options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential("<your_clientId>"))
    >        });
    >```
    >[Azure リソースのマネージド ID に関する FAQ](../active-directory/managed-identities-azure-resources/known-issues.md) で説明したように、使用されるマネージド ID を解決する既定の方法があります。 この場合、将来的にありえるランタイム問題 (たとえば、新しいユーザー割り当てマネージド ID が追加された場合やシステム割り当てマネージド ID が有効になった場合) を回避する目的で、望ましい ID を指定するように Azure ID ライブラリから強制されます。 そのため、ユーザー割り当てマネージド ID が 1 つだけ定義されており、システム割り当てマネージド ID がない場合であっても、clientId を指定する必要があります。
 
    

## <a name="deploy-your-application"></a>アプリケーションをデプロイする

マネージド ID を使用するには、アプリを Azure サービスにデプロイする必要があります。 マネージド ID は、ローカルで実行されているアプリの認証には使用できません。 「[Create an ASP.NET Core app with App Configuration](./quickstart-aspnet-core-app.md) (App Configuration を使用して ASP.NET Core アプリを作成する)」クイックスタートで作成し、マネージド ID を使用するように修正した .NET Core アプリを展開するには、「[Publish your web app](../app-service/quickstart-dotnetcore.md?pivots=development-environment-vs&tabs=netcore31#publish-your-web-app) (Web アプリを発行する)」ガイダンスに従います。

App Service の他にも、多くの Azure サービスがマネージド ID をサポートしています。 詳細については、「[Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure マネージド ID を追加して、App Configuration へのアクセスを効率化し、アプリの資格情報管理を改善しました。 App Configuration の使用方法の詳細については、Azure CLI のサンプルに進んでください。

> [!div class="nextstepaction"]
> [CLI のサンプル](./cli-samples.md)