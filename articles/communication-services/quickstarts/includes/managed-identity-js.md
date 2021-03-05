---
ms.openlocfilehash: 0fd97fe0eebb23130513409698b75d2ee7b98a6f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657629"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Communication Services ソリューションにマネージド ID を追加する (JS)

### <a name="install-the-client-library-packages"></a>クライアント ライブラリ パッケージをインストールする

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-client-library-packages"></a>クライアント ライブラリ パッケージを使用する

Azure ID と Azure Storage クライアント ライブラリを使用するために、次の `import` ディレクティブをコードに追加します。

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { SmsClient } from "@azure/communication-sms";
```

下の例では [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential) が使用されています。 この資格情報は、運用と開発の各環境に適しています。

開発環境でアプリケーションを登録し、環境変数を設定するには、[マネージド ID を使用したアクセスの承認](../managed-identity-from-cli.md)に関するページを参照してください。  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>マネージド ID を使用して ID を作成しトークンを発行する

次のコード例は、マネージド ID を使用してサービス クライアント オブジェクトを作成し、このクライアントを使用して新しいユーザーに対してトークンを発行する方法を示しています。

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserWithToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>マネージド ID を使用して SMS を送信する

次のコード例は、マネージド ID を使用してサービス クライアント オブジェクトを作成し、このクライアントを使用して SMS メッセージを送信する方法を示しています。

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SendRequest = { 
          from: fromNumber, 
          to: [toNumber], 
          message: message 
     };

     const response = await smsClient.send(
          sendRequest, 
          {} //Optional SendOptions
          );
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認証について学習する](../concepts/authentication.md)

次のことも実行できます。

- [Azure のロールベースのアクセス制御の詳細について学習する](../../../../articles/role-based-access-control/index.yml)
- [JS 用 Azure ID ライブラリの詳細について学習する](/javascript/api/overview/azure/identity-readme)
- [ユーザー アクセス トークンを作成する](../../quickstarts/access-tokens.md)
- [SMS メッセージの送信](../../quickstarts/telephony-sms/send.md)
- [SMS に関する詳細](../../concepts/telephony-sms/concepts.md)

