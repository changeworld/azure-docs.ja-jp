---
title: クイックスタート - Microsoft Azure Confidential Ledger Python クライアント ライブラリ
description: Python 用 Microsoft Azure Confidential Ledger クライアント ライブラリの使用方法を説明します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 04/27/2021
ms.service: confidential-ledger
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 292c298f3af1b693574ecf9e053de14373d4d597
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476321"
---
# <a name="quickstart-microsoft-azure-confidential-ledger-client-library-for-python"></a>クイックスタート: Python 用 Microsoft Azure Confidential Ledger クライアント ライブラリ

Python 用 Microsoft Azure Confidential Ledger クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。

Microsoft Azure Confidential Ledger は、機密データ レコードを管理するための、安全性の高い新しいサービスです。 Azure Confidential Ledger は、許可型ブロックチェーン モデルに基づき、不変性 (台帳を追加専用にする) や改ざん防止 (すべてのレコードをそのまま保持する) など、独自のデータ整合性の利点を提供します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[API のリファレンスのドキュメント](/python/api/overview/azure/keyvault-secrets-readme) | [ライブラリーのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/confidentialledger) | [パッケージ (Python Package Index) 管理ライブラリー](https://pypi.org/project/azure-mgmt-confidentialledger/)| [パッケージ (Python Package Index) クライアント ライブラリー](https://pypi.org/project/azure-confidentialledger/)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python 3.6 以降](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="set-up"></a>設定

このクイックスタートでは、Azure ID ライブラリを Azure CLI または Azure PowerShell と共に使用して、Azure サービスに対するユーザーの認証を行います。 また、開発者は、Visual Studio または Visual Studio Code を使用して自分の呼び出しを認証することもできます。 詳細については、[Azure ID クライアント ライブラリを使用したクライアントの認証](/python/api/overview/azure/identity-readme)に関するページを参照してください。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [Sign in to Azure](../../includes/confidential-ledger-sign-in-azure.md)]

### <a name="install-the-packages"></a>パッケージのインストール

ターミナルまたはコマンド プロンプトで、適切なプロジェクト フォルダーを作成したら、「[Python 仮想環境を使用する](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)」で説明されているように、Python 仮想環境を作成し、アクティブ化します。

Azure Active Directory ID クライアント ライブラリをインストールします。

```terminal
pip install azure-identity
```

Azure Confidential Ledger コントロール プレーン クライアント ライブラリをインストールします。

```terminal
pip install azure.mgmt.confidentialledger
```

Azure Confidential Ledger データ プレーン クライアント ライブラリをインストールします。

```terminal
pip install azure.confidentialledger 
```

### <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group](../../includes/confidential-ledger-rg-create.md)]

### <a name="register-the-microsoftconfidentialledger-resource-provider"></a>microsoft.ConfidentialLedger リソース プロバイダーを登録します

[!INCLUDE [Register the microsoft.ConfidentialLedger resource provider](../../includes/confidential-ledger-register-rp.md)]

## <a name="create-your-python-app"></a>Python アプリを作成する

### <a name="initialization"></a>初期化

これで、Python アプリケーションの作成を開始できます。  まず、必要なパッケージをインポートします。

```python
# Import the Azure authentication library

from azure.identity import DefaultAzureCredential

## Import the control plane sdk

from azure.mgmt.confidentialledger import ConfidentialLedger as ConfidentialLedgerAPI
from azure.mgmt.confidentialledger.models import ConfidentialLedger

# import the data plane sdk

from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient
```

次に、[DefaultAzureCredential クラス](/python/api/azure-identity/azure.identity.defaultazurecredential)を使用してアプリを認証します。

```python
credential = DefaultAzureCredential()
```

アプリケーションで使用するいくつかの変数 (リソース グループ (myResourceGroup)、作成する台帳の名前、およびデータ プレーン クライアント ライブラリで使用される 2 つの URL) を設定して、セットアップを完了します。

  > [!Important]
  > 各台帳には、グローバルに一意の名前が必要です。 次の例の \<your-unique-keyvault-name\> は、台帳の名前で置き換えてください。

```python
resource_group = "myResourceGroup"
ledger_name = "<your-unique-ledger-name>"
subscription_id = "<azure-subscription-id>"

identity_url = "https://identity.confidential-ledger.core.azure.com"
ledger_url = "https://" + ledger_name + ".confidential-ledger.azure.com"
```

### <a name="use-the-control-plane-client-library"></a>コントロール プレーン クライアント ライブラリを使用する

コントロール プレーン クライアント ライブラリ (azure.mgmt.confidentialledger) を使用すると、台帳に対する操作 (作成、変更、削除、サブスクリプションに関連付けられている台帳の一覧表示、特定の台帳の詳細の取得など) が可能になります。

このコードでは、まず、コントロール プレーン クライアントを作成します。それには、ConfidentialLedgerAPI に資格情報変数と Azure サブスクリプション ID (両方とも上記で設定) を渡します。  

```python
confidential_ledger_mgmt = ConfidentialLedgerAPI(
    credential, subscription_id
)
```

これで、`begin_create` を使用して台帳を作成できるようになりました。 `begin_create` 関数には、3 つのパラメーター (リソース グループ、台帳の名前、"properties" オブジェクト) が必要です。  

