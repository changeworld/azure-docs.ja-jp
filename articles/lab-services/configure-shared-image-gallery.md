---
title: Azure DevTest Labs での共有イメージ ギャラリーの構成 | Microsoft Docs
description: Azure DevTest Labs で共有イメージ ギャラリーを構成する方法について説明します
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: de857498aeb51c9b3711c90338d983e85b61cb70
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065430"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Azure DevTest Labs での共有イメージ ギャラリーの構成
DevTest Labs で[共有イメージ ギャラリー](../virtual-machines/windows/shared-image-galleries.md)機能がサポートされるようになりました。 ラボ ユーザーは、ラボ リソースの作成中に共有の場所からイメージにアクセスできます。 また、カスタム マネージド VM イメージに関連する構造および組織を構築できます。 共有イメージ ギャラリー機能では、次がサポートされています。

- イメージのマネージド グローバル レプリケーション
- 容易な管理のためのイメージのバージョン管理とグループ化
- 可用性ゾーンをサポートするリージョンでゾーン冗長ストレージ (ZRS) アカウントを使用して、イメージの可用性を高めます。 ZRS では、ゾーンの障害に対する回復性の向上が提供されます。
- ロールベースのアクセス制御 (RBAC) を使用して、サブスクリプション間やテナント間で共有します。

詳細については、[共有イメージ ギャラリーのドキュメント](../virtual-machines/windows/shared-image-galleries.md)を参照してください。 
 
メンテナンスが必要なマネージド イメージや社内全体で利用可能にしたいマネージド イメージが多数ある場合は、イメージを簡単に更新して共有できるリポジトリとして、共有イメージ ギャラリーを使用できます。 ラボ所有者は、既存の共有イメージ ギャラリーをラボにアタッチできます。 このギャラリーがアタッチされると、ラボ ユーザーはこれらの最新のイメージからマシンを作成できます。 この機能の主な利点は、DevTest Labs で共有イメージのメリットをラボ間、サブスクリプション間、リージョン間で利用できることです。 

## <a name="considerations"></a>考慮事項
- ラボにアタッチできる共有イメージ ギャラリーは一度に 1 つだけです。 別のギャラリーをアタッチする場合は、既存のものをデタッチして別のものをアタッチする必要があります。 
- DevTest Labs は現在、ラボを介したギャラリーへのイメージのアップロードをサポートしていません。 
- 共有イメージ ギャラリーのイメージを使用して仮想マシンを作成している間、DevTest Labs は常に、このイメージの最も新しく公開されたバージョンを使用します。
- ラボが存在するリージョンに共有イメージ ギャラリーがイメージをレプリケートすることを確実にするために、DevTest Labs は自動的に最善の試みをしますが、それが常に可能とは限りません。 これらのイメージから VM を作成するときにユーザーが問題に遭遇しないよう、イメージがラボのリージョンにレプリケート済みであることを確認してください。

## <a name="use-azure-portal"></a>Azure Portal の使用
1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側のナビゲーション メニューで、 **[すべてのサービス]** を選択します。
1. 一覧で **[DevTest Labs]** を選択します。
1. ラボの一覧で、目的の**ラボ**を選択します。
1. 左側のメニューの **[設定]** セクションで、 **[構成とポリシー]** を選択します。
1. 左側のメニューで、 **[仮想マシンのベース]** の下にある **[Shared Image Galleries]\(共有イメージ ギャラリー\)** を選択します。

    ![[Shared Image Galleries]\(共有イメージ ギャラリー\) メニュー](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. **[Attach]\(アタッチ\)** ボタンをクリックしてドロップダウンからギャラリーを選択することによって、既存の共有イメージ ギャラリーをラボにアタッチします。

    ![Attach](./media/configure-shared-image-gallery/attach-options.png)
1. アタッチ済みギャラリーに移動し、VM 作成用に共有イメージを**有効化または無効化**するようギャラリーを構成します。

    ![有効化または無効化](./media/configure-shared-image-gallery/enable-disable.png)
1. 以後、ラボ ユーザーは、 **[+ 追加]** をクリックすることで、有効化されたイメージを使用して仮想マシンを作成できます。また、 **[choose your base]\(ベースの選択\)** ページでイメージを検索できます。

    ![ラボ ユーザー](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>共有イメージ ギャラリーをラボにアタッチする
Azure Resource Manager テンプレートを使用して共有イメージ ギャラリーをラボにアタッチする場合、次の例に示すように、Resource Manager テンプレートの resources セクションの下にそれを追加する必要があります。

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

完全な Resource Manager テンプレートの例については、パブリックの GitHub リポジトリにある次の Resource Manager テンプレート サンプルを参照してください。[Configure a shared image gallery while creating a lab\(ラボの作成中に共有イメージ ギャラリーを構成する\)](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)。

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>共有イメージ ギャラリーのイメージを使用して VM を作成する
Azure Resource Manager テンプレートと、共有イメージ ギャラリーのイメージを使用して仮想マシンを作成する場合は、次のサンプルを使用します。

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

詳細については、パブリックの GitHub にある次の Resource Manager テンプレート サンプルを参照してください。
[Create a virtual machine using a shared image gallery image\(共有イメージ ギャラリーのイメージを使用して仮想マシンを作成する\)](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage)。

## <a name="use-api"></a>API を使用する

- API バージョン 2018-10-15-preview を使用します。
- ギャラリーをアタッチするには、次のスニペットで示しているように要求を送信します。
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- 共有イメージ ギャラリーのすべてのイメージを表示するために、次のようにして、すべての共有イメージをそのリソース ID と共に一覧表示できます

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- 共有イメージを使用して VM を作成するために、仮想マシン上で PUT を実行し、仮想マシンのプロパティで、以前の呼び出しで取得した共有イメージの ID を渡すことができます。 properties.SharedImageId に


## <a name="next-steps"></a>次の手順
成果物に関する次の記事をご覧ください。

- [ラボに必須の成果物の指定](devtest-lab-mandatory-artifacts.md)
- [カスタム アーティファクトの作成](devtest-lab-artifact-author.md)
- [ラボへの成果物リポジトリの追加](devtest-lab-artifact-author.md)
- [アーティファクトの失敗の診断](devtest-lab-troubleshoot-artifact-failure.md)
