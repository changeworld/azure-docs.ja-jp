---
title: 暗号化されたストレージ アカウントをデプロイするための Azure Resource Manager テンプレートの作成 | Microsoft Docs
description: Visual Studio Code を使用して、暗号化されたストレージ アカウントをデプロイするためのテンプレートを作成します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7c78636a210ae90c5bfe1d0bfd35e4e05633f5cd
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188201"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>チュートリアル: 暗号化されたストレージ アカウントをデプロイするための Azure Resource Manager テンプレートの作成

Azure Resource Manager テンプレートを完成させる情報を調べる方法について説明します。

このチュートリアルでは、Azure クイック スタート テンプレートからのベース テンプレートを使用して Azure Storage アカウントを作成します。  テンプレート リファレンス ドキュメントを使用し、基本テンプレートをカスタマイズして暗号化されたストレージ アカウントを作成します。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートの形式の理解
> * テンプレートでのパラメーターの使用
> * テンプレートでの変数の使用
> * テンプレートの編集
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/)。
* Resource Manager ツール拡張機能。 インストールするには、[Resource Manager ツール拡張機能のインストール](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)に関するページをご覧ください。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

このクイック スタートで使用されるテンプレートは、[Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) と呼ばれます。 テンプレートにより、Azure ストレージ アカウント リソースが定義されます。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. **[ファイル]**>**[名前を付けて保存]** を選択し、ファイルを **azuredeploy.json** としてご自身のローカル コンピューターに保存します。

## <a name="understand-the-format"></a>形式の理解

VS Code から、テンプレートをルート レベルに折りたたみます。 次の要素を含む最も単純な構造があります。

![Resource Manager テンプレートの最も単純な構造](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**: テンプレート言語のバージョンが記述されている JSON スキーマ ファイルの場所を指定します。
* **contentVersion**: この要素に値を指定して、テンプレートの大きな変更を記述します。
* **parameters**: リソースのデプロイをカスタマイズするためにデプロイを実行するときに提供される値を指定します。
* **variables**: テンプレート言語式を簡略化するためにテンプレート内で JSON フラグメントとして使用される値を指定します。
* **resources**: リソース グループ内でデプロイまたは更新されるリソースの種類を指定します。
* **outputs**: デプロイ後に返される値を指定します。

## <a name="use-parameters-in-template"></a>テンプレートでのパラメーターの使用

特定の環境に合った値をパラメーターに渡すことで、デプロイをカスタマイズすることができます。 ストレージ アカウントの値を設定するときに、テンプレートで定義されているパラメーターを使用します。

![Resource Manager テンプレートのパラメーター](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

このテンプレートでは、2 つのパラメーターが定義されます。 location.defaultValue 内でテンプレート関数が使用されていることにご注意ください。

```json
"defaultValue": "[resourceGroup().location]",
```

resourceGroup() 関数は、現在のリソース グループを表すオブジェクトを返します。 テンプレート関数の一覧については、「[Azure Resource Manager テンプレートの関数](./resource-group-template-functions.md)」をご覧ください。

テンプレートで定義されたパラメーターを使用するには:

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables-in-template"></a>テンプレートでの変数の使用

変数では、テンプレート内で使用できる値を作成できます。 変数は、テンプレートの複雑さを軽減するのに役立ちます。

![Resource Manager テンプレートの変数](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

このテンプレートでは、1 つの変数 *storageAccountName* を定義します。 定義では、2 つのテンプレート関数が使用されます。

- **concat()**: 文字列を連結します。 詳しくは、「[concat](./resource-group-template-functions-string.md#concat)」をご覧ください。
- **uniqueString()**: パラメーターとして渡された値に基づいて、決定論的ハッシュ文字列を作成します。 各 Azure ストレージ アカウントには、すべての Azure にわたって一意の名前が必要です。 この関数は、一意の文字列を提供します。 その他の文字列関数については、[文字列関数](./resource-group-template-functions-string.md)に関する記事をご覧ください。

テンプレートで定義された変数を使用するには:

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>テンプレートの編集

ストレージ アカウントの暗号化関連の構成を検索するには、Azure Storage アカウントのテンプレート リファレンスを使用できます。

1. [Azure テンプレート](https://docs.microsoft.com/azure/templates/)を参照します。
2. 左側の目次から、**[参照]**->**[ストレージ]**->**[ストレージ アカウント]** を選択します。 ページには、ストレージ アカウント情報を定義するための情報が含まれています。
3. 暗号化関連の情報を調べます。  
4. ストレージ アカウント リソース定義のプロパティ要素の内部に、次の json を追加します。

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
    この部分により、BLOB ストレージ サービスの暗号化関数が有効になります。

最終的な resources 要素は次のようになります。

![Resource Manager テンプレートの暗号化されたストレージ アカウントの resources](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをデプロイする方法は多数あります。  このチュートリアルでは、Azure portal から Cloud Shell を使用します。 Cloud Shell では、Azure CLI と Azure PowerShell の両方がサポートされます。 この説明では CLI を使用します。

1. [Azure ポータル](https://portal.azure.com)
2. 次の図のように、右上隅の **[Cloud Shell]** を選択します。

    ![Azure portal の Cloud Shell](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell.png)

3. Bash でない場合は、下向き矢印を選択して **[Bash]** を選択します。 このチュートリアルでは、Azure CLI を使用します。

    ![Azure portal の Cloud Shell の CLI](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-choose-cli.png)
4. **[再起動]** を選択し、シェルを再起動します。
5. **[ファイルのアップロード/ダウンロード]** を選択し、**[アップロード]** を選択します。

    ![Azure portal の Cloud Shell のファイルのアップロード](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-upload-file.png)
6. チュートリアルで前に保存したファイルを選択します。 既定の名前は **azuredeploy.json** です。
7. Cloud Shell から **Is** コマンドを実行し、ファイルが適切にアップロードされていることを確認します。 **cat** コマンドを使用して、テンプレートの内容を確認することもできます。

    ![Azure portal の Cloud Shell のファイルの一覧表示](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-list-file.png)
8. Cloud Shell で次のコマンドを実行します。

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file azuredeploy.json
    ```
    サンプルのデプロイのスクリーンショットを次に示します。

    ![Azure portal の Cloud Shell のテンプレートのデプロイ](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-deploy-template.png)

    スクリーンショットでは、次の値が使用されています。

    * **&lt;ResourceGroupName>**: myresourcegroup0719。 パラメーターが 2 つ表示されます。  同じ値を使用してください。
    * **&lt;AzureLocation>**: eastus2
    * **&lt;DeployName>**: mydeployment0719
    * **&lt;TemplateFile>**: azuredeploy.json

    スクリーンショットの出力では、ストレージ アカウント名は *fhqbfslikdqdsstandardsa* です。 

9. 次の PowerShell コマンドを実行して、新しく作成したストレージ アカウントの一覧を表示します。

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

    BLOB ストレージに対して暗号化が有効になっていることを示す、次のスクリーンショットのような出力が表示されます。

    ![Azure Resource Manager の暗号化されたストレージ アカウント](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、テンプレート参照を使用して既存のテンプレートをカスタマイズする方法について説明しました。 このチュートリアルで使用したテンプレートに含まれる Azure リソースは 1 つだけです。  次のチュートリアルでは、複数のリソースを含むテンプレートを開発します。  一部のリソースには依存リソースがあります。

> [!div class="nextstepaction"]
> [複数のリソースの作成](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
