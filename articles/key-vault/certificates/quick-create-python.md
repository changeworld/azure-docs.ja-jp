---
title: クイックスタート - Azure Key Vault Python クライアント ライブラリ - 証明書の管理
description: Python クライアント ライブラリを使用して Azure キー コンテナーから証明書を作成、取得、削除する方法について学習します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b9ff7397ad29ac681e21c32608ade9c6ce557c37
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488628"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>クイック スタート:クイックスタート - Python 用 Azure Key Vault 証明書クライアント ライブラリ

Python 用 Azure Key Vault クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。 Key Vault を使用して証明書を保存することで、証明書をコードに保存しなくて済むため、アプリのセキュリティが向上します。

[API のリファレンスのドキュメント](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [パッケージ (Python Package Index)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="set-up-your-local-environment"></a>ローカル環境を設定する

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Key Vault 証明書ライブラリをインストールします。

    ```terminal
    pip install azure-keyvault-certificates
    ```

## <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>サービス プリンシパルにキー コンテナーへのアクセス権を付与する

次の [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドを実行して、証明書に対する get、list、create 操作のサービス プリンシパルを承認します。

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create 
```

---

このコマンドは、前の手順で作成した `KEY_VAULT_NAME` および `AZURE_CLIENT_ID` 環境変数に依存しています。

詳細については、[アクセス ポリシーの割り当て - CLI](../general/assign-access-policy-cli.md) に関するページを参照してください。

## <a name="create-the-sample-code"></a>サンプル コードを作成する

Python 用 Azure Key Vault クライアント ライブラリを使用すると、証明書および関連するアセット (シークレット、暗号化キーなど) を管理できます。 以下のコード サンプルに、クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を示します。

このコードを含めた *kv_certificates.py* という名前のファイルを作成します。

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>コードの実行

前のセクションのコードが *kv_certificates.py* という名前のファイルに含まれていることを確認します。 次のコマンドを使用して、コードを実行します。

```terminal
python kv_certificates.py
```

- アクセス許可エラーが発生した場合は、[`az keyvault set-policy` コマンド](#give-the-service-principal-access-to-your-key-vault)を実行したことを確認してください。
- 同じキー名を使用してコードを再実行すると、"(競合) 証明書 <name> は現在削除されているが、回復可能な状態です" というエラーが生成されることがあります。 別のキー名を使用してください。

## <a name="code-details"></a>コードの詳細

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

前述のコードでは、[`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) オブジェクトにより、サービス プリンシパル用に作成した環境変数が使用されます。 この資格情報は、[`CertificateClient`](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python) など、Azure ライブラリからクライアント オブジェクトを作成するたびに、そのクライアントを使用して操作するリソースの URI と共に指定します。

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>証明書の保存

キー コンテナーのクライアント オブジェクトを取得したら、[begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-create-certificate-certificate-name--policy----kwargs-) メソッドを使用して証明書を作成できます。 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

ここでは、証明書に [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?view=azure-python#get-default--) メソッドを使用して取得したポリシーが必要です。

`begin_create_certificate` メソッドを呼び出すと、キー コンテナーに対する Azure REST API への非同期呼び出しが生成されます。 この非同期呼び出しにより、ポーラー オブジェクトが返されます。 操作の結果を待機するには、ポーラーの `result` メソッドを呼び出します。

要求を処理するとき、クライアントに提供した資格情報オブジェクトを使用して、Azure により、呼び出し元の ID (サービス プリンシパル) が認証されます。

また、呼び出し元が、要求されたアクションの実行を認可されているかどうかも確認されます。 この認可は、先ほど [`az keyvault set-policy` コマンド](#give-the-service-principal-access-to-your-key-vault)を使用して、サービス プリンシパルに付与しています。

### <a name="retrieve-a-certificate"></a>証明書の取得

証明書を Key Vault から読み取るには、[get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#get-certificate-certificate-name----kwargs-) メソッドを使用します。

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Azure CLI コマンド [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) を使用して、証明書が設定されていることを確認することもできます。

### <a name="delete-a-certificate"></a>証明書の削除

証明書を削除するには、[begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-delete-certificate-certificate-name----kwargs-) メソッドを使用します。

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate` メソッドは非同期であり、ポーラー オブジェクトを返します。 ポーラーの `result` メソッドを呼び出して、その完了を待機します。

Azure CLI コマンド [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) を使用して、証明書が削除されていることを確認できます。

削除されると、証明書は削除されたが回復可能な状態がしばらく維持されます。 コードをもう一度実行する場合は、別の証明書名を使用します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[シークレット](../secrets/quick-create-python.md)と[キー](../keys/quick-create-python.md)も試してみる場合は、この記事で作成した Key Vault を再利用できます。

それ以外の場合は、この記事で作成したリソースが完了したら、次のコマンドを使用して、リソース グループとそれに含まれるすべてのリソースを削除します。

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault の概要](../general/overview.md)
- [Azure Key Vault 開発者ガイド](../general/developers-guide.md)
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)
- [Key Vault を使用した認証](../general/authentication.md)
