<properties
   pageTitle="Azure Key Vault を使用してアプリケーション シークレットを保護する | Microsoft Azure"
   description="Key Vault サービスを使用してアプリケーション シークレットを格納する方法"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Azure Key Vault を使用してアプリケーション シークレットを保護する

この記事は[シリーズの一部]です。このシリーズに付属する完成した[サンプル アプリケーション]もあります。

## 概要

次のようなアプリケーション設定は機密情報であり、保護する必要があることは一般的です。

- データベース接続文字列
- パスワード
- 暗号化キー

セキュリティのベスト プラクティスとして、このような機密情報をソース管理に格納しないでください。ソース コード リポジトリがプライベートであったとしても、この情報はいとも簡単に漏えいします。また、これが意味するのは、機密情報を世間一般から守ることだけではありません。大規模なプロジェクトでは、運用環境の機密情報にアクセスできる開発者や運用者を制限することが必要になる場合があります (テスト環境または開発環境の設定は異なります)。

より安全な選択肢として、これらの機密情報を [Azure Key Vault][KeyVault] に格納する方法があります。Key Vault とは、暗号化キーやその他の機密情報を管理するための、クラウドでホストされているサービスです。この記事では、Key Vault を使用して、アプリケーションの構成設定を格納する方法について説明します。

[Tailspin Surveys][Surveys] アプリケーションでは、次の設定が機密情報です。

- データベース接続文字列。
- Redis 接続文字列。
- Web アプリケーションのクライアント シークレット。

構成の機密情報を Key Vault に格納するために、Surveys アプリケーションでは、カスタム構成プロバイダーが実装されています。カスタム構成プロバイダーは、ASP.NET Core 1.0 の[構成システム][configuration]に接続します。カスタム プロバイダーは、起動時に、Key Vault から構成設定を読み取ります。

Surveys アプリケーションは、次の場所から構成設定を読み込みます。

- appsettings.json ファイル
- [ユーザー シークレット ストア][user-secrets] (開発環境のみ、テスト用)
- ホスティング環境 (Azure Web アプリのアプリ設定)
- Key Vault

これらはそれぞれ、以前の設定を上書きするため、Key Vault に格納されているすべての設定が優先されます。

> [AZURE.NOTE] 既定では、Key Vault の構成プロバイダーは無効になっています。これは、アプリケーションをローカルで実行する場合には必要ありません。運用環境のデプロイで有効にします。

> [AZURE.NOTE] Key Vault プロバイダーは、[Microsoft.Azure.KeyVault][Microsoft.Azure.KeyVault] パッケージを必要とするため、現在 .NET Core ではサポートされていません。

アプリケーションは、起動時に、登録されたすべての構成プロバイダーから設定を読み取り、その設定を使用して、厳密に型指定された options オブジェクトに値を設定します (詳細については、「[Using Options and configuration objects (Options オブジェクトと構成オブジェクトの使用)][options]」を参照してください)。

## 実装

[KeyVaultConfigurationProvider][KeyVaultConfigurationProvider] クラスは、ASP.NET Core 1.0 の[構成システム][configuration]に接続する構成プロバイダーです。

`KeyVaultConfigurationProvider` を使用するには、スタートアップ クラスで `AddKeyVaultSecrets` 拡張メソッドを呼び出します。

```csharp
    var builder = new ConfigurationBuilder()
        .SetBasePath(appEnv.ApplicationBasePath)
        .AddJsonFile("appsettings.json");

    if (env.IsDevelopment())
    {
        builder.AddUserSecrets();
    }
    builder.AddEnvironmentVariables();
    var config = builder.Build();

    // Add key vault configuration:
    builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
        config["KeyVault:Name"],
        config["AzureAd:Asymmetric:CertificateThumbprint"],
        Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
        loggerFactory);
```

`KeyVaultConfigurationProvider` にはいくつかの構成設定が必要で、これらは他の構成ソースのいずれかに格納しておく必要があることに注意してください。

アプリケーションの起動時に、`KeyVaultConfigurationProvider` は Key Vault 内のシークレットをすべて列挙します。シークレットごとに、"ConfigKey" という名前のタグが検索され、そのタグの値が構成設定の名前となります。

