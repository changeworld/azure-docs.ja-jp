---
ms.openlocfilehash: 0bfb23977f6553568da24df614621bdf1eb9d06d
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113238"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- お使いのオペレーティング システムに対応した最新バージョンの [.NET Core クライアント ライブラリ](https://dotnet.microsoft.com/download/dotnet-core)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../create-communication-resource.md)。

### <a name="prerequisite-check"></a>前提条件のチェック

- ターミナルまたはコマンド ウィンドウで `dotnet` コマンドを実行して、.NET クライアント ライブラリがインストールされていることを確認します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`PhoneNumbersQuickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。**Program.cs**。

```console
dotnet new console -o PhoneNumbersQuickstart
```

新しく作成したアプリ フォルダーにディレクトリを変更し、`dotnet build` コマンドを使用してアプリケーションをコンパイルします。

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`dotnet add package` コマンドを使用して、.NET 用の Azure Communication PhoneNumbers クライアント ライブラリ パッケージをインストールします。

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0-beta.6
```

**Program.cs** の先頭に `using` ディレクティブを追加して名前空間をインクルードします。

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
```

`Main` の関数シグネチャを非同期となるように更新します。

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>クライアントを認証する

Phone Number クライアントは、[Azure portal][azure_portal] で Azure Communication リソースから取得した接続文字列を使用して認証できます。

```csharp
// Get a connection string to our Azure Communication resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

Phone Number クライアントの認証には、Azure Active Directory 認証を使用することもできます。 この認証方法を使用する場合、`AZURE_CLIENT_SECRET`、`AZURE_CLIENT_ID`、`AZURE_TENANT_ID` の各環境変数が設定されている必要があります。

```csharp
// Get an endpoint to our Azure Communication resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>電話番号を管理する

### <a name="search-for-available-phone-numbers"></a>利用可能な電話番号を検索する

電話番号を購入するにはまず、利用可能な電話番号を検索する必要があります。 電話番号を検索するには、市外局番、割り当ての種類、[電話番号機能](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)、[電話番号の種類](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)、数量を指定します。 ただし、電話番号の種類が無料電話番号の場合、市外局番の指定は任意です。

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>電話番号を購入する

電話番号の検索の結果は `PhoneNumberSearchResult` です。 そこに含まれる `SearchId` を番号購入 API に渡すことで、検索した番号を取得することができます。 電話番号購入 API を呼び出すと、Azure アカウントに料金が発生するので注意してください。

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionResponseAsync();
```

### <a name="get-phone-numbers"></a>電話番号を取得する

購入した番号は、その後、クライアントから取得することができます。
```csharp
var getPhoneNumberResponse = await client.GetPurchasedPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

購入したすべての電話番号を取得することもできます。
``` csharp
var purchasedPhoneNumbers = client.GetPurchasedPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>電話番号機能を更新する

購入した番号を使用して、機能を更新できます。

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>電話番号を解放する

購入した電話番号は解放することができます。

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionResponseAsync();
````

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```

## <a name="sample-code"></a>サンプル コード

サンプル アプリは [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers) からダウンロードできます
