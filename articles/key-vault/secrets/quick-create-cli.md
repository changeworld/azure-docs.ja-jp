---
title: クイックスタート - Azure Key Vault との間でシークレットの設定と取得を行う
description: Azure CLI を使用して Azure Key Vault との間でシークレットの設定と取得を行う方法を紹介したクイック スタート
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 655ea1920fc80c8cd677281f09cfca21120e1d61
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726432"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Key Vault との間でシークレットの設定と取得を行う

このクイックスタートでは、Azure CLI を使用して Azure Key Vault にキー コンテナーを作成します。 Azure Key Vault は、セキュリティで保護されたシークレット ストアとして機能するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Key Vault の詳細については、[概要](../general/overview.md)に関するページを参照してください。 Azure CLI は、コマンドまたはスクリプトを使用して Azure リソースを作成および管理するために使用します。 この作業を完了したら、シークレットを格納します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このクイックスタートには、Azure CLI のバージョン 2.0.4 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Key Vault を作成します

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Key Vault にシークレットを追加する

シークレットをコンテナーに追加するには、いくつかの追加の手順を実行する必要があります。 このパスワードは、アプリケーションによって使用される可能性があります。 パスワードは **ExamplePassword** と呼ばれ、値 **hVFkk965BuUv** がその中に格納されます。

下の Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) コマンドを使用して、値 **hVFkk965BuUv** を格納するシークレットを **ExamplePassword** という名前の Key Vault に作成します。

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

## <a name="retrieve-a-secret-from-key-vault"></a>Key Vault からシークレットを取得する

これで、Azure Key Vault に追加したパスワードは、その URI を使用すると参照できます。 現在のバージョンを取得するには、 **"https://<your-unique-keyvault-name>.vault.azure.net/secrets/ExamplePassword"** を使用します。

シークレットに格納されている値をプレーンテキストとして表示するには:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"
```

これで、キー コンテナーを作成し、シークレットを格納した後、取得しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Key Vault を作成してシークレットを格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [複数行のシークレットを Key Vault に格納](multiline-secrets.md)する方法について確認する
- [Azure CLI az keyvault コマンド](/cli/azure/keyvault)のリファレンスを参照する
- [Key Vault のセキュリティの概要](../general/security-overview.md)を確認する
