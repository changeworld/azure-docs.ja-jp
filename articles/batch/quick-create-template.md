---
title: Azure クイック スタート - Batch アカウントの作成 - Azure Resource Manager テンプレート
description: このクイックスタートでは、ARM テンプレートを使用して Batch アカウントを作成する方法を示します。
ms.date: 05/25/2021
ms.topic: quickstart
ms.custom: subject-armqs, mode-arm
ms.openlocfilehash: fcc0876bc9a165b06705e4c57090b084205dd0c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086832"
---
# <a name="quickstart-create-a-batch-account-by-using-arm-template"></a>クイック スタート:ARM テンプレートを使用して Batch アカウントを作成する

Azure Batch の使用を開始するには、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、ストレージなどの Batch アカウントを作成します。 コンピューティング リソース (コンピューティング ノードのプール) や Batch ジョブを作成するには、Batch アカウントが必要です。 Azure Storage アカウントと Batch アカウントをリンクできます。これは、アプリケーションをデプロイしたり、ほとんどの実際のワークロードの入力データと出力データを格納したりする場合に役立ちます。

このクイックスタートを完了すると、Batch サービスの主要な概念を理解し、より大規模でより現実的なワークロードで Batch を試せるようになります。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.batch%2Fbatchaccount-with-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

アクティブな Azure サブスクリプションが必要です。

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/batchaccount-with-storage/)からのものです。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.batch/batchaccount-with-storage/azuredeploy.json":::

テンプレートでは、次の 2 つの Azure リソースが定義されています。

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): ストレージ アカウントを作成します。
- [Microsoft.Batch/batchAccounts](/azure/templates/microsoft.batch/batchaccounts): Batch アカウントを作成します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 テンプレートによって、Azure Batch アカウントとストレージ アカウントが作成されます。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.batch%2Fbatchaccount-with-storage%2Fazuredeploy.json)

1. 次の値を選択または入力します。

   :::image type="content" source="media/quick-create-template/batch-template.png" alt-text="Resource Manager テンプレート、Batch アカウントの作成、ポータルのデプロイ":::

   - **サブスクリプション**: Azure サブスクリプションを選択します。
   - **リソース グループ**: **[新規作成]** を選択し、リソース グループの一意の名前を入力し、 **[OK]** をクリックします。
   - **場所**: 場所を選択します。 たとえば **[米国中部]** です。
   - **Batch アカウント名**: 既定値をそのまま使用します。
   - **ストレージ アカウントの SKU**: ストレージ アカウントの種類を選択します。 たとえば、**Standard_LRS** です。
   - **[場所]** :リソースがリソース グループと同じ場所に配置されるように、既定値のままにします。

1. **[Review + create]\(確認と作成\)** を選択し、次に **[作成]** を選択します。

数分後に、Batch アカウントが正常に作成されたという通知が表示されます。

この例では、テンプレートをデプロイするために Azure portal を使用します。 Azure portal だけでなく、Azure PowerShell、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

Azure portal で作成したリソース グループに移動して、デプロイを検証できます。 **[概要]** の画面で、Batch アカウントとストレージ アカウントが存在することを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この[チュートリアル](./tutorial-parallel-dotnet.md)をさらに継続する場合は、これらのリソースをそのまま残しておくことをお勧めします。 または、不要になった場合は、[リソース グループを削除](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)できます。これにより、Batch アカウントと作成したストレージ アカウントも削除されます。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Batch アカウントとストレージ アカウントを作成しました。 Azure Batch の詳細については、Azure Batch のチュートリアルを続行してください。

> [!div class="nextstepaction"]
> [Azure Batch のチュートリアル](./tutorial-parallel-dotnet.md)
