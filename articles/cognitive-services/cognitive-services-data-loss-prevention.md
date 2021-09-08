---
title: データ損失の防止
description: お客様は、Cognitive Services のデータ損失防止機能を使用して、自分の Cognitive Services リソースがアクセスを許可されている送信 URL の一覧を構成できます。 この構成により、お客様がデータ損失を防止するための別のレベルのコントロールが作成されます。
author: gclarkmt
ms.author: gregc
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: template-concept
ms.openlocfilehash: 1d6e7d6dd94efb11f5a2ea7cc8263f7916926fbe
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475766"
---
# <a name="configure-data-loss-prevention-for-azure-cognitive-services"></a>Azure Cognitive Services のデータ損失防止を構成する

お客様は、Cognitive Services のデータ損失防止機能を使用して、自分の Cognitive Services リソースがアクセスを許可されている送信 URL の一覧を構成できます。 これにより、お客様がデータ損失を防止するための別のレベルのコントロールが作成されます。 この記事では、Cognitive Services リソースに対してデータ損失防止機能を有効にするために必要な手順について説明します。

## <a name="prerequisites"></a>前提条件

要求を実行する前に、Azure アカウントと Azure Cognitive Services サブスクリプションが必要です。 既にアカウントをお持ちの場合は、次のセクションまでスキップして進んでください。 アカウントをお持ちでない場合は、数分で設定を行えるガイドをご覧ください。[Azure の Cognitive Services アカウントの作成](cognitive-services-apis-create-account.md)に関するページを参照してください。

[アカウントの作成](https://azure.microsoft.com/free/cognitive-services/)後に、[Azure portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) からサブスクリプション キーを取得できます。

## <a name="enabling-data-loss-prevention"></a>データ損失防止の有効化

データ損失防止の有効化には 2 つの部分があります。 最初に、プロパティ restrictOutboundNetworkAccess を true に設定する必要があります。 これを true に設定するときは、承認済み URL の一覧も指定する必要があります。 その URL の一覧が、allowedFqdnList プロパティに追加されます。 allowedFqdnList プロパティには、コンマ区切りの URL の配列が格納されます。

>[!NOTE]
>
> * `allowedFqdnList` プロパティ値では最大 1000 個の URL がサポートされます。
> * このプロパティでは、IP アドレスと完全修飾ドメイン名 (例: `www.microsoft.com` 値) の両方がサポートされます。
> * 更新が有効になるまで、最大で 15 分かかる可能性があります。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) をインストールして [サインイン](/cli/azure/authenticate-azure-cli)するか、 **[使ってみる]** を選択します。

1. Cognitive Services リソースの詳細を表示します。

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
    ```

1. Cognitive Services リソースの現在のプロパティを表示します。

    ```azurecli-interactive
    az rest -m get \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
    ```

1. restrictOutboundNetworkAccess プロパティを構成し、許可されている FqdnList を承認済み URL で更新します。

    ```azurecli-interactive
    az rest -m patch \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
        -b '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
    ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps) をインストールして [サインイン](/powershell/azure/authenticate-azureps)するか、 **[使ってみる]** を選択します。

1. Cognitive Services リソースの現在のプロパティを表示します。

    ```azurepowershell-interactive
    $getParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Method = 'GET'
    }
    Invoke-AzRestMethod @getParams
    ```

1. restrictOutboundNetworkAccess プロパティを構成し、許可されている FqdnList を承認済み URL で更新します。

    ```azurepowershell-interactive
    $patchParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Payload = '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
        Method = 'PATCH'
    }
    Invoke-AzRestMethod @patchParams
    ```

---

## <a name="supported-services"></a>サポートされているサービス

データ損失防止の構成は次のサービスでサポートされています。

- Computer Vision
- Content Moderator
- Custom Vision
- Face
- Speech Service
- QnA Maker

## <a name="next-steps"></a>次のステップ

- [仮想ネットワークを構成する](cognitive-services-virtual-networks.md)
