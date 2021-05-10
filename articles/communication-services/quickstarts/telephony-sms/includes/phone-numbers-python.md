---
ms.openlocfilehash: e62aed02a0ad5f26ec8fd0a79de5e91269386095
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450237"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7、3.5 以降
- デプロイされた Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

テキスト エディターを使用して phone_numbers_sample.py というファイルをプロジェクトのルート ディレクトリに作成し、次のコードを追加します。 残りのクイックスタート コードは、以降のセクションで追加していきます。

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`pip install` コマンドを使用して、Python 用の Azure Communication Services 管理クライアント ライブラリ パッケージをインストールします。

```console
pip install azure-communication-phonenumbers
```

## <a name="authenticate-the-phone-numbers-client"></a>Phone Numbers クライアントを認証する

`PhoneNumbersClient` は、Azure Active Directory 認証を使用することができます。 `DefaultAzureCredential` オブジェクトを使用するのが、Azure Active Directory を導入する最も簡単な方法です。これは、`pip install` コマンドを使用してインストールすることができます。

```console
pip install azure-identity
```

`DefaultAzureCredential` オブジェクトを作成するためには、あらかじめ `AZURE_CLIENT_ID`、`AZURE_CLIENT_SECRET`、`AZURE_TENANT_ID` を環境変数とし、登録済みの Azure AD アプリケーションから対応する値を取得して設定しておく必要があります。

`azure-identity` ライブラリのインストール後、クライアントの認証に進むことができます。

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

また、通信リソースのエンドポイントとアクセス キーを使用して認証を行うこともできます。

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>関数

`PhoneNumbersClient` の認証が済んだら、それによって実行できるさまざまな機能に取り組むことができます。

### <a name="search-for-available-phone-numbers"></a>利用可能な電話番号を検索する

電話番号を購入するにはまず、利用可能な電話番号を検索する必要があります。 電話番号を検索するには、市外局番、割り当ての種類、[電話番号機能](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)、[電話番号の種類](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)、数量を指定します (既定では、数量が 1 に設定されます)。 ただし、電話番号の種類が無料電話番号の場合、市外局番の指定は任意です。

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = self.phone_number_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>電話番号を購入する

電話番号の検索の結果は `PhoneNumberSearchResult` です。 そこに含まれる `searchId` を番号購入 API に渡すことで、検索した番号を取得することができます。 電話番号購入 API を呼び出すと、Azure アカウントに料金が発生するので注意してください。

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient,
    PhoneNumberCapabilityType,
    PhoneNumberAssignmentType,
    PhoneNumberType,
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>購入した電話番号を取得する

購入した番号は、その後、クライアントから取得することができます。

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

購入したすべての電話番号を取得することもできます。

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>電話番号機能を更新する

以前に購入した電話番号の機能を更新することができます。
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>電話番号を解放する

購入した電話番号は解放することができます。

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>コードの実行

コンソールのプロンプトから、phone_numbers_sample.py ファイルが格納されているディレクトリに移動し、次の Python コマンドを実行してアプリを実行します。

```console
./phone_numbers_sample.py
```
