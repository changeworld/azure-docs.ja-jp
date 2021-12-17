---
title: 成果物のエクスポートを無効にする
description: Premium Azure コンテナー レジストリからデータが流出しないように、レジストリのプロパティを設定します。
ms.topic: how-to
ms.date: 07/27/2021
ms.openlocfilehash: 8fa32197069376c71c035df0285852f4041efc64
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778836"
---
# <a name="disable-export-of-artifacts-from-an-azure-container-registry"></a>Azure コンテナー レジストリからの成果物のエクスポートを無効にする 

組織内のレジストリ ユーザーが故意に、または誤って仮想ネットワークの外部に成果物をリークするのを防ぐには、エクスポートを無効にするようにレジストリの "*エクスポート ポリシー*" を構成できます。

エクスポート ポリシーは、Premium コンテナー レジストリ用に API バージョン **2021-06-01-preview** で導入されたプロパティです。 `exportPolicy` プロパティの状態が `disabled` に設定されていると、ユーザーが次の操作を試みたとき、ネットワーク制限付きレジストリからの成果物のエクスポートがブロックされます。

* レジストリの成果物を別の Azure コンテナー レジストリに[インポートする](container-registry-import-images.md)
* 別のコンテナーレジストリに成果物を転送するレジストリ [エクスポート パイプライン](container-registry-transfer-images.md)を作成する

> [!NOTE]
> 成果物のエクスポートを無効にしても、許可されているユーザーが仮想ネットワーク内のレジストリにアクセスして、成果物をプルしたり、他のデータ プレーン操作を実行したりすることは妨げられません。 この使用を監査するため、レジストリ操作を[監視](monitor-service.md)するよう診断設定を構成することをお勧めします。 

## <a name="prerequisites"></a>前提条件

* [プライベート エンドポイント](container-registry-private-link.md)を使用して構成された Premium コンテナー レジストリ。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="other-requirements-to-disable-exports"></a>エクスポートを無効にするためのその他の要件

* **パブリック ネットワーク アクセスを無効にする** - 成果物のエクスポートを無効にするには、レジストリへのパブリック アクセスも無効にする必要があります (レジストリの `publicNetworkAccess` プロパティを `disabled` に設定する必要があります)。 エクスポートを無効にする前に、またはそれを無効にするのと同時に、レジストリへのパブリック ネットワーク アクセスを無効にすることができます。

    レジストリのパブリック エンドポイントへのアクセスを無効にすることで、レジストリ操作が仮想ネットワーク内でのみ許可されるようになります。 成果物をプルしたり、他の操作を実行したりするためのレジストリへのパブリック アクセスは禁止されます。 

*  **エクスポート パイプラインを削除する** - レジストリの `exportPolicy` の状態を `disabled` に設定する前に、レジストリに構成されている既存のエクスポート パイプラインを削除します。 パイプラインが構成されていると、`exportPolicy` の状態を変更できません。

## <a name="disable-exportpolicy-for-an-existing-registry"></a>既存のレジストリの exportPolicy を無効にする

レジストリを作成すると、`exportPolicy` の状態は既定で `enabled` に設定され、アーティファクトをエクスポートできるようになります。 ARM テンプレートまたは `az resource update` コマンドを使用して、状態を `disabled` に更新できます。

### <a name="arm-template"></a>ARM テンプレート 

`exportPolicy` の状態を更新し、`publicNetworkAccess` プロパティを `disabled` に設定するには、次の JSON を追加します。 詳細については、[ARM テンプレートでのリソースのデプロイ](../azure-resource-manager/templates/deploy-cli.md)に関する記事を参照してください。

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
      "publicNetworkAccess": "disabled",
      "policies": {
        "exportPolicy": {
          "status": "disabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

既存のレジストリで `exportPolicy` の状態を `disabled` に設定するには、[az resource update](/cli/azure/resource/#az_resource_update) を実行します。 レジストリとリソース グループの名前を置き換えてください。

この例で示すように、`exportPolicy` プロパティを無効にするときは、`publicNetworkAccess` プロパティも `disabled` に設定します。

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=disabled" \
    --set "properties.publicNetworkAccess=disabled"  
```

出力では、エクスポート ポリシーの状態が無効であることが示されています。

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "identity": null,
  "kind": null,
  "location": "centralus",
  "managedBy": null,
  "name": "myregistry",
  "plan": null,
  "properties": {
    [...]
    "policies": {
      "exportPolicy": {
        "status": "disabled"
      },
      "quarantinePolicy": {
        "status": "disabled"
      },
      "retentionPolicy": {
        "days": 7,
        "lastUpdatedTime": "2021-07-20T23:20:30.9985256+00:00",
        "status": "disabled"
      },
      "trustPolicy": {
        "status": "disabled",
        "type": "Notary"
      },
    "privateEndpointConnections": [],
    "provisioningState": "Succeeded",
    "publicNetworkAccess": "Disabled",
    "zoneRedundancy": "Disabled"
[...]
}
```

## <a name="enable-exportpolicy"></a>exportPolicy を有効にする 

レジストリで `exportPolicy` の状態を無効にした後は、ARM テンプレートまたは `az resource update` コマンドを使用して、いつでも再び有効にすることができます。

### <a name="arm-template"></a>ARM テンプレート 

`exportPolicy` の状態を `enabled` に更新するには、次の JSON を追加します。 詳細については、[ARM テンプレートでのリソースのデプロイ](../azure-resource-manager/templates/deploy-cli.md)に関する記事を参照してください

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
     "policies": {
        "exportPolicy": {
          "status": "enabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

`exportPolicy` の状態を `enabled` に設定するには、[az resource update](/cli/azure/resource/#az_resource_update) を実行します。 レジストリとリソース グループの名前を置き換えてください。

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=enabled"
```
 
## <a name="next-steps"></a>次のステップ

* [Azure Container Registry のロールとアクセス許可](container-registry-roles.md)について確認します。
* レジストリ成果物が誤って削除されないようにするには、[コンテナー イメージのロック](container-registry-image-lock.md)に関する記事を参照してください。
* Azure コンテナー レジストリをセキュリティで保護するには、組み込みの [Azure ポリシー](container-registry-azure-policy.md)について確認してください
