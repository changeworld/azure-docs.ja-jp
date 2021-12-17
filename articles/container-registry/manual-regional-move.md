---
title: Azure コンテナー レジストリを別のリージョンに移動する
description: Azure コンテナー レジストリの設定とデータを別の Azure リージョンに手動で移動します。
ms.topic: article
ms.date: 06/08/2021
ms.openlocfilehash: e2bc00287923a95e2e4d3698b22c4c2ca65bebc6
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835891"
---
# <a name="manually-move-a-container-registry-to-another-region"></a>コンテナー レジストリを別のリージョンに手動で移動する

Azure リージョン間での Azure コンテナー レジストリの移動が必要になる場合があります。 たとえば、別のリージョン内で開発パイプラインを実行するか、または新しいデプロイ ターゲットをホストして、近くのレジストリを提供したい場合があります。

現在、[Azure Resource Mover](../resource-mover/overview.md) で Azure コンテナー レジストリの移動を自動化することはできませんが、コンテナー レジストリを別のリージョンに手動で移動することは可能です。

* レジストリ設定を Resource Manager テンプレートにエクスポートする
* テンプレートを使用して別の Azure リージョン内でレジストリをデプロイする
* ソース レジストリからターゲット レジストリにレジストリ コンテンツをインポートする

[!INCLUDE [container-registry-geo-replication-include](../../includes/container-registry-geo-replication-include.md)]

## <a name="prerequisites"></a>前提条件

Azure CLI

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="considerations"></a>考慮事項