> [AZURE.NOTE] [Tags][key-tags] は、キーと一緒に格納されている、オプションのメタデータです。タグがここで使用されるのは、キー名にはコロン (:) を使用できないためです。

```csharp
var kvClient = new KeyVaultClient(GetTokenAsync);
var secretsResponseList = await kvClient.GetSecretsAsync(_vault, MaxSecrets, token);
foreach (var secretItem in secretsResponseList.Value)
{
    //The actual config key is stored in a tag with the Key "ConfigKey"
    // because ':' is not supported in a shared secret name by Key Vault.
    if (secretItem.Tags != null && secretItem.Tags.ContainsKey(ConfigKey))
    {
        var secret = await kvClient.GetSecretAsync(secretItem.Id, token);
        Data.Add(secret.Tags[ConfigKey], secret.Value);
    }
}
```

> [AZURE.NOTE] [KeyVaultConfigurationProvider.cs] を参照してください。

## Surveys アプリケーションでの Key Vault の設定

前提条件:

- [Azure Resource Manager コマンドレット][azure-rm-cmdlets]をインストールする。
- 「[How to run the Tailspin Surveys sample application (Tailspin Surveys サンプル アプリケーションの実行方法)][readme]」の説明に従って、Surveys アプリケーションを構成する。

手順の概要は次のとおりです。

1. テナントで管理者ユーザーを設定します。
2. クライアント証明書を設定します。
3. Key Vault を作成します。
4. 作成した Key Vault に構成設定を追加します。
5. Key Vault を有効にするコードをコメント解除します。
6. アプリケーションのユーザー シークレットを更新します。

### 管理者ユーザーを設定する

> [AZURE.NOTE] Key Vault を作成するには、Azure サブスクリプションを管理できるアカウントを使用する必要があります。また、Key Vault からの読み取りを承認する任意のアプリケーションを、そのアカウントと同じテナントに登録する必要があります。

この手順では、Surveys アプリケーションが登録されているテナントのユーザーとしてサインインした状態で、Key Vault を作成できることを確認します。

最初に、Azure サブスクリプションに関連付けられているディレクトリを変更します。

1. [Azure 管理ポータル][azure-management-portal]にログインします。

2. **[設定]** をクリックします。

    ![[設定]](media/guidance-multitenant-identity/settings.png)

3. Azure サブスクリプションを選択します。

4. ポータルの下部にある **[ディレクトリの編集]** をクリックします。

    ![Settings](media/guidance-multitenant-identity/edit-directory.png)

5. [関連付けられたディレクトリの変更] で、Surveys アプリケーションが登録されている Azure AD テナントを選択します。

    ![[設定]](media/guidance-multitenant-identity/edit-directory2.png)

6. 矢印ボタンをクリックして、ダイアログ ボックスを完了します。

Surveys アプリケーションが登録されている Azure AD テナント内に管理者ユーザーを作成します。

1. [Azure 管理ポータル][azure-management-portal]にログインします。

2. アプリケーションが登録されている Azure AD テナントを選択します。

3. **[ユーザー]**、**[ユーザーの追加]** の順にクリックします。

4. **[ユーザーの追加]** ダイアログ ボックスで、ユーザーを全体管理者ロールに割り当てます。

Azure サブスクリプションの共同管理者として、その管理者ユーザーを追加します。

1. [Azure 管理ポータル][azure-management-portal]にログインします。

2. **[設定]** をクリックして、Azure サブスクリプションを選択します。

3. **[管理者]** をクリックします。

4. ポータルの下部にある **[追加]** をクリックします。

5. 前に作成した管理者ユーザーの電子メール アドレスを入力します。

6. サブスクリプションのチェック ボックスをオンにします。

7. チェックマーク ボタンをクリックしてダイアログ ボックスを完了します。

![Add a co-administrator](media/guidance-multitenant-identity/co-admin.png)


### クライアント証明書を設定する

1. 次のように PowerShell スクリプト [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] を実行します。
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    `Subject` パラメーターでは、"surveysapp" などの任意の名前を入力します。このスクリプトによって、自己署名証明書が生成され、"現在のユーザー/個人用" 証明書ストアに保存されます。

