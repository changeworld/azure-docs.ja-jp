---
title: ASP.NET Core アプリで Azure App Configuration の Key Vault 参照を使用するためのチュートリアル | Microsoft Docs
description: このチュートリアルでは、ASP.NET Core アプリから Azure App Configuration の Key Vault 参照を使用する方法について説明します。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 9f095bbadf8f395b809d46c8beea5f6665932d12
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357906"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>チュートリアル:ASP.NET Core アプリで Key Vault 参照を使用する

このチュートリアルでは、Azure App Configuration サービスを Azure Key Vault と共に使用する方法について説明します。 App Configuration と Key Vault は補完的なサービスであり、ほとんどのアプリケーションのデプロイで併用されます。

これらをまとめて使用できるように、App Configuration では、Key Vault に格納されている値を参照するキーを作成できます。 App Configuration でこのようなキーを作成すると、App Configuration には、その値そのものではなく、Key Vault の値の URI が格納されます。

App Configuration に格納されているその他のキーの場合と同様、お使いのアプリケーションは App Configuration クライアント プロバイダーを使用して Key Vault 参照を取得します。 この場合、App Configuration に格納された値は、Key Vault の値を参照する URI です。 これらは、Key Vault の値でも資格情報でもありません。 クライアント プロバイダーはキーを Key Vault 参照として認識し、Key Vault を使用して値を取得します。

アプリケーションの役割は、App Configuration と Key Vault の両方に正しく認証されることです。 この 2 つのサービスが直接通信することはありません。

