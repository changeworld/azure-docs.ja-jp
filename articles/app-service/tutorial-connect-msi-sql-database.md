---
title: チュートリアル:マネージド ID を使用してデータにアクセスする
description: マネージド ID を使用してデータベース接続をより安全にする方法と、それを他の Azure サービスに適用する方法について説明します。
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2021
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 01a80fe6860f40827dee1c3367ec19a7eaf9589b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090064"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>チュートリアル:マネージド ID を使用した App Service からの Azure SQL Database 接続のセキュリティ保護

[App Service](overview.md) では、Azure の高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 さらに、[Azure SQL Database](/azure/sql-database/) やその他の Azure サービスへのアクセスをセキュリティ保護するためのターンキー ソリューションである[マネージド ID](overview-managed-identity.md) もアプリ向けに提供しています。 App Service のマネージド ID を使用すると、接続文字列内の認証情報などのシークレットをアプリから排除することで、アプリのセキュリティを強化できます。 このチュートリアルでは、次のいずれかのチュートリアルで作成したサンプル Web アプリにマネージド ID を追加します。 

- [チュートリアル:Azure SQL Database を使用して Azure に ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)
- [チュートリアル:Azure App Service での ASP.NET Core および Azure SQL Database アプリの作成](tutorial-dotnetcore-sqldb-app.md)

作業が完了すると、サンプル アプリは、ユーザー名とパスワードを必要とせずに SQL Database に安全に接続するようになります。

![チュートリアルのシナリオを表すアーキテクチャ図。](./media/tutorial-connect-msi-sql-database/architecture.png)

> [!NOTE]
> このチュートリアルで説明する手順は、以下のバージョンをサポートしています。
> 
> - .NET Framework 4.7.2 以降
> - .NET Core 2.2 以降
>

学習内容

> [!div class="checklist"]
> * マネージド ID を有効にする
> * SQL Database へのアクセスをマネージド ID に付与する
> * SQL Database で Azure AD 認証を使用するように Entity Framework を構成する
> * Azure AD 認証を使用して Visual Studio から SQL Database に接続する

> [!NOTE]
>Azure AD 認証は、オンプレミスの Active Directory (AD DS) の [統合 Windows 認証](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10))とは _異なります_。 AD DS と Azure AD はまったく異なる認証プロトコルを使用しています。 詳細については、「[Azure AD Domain Services のドキュメント](../active-directory-domain-services/index.yml)」を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

この記事は、「[チュートリアル:SQL Database を使用して Azure に ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)」または「[チュートリアル: Azure App Service での ASP.NET Core および SQL Database アプリの作成](tutorial-dotnetcore-sqldb-app.md)」の続きです。 まだどちらも完了していない場合は、先に 2 つのチュートリアルのうちのいずれかに従って作業してください。 または、SQL Database を使用して独自の .NET アプリに合わせた手順を実行することもできます。

SQL Database をバックエンドとして使用してご自分のアプリをデバッグするには、ご使用のコンピューターからのクライアント接続を許可していることを確認してください。 そうなっていない場合は、「[Azure portal を使用してサーバーレベルの IP ファイアウォール規則を管理する](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)」の手順に従ってください。

Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>データベースへのアクセスを Azure AD ユーザーに許可する

まず、Azure AD ユーザーをサーバーの Active Directory 管理者として割り当てることで SQL Database への Azure AD 認証を有効にします。 このユーザーは、ご使用の Azure サブスクリプションのサインアップに使用した Microsoft アカウントと異なります。 Azure AD に作成、インポート、同期、または招待したユーザーである必要があります。 許可されている Azure AD ユーザーの詳細については、[Azure AD の機能と SQL Database の制限事項](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations)に関するセクションを参照してください。

1. Azure AD テナントにまだユーザーが作成されていない場合は、「[Azure Active Directory を使用してユーザーを追加または削除する](../active-directory/fundamentals/add-users-azure-active-directory.md)」の手順に従ってユーザーを作成します。

