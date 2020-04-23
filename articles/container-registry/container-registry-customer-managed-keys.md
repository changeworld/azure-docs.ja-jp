---
title: カスタマー マネージド キーを使用した保存時の暗号化
description: Azure コンテナー レジストリの保存時の暗号化、および Azure Key Vault に格納されているカスタマー マネージド キーを使用してレジストリを暗号化する方法について説明します
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 2d5561998cf0b19698c8059a861a4014a171a7e7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461754"
---
# <a name="encryption-using-customer-managed-keys"></a>カスタマー マネージド キーを使用した暗号化

イメージや他の成果物を Azure コンテナー レジストリに格納すると、保存時のレジストリの内容は Azure によって[サービス マネージド キー](../security/fundamentals/encryption-atrest.md#data-encryption-models)を使用して自動的に暗号化されます。 Azure Key Vault 内で作成して管理するキーを使用すると、既定の暗号化を追加の暗号化レイヤーで補完することができます。 この記事では、Azure CLI と Azure portal を使用する手順について説明します。

カスタマー マネージド キーを使用するサーバー側暗号化は、[Azure Key Vault](../key-vault/general/overview.md) との統合によってサポートされます。 独自の暗号化キーを作成してキー コンテナーに格納したり、Azure Key Vault の API を使って暗号化キーを生成したりすることができます。 Azure Key Vault を使用してキー使用法を監査することもできます。

この機能は、**Premium** コンテナー レジストリ サービス レベルで使用できます。 レジストリ サービスのレベルと制限については、「[Azure Container Registry SKU](container-registry-skus.md)」をご覧ください。

> [!IMPORTANT]
> この機能は現在プレビュー段階であり、一定の[制限事項](#preview-limitations)が適用されます。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。
>
   
## <a name="preview-limitations"></a>プレビューの制限事項 

* 現在、この機能を有効にできるのは、レジストリを作成するときだけです。
* レジストリでカスタマー マネージド キーを有効にした後、それを無効にすることはできません。
* 現在、カスタマー マネージド キーで暗号化されたレジストリでは、[コンテンツの信頼](container-registry-content-trust.md)はサポートされていません。
* カスタマー マネージド キーで暗号化されたレジストリでは、現在、[ACR タスク](container-registry-tasks-overview.md)に対する実行ログは 24 時間だけ保持されます。 それより長くログを保持する必要がある場合は、[タスク実行ログのエクスポートと保存](container-registry-tasks-logs.md#alternative-log-storage)に関するガイダンスを参照してください。

## <a name="prerequisites"></a>前提条件

この記事の Azure CLI の手順を使用するには、Azure CLI バージョン 2.2.0 以降が必要です。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="enable-customer-managed-key---cli"></a>カスタマー マネージド キーを有効にする - CLI

### <a name="create-a-resource-group"></a>リソース グループを作成する

必要な場合は、[az group create][az-group-create] コマンドを実行して、キー コンテナー、コンテナー レジストリ、その他の必要なリソースを作成するためのリソース グループを作成します。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

[az identity create](../active-directory/managed-identities-azure-resources/overview.md) コマンドを使用して、ユーザー割り当てによる [Azure リソース用マネージド ID][az-identity-create] を作成します。 この ID は、Key Vault サービスにアクセスするためにレジストリによって使用されます。

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

コマンドの出力で、`id` と `principalId` の値を記録しておきます。 これらの値は、後の手順でキー コンテナーへのレジストリ アクセスを構成するために必要です。

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

利便性を考えて、これらの値を環境変数に格納します。

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Key Vault を作成します

[az keyvault create][az-keyvault-create] を使用して、レジストリ暗号化用のカスタマー マネージド キーを格納するためのキー コンテナーを作成します。 

キーまたはキー コンテナーを誤って削除してデータが失われないようにするには、次の設定を有効にする必要があります: **論理的な削除**と**消去保護**。 次の例には、これらの設定のパラメーターが含まれています。 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを追加する

ID でキー コンテナーにアクセスできるように、キー コンテナーのポリシーを構成します。 次の [az keyvault set-policy][az-keyvault-set-policy] コマンドでは、前に作成して環境変数に格納したマネージ ID のプリンシパル ID を渡します。 キーのアクセス許可を **get**、**unwrapKey**、**wrapKey** に設定します。  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>キーを作成してキー ID を取得する

[az keyvault key create][az-keyvault-key-create] コマンドを実行して、キー コンテナーにキーを作成します。

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

コマンドの出力で、キーの ID `kid` を記録しておきます。 この ID は、次のステップで使用します。

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
利便性を考えて、この値を環境変数に格納します。

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>カスタマー マネージド キーを使用してレジストリを作成する

[az acr create][az-acr-create] コマンドを実行してレジストリを作成し、カスタマー マネージド キーを有効にします。 前に環境変数に格納したマネージ ID のプリンシパル ID とキー ID を渡します。

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>暗号化の状態を表示する

カスタマー マネージド キーによるレジストリ暗号化が有効になっているかどうかを確認するには、[az acr encryption show][az-acr-encryption-show] コマンドを実行します。

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>カスタマー マネージド キーを有効にする - ポータル

### <a name="create-a-managed-identity"></a>マネージド ID の作成

Azure portal で、ユーザー割り当てによる [Azure リソース用マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を作成します。 手順については、「[ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)」を参照してください。

マネージド ID の**リソース名**を記録しておきます。 この名前は、後の手順で必要になります。

![Azure portal でユーザー割り当てマネージド ID を作成する](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Key Vault を作成します

キー コンテナーを作成する手順については、「[クイックスタート: Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../key-vault/secrets/quick-create-portal.md)」をご覧ください。

カスタマー マネージド キー用のキー コンテナーを作成するときに、 **[基本]** タブで、次の保護設定を有効にする必要があります: **論理的な削除**と**消去保護**。 これらの設定は、キーまたはキー コンテナーを誤って削除することでデータが失われないようにするのに役立ちます。

![Azure portal でキー コンテナーを作成する](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを追加する

ID でキー コンテナーにアクセスできるように、キー コンテナーのポリシーを構成します。

1. お使いのキー コンテナーに移動します。
1. **[設定]**  >  **[アクセス ポリシー] > [+ アクセス ポリシーの追加]** を選択します。
1. **[キーのアクセス許可]** を選択し、 **[取得]** 、 **[キーの折り返しを解除]** 、および **[キーを折り返す]** を選択します。
1. **[プリンシパルの選択]** を選択し、ユーザー割り当てマネージド ID のリソース名を選択します。  
1. **[追加]** を選択し、 **[保存]** を選択します。

![キー コンテナーのアクセス ポリシーを作成する](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>キーを作成する

1. お使いのキー コンテナーに移動します。
1. **[設定]**  >  **[キー]** の順に選択します。
1. **[+ 生成/インポート]** を選択し、キーの一意の名前を入力します。
1. それ以外については既定値をそのまま使用し、 **[作成]** を選択します。
1. 作成した後、キーを選択し、現在のキーのバージョンを記録しておきます。

### <a name="create-azure-container-registry"></a>Azure Container Registry の作成

1. **[リソースの作成]**  >  **[コンテナー]**  >  **[コンテナー レジストリ]** の順に選択します。
1. **[基本]** タブで、リソース グループを選択または作成し、レジストリ名を入力します。 **[SKU]** で **[Premium]** を選択します。
1. **[暗号化]** タブの **[顧客が管理するキー]** で、 **[有効]** を選択します。
1. **[ID]** で、作成したマネージド ID を選択します。
1. **[暗号化キー]** で、 **[キー コンテナーから選ぶ]** を選択します。
1. **[Azure Key Vault からのキーの選択]** ウィンドウで、前のセクションで作成したキー コンテナー、キー、およびバージョンを選択します。
1. **[暗号化]** タブで、 **[確認と作成]** を選択します。
1. **[作成]** を選択して、レジストリ インスタンスをデプロイします。

![Azure portal でコンテナー レジストリを作成する](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

ポータルでレジストリの暗号化状態を確認するには、お使いのレジストリに移動します。 **[設定]** で、 **[暗号化 (プレビュー)]** を選択します。

## <a name="enable-customer-managed-key---template"></a>カスタマー マネージド キーを有効にする - テンプレート

Resource Manager テンプレートを使用して、レジストリを作成し、カスタマー マネージド キーで暗号化を有効にすることもできます。 

次のテンプレートでは、新しいコンテナー レジストリとユーザー割り当てのマネージド ID が作成されます。 次の内容を新しいファイルにコピーし、`CMKtemplate.json` などのファイル名を使用して保存します。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

前のセクションの手順に従って、次のリソースを作成します。

* キー コンテナー、名前で識別されます
* キー コンテナー キー、キー ID で識別されます

次の [az group deployment create][az-group-deployment-create] コマンドを実行し、上のテンプレート ファイルを使用してレジストリを作成します。 要求されたら、新しいレジストリの名前とマネージド ID の名前、および作成したキー コンテナーの名前とキー ID を指定します。 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>暗号化の状態を表示する

レジストリの暗号化の状態を表示するには、[az acr encryption show-status][az-acr-encryption-show-status] コマンドを実行します。

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>レジストリを使用する

レジストリでカスタマー マネージド キーを使用したデータの暗号化を有効にした後は、カスタマー マネージド キーで暗号化されていないレジストリで実行するのと同じレジストリ操作を実行できます。 たとえば、レジストリを使用して認証を行ったり、Docker イメージをプッシュしたりできます。 「[イメージのプッシュとプル](container-registry-get-started-docker-cli.md)」のコマンドの例をご覧ください。

## <a name="rotate-key"></a>キーをローテーションする

Azure Key Vault のカスタマー マネージド キーは、お使いのコンプライアンス ポリシーに従ってローテーションすることができます。 新しいキーを作成した後、新しいキーを使用してデータを暗号化するようにレジストリを更新します。 これらの手順は、Azure CLI または Azure portal を使用して行うこともできます。

たとえば、新しいキーを作成するには、[az keyvault key create][az-keyvault-key-create] コマンドを実行します。

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

その後、[az acr encryption rotate-key][az-acr-encryption-rotate-key] コマンドを実行して、新しいキー ID と、前に構成したマネージド ID のプリンシパル ID を渡します。

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>キーを取り消す

カスタマー マネージド暗号化キーを取り消すには、キー コンテナーのアクセス ポリシーを変更するか、キーを削除します。 たとえば、レジストリによって使用されるマネージ ID のアクセス ポリシーを変更するには、[az keyvault delete-policy][az-keyvault-delete-policy] コマンドを使用します。 次に例を示します。

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

キーを取り消すと、レジストリは暗号化キーにアクセスできないため、すべてのレジストリ データへのアクセスが実質的にブロックされます。 キーへのアクセスを有効にするか、削除したキーを復元すると、レジストリによってキーが取得されるので、暗号化されたレジストリ データに再びアクセスできるようになります。

## <a name="next-steps"></a>次のステップ

* [Azure での保存時の暗号化](../security/fundamentals/encryption-atrest.md)についてさらに学習します。
* アクセス ポリシーと、[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/general/secure-your-key-vault.md)方法についてさらに学習します。
* Azure Container Registry でのカスタマー マネージド キーについてフィードバックを提供するには、[ACR GitHub サイト](https://aka.ms/acr/issues)にアクセスしてください。


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
