---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 131b21ea7bc47df9654dd7c163eb22adb68e6678
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185317"
---
## <a name="create-a-spatial-anchors-resource"></a>Spatial Anchors リソースを作成する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

<a href="https://portal.azure.com" target="_blank">Azure ポータル</a>にアクセスします。

左側のウィンドウで、 **[リソースの作成]** を選択します。

検索ボックスを使用して、「**Spatial Anchors**」を検索します。

![「Spatial Anchors」の検索結果を示すスクリーンショット。](./media/spatial-anchors-get-started-create-resource/portal-search.png)

**[Spatial Anchors]** を選択し、 **[作成]** を選択します。

**[Spatial Anchors アカウント]** ウィンドウで次を行います。

* 通常の英数字を使用して一意のリソース名を入力します。  
* リソースをアタッチするサブスクリプションを選択します。  
* **[新規作成]** を選択して、リソース グループを作成します。 「**myResourceGroup**」と名前を付け、 **[OK]** を選択します。  

  [!INCLUDE [resource group intro text](resource-group.md)]
  
* リソースを配置する場所 (リージョン) を選択します。  
* **[新規]** を選択して、リソースの作成を開始します。

![リソースを作成するための [Spatial Anchors] ウィンドウのスクリーンショット。](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

リソースが作成されると、Azure portal に、デプロイが完了したことが表示されます。 
   
![リソースのデプロイが完了したことを示すスクリーンショット。](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

**[リソースに移動]** を選択します。 これでリソースのプロパティを表示できます。 
   
リソースの **[アカウント ID]** 値は後で使用するためにテキスト エディターにコピーしておきます。

![[リソース プロパティ] ウィンドウのスクリーンショット。](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

また、リソースの **[アカウント ドメイン]** 値を後で使用するためにテキスト エディターにコピーします。

![リソースのアカウント ドメイン値を示すスクリーンショット。](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

**[設定]** で **[キー]** を選択します。 **[主キー]** 値の **[アカウント キー]** を後で使用するためにテキスト エディターにコピーします。

![アカウントの [キー] ウィンドウのスクリーンショット。](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. サインインしたら、[az account set](/cli/azure/account#az_account_set) コマンドを使用して、Spatial Anchors アカウントを設定するサブスクリプションを選択します。

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. [az group create](/cli/azure/group#az_group_create) コマンドを実行してリソース グループを作成するか、既存のリソース グループを使用します。

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   リソース グループの現在の Spatial Anchors アカウントを表示するには、[az spatial-anchors-account list](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_list) コマンドを使用します。

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   また、ご利用のサブスクリプションの Spatial Anchors アカウントを表示することもできます。

   ```azurecli
   az spatial-anchors-account list
   ```

1. Spatial Anchors アカウントを作成するには、[az spatial-anchors-account create](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_create) コマンドを実行します。

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. [az spatial-anchors-account show](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_show) コマンドを使用して、リソースのプロパティを表示します。

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   リソースの **[アカウント ID]** 値とリソースの **[アカウント ドメイン]** 値を後で使用するためにテキスト エディターにコピーします。

1. [az spatial-anchors-account key show](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_show) コマンドを実行して、主キーとセカンダリ キーを取得します。

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   後で使用するためにキー値をテキスト エディターにコピーします。

   キーを再生成する必要がある場合は、[az spatial-anchors-account key renew](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_renew) コマンドを使用します。

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

[az spatial-anchors-account delete](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_delete) コマンドを使用すると、アカウントを削除できます。

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

---
