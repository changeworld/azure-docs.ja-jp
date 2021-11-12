---
title: Azure Resource Manager テンプレートを使用して Azure DevTest Labs のラボを作成する
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure DevTest Labs のラボを作成します。 ラボの管理者は、ラボを設定し、ラボに VM を作成して、ポリシーを構成します。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/15/2021
ms.openlocfilehash: 0cdc7db0f29a09040b7d40507cfad840b75f0c91
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246958"
---
# <a name="quickstart-use-an-arm-template-to-create-a-lab-in-devtest-labs"></a>クイックスタート: ARM テンプレートを使用して DevTest Labs でラボを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure DevTest Labs のラボを作成します。 ラボには、Windows Server 2019 Datacenter 仮想マシン (VM) が含まれています。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

このクイックスタートでは、以下の操作を実行します。

> [!div class="checklist"]
> * テンプレートを確認します。
> * テンプレートをデプロイします。
> * デプロイを検証する。
> * リソースをクリーンアップする。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートでは、「[Windows Server VM を使用して Azure DevTest Labs でラボを作成する](https://azure.microsoft.com/resources/templates/dtl-create-lab-windows-vm/)」テンプレートを使用します。 このテンプレートでは、次のリソースを定義します。

- [Microsoft.DevTestLab/labs](/azure/templates/microsoft.devtestlab/labs)
- [Microsoft.DevTestLab labs/virtualnetworks](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [Microsoft.DevTestLab labs/virtualmachines](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.devtestlab/dtl-create-lab-windows-vm/azuredeploy.json":::

ARM テンプレートのその他のサンプルについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. ARM テンプレートを使用するには、次の **[Azure へのデプロイ]** ボタンを選択します。 テンプレートによって、Azure portal でラボ作成画面が開かれます。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm%2Fazuredeploy.json)

1. 「**Windows Server VM を使用して Azure DevTest Labs でラボを作成する**」テンプレートで、次の項目を入力します。

   - **リソース グループ**: 後でクリーンアップしやすいように新しいリソース グループを作成します。
   - **リージョン**: リソース グループの場所を選択します。
   - **ラボ名**: 新しいラボ インスタンスの名前を入力します。
   - **VM 名**: 作成する VM の名前を入力します。 
   - **ユーザー名**: VM にアクセスできるユーザーの名前を入力します。 
   - **パスワード**: ユーザーのパスワードを入力します。

1. **[確認および作成]** を選択し、検証に成功したら **[作成]** を選択します。

   :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="ラボの作成ページのスクリーンショット。":::

## <a name="validate-the-deployment"></a>デプロイの検証
1. デプロイ時に、画面の上部にある **[通知]** を選択し、 **[デプロイは進行中です]** を選択して、デプロイの **[概要]** ページを表示することができます。 デプロイ (特に VM の作成) には時間がかかります。

   :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="[デプロイは進行中です] を示すスクリーンショット。":::

1. デプロイが完了したら、 **[リソース グループに移動]** を選択して、自分のラボの [リソース グループ] ページに移動します。

   :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="デプロイの完了と [リソース グループに移動] ボタンを示すスクリーンショット。":::

1. **[リソース グループ]** ページには、ラボとその依存リソース (仮想ネットワークや VM など) を含む、リソース グループ内のリソースが一覧表示されます。 自分のラボの **[概要]** ページに進むには、自分の **DevTest Lab** リソースを選択します。

1. 自分のラボの **[概要]** ページで、 **[要求可能な仮想マシン]** を選択します。

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="[要求可能な仮想マシン] リンクが表示された、ラボの [概要] ページを示すスクリーンショット。":::

1. **[要求可能な仮想マシン]** ページで、VM の横にある **[その他のオプション]** の省略記号を選択し、 **[マシンの要求]** を選択します。

   :::image type="content" source="./media/create-lab-windows-vm-template/claim-vm.png" alt-text="[マシンの要求] オプションが表示された、[要求可能な仮想マシン] ページを示すスクリーンショット。":::

1. ラボの **[概要]** ページで、 **[自分の仮想マシン]** の下に **[起動中]** 状態で VM が表示されていることを確認します。 状態が **[実行中]** に変わるのを待ちます。

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-vm.png" alt-text="VM 一覧付きのラボの [概要] ページを示すスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クイックスタートのリソースを削除して、Azure でラボと VM を実行するための課金が生じないようにします。 ラボ内の VM にアクセスするために次のチュートリアルを実行する場合は、そのチュートリアルを完了した後にリソースをクリーンアップできます。

ラボが含まれているリソース グループを削除することはできません。 まず、リソース グループを削除できるようにラボを削除します。

1. ラボのホーム ページで、ツール バーの **[削除]** を選択します。

   :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="ラボのホーム ページの [削除] ボタンを示すスクリーンショット。":::

1. 確認ページでラボ名を入力し、 **[削除]** を選択します。

1. 削除中、画面の上部にある **[通知]** を選択すると進行状況を表示できます。 ラボの削除にはしばらく時間がかかります。

1. ラボが削除されたら、ラボのホーム ページで **[リソース グループ]** を選択します。 ラボのホーム ページが使用できなくなった場合は、Azure 検索ボックスで **リソース グループ** を検索し、自分のラボが含まれているリソース グループを選択します。

   :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="通知の削除の確認を示すスクリーンショット。":::

1. **[リソース グループ]** ページで、ツール バーの **[リソース グループの削除]** を選択します。 確認ページでリソース グループ名を入力し、 **[削除]** を選択します。 通知をチェックして、リソース グループが削除されたことを確認できます。
 
   :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="[リソース グループの削除] ボタンが表示されているスクリーンショット。":::

## <a name="next-steps"></a>次のステップ
このクイックスタートでは、VM を備えたラボを作成しました。 ラボと VM にアクセスする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: ラボにアクセスする](tutorial-use-custom-lab.md)
