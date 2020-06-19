---
title: クイック スタート:Data Science VM の作成 - Resource Manager テンプレート
titleSuffix: Azure Data Science Virtual Machine
description: このクイックスタートでは、Azure Resource Manager テンプレートを使用して、Data Science Virtual Machine を迅速にデプロイします。
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: machine-learning
ms.subservice: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: 7e3e45d96839be06deec238deccdc9873492d68f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660254"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-a-resource-manager-template"></a>クイック スタート:Resource Manager テンプレートを使用して Ubuntu Data Science Virtual Machine を作成する
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

このクイックスタートでは、Azure Resource Manager テンプレートを使用して Ubuntu 18.04 Data Science Virtual Machine を作成する方法について説明します。 Data Science Virtual Machine は、一連のデータ サイエンスおよび機械学習のフレームワークとツールがプリロードされているクラウドベースの仮想マシンです。 GPU を利用したコンピューティング リソースにデプロイすると、すべてのツールとライブラリは GPU を使用するよう構成されます。 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://aka.ms/AMLFree)を作成してください

* ご使用の**ローカル環境**からこのドキュメントの CLI コマンドを使用するには、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) が必要です

## <a name="create-a-workspace"></a>ワークスペースの作成

### <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/)からのものです。 この記事用のテンプレート全体は長すぎるため、ここでは表示できません。 テンプレート全体を確認するには、[azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json) を参照してください。 ここでは、DSVM の詳細を定義する部分が示されています。

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" range="235-276":::

このテンプレートでは、次のリソースが定義されています。

* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): クラウドベースの仮想マシンを作成します。 このテンプレートでは、この仮想マシンは、Ubuntu 18.04 を実行している Data Science Virtual Machine として構成されます。

### <a name="deploy-the-template"></a>テンプレートのデプロイ 

Azure CLI からテンプレートを使用するには、ログインして自分のサブスクリプションを選択します (「[Azure CLI を使用してサインインする](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)」を参照してください)。 次に、次のコマンドを実行します。

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey && 
echo "Press [ENTER] to continue ..." &&
read
```

上記のコマンドを実行するときには、次を入力してください。

1. DSVM および関連付けられたリソースを格納するために作成するリソース グループの名前。 
1. デプロイ先になる Azure の場所
1. 使用する認証の種類 (文字列 `password` または `sshPublicKey` を入力します)
1. 管理者アカウントのログイン名 (この値を `admin` にすることはできません)
1. このアカウントのパスワードまたは SSH 公開キーの値

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Data Science Virtual Machine を確認するには:

1. [https://resources.azure.com](https://portal.azure.com ) に移動します 
1. サインイン 
1. 先ほど作成したリソース グループを選択します

そのリソース グループの情報が表示されます。 

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="DSVM を含む基本的なリソース グループのスクリーンショット":::

仮想マシン リソースをクリックして、その情報ページに移動します。 ここでは、接続の詳細など、VM に関する情報を確認できます。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この仮想マシンが不要になった場合は削除してください。 DSVM はストレージ アカウントなどの他のリソースに関連付けられているため、作成したリソース グループ全体を削除することをお勧めします。 リソース グループは、ポータルで [削除] ボタンをクリックし、確定することで削除できます。 また、CLI を使用してリソース グループを削除することもできます。 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Resource Manager テンプレートから Data Science Virtual Machine を作成しました。 

> [!div class="nextstepaction"]
> [サンプル プログラムと ML のチュートリアル](dsvm-samples-and-walkthroughs.md)
