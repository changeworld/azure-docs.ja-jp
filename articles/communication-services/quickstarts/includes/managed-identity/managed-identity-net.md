---
ms.openlocfilehash: 424e70f4f3656be2196f4dbdfbddb852846b6897
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307484"
---
## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`ManagedIdentitiesQuickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (`Program.cs`) を使用する単純な "Hello World" C# プロジェクトが作成されます。

```console
dotnet new console -o ManagedIdentitiesQuickstartQuickstart
```

新しく作成したアプリ フォルダーにディレクトリを変更し、`dotnet build` コマンドを使用してアプリケーションをコンパイルします。

```console
cd ManagedIdentitiesQuickstart
dotnet build
```

### <a name="install-the-sdk-packages"></a>SDK パッケージをインストールする

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-sdk-packages"></a>SDK パッケージを使用する

Azure ID と Azure Storage SDK を使用するには、次の `using` ディレクティブを `Program.cs` に追加します。

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
using Azure;
```

## <a name="create-a-defaultazurecredential"></a>DefaultAzureCredential を作成する

このクイックスタートでは、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) を使用します。 この資格情報は、運用と開発の各環境に適しています。 これは各操作に必要なので、`Program.cs` クラス内に作成します。 ファイルの先頭に以下を追加します。

```csharp
     private DefaultAzureCredential credential = new DefaultAzureCredential();
```

## <a name="issue-a-token-with-managed-identities"></a>マネージド ID を使用してトークンを発行する

次に、作成した資格情報を使用するコードを追加して、VoIP アクセス トークンを発行します。 このコードは後で呼び出します。

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

## <a name="send-an-sms-with-managed-identities"></a>マネージド ID を使用して SMS を送信する

マネージ ID を使用するもう 1 つの例として、同じ資格情報を使用して SMS を送信する次のコードを追加します。

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
     }
```

## <a name="write-the-main-method"></a>Main メソッドを作成する

`Program.cs` には Main メソッドがすでに含まれているはずなので、前に作成したコードを呼び出してマネージド ID の使用方法を実演するコードを追加してみましょう。

```csharp
     static void Main(string[] args)
     {
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          Uri endpoint = new("https://<RESOURCENAME>.communication.azure.com/");

          // We need an instance of the program class to use within this method.
          Program instance = new();

          Console.WriteLine("Retrieving new Access Token, using Managed Identities");
          Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
          Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

          Console.WriteLine("Sending SMS using Managed Identities");

          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.SendSms(endpoint, "<Your ACS Phone Number>", "<The Phone Number you'd like to send the SMS to.>", "Hello from Managed Identities");
          Console.WriteLine($"Sms id: {result.MessageId}");
          Console.WriteLine($"Send Result Successful: {result.Successful}");
     }
```

最終的な `Program.cs` ファイルは次のようになります。

```csharp
class Program
     {
          private DefaultAzureCredential credential = new DefaultAzureCredential();
          static void Main(string[] args)
          {
               // You can find your endpoint and access key from your resource in the Azure portal
               // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
               Uri endpoint = new("https://acstestingrifox.communication.azure.com/");

               // We need an instance of the program class to use within this method.
               Program instance = new();

               Console.WriteLine("Retrieving new Access Token, using Managed Identities");
               Response<AccessToken> response = instance.CreateIdentityAndGetTokenAsync(endpoint);
               Console.WriteLine($"Retrieved Access Token: {response.Value.Token}");

               Console.WriteLine("Sending SMS using Managed Identities");

               // You will need a phone number from your resource to send an SMS.
               SmsSendResult result = instance.SendSms(endpoint, "+18445504651", "+14256253982", "Hello from Managed Identities");
               Console.WriteLine($"Sms id: {result.MessageId}");
               Console.WriteLine($"Send Result Successful: {result.Successful}");
          }
          public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
          {
               var client = new CommunicationIdentityClient(resourceEndpoint, this.credential);
               var identityResponse = client.CreateUser();
               var identity = identityResponse.Value;

               var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

               return tokenResponse;
          }
          public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
          {
               SmsClient smsClient = new SmsClient(resourceEndpoint, this.credential);
               SmsSendResult sendResult = smsClient.Send(
                    from: from,
                    to: to,
                    message: message,
                    new SmsSendOptions(enableDeliveryReport: true) // optional
               );

               return sendResult;
          }
    }
```

## <a name="run-the-program"></a>プログラムを実行する

これで、アプリケーション フォルダーから `dotnet run` を使用して、アプリケーションを実行できるようになりました。 出力は次のようになります。
```
Retrieving new Access Token, using Managed Identities
Retrieved Access Token: ey....
Sending SMS using Managed Identities
Sms id: Outgoing_..._noam
Send Result Successful: True
```
