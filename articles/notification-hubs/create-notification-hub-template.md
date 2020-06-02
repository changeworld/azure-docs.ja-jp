---
title: Azure Resource Manager テンプレートを使用して Azure 通知ハブを作成する
description: Azure Resource Manager テンプレートを使用して Azure 通知ハブを作成する方法を説明します。
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sethm
ms.date: 05/15/2020
ms.reviewer: thsomasu
ms.lastreviewed: 05/15/2020
ms.openlocfilehash: aefccb831fe35898962893a173c5bd1125877def
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743529"
---
# <a name="quickstart-create-a-notification-hub-using-an-azure-resource-manager-template"></a>クイックスタート: Azure Resource Manager テンプレートを使用して通知ハブを作成する

Azure Notification Hubs は、すべてのバックエンド (クラウドまたはオンプレミス) からすべてのプラットフォーム (iOS、Android、Windows、Kindle など) に通知を送信できる、使いやすいスケールアウトされたプッシュ エンジンです。 このサービスの詳細については、「[Azure Notification Hubs とは](notification-hubs-push-notification-overview.md)」を参照してください。

このクイックスタートでは、Azure Resource Manager テンプレートを使用して、Azure Notification Hubs 名前空間と、その名前空間内に "MyHub" という名前の通知ハブを作成します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

[なし] :

## <a name="create-a-notification-hubs-namespace-and-hub"></a>Notification Hubs 名前空間とハブを作成する

<!-- The second H2 must start with "Create a". For example,  'Create a Key Vault', 'Create a virtual machine', etc. -->

### <a name="review-the-template"></a>テンプレートを確認する

このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-notification-hub/)からのものです。

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json" range="1-45" highlight="22-40":::

* [Microsoft.NotificationHubs/namespaces](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces)
* [Microsoft.NotificationHubs/namespaces/notificationHubs](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートでは、Notification Hubs 名前空間名をパラメーターとして受け取ります。 次いで、このテンプレートではその名前の名前空間と、その名前空間内に **MyHub** という名前の通知ハブを作成します。

[![Azure へのデプロイ](./media/create-notification-hub-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

デプロイされたリソースは、Azure portal を使用して確認するか、Azure CLI または Azure PowerShell スクリプトを使用してデプロイされた Notification Hubs 名前空間とハブを一覧表示することができます。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
':::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::': null
---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、リソース グループを削除します。これにより、リソース グループ内のリソースが削除されます。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>次のステップ

テンプレートの作成手順について説明したチュートリアルについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [チュートリアル:初めての Azure Resource Manager テンプレートを作成およびデプロイする](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