次のキーと値を使用して `properties` 辞書を作成し、それを変数に割り当てます。

```python
properties = {
    "location": "eastus",
    "tags": {},
    "properties": {
        "ledgerType": "Public",
        "aadBasedSecurityPrincipals": [],
    },
}

ledger_properties = ConfidentialLedger(**properties)
```

次に、リソース グループ、台帳の名前、および properties オブジェクトを `begin_create` に渡します。

```python
confidential_ledger_mgmt.ledger.begin_create(resource_group, ledger_name, ledger_properties)
```

台帳が正常に作成されたことを確認するには、`get` 関数を使用してその詳細を表示します。

```python
myledger = confidential_ledger_mgmt.ledger.get(resource_group, ledger_name)

print("Here are the details of your newly created ledger:")
print (f"- Name: {myledger.name}")
print (f"- Location: {myledger.location}")
print (f"- ID: {myledger.id}")

```

### <a name="use-the-data-plane-client-library"></a>データ プレーン クライアント ライブラリを使用する

台帳を用意できたので、データ プレーン クライアント ライブラリ (azure.confidentialledger) を使用して台帳を操作します。 

まず、Confidential Ledger 証明書を生成して保存します。  

```python
identity_client = ConfidentialLedgerIdentityServiceClient(identity_url)
network_identity = identity_client.get_ledger_identity(
     ledger_id=ledger_name
)

ledger_tls_cert_file_name = "networkcert.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)
```

これで、ネットワーク証明書を台帳 URL および資格情報と共に使用して Confidential Ledger クライアントを作成できるようになります。

```python
ledger_client = ConfidentialLedgerClient(
     endpoint=ledger_url, 
     credential=credential,
     ledger_certificate_path=ledger_tls_cert_file_name
)
```

台帳に書き込む準備ができました。  これを行うには、`append_to_ledger` 関数を使用します。

```python
append_result = ledger_client.append_to_ledger(entry_contents="Hello world!")
print(append_result.transaction_id)
```

print 関数により、台帳に対する書き込みのトランザクション ID が返されます。これを使用すると、台帳に書き込んだメッセージを取得できます。

```python
entry = ledger_client.get_ledger_entry(transaction_id=append_result.transaction_id)
print(entry.contents)
```

print 関数では、トランザクション ID に対応する台帳内のメッセージである "Hello world!" が返されます。

## <a name="full-sample-code"></a>完全なサンプル コード

```python
from azure.identity import DefaultAzureCredential

## Import control plane sdk

from azure.mgmt.confidentialledger import ConfidentialLedger as ConfidentialLedgerAPI
from azure.mgmt.confidentialledger.models import ConfidentialLedger

# import data plane sdk

from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

# Set variables

rg = "myResourceGroup"
ledger_name = "<unique-ledger-name>"
subscription_id = "<azure-subscription-id>"

identity_url = "https://identity.confidential-ledger.core.azure.com"
ledger_url = "https://" + ledger_name + ".eastus.cloudapp.azure.com"

# Authentication

# Need to do az login to get default credential to work

credential = DefaultAzureCredential()

# Control plane (azure.mgmt.confidentialledger)
# 
# initialize endpoint with credential and subscription

confidential_ledger_mgmt = ConfidentialLedgerAPI(
    credential, "<subscription-id>"
)

# Create properties dictionary for begin_create call 

properties = {
    "location": "eastus",
    "tags": {},
    "properties": {
        "ledgerType": "Public",
        "aadBasedSecurityPrincipals": [],
    },
}

ledger_properties = ConfidentialLedger(**properties)

# Create a ledger

foo = confidential_ledger_mgmt.ledger.begin_create(rg, ledger_name, ledger_properties)

# Get the details of the ledger you just created

print(f"{rg} / {ledger_name}")
 
print("Here are the details of your newly created ledger:")
myledger = confidential_ledger_mgmt.ledger.get(rg, ledger_name)

print (f"- Name: {myledger.name}")
print (f"- Location: {myledger.location}")
print (f"- ID: {myledger.id}")

# Data plane (azure.confidentialledger)
#
# Create a CL client

identity_client = ConfidentialLedgerIdentityServiceClient(identity_url)
network_identity = identity_client.get_ledger_identity(
     ledger_id=ledger_name
)

ledger_tls_cert_file_name = "networkcert.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)


ledger_client = ConfidentialLedgerClient(
     endpoint=ledger_url, 
     credential=credential,
     ledger_certificate_path=ledger_tls_cert_file_name
)

# Write to the ledger
append_result = ledger_client.append_to_ledger(entry_contents="Hello world!")
print(append_result.transaction_id)

# Read from the ledger
entry = ledger_client.get_ledger_entry(transaction_id=append_result.transaction_id)
print(entry.contents)
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Confidential Ledger に関するその他の記事は、このクイックスタートに基づいている場合があります。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。

それ以外の場合は、この記事で作成したリソースの操作が完了したら、Azure CLI の [az group delete](/cli/azure/group?#az_group_delete) コマンドを使用して、リソース グループとそれに含まれるすべてのリソースを削除します。

```azurecli
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>次の手順

- [Microsoft Azure Confidential Ledger の概要](overview.md)
