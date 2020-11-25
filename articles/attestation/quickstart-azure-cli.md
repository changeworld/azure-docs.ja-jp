---
title: Azure CLI を使用して Azure Attestation を設定する
description: Azure CLI を使用して構成証明プロバイダーを設定、構成する方法。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5ffcd56be1ce7427697cd3a75bd7c4232cc3b8a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663979"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>クイックスタート: Azure CLI を使用して Azure Attestation を設定する

Azure CLI を使用して Azure Attestation を導入し、構成証明を設定します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>はじめに

1. 次のコマンドを使用して、Azure にサインインします。

   ```azurecli
   az login
   ```

1. 必要に応じて、Azure Attestation のサブスクリプションに切り替えます。

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. [az provider register](/cli/azure/provider#az_provider_register) コマンドを使用して、サブスクリプションに Microsoft.Attestation リソース プロバイダーを登録します。

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Azure リソース プロバイダーについて、またリソース プロバイダーを構成、管理する方法について詳しくは、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

   > [!NOTE]
   > リソース プロバイダーはサブスクリプションに 1 回だけ登録する必要があります。

1. 構成証明プロバイダー用のリソース グループを作成します。 クライアント アプリケーション インスタンスがインストールされた仮想マシンなど、他の Azure リソースを同じリソース グループに配置することができます。 [az group create](/cli/azure/group#az_group_create) コマンドを実行してリソース グループを作成するか、既存のリソース グループを使用します。

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>構成証明プロバイダーを作成して管理する

構成証明プロバイダーを作成して管理するには、次の手順に従います。

1. [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) コマンドを実行して構成証明プロバイダーを作成します。

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   **--certs-input-path** パラメーターは、信頼済みの一連の署名キーを指定します。 このパラメーターにファイル名を指定した場合、構成証明プロバイダーの構成には必ず、署名された JWT 形式のポリシーを使用する必要があります。 それ以外の場合は、テキストまたは署名されていない JWT 形式でポリシーを構成することができます。 JWT については、「[基本的な概念](basic-concepts.md)」を参照してください。 証明書のサンプルについては、「[構成証明ポリシー署名者証明書の例](policy-signer-examples.md)」を参照してください。

1. [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) コマンドを実行して、構成証明プロバイダーのプロパティ (状態や AttestURI など) を取得します。

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
   ```

   このコマンドを実行すると、次の出力に示したような値が表示されます。

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

構成証明プロバイダーは、[az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) コマンドを使用して削除できます。

```azurecli
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>ポリシー管理

ポリシーを管理するには、Azure AD ユーザーに次の `Actions` のアクセス許可が必要になります。

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

これらのアクセス許可は、`Owner` (ワイルドカードのアクセス許可)、`Contributor` (ワイルドカードのアクセス許可)、`Attestation Contributor` (Azure Attestation 専用のアクセス許可) などのロールを経由して AD ユーザーに割り当てることができます。  

ポリシーを読み取るには、Azure AD ユーザーに次の `Actions` のアクセス許可が必要になります。

- `Microsoft.Attestation/attestationProviders/attestation/read`

これらのアクセス許可は、`Reader` (ワイルドカードのアクセス許可) または `Attestation Reader` (Azure Attestation 専用のアクセス許可) などのロールを経由して AD ユーザーに割り当てることができます。

構成証明プロバイダー用のポリシーの管理は、ここで説明するコマンドを使用して行います (一度に 1 TEE)。

[az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) コマンドからは、指定の TEE の現行ポリシーが返されます。

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> このコマンドを実行すると、テキストと JWT 形式の両方でポリシーが表示されます。

サポートされる TEE タイプは次のとおりです。

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

指定した TEE に使用する新しいポリシーを設定するには、[az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) コマンドを使用します。

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

JWT 形式の構成証明ポリシーには、`AttestationPolicy` という名前の要求が含まれている必要があります。 署名済みのポリシーは、既存のポリシー署名者証明書のいずれかに対応するキーで署名する必要があります。

ポリシーのサンプルについては、「[構成証明ポリシーの例](policy-examples.md)」を参照してください。

指定した TEE に使用する新しいポリシーを [az attestation policy reset](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) コマンドで設定します。

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>ポリシー署名者証明書の管理

構成証明プロバイダーのポリシー署名者証明書を管理するには、次のコマンドを使用します。

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

ポリシー署名者証明書は、`maa-policyCertificate` という名前の要求を含んだ署名済みの JWT です。 要求の値は、追加する信頼済み署名キーを含んだ JWK です。 この JWT は、既存のポリシー署名者証明書のいずれかに対応する秘密キーで署名する必要があります。 JWT と JWK については、「[基本的な概念](basic-concepts.md)」を参照してください。

ポリシー署名者証明書のセマンティック操作はすべて、Azure CLI の外で実行しなければならないことに注意してください。 Azure CLI からは、単純な文字列と見なされます。

証明書のサンプルについては、「[構成証明ポリシー署名者証明書の例](policy-signer-examples.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [構成証明ポリシーを作成して署名する方法](author-sign-policy.md)
- [コード サンプルを使用して SGX エンクレーブの構成証明を実装する](/samples/browse/?expanded=azure&terms=attestation)
