---
title: チュートリアル - Azure ML ワークスペースの作成 - Resource Manager テンプレート
description: このチュートリアルでは、Azure Resource Manager テンプレートを使用して機械学習用の Azure ワークスペースをすばやくデプロイします
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: b6eac711ed6c420330b036cb6094c841508cee9f
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790717"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-a-resource-manager-template"></a>チュートリアル:Resource Manager テンプレートを使用して Azure Machine Learning ワークスペースをデプロイする
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

このチュートリアルでは、Azure Resource Manager テンプレートを使用して Azure Machine Learning ワークスペースを作成する方法について説明します。 Azure Machine Learning ワークスペースでは、ベースライン データセットからデプロイ済みのモデルに至るまで、あらゆる機械学習アセットが系統立てて整理されます。 ワークスペースは、実験の作成、実行、確認、トレーニングおよび推論のコンピューティング リソースの管理、デプロイ済みモデルの監視とバージョン管理を同僚と共同で行うことができる単一の場所です。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://aka.ms/AMLFree)を作成してください

* ご使用の**ローカル環境**からこのドキュメントの CLI コマンドを使用するには、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) が必要です

## <a name="create-a-workspace"></a>ワークスペースの作成

### <a name="review-the-template"></a>テンプレートを確認する

このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-machine-learning-create/)からのものです。

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json":::

このテンプレートでは、次のリソースが定義されています。

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces): Azure ML ワークスペースを作成します。 このテンプレートで、ユーザーが引数として渡すか対話形式で入力できるパラメーターは、場所と名前です。

### <a name="deploy-the-template"></a>テンプレートのデプロイ 

Azure CLI からテンプレートを使用するには、ログインして自分のサブスクリプションを選択します (「[Azure CLI を使用してサインインする](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)」を参照してください)。 次に、次のコマンドを実行します。

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location && 
echo "Press [ENTER] to continue ..." &&
read
```

上記のコマンドを実行するときには、次を入力してください。

1. 作成するリソース グループと Azure ML ワークスペースの各名前のベースになるプロジェクト名
1. デプロイ先になる Azure の場所

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure ML ワークスペースを確認するには、次の手順を実行します。

1. [https://resources.azure.com](https://portal.azure.com ) に移動します 
1. サインイン 
1. 先ほど作成したワークスペースを選択します

Azure Machine Learning のホームページが表示されます。 

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Azure ML ワークスペースのスクリーンショット":::

デプロイに関連付けられているすべてのリソースを確認するには、左上にあるワークスペース名のリンク (スクリーンショットでは `my_templated_ws`) をクリックします。 このリンクをクリックすると、Azure portal のリソース グループに移動します。 リソース グループの名前は `{projectName}rg`、ワークスペースの名前は `{projectName}ws` です。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このワークスペースが不要になった場合は削除してください。 ワークスペースは、ストレージ アカウントなどの他のリソースに関連付けられているため、作成したリソース グループ全体を削除することをお勧めします。 リソース グループは、ポータルで [削除] ボタンをクリックし、確定することで削除できます。 また、CLI を使用してリソース グループを削除することもできます。 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Resource Manager テンプレートから Azure Machine Learning ワークスペースを作成しました。 Azure Machine Learning についてもっと知りたい場合は、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
> [チュートリアル:Python SDK で初めての ML 実験を作成する](tutorial-1st-experiment-sdk-setup.md)