このチュートリアルでは、自分のコードに Key Vault 参照を実装する方法について説明します。 これは、クイック スタートで紹介されている Web アプリに基づいています。 先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 たとえば、[Visual Studio Code](https://code.visualstudio.com/) は、Windows、macOS、および Linux の各オペレーティング システムで使用できるクロスプラットフォーム コード エディターです。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Key Vault に格納されている値を参照する App Configuration キーを作成する。
> * ASP.NET Core Web アプリケーションからこのキーの値にアクセスする。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、[.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>コンテナーの作成

1. Azure Portal の左上隅にある **[リソースの作成]** オプションを選択します。

    ![Azure portal の [リソースの作成] オプションを示すスクリーンショット。](./media/quickstarts/search-services.png)
1. 検索ボックスに「**Key Vault**」と入力し、ドロップダウンから **Key Vault** を選択します。
1. 結果リストで、左側の **[キー コンテナー]** を選択します。
1. **[キー コンテナー]** で、 **[追加]** を選択します。
1. **[キー コンテナーの作成]** の右側に、次の情報を入力します。
    - **[サブスクリプション]** を選択してサブスクリプションを選択します。
    - **[リソース グループ]** で、既存のリソース グループ名を入力するか、 **[新規作成]** を選択してリソース グループ名を入力します。
    - **[キー コンテナー名]** では、一意の名前が必要です。
    - **[リージョン]** ドロップダウン リストで、場所を選択します。
1. **[キー コンテナーの作成]** オプションは既定値のままにしておきます。
1. **[確認および作成]** をクリックします。
1. システムによって、入力したデータが検証されて表示されます。 **[作成]** をクリックします。

この時点で、お使いの Azure アカウントが、この新しいコンテナーへのアクセスが承認されている唯一のアカウントになります。


## <a name="add-a-secret-to-key-vault"></a>Key Vault にシークレットを追加する

シークレットをコンテナーに追加するには、いくつかの追加の手順を行う必要があります。 この場合、Key Vault の取得をテストするために使用できるメッセージを追加します。 このメッセージは **Message** という名前で、それには "Hello from Key Vault" という値が格納されます。

1. Key Vault のプロパティ ページで、 **[シークレット]** を選択します。
1. **[Generate/Import]\(生成/インポート\)** を選択します。
1. **[シークレットの作成]** ウィンドウで、次の値を入力します。
    - **[アップロード オプション]** :「**Manual**」と入力します。
    - **Name**:「**Message**」と入力します。
    - **値**: 「**Hello from Key Vault**」と入力します。
1. **[シークレットの作成]** の他のプロパティは既定値のままにしておきます。
1. **［作成］** を選択します

## <a name="add-a-key-vault-reference-to-app-configuration"></a>App Configuration に Key Vault 参照を追加する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成した App Configuration ストア インスタンスを選択します。

1. **[構成エクスプローラー]** を選択します。

1. **[+ 作成]** 、 **[キー コンテナー参照]** の順に選択し、次の値を指定します。
    - **[キー]** :**TestApp:Settings:KeyVaultMessage** を選択します。
    - **ラベル**:この値は空白のままにしておきます。
    - **[サブスクリプション]** 、 **[リソース グループ]** 、 **[キー コンテナー]** : 前のセクションで作成したキー コンテナーの値に対応する値を入力します。
    - **[シークレット]** : 前のセクションで作成した、**Message** という名前のシークレットを選択します。

![新しいキー コンテナー参照の作成フォームのスクリーンショット](./media/create-key-vault-reference.png)

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Key Vault 参照を使用するようコードを更新する

1. 次のコマンドを実行して、必要な NuGet パッケージへの参照を追加します。

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. *Program.cs* を開き、次の必須パッケージへの参照を追加します。

    ```csharp
    using Azure.Identity;
    ```

1. `config.AddAzureAppConfiguration` メソッドを呼び出して App Configuration を使用するように、`CreateWebHostBuilder` メソッドを更新します。 `ConfigureKeyVault` オプションを追加し、ご利用のキー コンテナーに正しい資格情報を渡します。

     #### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```
    
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

1. App Configuration への接続を初期化するときは、`ConfigureKeyVault` メソッドを呼び出して、Key Vault への接続を設定します。 初期化後、通常の App Configuration キーの値にアクセスする場合と同じ方法で、Key Vault 参照の値にアクセスできます。

    このプロセスの動作を確認するには、**Views** > **Home** フォルダーにある *Index.cshtml* を開きます。 その内容を次のコードに置き換えます。

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Key Vault 参照の値 **TestApp:Settings:KeyVaultMessage** には、構成値 **TestApp:Settings:Message** と同様にアクセスできます。


## <a name="grant-your-app-access-to-key-vault"></a>Key Vault へのアクセス許可をアプリに付与する

Azure App Configuration がキー コンテナーにアクセスすることはありません。 アプリは Key Vault から直接読み取るため、キー コンテナー内のシークレットへの読み取りアクセス権をアプリに付与する必要があります。 これにより、シークレットは常にアプリに保持されます。 アクセス権を付与するには、[Key Vault アクセス ポリシー](../key-vault/general/assign-access-policy-portal.md)または [Azure のロールベースのアクセス制御](../key-vault/general/rbac-guide.md)を使用します。

上のコードでは `DefaultAzureCredential` を使用しています。 これは、`EnvironmentCredential`、`ManagedIdentityCredential`、`SharedTokenCacheCredential`、`VisualStudioCredential` など、さまざまな資格情報の種類が自動的に試される、集約されたトークン資格情報です。 詳細については、「[DefaultAzureCredential クラス](/dotnet/api/azure.identity.defaultazurecredential)」を参照してください。 `DefaultAzureCredential` は、任意の資格情報の種類に明示的に置き換えることができます。 ただし、`DefaultAzureCredential` を使用すると、ローカル環境と Azure 環境の両方で同じコードを実行できます。 たとえば、自分の資格情報にキー コンテナーへのアクセス権を付与します。 ローカル開発に Visual Studio を使用すると、`DefaultAzureCredential` は自動的に `SharedTokenCacheCredential` または `VisualStudioCredential` に戻ります。

または、AZURE_TENANT_ID、AZURE_CLIENT_ID、AZURE_CLIENT_SECRET の各環境変数を設定することもでき、`DefaultAzureCredential` により `EnvironmentCredential` に設定されているクライアント シークレットを使用してキー コンテナーでの認証が行われます。 マネージド ID を有効にして、Azure App Service、Azure Kubernetes Service、Azure Container Instance などの Azure サービスにアプリをデプロイした後、Azure サービスのマネージド ID に、キー コンテナーにアクセスするためのアクセス許可を付与します。 アプリが Azure で実行されているときは、`DefaultAzureCredential` により `ManagedIdentityCredential` が自動的に使用されます。 同じマネージド ID を使用して、App Configuration と Key Vault 両方での認証を行うことができます。 詳細については、[マネージド ID を使用して App Configuration にアクセスする方法](howto-integrate-azure-managed-service-identity.md)に関する記事を参照してください。

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

    ```dotnetcli
    dotnet build
    ```

1. ビルドが完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

    ```dotnetcli
    dotnet run
    ```

1. ブラウザー ウィンドウを開いて、`http://localhost:5000` (ローカルでホストされた Web アプリの既定の URL) に移動します。

    ![クイック スタートのアプリ (ローカルで起動)](./media/key-vault-reference-launch-local.png)



## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Key Vault に格納されているシークレットを参照するキーを App Configuration で作成しました。
Key Vault からシークレットと証明書を自動的に再度読み込む方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Key Vault からシークレットと証明書を自動的に再度読み込む](./reload-key-vault-secrets-dotnet.md)

マネージド ID を使用して App Configuration と Key Vault へのアクセスを効率化する方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)