* この記事の手順を使用して、レジストリを、同じサブスクリプション内の別のリージョンに移動します。 レジストリを別の Azure サブスクリプションまたは同じ Active Directory テナントに移動するには、さらに構成が必要な場合があります。
* Resource Manager テンプレートをエクスポートして使用すると、多くのレジストリ設定を再作成するのに役立ちます。 テンプレートを編集してさらに設定を構成したり、作成後にターゲット レジストリを更新したりできます。
* 現在、Azure Container Registry によって、異なる Active Directory テナントへのレジストリの移動はサポートされていません。 この制限は、[カスタマー マネージド キー](container-registry-customer-managed-keys.md)で暗号化されたレジストリと暗号化されていないレジストリの両方に適用されます。
* レジストリを移動できない場合は、この記事で説明されているように、新しいレジストリを作成し、設定を手動で作成し直して、[ターゲット レジストリ にレジストリ コンテンツをインポート](#import-registry-content-in-target-registry)します。

## <a name="export-template-from-source-registry"></a>ソース レジストリからテンプレートをエクスポートする 

Azure portal、Azure CLI、Azure PowerShell などの Azure ツールを使用して、Resource Manager テンプレートをエクスポートします。 Azure ポータルを使用するには。

1. [Azure portal](https://portal.azure.com) で、お使いのソース レジストリに移動します。
1. メニューの **[オートメーション]** で、 **[テンプレートのエクスポート]**  >  **[ダウンロード]** を選択します。

    :::image type="content" source="media/manual-regional-move/export-template.png" alt-text="コンテナー レジストリのテンプレートをエクスポートする":::

## <a name="redeploy-target-registry-in-new-region"></a>新しいリージョン内でターゲット レジストリを再デプロイする

### <a name="modify-template"></a>テンプレートを変更する

ダウンロードしたテンプレート JSON ファイル内のレジストリ プロパティを調べ、必要な変更を行います。 少なくとも以下を実行します。

* レジストリ名の `defaultValue` をターゲット レジストリの目的の名前に変更する
* `location` をターゲット レジストリの目的の Azure リージョンに更新する

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "registries_myregistry_name": {
            "defaultValue": "myregistry",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('myregistry_name')]",
            "location": "centralus",
[...]
```

詳細については、「[Azure portal からエクスポートしたテンプレートを使用する](../azure-resource-manager/templates/template-tutorial-export-template.md)」および[テンプレート リファレンス](/azure/templates/microsoft.containerregistry/registries)に関するページをご覧ください。

> [!IMPORTANT]
> カスタマー マネージド キーを使用してターゲット レジストリを暗号化する場合は、必要なマネージド ID、キー コンテナー、キーの設定を使用して、必ずテンプレートを更新してください。 カスタマー マネージド キーは、レジストリをデプロイするときにのみ有効にできます。
> 
> 詳細については、[カスタマー マネージド キーを使用したレジストリの暗号化](./container-registry-customer-managed-keys.md#enable-customer-managed-key---template)に関するページをご覧ください。

### <a name="create-resource-group"></a>リソース グループの作成 

[az group create](/cli/azure/group#az_group_create) コマンドを使用して、ターゲット レジストリのリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。 

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="deploy-target-registry-in-new-region"></a>新しいリージョン内でターゲット レジストリをデプロイする

テンプレートを使用してターゲット レジストリをデプロイするには、[az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) コマンドを使用します。

```azurecli
az deployment group --resource-group myResourceGroup \
   --template-file template.json --name mydeployment
```

> [!NOTE]
> デプロイ中にエラーが発生した場合は、テンプレート ファイル内での特定の構成の更新と、コマンドの再試行が必要になる可能性があります。

## <a name="import-registry-content-in-target-registry"></a>ターゲット レジストリにレジストリ コンテンツをインポートする

ターゲット リージョン内でレジストリを作成したら、[az acr import](/cli/azure/acr#az_acr_import) コマンドまたは同等の PowerShell コマンド `Import-AzContainerImage` を使用して、保持するイメージやその他の成果物をソース レジストリからターゲット レジストリにインポートします。 コマンドの例については、「[コンテナー レジストリにコンテナー イメージをインポートする](container-registry-import-images.md)」を参照してください。

* Azure CLI コマンド [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list) および [az acr repository tags](/cli/azure/acr/repository#az_acr_repository_show_tags)、または Azure PowerShell の同等のコマンドを使用すると、ご自身のソース レジストリのコンテンツを列挙できます。
* 個々の成果物に対してインポート コマンドを実行するか、成果物の一覧に対してコマンドが実行されるようにスクリプトを作成します。

次のサンプル Azure CLI スクリプトでは、ソース リポジトリとタグが列挙されて、成果物が同じ Azure サブスクリプションのターゲット レジストリにインポートされます。 必要に応じて、特定のリポジトリまたはタグをインポートします。 異なるサブスクリプションまたはテナント内のレジストリからインポートするには、「[コンテナー レジストリにコンテナー イメージをインポートする](container-registry-import-images.md)」の例を参照してください。

```azurecli
#!/bin/bash
# Modify registry names for your environment
SOURCE_REG=myregistry
TARGET_REG=targetregistry

# Get list of source repositories
REPO_LIST=$(az acr repository list \
    --name $SOURCE_REG --output tsv)

# Enumerate tags and import to target registry
for repo in $REPO_LIST; do
    TAGS_LIST=$(az acr repository show-tags --name $SOURCE_REG --repository $repo --output tsv);
    for tag in $TAGS_LIST; do
        echo "Importing $repo:$tag";
        az acr import --name $TARGET_REG --source $SOURCE_REG.azurecr.io/$repo":"$tag;
    done
done
```



## <a name="verify-target-registry"></a>ターゲット レジストリを確認する

お使いのターゲット レジストリ内で、次の情報を確認します。

* レジストリ名、サービス レベル、パブリック アクセス、レプリケーションなどのレジストリ設定
* 保持するコンテンツのリポジトリとタグ。


### <a name="additional-configuration"></a>追加構成

* 必要に応じて、プライベート エンドポイント、IP アクセス規則、マネージド ID など、ターゲット レジストリ内の設定を手動で構成します。

* ソース レジストリではなくターゲット レジストリを使用するように、開発システムとデプロイ システムを更新します。

* ターゲット レジストリへのアクセスを許可するように、すべてのクライアント ファイアウォール規則を更新します。

## <a name="delete-original-registry"></a>元のレジストリを削除する

ターゲット レジストリ、移行されたコンテンツ、検証済みレジストリ設定を適切にデプロイしたら、ソース レジストリを削除することができます。

## <a name="next-steps"></a>次のステップ

* パブリック レジストリや別のプライベート レジストリから Azure コンテナー レジストリへの[コンテナー イメージのインポート](container-registry-import-images.md)について学習する。 
* Azure Container Registry の [Resource Manager テンプレートのリファレンス](/azure/templates/microsoft.containerregistry/registries)を確認する。