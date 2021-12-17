---
title: Azure Resource Manager テンプレートを使用して Azure DevTest Labs のラボを作成する
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure DevTest Labs のラボを作成します。 ラボの管理者は、ラボを設定し、ラボに VM を作成して、ポリシーを構成します。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/29/2021
ms.openlocfilehash: 7d6a1390c935d68b978146d9eb06e8d2681e50c2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847532"
---
# <a name="quickstart-use-an-arm-template-to-create-a-lab-in-devtest-labs"></a>クイックスタート: ARM テンプレートを使用して DevTest Labs でラボを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure DevTest Labs のラボを作成する方法を説明します。 ラボには、Windows Server 2019 Datacenter 仮想マシン (VM) が含まれています。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm-claimed%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab)からのものです。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.devtestlab/dtl-create-lab-windows-vm-claimed/azuredeploy.json":::

テンプレートには、次の 3 つの Azure リソースが定義されています。

- [Microsoft.DevTestLab/labs](/azure/templates/microsoft.devtestlab/labs): DevTest Labs のラボを作成します。
- [Microsoft.DevTestLab labs/virtualnetworks](/azure/templates/microsoft.devtestlab/labs/virtualnetworks): DevTest Labs の仮想ネットワークを作成します。
- [Microsoft.DevTestLab labs/virtualmachines](/azure/templates/microsoft.devtestlab/labs/virtualmachines): DevTest Labs の仮想マシンを作成します。

ARM テンプレートのその他のサンプルについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. 下の **[Azure に配置する]** ボタンを選択して Azure にサインインし、ARM テンプレートを開きます。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm-claimed%2Fazuredeploy.json)

1. 次の値を入力または選択します。

    |プロパティ | 説明 |
    |---|---|
    |サブスクリプション| ドロップダウン リストから、ラボに使用する Azure サブスクリプションを選択します。|
    |Resource group| ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** を選択します。|
    |リージョン | この値には、リソース グループに使用される場所が自動入力されます。|
    |ラボ名| サブスクリプションの一意のラボ名を入力します。|
    |場所| 現状のままにします。 |
    |VM 名| サブスクリプションの一意の VM 名を入力します。|
    |VM サイズ | 現状のままにします。 |
    |[ユーザー名] | VM のユーザー名を入力します。|
    |Password| 8 から 123 文字の長さのパスワードを入力します。|

   :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="ラボの作成ページのスクリーンショット。":::

1. **[確認および作成]** を選択し、検証に成功したら **[作成]** を選択します。 その後、デプロイの **[概要]** ページが表示され、そちらで進行状況を監視できます。 デプロイにかかる時間は、選択したハードウェア、基本イメージ、成果物によって異なります。 このテンプレートで使用する構成のデプロイ時間は約 12 分です。

## <a name="validate-the-deployment"></a>デプロイの検証

1. デプロイが完了したら、テンプレートの **[概要]** ページまたは **[通知]** で **[リソース グループに移動]** を選択します。

   :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="デプロイの完了と [リソース グループに移動] ボタンを示すスクリーンショット。":::

1. **[リソース グループ]** ページには、ラボとその依存リソース (仮想ネットワークや VM など) を含む、リソース グループ内のリソースが一覧表示されます。 自分のラボの **[概要]** ページに進むには、自分の **DevTest Lab** リソースを選択します。

   :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-overview.png" alt-text="リソース グループの概要のスクリーンショット。":::

1. ラボの **[概要]** ページの **[自分の仮想マシン]** セクションに自分の仮想マシンが表示されます。

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="ラボの [概要] ページに仮想マシンが表示されているスクリーンショット。":::

1. 前の手順に戻り、サブスクリプション用のリソース グループを一覧表示します。 デプロイによって VM を保持する新しいリソース グループが作成されたことを確認します。 ラボ名 + VM 名 + ランダムな数値という構文です。 この記事で使用されている値に基づいて、自動生成された名前は `MyOtherLab-myVM-173385` です。

   :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-list.png" alt-text="リソース グループの一覧のスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースを削除して、Azure でラボと VM を実行するための課金が生じないようにします。 ラボ内の VM にアクセスするために次のチュートリアルを実行する場合は、そのチュートリアルを完了した後にリソースをクリーンアップできます。 それ以外の場合は、次の手順に従います。 

1. 作成したラボのホーム ページに戻ります。

1. 上部のメニューで **[削除]** を選択します。

   :::image type="content" source="./media/create-lab-windows-vm-template/portal-lab-delete.png" alt-text="ラボの [削除] ボタンのスクリーンショット。":::

1. **[それを削除してよろしいですか]** のページで、テキスト ボックスにラボの名前を入力し、 **[削除]** を選択します。

1. 削除中、画面の上部にある **[通知]** を選択すると進行状況を表示できます。 ラボの削除には少し時間がかかります。 ラボが削除されたら、次の手順に進みます。

1. 既存のリソース グループ内にラボを作成した場合は、すべてのラボ リソースが削除されています。 このチュートリアル用に新しいリソース グループを作成した場合は、それが空になっていて削除できます。 ラボがまだ存在している場合にリソース グループが削除されることはありません。

## <a name="next-steps"></a>次の手順
このクイックスタートでは、VM を備えたラボを作成しました。 ラボと VM にアクセスする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: ラボにアクセスする](tutorial-use-custom-lab.md)
