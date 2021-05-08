---
title: .NET SDK を使用して Purview アカウントを作成する
description: .NET SDK を使用して Azure Purview アカウントを作成します。
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: 04ed5cef351c81355a2390dd0b983c162f2b9532
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387173"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>クイックスタート: .NET SDK を使用して Purview アカウントを作成する

このクイックスタートでは、.NET SDK を使用して Azure Purview アカウントを作成する方法について説明します 

> [!IMPORTANT]
> Azure Purview は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 所有する [Azure Active Directory テナント](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* ご使用のアカウントには、サブスクリプションにリソースを作成するためのアクセス許可が必要です。

* **ストレージ アカウント** および **EventHub 名前空間** の作成をすべてのアプリケーションに禁止する **Azure Policy** がある場合は、タグを使用してポリシーの例外を作成する必要があります。これは、Purview アカウントを作成する過程で入力できます。 その主な理由は、作成した各 Purview アカウントでマネージド リソース グループを作成し、また、そのリソース グループ内にストレージ アカウントと EventHub 名前空間を作成する必要があるためです。 詳細については、[Catalog ポータルの作成](create-catalog-portal.md)に関する記事をご覧ください

### <a name="visual-studio"></a>Visual Studio

この記事のチュートリアルでは、Visual Studio 2019 を使用します。 Visual Studio 2013、2015、または 2017 での手順は少し異なります。

### <a name="azure-net-sdk"></a>Azure .NET SDK

[Azure .NET SDK](https://azure.microsoft.com/downloads/) をマシンにダウンロードしてインストールします。

## <a name="create-an-application-in-azure-active-directory"></a>Azure Active Directory にアプリケーションを作成する

「*方法: リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する*」の各セクションの手順に従って、以下のタスクを実行します。

1. 「[Azure Active Directory アプリケーションを作成する](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)」で、このチュートリアルで作成している .NET アプリケーションを表すアプリケーションを作成します。 サインオン URL については、この記事に示されているようにダミーの URL (`https://contoso.org/exampleapp`) を指定できます。
2. 「[サインインするための値を取得する](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)」で、**アプリケーション ID** と **テナント ID** を取得し、それらの値をメモしておいてください。このチュートリアルで後ほど使用します。 
3. 「[証明書とシークレット](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)」で、**認証キー** を取得し、その値をメモしておいてください。このチュートリアルで後ほど使用します。
4. 「[アプリケーションをロールに割り当てる](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)」で、アプリケーションがサブスクリプションにデータ ファクトリを作成できるように、サブスクリプション レベルでアプリケーションを **共同作成者** ロールに割り当てます。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

次に、Visual Studio で、C# .NET コンソール アプリケーションを作成します。

1. **Visual Studio** を起動します。
2. スタート ウィンドウで、 **[新しいプロジェクトの作成]**  >  **[コンソール アプリ (.NET Framework)]** の順に選択します。 .NET バージョン 4.5.2 以降が必要です。
3. **[プロジェクト名]** に「**ADFv2QuickStart**」と入力します。
4. **[作成]** を選択してプロジェクトを作成します。

## <a name="install-nuget-packages"></a>NuGet パッケージのインストール

1. **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。
2. **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを実行してパッケージをインストールします。 詳細については、[Microsoft.Azure.Management.Purview NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/)に関する記事をご覧ください。

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Purview クライアントを作成する

1. **Program.cs** を開き、次のステートメントを挿入して、名前空間への参照を追加します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. 変数を設定する次のコードを **Main** メソッドに追加します。 プレースホルダーを実際の値に置き換えてください。 現在 Purview が利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」ページで「**Azure Purview**」を検索し、目的のリージョンを選択してください。

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. **PurviewManagementClient** クラスのインスタンスを作成する次のコードを **Main** メソッドに追加します。 このオブジェクトを使用して、Purview アカウントを作成します。

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Purview アカウントを作成する

**Purview アカウント** を作成する次のコードを **Main** メソッドに追加します。 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>コードの実行

アプリケーションをビルドして起動し、実行を確認します。

コンソールにより、Purview アカウントの作成の進行状況が出力されます。

### <a name="sample-output"></a>サンプル出力

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>出力を検証する

[Azure portal](https://portal.azure.com) の **[Purview アカウント]** ページに移動し、上記のコードを使用して作成されたアカウントを確認します。 

## <a name="delete-purview-account"></a>Purview アカウントを削除する

プログラムによって Purview アカウントを削除するには、次のコード行をプログラムに追加します。 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Purview アカウント名を使用できるかどうかを確認する

Purview アカウントの可用性を確認するには、次のコードを使用します。 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

上記のコードでは、名前を使用できる場合は "True"、名前を使用できない場合は "False" が出力されます。


## <a name="next-steps"></a>次のステップ

このチュートリアルのコードでは、Purview アカウントの作成、Purview アカウントの削除、および Purview アカウントの名前の可用性を確認します。 これで、.NET SDK をダウンロードし、Purview アカウントに対して実行できるその他のリソース プロバイダー アクションについて学習することができます。

次の記事に進んで、Azure Purview アカウントへのアクセスをユーザーに許可する方法を学習してください。 

> [!div class="nextstepaction"]
> [Azure Purview アカウントにユーザーを追加する](catalog-permissions.md)
