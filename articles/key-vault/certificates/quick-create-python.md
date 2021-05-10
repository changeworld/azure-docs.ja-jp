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
ms.openlocfilehash: 62249f579909c3f8bfa9bcdf4e77e45453fcb68b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792023"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>クイックスタート: Python 用 Azure Key Vault 証明書クライアント ライブラリ

Python 用 Azure Key Vault 証明書クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。 Key Vault を使用して証明書を保存することで、証明書をコードに保存しなくて済むため、アプリのセキュリティが向上します。

[API のリファレンスのドキュメント](/python/api/overview/azure/keyvault-certificates-readme) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [パッケージ (Python Package Index)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python 2.7 以降または 3.6 以降](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

このクイックスタートは、Linux ターミナル ウィンドウで [Azure CLI](/cli/azure/install-azure-cli) を実行していることを前提としています。

## <a name="set-up-your-local-environment"></a>ローカル環境を設定する

このクイックスタートでは、Azure CLI と Azure Identity ライブラリを使用して、Azure サービスに対するユーザーの認証を行います。 開発者は、Visual Studio または Visual Studio Code を使用してその呼び出しを認証することもできます。詳細については、[Azure Identity クライアント ライブラリを使用してクライアントを認証する](/python/api/overview/azure/identity-readme)方法に関するページを参照してください。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

1. `login` コマンドを実行します。

    ```azurecli-interactive
    az login
    ```

    CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。

    それ以外の場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。

2. ブラウザーでアカウントの資格情報を使用してサインインします。

### <a name="install-the-packages"></a>パッケージのインストール

1. ターミナルまたはコマンド プロンプトで、適切なプロジェクト フォルダーを作成したら、「[Python 仮想環境を使用する](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)」で説明されているように、Python 仮想環境を作成し、アクティブ化します。

1. Azure Active Directory ID ライブラリをインストールします。

    ```terminal
    pip install azure.identity
    ```


1. Key Vault 証明書クライアント ライブラリをインストールします。

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>キー コンテナーへのアクセス許可を付与する

自分のユーザー アカウントに証明書のアクセス許可を付与するアクセス ポリシーをキー コンテナーに対して作成します

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
```

#### <a name="set-environment-variables"></a>環境変数の設定

このアプリケーションでは、`KEY_VAULT_NAME` という環境変数にキー コンテナーの名前を使用します。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS または Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>サンプル コードを作成する

Python 用 Azure Key Vault 証明書クライアント ライブラリを使用すると、証明書を管理できます。 次のコード サンプルは、クライアントの作成、証明書の設定、証明書の取得、証明書の削除を行う方法を示しています。

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

- アクセス許可エラーが発生した場合は、[`az keyvault set-policy` コマンド](#grant-access-to-your-key-vault)を実行したことを確認してください。
- 同じキー名を使用してコードを再実行すると、"(競合) 証明書 <name> は現在削除されているが、回復可能な状態です" というエラーが生成されることがあります。 別のキー名を使用してください。

## <a name="code-details"></a>コードの詳細

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

このクイックスタートでは、ログイン ユーザーを使用してキー コンテナーに対する認証を行います。ローカル開発では、これが推奨される方法となります。 Azure にデプロイされるアプリケーションの場合は、App Service または仮想マシンにマネージド ID を割り当てる必要があります。詳細については、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関するページを参照してください。

以下の例では、キー コンテナーの名前は、"https://\<your-key-vault-name\>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。 この例では、["DefaultAzureCredential()"](/python/api/azure-identity/azure.identity.defaultazurecredential) クラスを使用しています。環境や使用するオプションが変わっても、同じコードを使用して ID を提供することができます。 詳細については、[DefaultAzureCredential 認証](https://docs.microsoft.com/python/api/overview/azure/identity-readme)に関するセクションを参照してください。 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>証明書の保存

キー コンテナーのクライアント オブジェクトを取得したら、[begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) メソッドを使用して証明書を作成できます。 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

ここでは、証明書に [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--) メソッドを使用して取得したポリシーが必要です。

`begin_create_certificate` メソッドを呼び出すと、キー コンテナーに対する Azure REST API への非同期呼び出しが生成されます。 この非同期呼び出しにより、ポーラー オブジェクトが返されます。 操作の結果を待機するには、ポーラーの `result` メソッドを呼び出します。

要求を処理するとき、クライアントに提供した資格情報オブジェクトを使用して、Azure により、呼び出し元の ID (サービス プリンシパル) が認証されます。


### <a name="retrieve-a-certificate"></a>証明書の取得

証明書を Key Vault から読み取るには、[get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-) メソッドを使用します。

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Azure CLI コマンド [az keyvault certificate show](/cli/azure/keyvault/certificate?#az_keyvault_certificate_show) を使用して、証明書が設定されていることを確認することもできます。

### <a name="delete-a-certificate"></a>証明書の削除

証明書を削除するには、[begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-) メソッドを使用します。

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate` メソッドは非同期であり、ポーラー オブジェクトを返します。 ポーラーの `result` メソッドを呼び出して、その完了を待機します。

Azure CLI コマンド [az keyvault certificate show](/cli/azure/keyvault/certificate?#az_keyvault_certificate_show) を使用して、証明書が削除されていることを確認できます。

削除されると、証明書は削除されたが回復可能な状態がしばらく維持されます。 コードをもう一度実行する場合は、別の証明書名を使用します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[シークレット](../secrets/quick-create-python.md)と[キー](../keys/quick-create-python.md)も試してみる場合は、この記事で作成した Key Vault を再利用できます。

それ以外の場合は、この記事で作成したリソースが完了したら、次のコマンドを使用して、リソース グループとそれに含まれるすべてのリソースを削除します。

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault の概要](../general/overview.md)
- [キー コンテナーへのアクセスをセキュリティで保護する](../general/security-overview.md)
- [Azure Key Vault 開発者ガイド](../general/developers-guide.md)
- [Key Vault のセキュリティの概要](../general/security-overview.md)
- [Key Vault を使用した認証](../general/authentication.md)
