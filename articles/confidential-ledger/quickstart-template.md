---
title: Azure Resource Manager テンプレートを使用して Microsoft Azure Confidential Ledger を作成する
description: Azure Resource Manager テンプレートを使用して、Microsoft Azure Confidential Ledger を作成する方法を学習します。
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 04/15/2021
ms.openlocfilehash: 0b393c39940e0912a2497b6efba9ca920ce71fe7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438837"
---
# <a name="quickstart-create-an-microsoft-azure-confidential-ledger-with-an-arm-template"></a>クイック スタート: ARM テンプレートを使用して Microsoft Azure Confidential Ledger を作成する

[Microsoft Azure Confidential Ledger](overview.md) は、機密データ レコードを管理するための、安全性の高い新しいサービスです。 このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して新しい台帳を作成する方法を説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.confidentialledger%2Fconfidential-ledger-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

### <a name="azure-subscription"></a>Azure サブスクリプション

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

### <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

[!INCLUDE [Register the microsoft.ConfidentialLedger resource provider](../../includes/confidential-ledger-register-rp.md)]

### <a name="obtain-your-principal-id"></a>プリンシパル ID の取得

テンプレートにはプリンシパル ID が必要です。 プリンシパル ID を取得するには、`--show-mine` フラグを付けて Azure CLI [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) コマンドを実行します。

```azurecli-interactive
az ad sp list --show-mine -o table
```

プリンシパル ID が "ObjectId" 列に表示されます。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates)からのものです。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.confidentialledger/confidential-ledger-create/azuredeploy.json":::

テンプレート内に定義されている Azure リソース:

- Microsoft.ConfidentialLedger/ledgers

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次の画像を選択します。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.confidentialledger%2Fconfidential-ledger-create%2Fazuredeploy.json)

1. 次の値を選択または入力します。

    指定がない場合は、既定値を使用して Confidential Ledger を作成します。

    - **台帳名**: 台帳の名前を選択します。 台帳名はグローバルに一意である必要があります。
    - **[場所]** :場所を選択します。 たとえば、**East US** などとします。
    - **PrincipalId**: 上記の「[前提条件](#obtain-your-principal-id)」セクションでメモしたプリンシパル ID を指定します。

1. **[購入]** を選択します。 Confidential Ledger リソースが正常にデプロイされると、通知を受け取ります。

テンプレートをデプロイするには Azure portal を使用します。 Azure portal だけでなく、Azure PowerShell、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用して台帳リソースをチェックできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Confidential Ledger に関するその他の記事は、このクイックスタートに基づいている場合があります。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。

不要になった場合、リソース グループを削除すれば、台帳リソースも削除されます。 Azure CLI または Azure PowerShell を使用してリソース グループを削除するには、次を実行します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用して Confidential Ledger リソースを作成し、デプロイを検証しました。 サービスの詳細については、「[Microsoft Azure Confidential Ledger の概要](overview.md)」を参照してください。