---
title: Azure リソースを別のリソース グループに移動する | Microsoft Docs
description: リソース グループ間やサブスクリプション間で Azure リソースを移動する方法について説明します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/19/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0ae29146b1b44f3017d37b3cebf7ec4cf39115d0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731761"
---
# <a name="tutorial-move-azure-resources-to-another-resource-group-or-subscription"></a>チュートリアル:Azure リソースを別のリソース グループやサブスクリプションに移動する

Azure リソースを 1 つのリソース グループから別のリソース グループに移動する方法について説明します。 1 つの Azure サブスクリプションから別の Azure サブスクリプションに Azure リソースを移動することもできます。 このチュートリアルでは、リソース マネージャー テンプレートを使用して 2 つのリソース グループと 1 つのストレージ アカウントをデプロイします。 その後、ストレージ アカウントを 1 つのリソース グループから別のリソース グループに移動します。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * リソースを準備する。
> * リソースを移動できることを確認する。
> * リソースの移動前のチェック リスト。
> * 移動操作を検証する。
> * リソースを移動する。
> * リソースをクリーンアップする。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prepare-the-resources"></a>リソースを準備する

テンプレートは既に作成され、[共有ストレージ アカウント](https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json)に置かれています。 テンプレートでは、2 つのリソース グループと 1 つのストレージ アカウントが定義されています。 テンプレートをデプロイする際には、プロジェクト名を指定する必要があります。 プロジェクト名は、一意のリソース名を生成するために使用されます。  次の JSON は、テンプレートから抽出されたものです。

```json
"variables": {
  "resourceGroupSource": {
    "name": "[concat(parameters('projectName'), 'rg1')]",
    "location": "eastus"
  },
  "resourceGroupDestination": {
    "name": "[concat(parameters('projectName'), 'rg2')]",
    "location": "westus"      
  },
  "storageAccount": {
    "name": "[concat(parameters('projectName'), 'store')]",
    "location": "eastus"
  }
},
```

JSON で定義されている場所に注意してください。2 つのリソース グループは、米国東部と米国西部に配置されています。 ストレージ アカウントは、米国東部に配置されています。 別の場所を使ってリソースを別のリソース グループに移動した場合、その移動操作ではリソースの場所は変更されません。

**[使ってみる]** を選択して Cloud Shell を開き、Cloud Shell 内で PowerShell スクリプトを実行してください。

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
New-AzureRmDeployment `
    -Name $projectname `
    -Location "centralus" `
    -TemplateUri "https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json" `
    -projectName $projectName
```

スクリプトが正常に完了するまで待機し、その後 [Azure portal](https://portal.azure.com) を開いて、リソース グループとストレージ アカウントが意図したとおりにデプロイされていることを確認してください。

> [!NOTE]
> テンプレートでは 2 つのリソース グループが定義されているため、このデプロイはサブスクリプション レベルのデプロイと見なされます。 ポータル テンプレートのデプロイでは、サブスクリプション レベルのデプロイはサポートされていません。 そのため、このチュートリアルでは Azure PowerShell が使用されています。 Azure CLI では、サブスクリプション レベルのデプロイもサポートされています。 「[Azure サブスクリプションのリソース グループとリソースを作成する](./deploy-to-subscription.md)」をご覧ください。

## <a name="verify-the-resource-can-be-moved"></a>リソースを移動できることを確認する

すべてのリソースが移動可能というわけではありません。 このチュートリアルで使用されているリソースはストレージ アカウントなので、移動可能です。 リソースが移動可能かどうかを確認するには、「[移動可能なサービス](./resource-group-move-resources.md#services-that-can-be-moved)」をご覧ください。

## <a name="checklist-before-moving-resources"></a>リソースの移動前のチェック リスト

この手順は既に行ったので、このチュートリアルでは省略可能です。

リソースを移動する前に行うべき重要な手順がいくつかあります。 「[リソースの移動前のチェックリスト](./resource-group-move-resources.md#checklist-before-moving-resources)」をご覧ください。

## <a name="validate-the-move"></a>移動を検証する

移動の検証は既に行ったので、このチュートリアルでは省略可能です。

移動の検証操作を使用すると、実際にリソースを移動することなく、必要な移動のシナリオをテストすることができます。 この操作は、正常に移動されるかどうかを事前に確かめる目的で使用します。 詳しくは、「[移動の検証](./resource-group-move-resources.md#validate-move)」をご覧ください。

## <a name="move-the-resource"></a>リソースを移動する

ストレージ アカウントはソース リソース グループ (rg1) 内にあります。次の PowerShell スクリプトを実行して、リソースを移動先のリソース グループ (rg2) に移動します。 必ず、リソースのデプロイ時に使用したのと同じプロジェクト名を使用してください。

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
$resourceGroupSource = $projectName + "rg1"
$resourceGroupDestination = $projectName + "rg2"
$storageAccountName = $projectName + "store"

$storageAccount = Get-AzureRmResource -ResourceGroupName $resourceGroupSource -ResourceName $storageAccountName
Move-AzureRmResource -DestinationResourceGroupName $resourceGroupDestination -ResourceId $storageAccount.ResourceId
```

[Azure portal](https://portal.azure.com) を開いて、ストレージ アカウントが他のリソース グループに移動したことと、ストレージ アカウントの場所が米国東部のままになっていることを確認します。

リソースを移動する場合は、その操作の間、ソース グループとターゲット グループの両方がロックされます。 これらのリソース グループに対する書き込み操作および削除操作は、移動が完了するまでブロックされます。 このロックはリソース グループでリソースを追加、更新、削除できなくなることを意味しますが、リソースが停止されるわけではありません。 たとえば、SQL Server とそのデータベースを新しいリソース グループに移動する場合、そのデータベースを使用するアプリケーションにダウンタイムは発生しません。 これまでどおり、データベースの読み取りと書き込みを行うことができます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. 移動元のリソース グループ名を選択します。  
4. トップ メニューから **[リソース グループの削除]** を選択します。
5. 移動先のリソース グループ名を選択します。  
6. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、あるリソース グループから別のリソース グループにストレージ アカウントを移動する方法について説明しました。 ここまでは、1 つのストレージアカウントや複数のストレージ アカウント インスタンスを操作しました。 次のチュートリアルでは、複数のリソースと複数のリソース タイプを含むテンプレートを作成します。 一部のリソースには依存リソースがあります。

> [!div class="nextstepaction"]
> [依存リソースの作成](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
