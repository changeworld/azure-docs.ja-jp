---
ms.openlocfilehash: 2c816f005dea452c3ffa2889aa7d2742a5762759
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657646"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Communication Services ソリューションにマネージド ID を追加する (.Net)

### <a name="install-the-client-library-packages"></a>クライアント ライブラリ パッケージをインストールする

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>クライアント ライブラリ パッケージを使用する

Azure ID と Azure Storage クライアント ライブラリを使用するために、次の `using` ディレクティブをコードに追加します。

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

下の例では [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) が使用されています。 この資格情報は、運用と開発の各環境に適しています。

`AZURE_CLIENT_SECRET`、`AZURE_CLIENT_ID`、`AZURE_TENANT_ID` の環境変数は、`DefaultAzureCredential` オブジェクトを作成するために必要です。 開発環境で登録済みアプリケーションを作成し、環境変数を設定するには、[マネージド ID を使用したアクセスの承認](../managed-identity-from-cli.md)に関するページを参照してください。

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>マネージド ID を使用して ID を作成しトークンを発行する

次のコード例は、Azure Active Directory トークンを使用してサービス クライアント オブジェクトを作成する方法を示しています。

次に、クライアントを使用して、新しいユーザーに対してトークンを発行します。

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndGetTokenAsync(Uri resourceEdnpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>マネージド ID を使用して SMS を送信する

次のコード例は、マネージド ID を使用して SMS サービス クライアント オブジェクトを作成し、このクライアントを使用して SMS メッセージを送信する方法を示しています。

```csharp
     public async Task SendSms(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認証について学習する](../concepts/authentication.md)

次のことも実行できます。

- [Azure のロールベースのアクセス制御の詳細について学習する](../../../../articles/role-based-access-control/index.yml)
- [.NET 用 Azure ID ライブラリの詳細について学習する](/dotnet/api/overview/azure/identity-readme)
- [ユーザー アクセス トークンを作成する](../../quickstarts/access-tokens.md)
- [SMS メッセージの送信](../telephony-sms/send.md)
- [SMS に関する詳細](../../concepts/telephony-sms/concepts.md)