1. [`az ad user list`](/cli/azure/ad/user#az_ad_user_list) を使用して Azure AD ユーザーのオブジェクト ID を見つけます。 *\<user-principal-name>* は置き換えてください。 結果は変数に保存されます。

    ```azurecli-interactive
    azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
    ```

    > [!TIP]
    > Azure AD 内のすべてのユーザー プリンシパル名の一覧を表示するには、`az ad user list --query [].userPrincipalName` を実行します。
    >

1. Cloud Shell で [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) コマンドを使用して、この Azure AD ユーザーを Active Directory 管理者として追加します。 次のコマンドの *\<server-name>* は、サーバー名 (`.database.windows.net` サフィックスなし) に置き換えてください。

    ```azurecli-interactive
    az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
    ```

Active Directory 管理者の追加の詳細については、[サーバーの Azure Active Directory 管理者のプロビジョニング](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)に関するセクションを参照してください。

## <a name="set-up-visual-studio"></a>Visual Studio を設定する

# <a name="windows-client"></a>[Windows クライアント](#tab/windowsclient)

1. Visual Studio for Windows は Azure AD 認証と統合されています。 Visual Studio で開発とデバッグを有効にするには、Visual Studio のメニューから **[ファイル]**  >  **[アカウント設定]** の順に選択し、 **[アカウントを追加します]** をクリックして、実際の Azure AD ユーザーを追加します。

1. Azure サービス認証用に Azure AD ユーザーを設定するには、メニューから **[ツール]**  >  **[オプション]** の順に選択した後、 **[Azure Service Authentication]\(Azure サービス認証\)**  >  **[アカウントの選択]** の順に選択します。 追加した Azure AD ユーザーを選択し、 **[OK]** をクリックします。

# <a name="macos-client"></a>[macOS クライアント](#tab/macosclient)

1. Visual Studio for Mac は Azure AD 認証と統合されていません。 ただし、後で使用する [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ライブラリでは、Azure CLI からのトークンを使用することができます。 Visual Studio での開発とデバッグを可能にするには、ご使用のローカル コンピューター上に [Azure CLI をインストール](/cli/azure/install-azure-cli)します。

1. ご自分の Azure AD ユーザーを使用して、次のコマンドで Azure CLI にサインインします。

    ```azurecli
    az login --allow-no-subscriptions
    ```

-----

これで、Azure AD 認証を使用し、SQL Database をバックエンドとして利用して、ご自分のアプリを開発およびデバッグする準備ができました。

## <a name="modify-your-project"></a>プロジェクトを変更する

プロジェクトに対して実行する手順は、ASP.NET プロジェクトであるか ASP.NET Core プロジェクトであるかによって異なります。

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

1. Visual Studio で、パッケージ マネージャー コンソールを開き、NuGet パッケージ [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) を追加します。

    ```powershell
    Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
    ```

1. *Web.config* で、ファイルの先頭から作業を行い、次の変更を加えます。

    - `<configSections>` に、次の section 宣言を追加します。
    
        ```xml
        <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
        ```
    
    - 閉じる `</configSections>` タグの下に、`<SqlAuthenticationProviders>` の次の XML コードを追加します。
    
        ```xml
        <SqlAuthenticationProviders>
          <providers>
            <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
          </providers>
        </SqlAuthenticationProviders>
        ```    
    
    - `MyDbConnection` という接続文字列を見つけ、その `connectionString` 値を `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` に置き換えます。 _\<server-name>_ と _\<db-name>_ を実際のサーバー名とデータベース名に置き換えます。
    
    > [!NOTE]
    > 登録した SqlAuthenticationProvider は、先ほどインストールした AppAuthentication ライブラリに基づいています。 既定では、システム割り当ての ID が使用されます。 ユーザー割り当ての ID を活用するには、追加の構成を行う必要があります。 AppAuthentication ライブラリについては、「[接続文字列のサポート](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support)」を参照してください。

    これが、SQL Database に接続するために必要な作業のすべてです。 Visual Studio でデバッグする場合は、「[Visual Studio を設定する](#set-up-visual-studio)」で構成した Azure AD ユーザーが、コードによって使用されます。 後で、App Service アプリのマネージド ID からの接続を許可するように SQL Database を設定します。

1. `Ctrl+F5` キーを押してアプリをもう一度実行します。 これで、お使いのブラウザー内で同じ CRUD アプリが Azure AD 認証を使用して Azure SQL Database に直接接続します。 この設定により、Visual Studio からのデータベースの移行を実行できます。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

> [!NOTE]
> **Microsoft.Azure.Services.AppAuthentication** は、新しい Azure SDK での使用は非推奨になりました。 これは、.NET、Java、TypeScript、Python 向けに提供されている新しい **Azure ID クライアント ライブラリ** に置き換えられています。新規の開発ではすべて、これを使用する必要があります。 `Azure Identity` への移行方法の詳細については、[AppAuthentication から Azure.Identity への移行ガイダンス](/dotnet/api/overview/azure/app-auth-migration)に関する記事を参照してください。

1. Visual Studio で、パッケージ マネージャー コンソールを開き、次のように NuGet パッケージ [Azure.Identity](https://www.nuget.org/packages/Azure.Identity) を追加します。

    ```powershell
    Install-Package Microsoft.Data.SqlClient -Version 2.1.2
    Install-Package Azure.Identity -Version 1.4.0
    ```

1. [ASP.NET Core および SQL Database のチュートリアル](tutorial-dotnetcore-sqldb-app.md)の場合、ローカル開発環境では Sqlite データベース ファイルが使用され、Azure 運用環境では App Service の接続文字列が使用されるため、`MyDbConnection` 接続文字列はまったく使用されません。 Active Directory 認証では、両方の環境で同じ接続文字列を使用することが望まれます。 *appsettings.json* で、`MyDbConnection` 接続文字列の値を次のように置き換えます。

    ```json
    "Server=tcp:<server-name>.database.windows.net;Authentication=Active Directory Device Code Flow; Database=<database-name>;"
    ```

    > [!NOTE]
    > 認証の種類として `Active Directory Device Code Flow` を使用します。これが、カスタム オプションに最も近いものであるためです。 `Custom Authentication` という種類を使用できるのが理想的です。 現時点で使用するものとして、より適切なものがないため、`Device Code Flow` を使用します。
    >

1. 次に、SQL Database 用のアクセス トークンを使用して Entity Framework データベース コンテキストを取得し、提供するための、カスタム認証プロバイダー クラスを作成する必要があります。 *Data\\* ディレクトリ内に、次のコードを含む新しいクラス `CustomAzureSQLAuthProvider.cs` を追加します。

    ```csharp
    public class CustomAzureSQLAuthProvider : SqlAuthenticationProvider
    {
        private static readonly string[] _azureSqlScopes = new[]
        {
            "https://database.windows.net//.default"
        };
    
        private static readonly TokenCredential _credential = new DefaultAzureCredential();
    
        public override async Task<SqlAuthenticationToken> AcquireTokenAsync(SqlAuthenticationParameters parameters)
        {
            var tokenRequestContext = new TokenRequestContext(_azureSqlScopes);
            var tokenResult = await _credential.GetTokenAsync(tokenRequestContext, default);
            return new SqlAuthenticationToken(tokenResult.Token, tokenResult.ExpiresOn);
        }
    
        public override bool IsSupported(SqlAuthenticationMethod authenticationMethod) => authenticationMethod.Equals(SqlAuthenticationMethod.ActiveDirectoryDeviceCodeFlow);
    }
    ```

1. *Startup.cs* の `ConfigureServices()` メソッドを次のコードで更新します。

    ```csharp
    services.AddControllersWithViews();
    services.AddDbContext<MyDatabaseContext>(options =>
    {
        SqlAuthenticationProvider.SetProvider(
            SqlAuthenticationMethod.ActiveDirectoryDeviceCodeFlow, 
            new CustomAzureSQLAuthProvider());
        var sqlConnection = new SqlConnection(Configuration.GetConnectionString("MyDbConnection"));
        options.UseSqlServer(sqlConnection);
    });
    ```

    > [!NOTE]
    > このデモ コードは、わかりやすく単純にするために同期型になっています。
    
    前のコードでは `Azure.Identity` ライブラリが使用されてるため、コードの実行場所に関係なく、データベースのアクセス トークンを認証および取得できます。 ローカル コンピューターで実行している場合、`DefaultAzureCredential()` が複数のオプションを巡回し、ログインしている有効なアカウントを見つけます。 [DefaultAzureCredential クラス](/dotnet/api/azure.identity.defaultazurecredential)の詳細をご覧ください。

    これが、SQL Database に接続するために必要な作業のすべてです。 Visual Studio でデバッグする場合は、「[Visual Studio を設定する](#set-up-visual-studio)」で構成した Azure AD ユーザーが、コードによって使用されます。 後で、App Service アプリのマネージド ID からの接続を許可するように SQL Database を設定します。 `DefaultAzureCredential` クラスは、トークンをメモリ内にキャッシュし、有効期限の直前に Azure AD から取得します。 トークンを更新するためのカスタム コードは必要ありません。

1. `Ctrl+F5` キーを押してアプリをもう一度実行します。 これで、お使いのブラウザー内で同じ CRUD アプリが Azure AD 認証を使用して Azure SQL Database に直接接続します。 この設定により、Visual Studio からのデータベースの移行を実行できます。

-----

## <a name="use-managed-identity-connectivity"></a>マネージド ID の接続の使用

次に、システムによって割り当てられたマネージド ID を使用して SQL Database に接続するよう、ご自分の App Service アプリを構成します。

> [!NOTE]
> このセクションの手順では、システム割り当て ID を使用していますが、ユーザー割り当て ID も同じように簡単に使用できます。 そのためには、 目的のユーザー割り当て ID を使用するように `az webapp identity assign command` を変更する必要があります。 その後 SQL ユーザーを作成するときは、サイト名ではなく、ユーザー割り当て ID リソースの名前を使用してください。

### <a name="enable-managed-identity-on-app"></a>アプリのマネージド ID を有効にする

Azure アプリのマネージド ID を有効にするには、Cloud Shell で [az webapp identity assign](/cli/azure/webapp/identity#az_webapp_identity_assign) コマンドを使用します。 次のコマンドの *\<app-name>* を置き換えます。

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

> [!NOTE]
> [デプロイ スロット](deploy-staging-slots.md)のマネージド ID を有効にするには、`--slot <slot-name>` を追加し、 *\<slot-name>* にスロットの名前を使用します。

出力の例を次に示します。

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>マネージド ID にアクセス許可を付与する

> [!NOTE]
> 必要に応じて、[Azure AD グループ](../active-directory/fundamentals/active-directory-manage-groups.md)に ID を追加し、SQL Database へのアクセスを ID ではなく Azure AD グループに許可することができます。 たとえば、次のコマンドは、前の手順のマネージド ID を _myAzureSQLDBAccessGroup_ という名前の新しいグループに追加します。
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

1. Cloud Shell 内で、SQLCMD コマンドを使用して SQL Database にサインインします。 _\<server-name>_ は実際のサーバー名に、 _\<db-name>_ はアプリが使用するデータベース名に、 _\<aad-user-name>_ と _\<aad-password>_ は Azure AD ユーザーの資格情報に置き換えます。

    ```bash
    sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
    ```

1. 対象のデータベースの SQL プロンプトで次のコマンドを実行して、ご自分のアプリに必要なアクセス許可を付与します。 たとえば、次のように入力します。 

    ```sql
    CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
    ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
    ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
    ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
    GO
    ```

    *\<identity-name>* は、Azure AD のマネージド ID の名前です。 ID がシステムによって割り当てられる場合は常に、App Service アプリと同じ名前になります。 [デプロイ スロット](deploy-staging-slots.md)の場合、システムによって割り当てられた ID の名前は、 *\<app-name>/slots/\<slot-name>* です。 Azure AD グループのアクセス許可を付与するには、代わりにグループの表示名を使用します (たとえば、*myAzureSQLDBAccessGroup*)。

1. 「`EXIT`」と入力して Cloud Shell プロンプトに戻ります。

    > [!NOTE]
    > マネージド ID のバックエンド サービスは、[トークン キャッシュも管理](overview-managed-identity.md#obtain-tokens-for-azure-resources)します。トークン キャッシュでは、有効期限が切れたときにのみターゲット リソースのトークンが更新されます。 SQL Database のアクセス許可に設定ミスがあり、アプリでトークンを取得しようとした "*後に*" アクセス許可に変更を加えようとしても、実際には、キャッシュされたトークンの有効期限が切れるまで、更新されたアクセス許可で新しいトークンを取得することはできません。

    > [!NOTE]
    > Azure Active Directory とマネージド ID は、オンプレミスの SQL Server ではサポートされていません。 

### <a name="modify-connection-string"></a>接続文字列を変更する

*Web.config* または *appsettings.json* 内で行ったのと同じ変更をマネージド ID に対して使用できるため、必要な作業は App Service 内の既存の接続文字列 (ご自分のアプリを初めてデプロイすると Visual Studio によって作成されます) を削除することだけである点に注意してください。 次のコマンドを使用しますが、 *\<app-name>* はご自分のアプリの名前に置き換えます。

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>変更を発行する

あとは、Azure に変更を発行するだけです。

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

1. **「[チュートリアル: SQL Database を使用して Azure に ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)」からこのチュートリアルに進んできた場合は**、Visual Studio での変更を発行します。 **ソリューション エクスプローラー** で **DotNetAppSqlDb** プロジェクトを右クリックし、 **[発行]** を選択します。

    ![ソリューション エクスプローラーから発行する](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. 発行ページで **[発行]** をクリックします。 

    > [!IMPORTANT]
    > App Service 名が既存の[アプリの登録](../active-directory/manage-apps/add-application-portal.md)と一致していないことを確認してください。 これにより、プリンシパル ID の競合が発生します。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

**「[チュートリアル: Azure App Service での ASP.NET Core および SQL Database アプリの作成](tutorial-dotnetcore-sqldb-app.md)」からこのチュートリアルに進んできた場合は**、以下のコマンドで、Git を使用して変更を発行します。

```bash
git commit -am "configure managed identity"
git push azure main
```

-----

新しい Web ページに To-Do リストを表示するとき、アプリはマネージド ID を使用してデータベースに接続しています。

![Code First Migration の手順後の Azure アプリ](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

以前と同様に To-Do リストを編集できるようになりました。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次のステップ

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * マネージド ID を有効にする
> * SQL Database へのアクセスをマネージド ID に付与する
> * SQL Database で Azure AD 認証を使用するように Entity Framework を構成する
> * Azure AD 認証を使用して Visual Studio から SQL Database に接続する

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure App Service にマップする](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [チュートリアル: マネージド ID をサポートしていない Azure サービスに接続する (Key Vault を使用)](tutorial-connect-msi-key-vault.md)

> [!div class="nextstepaction"]
> [チュートリアル: 仮想ネットワーク統合を使用してバックエンド通信を分離する](tutorial-networking-isolate-vnet.md)
