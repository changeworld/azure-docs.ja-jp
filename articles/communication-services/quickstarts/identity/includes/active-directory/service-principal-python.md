---
ms.openlocfilehash: ad95a54a62c863b3c80cc372d57efbdd2adc49dc
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656964"
---
> [!NOTE]
> このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/use-managed-Identity) にあります

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir active-directory-authentication-quickstart && cd active-directory-authentication-quickstart
```

### <a name="install-the-sdk-packages"></a>SDK パッケージをインストールする

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="create-a-new-file"></a>新しいファイルを作成する
作成したフォルダー内に `authentication.py` という名前の新しいファイルを開いて保存すると、このファイル内にコードが配置されます。

### <a name="use-the-sdk-packages"></a>SDK パッケージを使用する

インストールした SDK を使用するには、ファイルの先頭に次の `import` ステートメントを追加します。

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>DefaultAzureCredential を作成する

[DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential) を使用します。 この資格情報は、運用と開発の各環境に適しています。 このクイックスタート全体で使用するため、ファイルの先頭に作成します。

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-service-principals"></a>ID を作成し、サービス プリンシパルを使用してトークンを発行する

次に、作成した資格情報を使用するコードを追加して、VoIP アクセストークンを発行します。 このコードは後で呼び出します。

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-service-principals"></a>サービス プリンシパルを使用して SMS を送信する
サービス プリンシパルのもう 1 つの使用例として、同じ資格情報を使用して SMS を送信するこのコードを追加します。

```python
def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

## <a name="write-our-main-code"></a>メイン コードを作成する

関数を作成したら、以前に作成した関数を呼び出すメイン コードを作成できるようになります。

```python
# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Service Principals");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Service Principals");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Service Principals");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```

最終的な `authentication.py` ファイルは次のようになります。

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient

credential = DefaultAzureCredential()

def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     response = sms_client.send(
          from_=from_phone_number,
          to=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
     return response

# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Service Principals");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Service Principals");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Service Principals");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```
## <a name="run-the-program"></a>プログラムを実行する

すべてが完了したら、プロジェクトのディレクトリから `python authentication.py` と入力してファイルを実行できます。 正常に実行されると、次のような出力結果が表示されます。

```Bash
    $ python authentication.py
    Retrieving new Access Token, using Service Principals
    Retrieved Access Token: ey...Q
    Sending SMS using using Service Principals
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