2. スクリプトの出力は、JSON フラグメントです。これを、次のように Web アプリのアプリケーション マニフェストに追加します。

    1. [Azure 管理ポータル][azure-management-portal]にログインし、Azure AD ディレクトリに移動します。

    2. **[アプリケーション]** をクリックします。

    3. Surveys アプリケーションを選択します。

    4.	**[マニフェストの管理]** をクリックして、**[マニフェストのダウンロード]** を選択します。

    5.	マニフェスト JSON ファイルをテキスト エディターで開きます。スクリプトの出力を `keyCredentials` プロパティに貼り付けます。次のようになります。
    ```
            "keyCredentials": [
                {
                  "type": "AsymmetricX509Cert",
                  "usage": "Verify",
                  "keyId": "29d4f7db-0539-455e-b708-....",
                  "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
                  "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
                }
              ],
    ```          
    6.	変更内容を JSON ファイルに保存します。

    7.	ポータルに戻ります。**[マニフェストの管理]**、**[マニフェストのアップロード]** の順にクリックし、JSON ファイルをアップロードします。

3. 同じ JSON フラグメントを Web API (Surveys.WebAPI) のアプリケーション マニフェストに追加します。

4. 次のコマンドを実行して、証明書の拇印を取得します。
    ```
    certutil -store -user my [subject]
    ```
    ここで、`[subject]` は、PowerShell スクリプトの Subject に指定した値です。拇印は "Cert Hash(sha1)" の下に表示されます。16 進数の間のスペースを削除します。

拇印は後で使用します。

### Key Vault を作成します

1. 次のように PowerShell スクリプト [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] を実行します。

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    資格情報の入力を求められたら、先ほど作成した Azure AD ユーザーとしてサインインします。このスクリプトにより、新しいリソース グループと、そのリソース グループ内に新しい Key Vault が作成されます。

    注: -Location パラメーターについては、次の PowerShell コマンドを使用して、有効なリージョンの一覧を取得することができます。

    ```
    Get-AzureRmResourceProvider -ProviderNamespace "microsoft.keyvault" | Where-Object { $_.ResourceTypes.ResourceTypeName -eq "vaults" } | Select-Object -ExpandProperty Locations
    ```

2. 次のパラメーターを使用して、もう一度 SetupKeyVault.ps を実行します。

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<web app client ID>>", "<<web API client ID>>")
    ```

    各値の説明:

    - key vault name = 前の手順で Key Vault に指定した名前。
    - web app client ID = Surveys Web アプリケーションのクライアント ID。
    - web api client ID = Surveys.WebAPI アプリケーションのクライアント ID。

    例:
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    > [AZURE.NOTE] クライアント ID は、[Azure 管理ポータル][azure-management-portal]から取得できます。Azure AD テナントを選択し、アプリケーションを選択して、**[構成]** をクリックします。

    このスクリプトでは、Web アプリと Web API が Key Vault からシークレットを取得することを承認します。詳細については、「[Azure Key Vault の概要][authorize-app]」を参照してください。

### 作成した Key Vault に構成設定を追加する

1. 次のように SetupKeyVault.ps を実行します。

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName RedisCache -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" -ConfigName "Redis:Configuration"
    ```
    各値の説明:

    - key vault name = 前の手順で Key Vault に指定した名前。
    - Redis DNS name = Redis Cache インスタンスの DNS 名。
    - Redis access key = Redis Cache インスタンスのアクセス キー。

    このコマンドでは、Key Vault にシークレットを追加します。シークレットは、名前と値の組み合わせにタグを追加したものです。

    -	キー名は、アプリケーションで使用されませんが、Key Vault 内で一意である必要があります。
    -	値は、構成オプションの値で、この場合は Redis 接続文字列です。
    -	"ConfigKey" タグは、構成キーの名前を保持します。

2. この時点で、Key Vault にシークレットが正常に保存されたかどうかをテストすることをお勧めします。次の PowerShell コマンドを実行します。

    ```
    Get-AzureKeyVaultSecret <<key vault name>> RedisCache | Select-Object *
    ```
    シークレットの値といくつかのメタデータが出力に表示されます。

    ![PowerShell の出力](media/guidance-multitenant-identity/get-secret.png)

