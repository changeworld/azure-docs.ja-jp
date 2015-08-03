### (省略可能) Azure Active Directory 用に .NET Mobile Services を構成する

>[AZURE.NOTE]以下の手順は、Azure Active Directory ログイン プロバイダーにのみ適用されるので、省略可能です。

1. [WindowsAzure.MobileServices.Backend.Security NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend.Security)をインストールします。

2. Visual Studio で、App_Start を展開し、WebApiConfig.cs ファイルを開きます。次の `using` ステートメントを先頭に追加します。

        using Microsoft.WindowsAzure.Mobile.Service.Security.Providers;

3. また、WebApiConfig.cs で、`options` がインスタンス化された直後に、次のコードを `Register` メソッドに追加します。

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

<!---HONumber=July15_HO4-->