---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 778255dc7259bd66a7c7059ede855464c1e39569
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445200"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet/)。
- お使いのオペレーティング システムの最新バージョンの [.NET Core クライアント ライブラリ](https://dotnet.microsoft.com/download/dotnet-core)。
- [.NET Identity クライアント ライブラリ](/dotnet/api/azure.identity)の最新バージョンを取得します。
- [.NET 管理クライアント ライブラリ](../../concepts/sdk-options.md)の最新バージョンを取得します。

## <a name="installing-the-client-library"></a>クライアント ライブラリのインストール

まず、Communication Services 管理クライアント ライブラリを C# プロジェクトにインクルードします。

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>サブスクリプション ID

Azure サブスクリプションの ID を把握しておく必要があります。 これは、ポータルから取得できます。

1.  Azure アカウントにログインします
2.  左側のサイドバーでサブスクリプションを選択します
3.  必要なサブスクリプションを選択します
4.  [概要] をクリックします
5.  サブスクリプション ID を選択します

このクイックスタートでは、 `AZURE_SUBSCRIPTION_ID` という名前の環境変数にサブスクリプション ID を格納していることを前提としています。

## <a name="authentication"></a>認証

Azure Communication Services と通信するには、まず Azure に対して自分自身を認証する必要があります。 通常は、サービス プリンシパル ID を使用してこれを行います。

### <a name="option-1-managed-identity"></a>オプション 1: マネージド ID

コードが Azure のサービスとして実行されている場合、最も簡単な認証方法は、Azure からマネージド ID を取得することです。 [マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) の詳細を確認してください。

[マネージド ID がサポートされる Azure サービス](../../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

[App Service と Azure Functions でマネージド ID を使用する方法](../../../app-service/overview-managed-identity.md?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[システム割り当てマネージド ID](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[ユーザー割り当てマネージド ID](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)

作成したマネージド ID の ClientId は、明示的に `ManagedIdentityCredential` に渡す必要があります。

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>オプション 2:サービス プリンシパル

マネージド ID を使用する代わりに、自分で管理するサービス プリンシパルを使用して Azure に対する認証を行うことができます。 詳細については、[Azure Active Directory でのサービス プリンシパルの作成と管理](../../../active-directory/develop/howto-create-service-principal-portal.md)に関するドキュメントを参照してください。

サービス プリンシパルを作成したら、それに関する次の情報を Azure portal から収集する必要があります。

- **クライアント ID**
- **クライアント シークレット**
- **テナント ID**

これらの値をそれぞれ `AZURE_CLIENT_ID`、 `AZURE_CLIENT_SECRET`、 `AZURE_TENANT_ID` という名前の環境変数に格納します。 その後、次のような Communication Services 管理クライアントを作成できます。

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>オプション 3:ユーザー ID

サービス ID を使用するのではなく、対話ユーザーの代わりに Azure を呼び出す場合は、次のコードを使用して Azure Communication Services 管理クライアントを作成できます。 これにより、ブラウザー ウィンドウが開き、ユーザーに MSA または Azure AD 資格情報の入力を求めるメッセージが表示されます。

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>Communication Services リソースの管理

### <a name="interacting-with-azure-resources"></a>Azure リソースの操作

これで認証されたので、管理クライアントを使用して API を呼び出すことができます。

次の各例では、Communication Services リソースを既存のリソース グループに割り当てます。

リソース グループを作成する必要がある場合は、 [Azure portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md) または [Azure Resource Manager クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md)を使用して実行できます。

### <a name="create-and-manage-a-communication-services-resource"></a>Communication Services リソースを作成して管理する

Communication Services 管理クライアント ライブラリ クライアント (``Azure.ResourceManager.Communication.CommunicationManagementClient``) のインスタンスを使用して、Communication Services リソースに対する操作を実行できます。

#### <a name="create-a-communication-services-resource"></a>Communication Services リソースを作成する

Communication Services リソースの作成時、リソース グループ名とリソース名を指定します。 `Location` プロパティは常に `global` であり、パブリック プレビュー中は `DataLocation` の値を `UnitedStates` にする必要があることに注意してください。

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>Communication Services リソースを更新する

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>Communication Services リソースをすべて一覧表示する

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>Communication Services リソースを削除する

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>キーと接続文字列の管理

すべての Communication Services リソースには、アクセスキーと対応する接続文字列のペアがあります。 これらのキーには、管理クライアント ライブラリを使用してアクセスできます。それから他の Communication Services クライアント ライブラリで Azure Communication Services に対して自身を認証するためにこれらを使用できます。

#### <a name="get-access-keys-for-a-communication-services-resource"></a>Communication Services リソースのアクセス キーを取得する

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>Communication Services リソースのアクセス キーを再生成する

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```