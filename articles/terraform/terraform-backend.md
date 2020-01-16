---
title: チュートリアル - Terraform 状態を Azure Storage に格納する
description: Terraform 状態を Azure Storage に格納する方法の紹介。
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 1cc475e5070b21a7ea96585f2183c07d258acdc5
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708426"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>チュートリアル:Terraform 状態を Azure Storage に格納する

Terraform 状態は、Terraform 構成を使用してデプロイされたリソースを調整するために使用されます。 Terraform では、状態によって、追加、更新、または削除する Azure リソースが認識されます。 既定では、`terraform apply` コマンドを実行すると、Terraform 状態がローカルに格納されます。 この構成は、次の理由で最適ではありません。

- ローカルの状態は、チーム環境または共同作業環境ではあまり実用的でない。
- Terraform 状態に機密情報が含まれる可能性がある。
- 状態をローカルに格納すると、不注意で削除される可能性が高くなる。

Terraform は、リモート ストレージへの状態の永続化をサポートしています。 そのようなサポート対象のバックエンドの 1 つとして Azure Storage があります。 このドキュメントでは、この目的のために Azure Storage を構成して使用する方法について説明します。

## <a name="configure-storage-account"></a>ストレージ アカウントの構成

Azure Storage をバックエンドとして使用する前に、ストレージ アカウントを作成する必要があります。 ストレージ アカウントは、Azure portal、PowerShell、Azure CLI、または Terraform 自体を使用して作成できます。 Azure CLI を使用してストレージ アカウントを構成するには、次のサンプルを使用します。

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

ストレージ アカウント名、コンテナー名、ストレージ アクセス キーをメモしておきます。 これらの値は、リモート状態を構成するときに必要です。

## <a name="configure-state-back-end"></a>状態バックエンドの構成

Terraform 状態バックエンドは、`terraform init` コマンドを実行すると構成されます。 状態バックエンドを構成するには、次のデータが必要です。

- **storage_account_name**:Azure ストレージ アカウントの名前。
- **container_name**:BLOB コンテナーの名前。
- **key**:作成する状態ストア ファイルの名前。
- **access_key**:ストレージ アクセス キー。

これらの各値は、Terraform 構成ファイルまたはコマンド ラインで指定できます。 `access_key` 値には環境変数を使用することをお勧めします。 環境変数を使用することで、キーがディスクに書き込まれるのを防ぐことができます。

`ARM_ACCESS_KEY` という名前の環境変数を作成し、その値に Azure ストレージ アクセス キーを指定します。

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Azure ストレージ アカウントのアクセス キーをさらに保護するには、これを Azure Key Vault に格納します。 環境変数は、次のようなコマンドを使用して設定できます。 Azure Key Vault の詳細については、[Azure Key Vault のドキュメント](../key-vault/quick-create-cli.md)を参照してください。

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Terraform でバックエンドを使用するように構成するには、次の手順を実行する必要があります。
- `azurerm` 型の `backend` 構成ブロックを含めます。
- 構成ブロックに `storage_account_name` 値を追加します。
- 構成ブロックに `container_name` 値を追加します。
- 構成ブロックに `key` 値を追加します。

次の例では、Terraform バックエンドを構成し、Azure リソース グループを作成します。

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

次の手順を実行して、構成を初期化します。

1. `terraform init` コマンドを実行します。
1. `terraform apply` コマンドを実行します。

これで、Azure Storage BLOB で状態ファイルを見つけることができます。

## <a name="state-locking"></a>状態のロック

Azure Storage BLOB は、状態を書き込む操作の前に自動的にロックされます。 このパターンによって、破損を引き起こす可能性がある、同時状態操作が防止されます。 

詳細については、Terraform ドキュメントの「[状態のロック](https://www.terraform.io/docs/state/locking.html)」を参照してください。

Azure portal または他の Azure 管理ツールを使用して BLOB を調べると、ロックを確認できます。

![ロックされた Azure BLOB](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>保存時の暗号化

Azure BLOB の格納データは、永続化される前に暗号化されます。 必要に応じて、Terraform はバックエンドから状態を取得し、ローカル メモリに格納します。 このパターンを使用すると、状態はローカル ディスクに書き込まれません。

Azure Storage 暗号化の詳細については、[保存データに対する Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"] 
> [Azure での Terraform の使用について詳細を参照](/azure/terraform)
