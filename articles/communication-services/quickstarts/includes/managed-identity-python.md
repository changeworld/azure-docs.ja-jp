---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450381"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Communication Services ソリューションにマネージド ID を追加する

### <a name="install-the-sdk-packages"></a>SDK パッケージをインストールする

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>SDK パッケージを使用する

Azure Identity を使用するには、次の `import` をコードに追加します。

```python
from azure.identity import DefaultAzureCredential
```

下の例では [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential) が使用されています。 この資格情報は、運用と開発の各環境に適しています。

マネージド ID 認証を使用する簡単な方法については、[マネージド ID を使用したアクセスの承認](../managed-identity-from-cli.md)に関する記事を参照してください。

DefaultAzureCredential オブジェクトの仕組みと、このクイックスタートで示されていないその使用方法についての詳細は、[Python 用 Azure ID クライアント ライブラリ](https://docs.microsoft.com/python/api/overview/azure/identity-readme)に関する記事を参照してください。

### <a name="create-an-identity-and-issue-a-token"></a>ID を作成してトークンを発行する

次のコード例は、マネージド ID を使用してサービス クライアント オブジェクトを作成し、このクライアントを使用して新しいユーザーに対してトークンを発行する方法を示しています。

```python
from azure.communication.identity import CommunicationIdentityClient

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Azure マネージド ID を使用して SMS を送信する
次のコード例は、Azure マネージド ID を使用してサービス クライアント オブジェクトを作成し、このクライアントを使用して SMS メッセージを送信する方法を示しています。

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

### <a name="list-all-your-purchased-phone-numbers"></a>すべての購入済み電話番号を一覧表示する

次のコード例は、Azure マネージド ID を使用してサービス クライアント オブジェクトを作成し、そのクライアントを使用して、リソースに含まれるすべての購入済み電話番号を表示する方法を示しています。

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```