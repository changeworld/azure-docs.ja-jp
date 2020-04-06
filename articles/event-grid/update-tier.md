---
title: Azure Event Grid トピックまたはドメインの価格レベルを更新する
description: この記事では、Azure portal、Azure CLI、および Azure PowerShell を使用して、Azure Event Grid トピックまたはドメインの価格レベルを更新する方法 (Basic から Premium、Premium から Basic) について説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300594"
---
# <a name="update-pricing-tier"></a>価格レベルの更新 
この記事では、Azure portal、Azure CLI、および Azure PowerShell を使用して、Azure Event Grid トピックまたはドメインの価格レベルを更新する方法について説明します。 

## <a name="use-azure-portal"></a>Azure Portal の使用
このセクションでは、Azure portal でトピックまたはドメインの価格レベルを変更する方法について説明します。 

### <a name="overview-page"></a>[概要] ページ
トピックまたはドメインの価格レベルは、 **[概要]** ページで変更できます。 次の例では、Basic レベルから Premium レベルにトピックをアップグレードする方法を示します。 Premium レベルから Basic レベルにダウングレードする手順もほぼ同じです。

1. [Azure portal](https://portal.azure.com) で、トピックまたはドメインのページに移動します。 
2. **[概要]** ページで、現在の価格レベルを選択します (次の例では **[Basic]** です)。
    
    ![現在の価格レベルの選択](./media/update-tier/select-tier.png)
3. **[価格レベル]** ページでレベルを変更し、 **[OK]** を選択します。 

    ![価格レベルの変更](./media/update-tier/change-tier.png)
4. **[通知]** ページで操作の状態を確認します。

    ![更新の状態](./media/update-tier/status.png)    
5. **[概要]** ページで、更新されたレベルが表示されていることを確認します。 

    ![更新の状態](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>[ネットワーク] ページ
**[ネットワーク]** ページでは、Basic レベルから Premium レベルに**アップグレード**できます。 ただし、このページでは Premium レベルから Basic レベルにダウングレードすることはできません。 

1. [Azure portal](https://portal.azure.com) で、トピックまたはドメインのページに移動します。 
2. **[ネットワーク]** ページで、 **[プライベート エンドポイント接続 (プレビュー)]** タブに切り替えます。 
3. 現在の価格レベルが **Basic** である場合は、次のメッセージが表示されます。 それを選択します。 

    ![プライベート エンドポイント接続のページでのレベルの更新](./media/update-tier/private-endpoint-connections-page.png)
4. **[Update to premium pricing tier]\(Premium 価格レベルに更新\)** ページで、 **[はい]** を選択します。 
    
    ![アップグレードの確認](./media/update-tier/message-private-endpoint-connection.png)
5. **[通知]** ページで操作の状態を確認します。

    ![更新の状態](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Azure CLI の使用
このセクションでは、Azure CLI コマンドを使用して、トピックまたはドメインの価格レベルを変更する方法について説明します。 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>トピックを Basic から Premium にアップグレードする

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>トピックを Premium から Basic にダウングレードする

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>ドメインを Basic から Premium にアップグレードする

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>ドメインを Premium から Basic にダウングレードする

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Azure PowerShell の使用
このセクションでは、PowerShell コマンドを使用して、トピックまたはドメインの価格レベルを変更する方法について説明します。 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>REST API 呼び出し用のトークンとヘッダーを準備する 
次の前提条件コマンドを実行して、REST API 呼び出しで使用する認証トークンを取得し、Authorization やその他のヘッダー情報を設定します。 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>トピックを Basic から Premium にアップグレードする

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>トピックを Premium から Basic にダウングレードする

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>ドメインを Basic から Premium にアップグレードする

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>ドメインを Premium から Basic にダウングレードする

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>次のステップ
Premium レベルのトピックとドメインでは、選択した仮想ネットワークからのアクセスのみに制限するようにプライベート エンドポイントを構成できます。 詳細な手順については、[プライベート エンドポイントの構成](configure-private-endpoints.md)に関する記事を参照してください。