3. もう一度 SetupKeyVault.ps を実行して、データベース接続文字列を追加します。

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName ConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    ここでは、`<<DB connection string>>` がデータベース接続文字列の値です。

    ローカル データベースでテストする場合は、Tailspin.Surveys.Web/appsettings.json ファイルから接続文字列をコピーします。コピーする場合は、必ず 2 つのバックスラッシュ ("\\") を 1 つのバックスラッシュに変更してください。2 つのバックスラッシュは、JSON ファイルにおけるエスケープ文字です。

    例:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName ConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" -ConfigName "Data:SurveysConnectionString"
    ```

### Key Vault を有効にするコードをコメント解除する

1. Tailspin.Surveys ソリューションを開きます。

2. [Tailspin.Surveys.Web/Startup.cs][web-startup] で、次のコード ブロックを探してコメント解除します。

    ```csharp
    //#if DNX451
    //            _configuration = builder.Build();
    //            builder.AddKeyVaultSecrets(_configuration["AzureAd:ClientId"],
    //                _configuration["KeyVault:Name"],
    //                _configuration["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(_configuration["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

3. [Tailspin.Surveys.WebAPI/Startup.cs][web-api-startup] で、次のコード ブロックを探してコメント解除します。

    ```csharp
    //#if DNX451
    //            var config = builder.Build();
    //            builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
    //                config["KeyVault:Name"],
    //                config["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

4. [Tailspin.Surveys.Web/Startup.cs][web-startup] で、`ICredentialService` を登録するコードを見つけます。`CertificateCredentialService` を使用する行をコメント解除し、`ClientCredentialService` を使用する行をコメントにします。

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    この変更により、Web アプリケーションでは、[クライアント アサーション][client-assertion] を使用して、OAuth アクセス トークンを取得できます。クライアント アサーションを使用すると、OAuth クライアント シークレットは不要になります。代わりに、Key Vault にクライアント シークレットを格納することができます。ただし、Key Vault とクライアント アサーションの両方がクライアント証明書を使用するため、Key Vault を有効にする場合は、クライアント アサーションも有効にすることをお勧めします。

### ユーザー シークレットを更新する

ソリューション エクスプローラーで Tailspin.Surveys.Web プロジェクトを右クリックし、**[ユーザー シークレットの管理]** を選択します。secrets.json ファイルで、既存の JSON を削除し、次のコードを貼り付けます。

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

角かっこ ([ ]) 内のエントリを適切な値に置き換えます。

- `AzureAd:ClientId`: Surveys アプリケーションのクライアント ID。
- `AzureAd:WebApiResourceId`: Azure AD で Surveys.WebAPI アプリケーションの作成時に指定したアプリ ID URI。
- `Asymmetric:CertificateThumbprint`: クライアント証明書の作成時に以前入手した証明書の拇印。
- `KeyVault:Name`: Key Vault の名前。

> [AZURE.NOTE] `Asymmetric:ValidationRequired` が false になっているのは、前に作成した証明書がルート CA (証明機関) によって署名されていないためです。運用時には、CA によって署名された証明書を使用して、`ValidationRequired` を true に設定してください。

更新した secrets.json ファイルを保存します。

次に、ソリューション エクスプローラーで、Tailspin.Surveys.WebApi プロジェクトを右クリックし、**[ユーザー シークレットの管理]** を選択します。既存の JSON を削除し、次のコードを貼り付けます。

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

角かっこ ([ ]) 内のエントリを置き換え、secrets.json ファイルを保存します。

> [AZURE.NOTE] Web API の場合は、Surveys アプリケーションではなく、Surveys.WebAPI アプリケーションのクライアント ID を使用してください。


<!-- Links -->
[authorize-app]: ../key-vault/key-vault-get-started.md/#authorize
[azure-management-portal]: https://manage.windowsazure.com/
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: guidance-multitenant-identity-client-assertion.md
[configuration]: https://docs.asp.net/en/latest/fundamentals/configuration.html
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[KeyVaultConfigurationProvider]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[key-tags]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: https://docs.asp.net/en/latest/fundamentals/configuration.html#using-options-and-configuration-objects
[readme]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[web-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[web-api-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[シリーズの一部]: guidance-multitenant-identity.md
[KeyVaultConfigurationProvider.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[サンプル アプリケーション]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->