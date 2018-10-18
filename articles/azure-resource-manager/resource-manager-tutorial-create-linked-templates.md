---
title: リンクされた Azure Resource Manager テンプレートの作成 | Microsoft Docs
description: 仮想マシンを作成するためにリンクされた Azure Resource Manager テンプレートを作成する方法
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d25ca14b78465a6c4fec7e90bc20436e3ca553fc
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419629"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>チュートリアル: リンクされた Azure Resource Manager テンプレートの作成

リンクされた Azure Resource Manager テンプレートを作成する方法について説明します。 リンクされたテンプレートを使用すると、あるテンプレートから別のテンプレートを呼び出すことができます。 これは、テンプレートをモジュール化する場合に役立ちます。 このチュートリアルでは、[チュートリアル: Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](./resource-manager-tutorial-create-multiple-instances.md)で使用したのと同じテンプレートを使用し、仮想マシン、仮想ネットワーク、およびその他の依存リソース (ストレージ アカウントを含む) を作成します。 リンクされたテンプレートに、ストレージ アカウントのリソースを分離します。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * リンクされたテンプレートを作成する
> * リンクされたテンプレートをアップロードする
> * リンクされたテンプレートにリンクする
> * 依存関係を構成する
> * リンク済みテンプレートから値を取得する
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/)。
* Resource Manager ツール拡張機能。  [拡張機能のインストールに関する記事](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)を参照してください。
* 「[チュートリアル: Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](./resource-manager-tutorial-create-multiple-instances.md)」を完了します。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」(単純な Windows VM をデプロイする) と呼ばれます。 これは、「[チュートリアル: Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](./resource-manager-tutorial-create-multiple-instances.md)」で使用されているのと同じテンプレートです。 以下と同じ使用するテンプレートの 2 つのコピーを保存します。

- **メイン テンプレート**: ストレージ アカウントを除くすべてのリソースを作成します。
- **リンクされたテンプレート**: ストレージ アカウントを作成します。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. **[ファイル]**>**[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。
5. **[ファイル]** > **[名前を付けて保存]** を選択し、このファイルの別のコピーを **linkedTemplate.json** という名前で作成します。

## <a name="create-the-linked-template"></a>リンクされたテンプレートを作成する

リンクされたテンプレートにより、ストレージ アカウントが作成されます。 リンクされたテンプレートは、ストレージ アカウントを作成するスタンドアロンのテンプレートとほぼ同じです。 このチュートリアルでは、リンクされたテンプレートから、値をメイン テンプレートに渡し返す必要があります。 この値は `outputs` 要素で定義されます。

1. linkedTemplate.json が開いていない場合、Visual Studio Code で開きます。
2. 次の変更を行います。

    - ストレージ アカウントを除くすべてのリソースを削除します。 合計で 4 つのリソースを削除します。
    - **outputs** 要素を更新します。その結果、次のようになります。

        ```json
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
        ```
        **storageUri**は、メイン テンプレートの仮想マシン リソース定義で必要です。  この値を、出力値としてメイン テンプレートに値を渡し返します。
    - 使用されないパラメーターを削除します。 これらのパラメーターの下には、緑の波線が表示されます。 **location** というパラメーターを 1 つだけ残します。
    - **variables** 要素を削除します。 これらは、このチュートリアルでは必要ありません。
    - **storageAccountName** というパラメーターを追加します。 ストレージ アカウント名は、パラメーターとして、メイン テンプレートから、リンクされたテンプレートに渡されます。

    完了すると、テンプレートは次のようになります。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountName":{
            "type": "string",
            "metadata": {
              "description": "Azure Storage account name."
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
          }
        ],
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
    }
    ```
3. 変更を保存します。

## <a name="upload-the-linked-template"></a>リンクされたテンプレートをアップロードする

テンプレートは、デプロイを実行する場所からアクセス可能である必要があります。 この場所には、Azure ストレージ アカウント、Github、Dropbox などがあります。 テンプレートに機密情報が含まれている場合は、それらへのアクセスを保護するようにしてください。 このチュートリアルでは、「 [チュートリアル: Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](./resource-manager-tutorial-create-multiple-instances.md)」で使用した、Cloud シェル デプロイ方法を使用します。 メイン テンプレート (azuredeploy.json) は、シェルにアップロードされます。 リンクされたテンプレート (linkedTemplate.json) は、別の場所で共有する必要があります。  このチュートリアルのタスクを減らすために、前のセクションで定義したリンクされたテンプレートが、[Azure ストレージ アカウント](https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json)にアップロードされています。

## <a name="call-the-linked-template"></a>リンクされたテンプレートを呼び出す

メイン テンプレートは azuredeploy.json と呼ばれます。

1. azuredeploy.json が開いていない場合、Visual Studio Code で開きます。
2. テンプレートからストレージ アカウント リソース定義を削除します。
3. 次の json スニペットを、ストレージ アカウント定義があった場所に追加します。

    ```json
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    次の点に注意してください。

    - メイン テンプレートの `Microsoft.Resources/deployments` リソースは、別のテンプレートにリンクするために使用されます。
    - `deployments` リソースは、`linkedTemplate` という名前を持っています。 この名前は、[依存関係を構成する](#configure-dependency)場合に使用されます。  
    - リンクされたテンプレートを呼び出すときは、[増分](./deployment-modes.md)デプロイ モードのみを使用できます。
    - `templateLink/uri` には、リンクされたテンプレート URI が含まれています。 リンクされたテンプレートは、共有ストレージ アカウントにアップロードされました。 インターネット上の別の場所にテンプレートをアップロードする場合は、URI を更新できます。
    - メイン テンプレートから、リンクされたテンプレートに値を渡すには、`parameters` を使用します。
4. 変更を保存します。

## <a name="configure-dependency"></a>依存関係を構成する

「[チュートリアル: Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](./resource-manager-tutorial-create-multiple-instances.md)」で説明したように、仮想マシン リソースはストレージ アカウントによって異なります。

![Azure Resource Manager テンプレートの依存関係図](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

ストレージ アカウントが、リンクされたテンプレートで定義されているので、`Microsoft.Compute/virtualMachines` リソースの次の 2 つの要素を更新する必要があります。

- `dependOn` 要素を再構成します。 ストレージ アカウントの定義が、リンクされたテンプレートに移動されます。
- `properties/diagnosticsProfile/bootDiagnostics/storageUri` 要素を再構成します。 「[リンクされたテンプレートを作成する](#create-the-linked-template)」で、出力値を追加しました。

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    この値は、メイン テンプレートで必要です。

1. azuredeploy.json が開いていない場合、Visual Studio Code で開きます。
2. 仮想マシン リソース定義を展開し、次のスクリーンショットに示すように、**dependsOn** を更新します。

    ![Azure Resource Manager のリンクされたテンプレートで依存関係を構成する ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)
    
    "linkedTemplate" は、デプロイ リソースの名前です。  
3. 前のスクリーンショットに示すように、**properties/diagnosticsProfile/bootDiagnostics/storageUri** を更新します。

詳細については、「[Azure リソース デプロイ時のリンクされたテンプレートおよび入れ子になったテンプレートの使用](./resource-group-linked-templates.md)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

デプロイ手順については、「[テンプレートのデプロイ](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template)」セクションを参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、リンクされたテンプレートを開発およびデプロイします。 Azure のリソースを複数のリージョンにデプロイする方法と、安全なデプロイ方法については、以下を参照してください。


> [!div class="nextstepaction"]
> [Azure Deployment Manager の使用](./deployment-manager-tutorial.md)

