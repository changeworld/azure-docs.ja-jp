---
ms.openlocfilehash: aeeac4ba772899575ab426d76b785a2103a3cdcb
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486602"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Communication Services ソリューションにマネージド ID を追加する

### <a name="install-the-client-library-packages"></a>クライアント ライブラリ パッケージをインストールする

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>クライアント ライブラリ パッケージを使用する

Azure Identity を使用するには、次の `import` をコードに追加します。

```python
from azure.identity import DefaultAzureCredential
```

下の例では [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential) が使用されています。 この資格情報は、運用と開発の各環境に適しています。

開発環境でアプリケーションを登録し、環境変数を設定するには、[マネージド ID を使用したアクセスの承認](../managed-identity-from-cli.md)に関するページを参照してください。

### <a name="create-an-identity-and-issue-a-token"></a>ID を作成してトークンを発行する

次のコード例は、マネージド ID を使用してサービス クライアント オブジェクトを作成し、このクライアントを使用して新しいユーザーに対してトークンを発行する方法を示しています。

```python
import azure.communication.identity 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Azure マネージド ID を使用して SMS を送信する

次のコード例は、Azure マネージド ID を使用してサービス クライアント オブジェクトを作成し、このクライアントを使用して SMS メッセージを送信する方法を示しています。

```python
from azure.communication.sms import (
    PhoneNumberIdentifier,
    SendSmsOptions,
    SmsClient
)

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_phone_number=PhoneNumberIdentitifier(from_phone_number),
          to_phone_numbers=[PhoneNumberIdentifier(to_phone_number)],
          message=message_content,
          send_sms_options=SendSmsOptions(enable_delivery_report=True))  # optional property
     )
```