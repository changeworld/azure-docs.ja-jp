### (省略可能) AAD ログイン用に .NET モバイル サービスを構成する

>[AZURE.NOTE] 以下の手順は、Azure Active Directory ログイン プロバイダーにのみ適用されるので、省略可能です。

1. **WindowsAzure.MobileServices.Backend.Security** NuGet パッケージをインストールします。

2. Visual Studio で、App_Start を拡張し、WebApiConfig.cs ファイルを開きます。 `options` がインスタンス化された後すぐに、次のコードを  `Register` メソッドに追加します。

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));


<!--HONumber=42-->
