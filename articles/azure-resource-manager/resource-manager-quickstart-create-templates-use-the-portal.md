---
title: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ | Microsoft Docs
description: Azure portal を使用して最初の Azure Resource Manager テンプレートを作成する方法とテンプレートをデプロイする方法を説明します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/16/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 8094105fd14ef13dd5f6b892425608806ca4dbd2
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378035"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ

Azure portal を使用してテンプレートを生成して最初の Azure Resource Manager テンプレートを作成する方法と、Azure portal でテンプレートを編集およびデプロイするプロセスを説明します。 Resource Manager テンプレートとは、ソリューションに対してデプロイが必要なリソースを定義した JSON ファイルのことをいいます。 このチュートリアルの手順では、Azure ストレージ アカウントを作成します。 同じプロセスを使用して、他の Azure リソースを作成できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="generate-a-template-using-the-portal"></a>ポータルを使用したテンプレートの生成

このセクションでは、Azure portal を使用してストレージ アカウントを作成します。 ストレージ アカウントをデプロイする前に、構成に基づいてポータルによって生成されたテンプレートの詳細を確認することもできます。 テンプレートを保存し、今後再利用できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[リソースの作成]** > **[Storage]** > **[ストレージ アカウント - Blob、File、Table、Queue]** を選択します。

    ![Azure portal を使用して Azure ストレージ アカウントを作成する](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. 次の情報を入力します。 

    - **リソース グループ**: 任意の名前で新しい Azure リソース グループを作成します。 スクリーンショットでは、リソース グループ名は *mystorage1016rg* です。
    - **名前**: ストレージ アカウントに一意の名前を付けます。 スクリーンショットでは、名前は *mystorage1016* です。

    残りのプロパティは既定値のままでかまいません。

    ![Azure portal を使用して Azure ストレージ アカウント構成を作成する](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > エクスポートされたテンプレートの中には、デプロイする前に編集を必要とするものがあります。

4. 画面下部の **[確認および作成]** を選択します。 
5. 画面下部の **[Automation のテンプレートをダウンロードする]** を選択します。 ポータルに、生成されたテンプレートが次のように表示されます。

    ![ポータルを使用してテンプレートを生成する](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    メイン ウィンドウにテンプレートが表示されます。 これは、4 つの最上位要素 (`schema`、`contentVersion`、`parameters`、および `resources`) が含まれた JSON ファイルです。 詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](./resource-group-authoring-templates.md)」をご覧ください。

    6 個のパラメーターが定義されています。 そのうちの 1 つは、**storageAccountName** という名前です。 2 番目の強調表示された部分は、テンプレートでこのパラメーターを使用する方法を示しています。 次のセクションでは、生成された名前をストレージ アカウントに使用するようにテンプレートを編集します。

    テンプレートには、1 つの Azure リソースが定義されています。 その種類は、[Microsoft.Storage/storageAccounts] です。 リソースがどのように定義されているかと、定義の構造を確認してください。
6. **[Download]** を選択します。 ダウンロードしたパッケージの **template.json** をコンピューターに保存します。 次のセクションでは、テンプレート デプロイ ツールを使用してテンプレートを編集します。
7. **[パラメーター]** タブを選択して、パラメーターに指定した値を確認します。 これらの値を書き留めておきます。次のセクションでテンプレートをデプロイするときに必要になります。

    ![ポータルを使用してテンプレートを生成する](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    テンプレートとパラメーター ファイルの両方を使用して、Azure ストレージ アカウントを作成できます。

## <a name="edit-and-deploy-the-template"></a>テンプレートの編集とデプロイ

Azure portal では、いくつかの基本的なテンプレート編集を行うことができます。 このクイック スタートでは、"*Template Deployment*" と呼ばれるポータル ツールを使用します。 より複雑なテンプレートを編集する場合は、豊富な編集機能を備えた [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md) を使用することを検討してください。

Azure では、各 Azure サービスで一意の名前が使用される必要があります。 既に存在するストレージ アカウント名を入力すると、デプロイが失敗します。 この問題を回避するために、テンプレート関数呼び出し `uniquestring()` を使用して一意のストレージ アカウント名を生成できます。

1. Azure Portal で、**[リソースの作成]** を選択します。
2. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。
3. **[テンプレートのデプロイ]** を選択します。

    ![Azure Resource Manager のテンプレート ライブラリ](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. **作成**を選択します。
5. **[Build your own template in the editor] \(エディターで独自のテンプレートをビルド\)** を選択します。
6. **[ファイルの読み込み]** を選択し、手順に従って、前のセクションでダウンロードした template.json を読み込みます。
7. 次のスクリーンショットに示されているように、1 つの変数を追加します。

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
    ```
    ![Azure Resource Manager のテンプレート](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

    ここでは、`concat()` と `uniqueString()` という 2 つの関数が使用されています。

8. 前のスクリーンショットで強調表示されている **storageAccountName** パラメーターを削除します。
9. このパラメーターの代わりに、新しく定義した変数を使用するように、**Microsoft.Storage/storageAccounts** リソースの name 要素を更新します。

    ```json
    "name": "[variables('storageAccountName')]",
    ```

    最終的なテンプレートは次のようになります。

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string"
            },
            "accountType": {
                "type": "string"
            },
            "kind": {
                "type": "string"
            },
            "accessTier": {
                "type": "string"
            },
            "supportsHttpsTrafficOnly": {
                "type": "bool"
            }
        },
        "variables": {
            "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
            {
                "name": "[variables('storageAccountName')]",
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2018-07-01",
                "location": "[parameters('location')]",
                "properties": {
                    "accessTier": "[parameters('accessTier')]",
                    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
                },
                "dependsOn": [],
                "sku": {
                    "name": "[parameters('accountType')]"
                },
                "kind": "[parameters('kind')]"
            }
        ],
        "outputs": {}
    }
    ```
7. **[保存]** を選択します。
8. 次の値を入力します。

    - **リソース グループ**: リソース グループに一意の名前を付けます。
    - **場所**: リソース グループの場所を選択します。
    - **[場所]**: ストレージ アカウントの場所を選択します。  リソース グループと同じ場所を使用できます。
    - **アカウントの種類**: このクイック スタートでは、「**Standard_LRS**」と入力します。
    - **種類**: このクイック スタートでは、「**StorageV2**」と入力します。
    - **アクセス レベル**: このクイック スタートでは、「**Hot**」と入力します。
    - **[Https Traffic Only Enabled]\(HTTPS トラフィックのみ有効\)**:   このクイック スタートでは、**[true]** を選択します。
    - **[上記の使用条件に同意する]**: (オン)

    サンプルのデプロイのスクリーンショットを次に示します。

    ![Azure Resource Manager テンプレートのデプロイ](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. **[購入]** を選択します。
11. 画面の上部にあるベルのアイコン (通知) を選択して、デプロイの状態を確認します。 デプロイが完了するまでお待ちください。

    ![Azure Resource Manager テンプレートのデプロイ通知](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. 通知ウィンドウで **[リソース グループに移動]** を選択します。 次のような画面が表示されます。

    ![Azure Resource Manager テンプレートのデプロイ リソース グループ](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    デプロイの状態が成功であったことのほか、リソース グループ内のストレージ アカウントが 1 つのみであることを確認できます。 ストレージ アカウント名は、テンプレートによって生成された一意の文字列です。 Azure ストレージ アカウントの使用の詳細については、「[クイック スタート: Azure portal を使用して BLOB をアップロード、ダウンロード、および一覧表示する](../storage/blobs/storage-quickstart-blobs-portal.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューの **[リソース グループ]** を選択します。
2. **[Filter by name]\(名前でフィルター処理\)** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループにストレージ アカウントが表示されます。
4. 上部のメニューの **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure portal を使用してテンプレートを生成する方法と、ポータルを使用してテンプレートをデプロイする方法を説明しました。 このクイック スタートで使用したテンプレートは、Azure リソースが 1 つだけ含まれた単純なテンプレートです。 テンプレートが複雑な場合は、Visual Studio Code または Visual Studio を使用してテンプレートを開発する方が簡単です。 次のクイック スタートでは、Azure PowerShell および Azure コマンド ライン インターフェイス (CLI) を使用してテンプレートをデプロイする方法についても説明しています。

> [!div class="nextstepaction"]
> [Visual Studio Code を使用してテンプレートを作成する](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
