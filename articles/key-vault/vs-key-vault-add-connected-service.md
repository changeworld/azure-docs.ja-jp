---
title: Visual Studio を使用して ASP.NET プロジェクトに Key Vault のサポートを追加する - Azure Key Vault | Microsoft Docs
description: このチュートリアルを使用すると、ASP.NET または ASP.NET Core Web アプリケーションに Key Vault のサポートを追加する方法を学習できます。
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 1c12cf4a7bd097c6d33d032065734b477920644b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457003"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Visual Studio 接続済みサービスを使用して Web アプリケーションに Key Vault を追加する

このチュートリアルでは、Azure Key Vault を使用して Web プロジェクト用のシークレットを管理するために必要なもののすべてを、Visual Studio で簡単に追加する方法を学習します。使用するプロジェクトは、ASP.NET Core プロジェクトでも、その他の種類の ASP.NET プロジェクトでもかまいません。 Visual Studio の接続済みサービスの機能を使用することで、Azure 内の Key Vault に接続するために必要なすべての NuGet パッケージと構成設定の追加を Visual Studio に自動的に実行させることができます。

接続済みサービスが Key Vault を有効にするためにプロジェクト内で実行する変更の詳細については、[Key Vault 接続済みサービス - ASP.NET 4.7.1 プロジェクトで何が起こるか](#how-your-aspnet-framework-project-is-modified)、または[Key Vault 接続済みサービス - ASP.NET Core プロジェクトで何が起こるか](#how-your-aspnet-core-project-is-modified)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**。 サブスクリプションをお持ちでない場合には、[無料のアカウントにサインアップ](https://azure.microsoft.com/pricing/free-trial/)してください。
- **Visual Studio 2019 バージョン 16.3** 以降または **Visual Studio 2017 バージョン 15.7** (**Web 開発**ワークロードのインストールあり)。 [こちら](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)でダウンロードできます。
- ASP.NET (Core ではありません) と Visual Studio 2017 の組み合わせの場合、.NET Framework 4.7.1 以降の開発ツールが必要ですが、これは既定ではインストールされません。 インストールするには、Visual Studio インストーラーを起動し、 **[変更]** を選択します。次に、 **[個々 のコンポーネント]** を選択した後、右側にある **[ASP.NET と Web 開発]** を展開し、 **[.NET Framework 4.7.1 開発ツール]** を選択します。
- ASP.NET 4.7.1 以降、または ASP.NET Core 2.0 以降の Web プロジェクトが開くこと。

## <a name="add-key-vault-support-to-your-project"></a>Key Vault のサポートをプロジェクトに追加する

開始する前に、必ず Visual Studio にサインインしてください。 Azure サブスクリプションに使用するものと同じアカウントでサインインします。 次に、ASP.NET 4.7.1 以降か、ASP.NET Core 2.0 Web プロジェクトを開き、次の手順を行います。

1. **ソリューション エクスプローラー**で、キー コンテナーのサポートを追加するプロジェクトを右クリックし、 **[追加]** 、 >  **[接続済みサービス]** を選択します。
   [接続済みサービス] ページが開いて、プロジェクトに追加できるサービスが表示されます。
1. 使用可能なサービスのメニューから、 **[Secure Secrets With Azure Key Vault]\(Azure Key Vault でシークレットを保護する\)** を選択します。

   ![[Secure Secrets With Azure Key Vault]\(Azure Key Vault でシークレットを保護する\) を選択する](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. 使用するサブスクリプションを選択し、新規または既存の Key Vault を選択します。 新しい Key Vault を選択すると、 **[編集]** リンクが表示されます。 これを選択し、新しい Key Vault を構成します。

   ![サブスクリプションを選択します。](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. **[Azure Key Vault の編集]** に Key Vault で使用する名前を入力します。

1. 既存の**リソース グループ**を選択するか、自動的に生成された一意の名前を持つ新しいパーティションの作成を選択します。  新しいグループを別の名前で作成する場合は、[Azure portal](https://portal.azure.com) を使用できます。その後、ページを閉じ、再起動してリソース グループの一覧を再度読み込みます。
1. Key Vault を作成する**場所**を選択します。 Web アプリケーションが Azure でホストされる場合は、Web アプリケーションを最適なパフォーマンスでホストするリージョンを選択します。
1. **価格レベル**を選択します。 詳細については、「[Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)」を参照してください。
1. **[OK]** を選択し、構成の選択を受け入れます。
1. 既存の Key Vault を選択するか、新しい Key Vault を構成したら、Visual Studio の **[Azure Key Vault]** タブで、 **[追加]** を選択し、接続済みサービスを追加します。
1. **[このキー コンテナーに格納されているシークレットの管理]** リンクを選択し、Key Vault の **[シークレット]** ページを開きます。 ページまたはプロジェクトが閉じている場合、[Azure portal](https://portal.azure.com) で **[すべてのサービス]** を選択し、 **[セキュリティ]** の下の **[Key Vault]** を選択してページまたはプロジェクトに移動し、Key Vault を選択できます。
1. 作成したキー コンテナーの [キー コンテナー] セクションで、 **[シークレット]** 、 **[生成/インポート]** の順に選択します。

   ![シークレットを生成/インポートする](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. 「*MySecret*」などのシークレットを入力し、テストとして任意の文字列値を指定した後、 **[作成]** ボタンを選択します。

   ![シークレットの作成](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (省略可能) 別のシークレットを入力しますが、今回は「*Secrets--MySecret*」という名前を付けることでカテゴリに配置します。 この構文は、"MySecret" というシークレットを含む "Secrets" というカテゴリを指定します。

これで、コードでシークレットにアクセスできます。 次の手順は、ASP.NET 4.7.1 または ASP.NET Core のどちらを使用しているかに応じて異なります。

## <a name="access-your-secrets-in-code-aspnet-core"></a>コードでシークレットにアクセスする (ASP.NET Core)

1. ソリューション エクスプローラーで、プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 **[参照]** タブで、次の 2 つの NuGet パッケージを検索してインストールします。[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) .NET Core 2 の場合は [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) を追加します .NET Core 3 の場合は [Microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) を追加します。

1. .NET Core 2 の場合は、[`Program.cs`] タブを選択し、Program クラスの `BuildWebHost` 定義を次のように変更します。

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
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
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   .NET Core 3 の場合は、次のコードを使用します。

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. 次に、いずれかのページ ファイル (*Index.cshtml.cs* など) を開き、次のコードを記述します。
   1. この using ディレクティブによって `Microsoft.Extensions.Configuration` の参照を含めます。

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. 構成変数を追加します。

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. このコンストラクターを追加するか、既存のコンストラクターを次のように置き換えます。

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. `OnGet` メソッドを更新します。 ここに示すプレース ホルダーの値を、上記のコマンドで作成したシークレット名で更新します。

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. 実行時に値を確認するには、`ViewData["Message"]` を表示するコードを *.cshtml* ファイルに追加して、メッセージ内にシークレットを表示します。

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

アプリをローカルで実行して、キー コンテナーからシークレットが正常に取得されることを確認できます。

## <a name="access-your-secrets-aspnet"></a>シークレットにアクセスする (ASP.NET)

web.config ファイルの `appSettings` 要素のダミーの値が実行時に true 値に置き換えられるように、構成を設定できます。 これには、`ConfigurationManager.AppSettings` データ構造体を使用してアクセスできます。

1. web.config ファイルを編集します。  appSettings タグを検索し、属性 `configBuilders="AzureKeyVault"` を追加し、行を追加します。

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. 確認のために値が表示されるように、*HomeController.cs* の `About` メソッドを編集します。

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. デバッガーの下でアプリをローカルで実行し、 **[About]\(概要\)** タブに切り替えて、キー コンテナーの値が表示されていることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、リソース グループを削除します。 これにより、キー コンテナーと関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください]** ボックスにリソース グループの名前を入力して **[削除]** を選択します。

## <a name="troubleshooting"></a>トラブルシューティング

お使いのキー コンテナーを Visual Studio にログインしているものとは異なる Microsoft アカウントで実行している場合 (たとえば、キー コンテナーは職場アカウントで実行しているが、Visual Studio ではプライベート アカウントを使用している場合)、お使いの Program.cs ファイルで、Visual Studio によるキー コンテナーへのアクセスを実行できないというエラーが発生します。 この問題を解決するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) に移動してお使いのキー コンテナーを開きます。

1. **[アクセス ポリシー]** 、 **[アクセス ポリシーの追加]** の順に選択して、プリンシパルとしてログインしているアカウントを選択します。

1. Visual Studio で、 **[ファイル]**  >  **[アカウント設定]** の順に選択します。
**[すべてのアカウント]** セクションから **[アカウントの追加]** を選択します。 お使いのアクセス ポリシーのプリンシパルとして選択したアカウントでサインインします。

1. **[ツール]**  >  **[オプション]** の順に選択し、 **[Azure Service Authentication]\(Azure サービス認証\)** を探します。 次に、Visual Studio に追加したアカウントを選択します。

これで、ご自分のアプリケーションをデバッグするときに、お使いのキー コンテナーが配置されているアカウントに Visual Studio が接続されます。

## <a name="how-your-aspnet-core-project-is-modified"></a>ASP.NET Core プロジェクトの変更

このセクションでは、Visual Studio を使用して Key Vault 接続済みサービスを追加したときに、ASP.NET プロジェクトに対して行われる正確な変更内容を説明します。

### <a name="added-references-for-aspnet-core"></a>ASP.NET Core の参照を追加しました

プロジェクト ファイルの .NET リファレンスと NuGet パッケージのリファレンスに影響します。

| Type | リファレンス |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>ASP.NET Core のファイルを追加しました

- `ConnectedService.json` を追加しました。このファイルには、接続済みサービス プロバイダー、バージョン、ドキュメントのリンクに関する情報が記録されます。

### <a name="project-file-changes-for-aspnet-core"></a>ASP.NET Core のプロジェクト ファイル変更

- 接続済みサービスの ItemGroup と `ConnectedServices.json` ファイルを追加しました。

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>ASP.NET Core の launchsettings.json 変更

- IIS Express プロファイルと、ご使用の Web プロジェクト名に一致するプロファイルの両方に、次の環境変数エントリが追加されます。

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>ASP.NET Core のための Azure 上の変更

- リソース グループを作成します (または、既存のものを使用します)。
- 指定したリソース グループにキー コンテナーが作成されます。

## <a name="how-your-aspnet-framework-project-is-modified"></a>ASP.NET Framework プロジェクトの変更

このセクションでは、Visual Studio を使用して Key Vault 接続済みサービスを追加したときに、ASP.NET プロジェクトに対して行われる正確な変更内容を説明します。

### <a name="added-references-for-aspnet-framework"></a>ASP.NET Framework の追加される参照

プロジェクト ファイル (.NET 参照) と `packages.config` (NuGet 参照) に影響します。

| Type | リファレンス |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>ASP.NET Framework の追加されるファイル

- `ConnectedService.json` が追加されます。このファイルには、接続済みサービス プロバイダー、バージョン、ドキュメントのリンクに関する情報が記録されます。

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET Framework のプロジェクト ファイル変更

- 接続済みサービスの ItemGroup と ConnectedServices.json ファイルが追加されます。
- 「[追加された参照](#added-references-for-aspnet-framework)」セクションで説明されている .NET アセンブリへの参照。

### <a name="webconfig-or-appconfig-changes"></a>web.config または app.config の変更

- 次の構成エントリが追加されます。

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>ASP.NET Framework 向けの Azure での変更点

- リソース グループを作成します (または、既存のものを使用します)。
- 指定したリソース グループにキー コンテナーが作成されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルに従っている場合、お使いのキー コンテナーへのアクセス許可は、ご自分の Azure サブスクリプションで実行するように設定されますが、これは運用環境のシナリオには適していない可能性があります。 マネージド ID を作成して、アプリの Key Vault へのアクセスを管理できます。 「[マネージド ID で Key Vault の認証を提供する](/azure/key-vault/managed-identity)」を参照してください。

Key Vault の開発の詳細については、「[Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照してください。
