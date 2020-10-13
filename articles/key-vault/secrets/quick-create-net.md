---
title: クイックスタート - .NET 用 Azure Key Vault クライアント ライブラリ (v4)
description: .NET クライアント ライブラリ (v4) を使用して Azure キー コンテナーからシークレットを作成、取得、および削除する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 97578233c6b636b5ffe35fa8ff0b138903425f79
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631700"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>クイック スタート:.NET 用 Azure Key Vault クライアント ライブラリ (SDK v4)

.NET 用 Azure Key Vault クライアント シークレット ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。

[API リファレンスのドキュメント](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet&preserve-view=true) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

このクイックスタートでは、`dotnet` および Azure CLI を使用します

## <a name="setup"></a>セットアップ

### <a name="create-a-new-console-app"></a>新しいコンソール アプリを作成する

このクイックスタートでは、Azure CLI と Azure Identity ライブラリを使用して、Azure サービスに対するユーザーの認証を行います。 開発者は、Visual Studio または Visual Studio Code を使用してその呼び出しを認証することもできます。詳細については、[Azure Identity クライアント ライブラリを使用してクライアントを認証する](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?view=azure-dotnet#authenticate-the-client&preserve-view=true)方法に関するページを参照してください。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

1. `login` コマンドを実行します。

    ```azurecli-interactive
    az login
    ```

    CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。

    それ以外の場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。

2. ブラウザーでアカウントの資格情報を使用してサインインします。


### <a name="create-new-net-console-app"></a>新しい .NET コンソール アプリを作成する

1. コマンド シェルで、次のコマンドを実行して、`key-vault-console-app` という名前のプロジェクトを作成します。

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. 新しく作成した *key-vault-console-app* ディレクトリに移動し、次のコマンドを実行してプロジェクトをビルドします。

    ```dotnetcli
    dotnet build
    ```

    ビルドの出力に警告やエラーが含まれないようにする必要があります。
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>パッケージのインストール

コマンド シェルから、.NET 用 Azure Key Vault クライアント ライブラリをインストールします。

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

このクイックスタートでは、Azure Identity 用 Azure SDK クライアント ライブラリもインストールする必要があります。

```dotnetcli
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE[Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>キー コンテナーへのアクセス許可を付与する

自分のユーザー アカウントにシークレットのアクセス許可を付与するアクセス ポリシーをキー コンテナーに対して作成します。

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>環境変数の設定

このアプリケーションでは、`KEY_VAULT_NAME` という環境変数にキー コンテナーの名前を使用します。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS または Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>オブジェクト モデル

シークレットは、.NET 用 Azure Key Vault クライアント シークレット ライブラリを使用して管理できます。 クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を「[コード例](#code-examples)」セクションで紹介しています。

コンソール アプリ全体は https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app から入手できます。

## <a name="code-examples"></a>コード例

### <a name="add-directives"></a>ディレクティブの追加

*Program.cs* の先頭に次のディレクティブを追加します。

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

このクイックスタートでは、ログイン ユーザーを使用してキー コンテナーに対する認証を行います。ローカル開発では、これが推奨される方法となります。 Azure にデプロイされるアプリケーションの場合は、App Service または仮想マシンにマネージド ID を割り当てる必要があります。詳細については、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関するページを参照してください。

以下の例では、キー コンテナーの名前は、"https://\<your-key-vault-name\>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。 この例では、["DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) クラスを使用しています。環境や使用するオプションが変わっても、同じコードを使用して ID を提供することができます。 詳細については、[DefaultAzureCredential 認証](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?view=azure-dotnet#defaultazurecredential)に関するセクションを参照してください。 

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>シークレットを保存する

コンソール アプリの認証が済んだら、キー コンテナーにシークレットを追加します。 このタスクには、[client.SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) メソッドを使用します。 このメソッドの第 1 パラメーターには、シークレットの名前 (このサンプルでは "mySecret") を指定します。

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

シークレットが設定されたことは、[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true) コマンドを使用して確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>シークレットを取得する

先ほど設定した値は、[client.GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync) メソッドを使用して取得できます。

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

これで、シークレットが `secret.Value` として保存されました。

### <a name="delete-a-secret"></a>シークレットを削除します

最後に、[client.DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync) メソッドを使用して、キー コンテナーからシークレットを削除してみましょう。

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true) コマンドを使用して、シークレットがなくなったことを確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、Azure CLI または Azure PowerShell を使用して、キー コンテナーとそれに対応するリソース グループを削除できます。

### <a name="delete-a-key-vault"></a>Key Vault の削除

```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Key Vault の消去

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>リソース グループの削除

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>サンプル コード

Key Vault を対話的に操作するために、次の手順を完了して .NET Core コンソール アプリに変更を加えます。

1. *Program.cs* のコードを次のコードに置き換えます。

    ```csharp
    using System;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static void Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                client.SetSecret(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                KeyVaultSecret secret = client.GetSecret(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = client.StartDeleteSecret(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                while (!operation.HasCompleted)
                {
                    Thread.Sleep(2000);
                
                    operation.UpdateStatus();
                }
                client.PurgeDeletedSecret(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```

1. 次のコマンドを実行して、アプリを実行します。

    ```dotnetcli
    dotnet run
    ```

1. メッセージが表示されたら、シークレットの値を入力します (例: mySecretPassword)。

    次のような出力が表示されます。

    ```console
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.    
    ```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成し、シークレットを格納して、そのシークレットを取得しました。 [コンソール アプリ全体は GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app) で確認してください。

Key Vault の詳細およびアプリとの統合方法の詳細については、以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [App Service アプリケーションから Key Vault にアクセスする方法についてのチュートリアル](../general/tutorial-net-create-vault-azure-web-app.md)を参照する
- [仮想マシンから Key Vault にアクセスする方法についてのチュートリアル](../general/tutorial-net-virtual-machine.md)を参照する
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)を確認する
