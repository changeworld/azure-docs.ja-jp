---
title: サービス リソースをリージョン間で移動する方法
titleSuffix: Azure Cognitive Search
description: この記事では、Azure クラウドで Azure Cognitive Search リソースをあるリージョンから別のリージョンに移動する方法について説明します。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599636"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Azure Cognitive Search サービスを別の Azure リージョンに移動する

Azure Cognitive Service アカウントをあるリージョンから別のリージョンに移動するには、エクスポート テンプレートを作成してサブスクリプションを移動します。 サブスクリプションを移動したら、データを移動してサービスを再作成する必要があります。

この記事では、次の方法について学習します。

> [!div class="checklist"]
> * テンプレートをエクスポートします。
> * ターゲット リージョン、検索名、ストレージ アカウント名を追加して、テンプレートを変更します。
> * テンプレートをデプロイして、新しい検索とストレージ アカウントを作成します。
> * 新しいリージョンでサービスの状態を確認する
> * ソース リージョンにあるリソースをクリーンアップします。

## <a name="prerequisites"></a>前提条件

- お使いのアカウントで使用されるサービスと機能が、ターゲット リージョンでサポートされていることを確認してください。

- プレビュー機能については、お使いのサブスクリプションがターゲット リージョンのホワイトリストに登録されていることを確認してください。 プレビュー機能の詳細については、[ナレッジ ストア](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro)、[インクリメンタル エンリッチメント](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)、[プライベート エンドポイント](https://docs.microsoft.com/azure/search/service-create-private-endpoint)に関する記事を参照してください。

## <a name="assessment-and-planning"></a>評価と計画

検索サービスを新しいリージョンに移動する場合は、[データを新しいストレージ サービスに移動](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account)し、インデックス、スキルセット、およびナレッジ ストアを再構築する必要があります。 サービスを簡単かつ迅速に再構築できるように、現在の設定を記録し、json ファイルをコピーする必要があります。

## <a name="moving-your-search-services-resources"></a>検索サービスのリソースの移動

開始するには、Resource Manager テンプレートをエクスポートして変更します。

### <a name="export-a-template"></a>テンプレートをエクスポートする

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 自分のリソース グループのページに移動します。

> [!div class="mx-imgBorder"]
> ![リソース グループのページの例](./media/search-move-resource/export-template-sample.png)

3. **[すべてのリソース]** を選択します。

3. 左側のナビゲーション メニューで、 **[テンプレートのエクスポート]** を選択します。

4. **[テンプレートのエクスポート]** ページで **[ダウンロード]** を選択します。

5. ポータルからダウンロードした .zip ファイルを見つけて、選択したフォルダーにそのファイルを解凍します。

zip ファイルには、テンプレートとテンプレートをデプロイするためのスクリプトから構成される .json ファイルが含まれています。

### <a name="modify-the-template"></a>テンプレートの変更

検索名とストレージ アカウント名、リージョンを変更して、テンプレートを変更します。 名前は、各サービスとリージョンの名前付け規則に従う必要があります。 

リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。  リージョンのコードは、スペースを含まないリージョン名です (**Central US** = **centralus**)。

1. Azure Portal で、 **[リソースの作成]** を選択します。

2. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。

3. **[テンプレートのデプロイ]** を選択します。

4. **作成** を選択します。

5. **[Build your own template in the editor] \(エディターで独自のテンプレートをビルド\)** を選択します。

6. **[ファイルの読み込み]** を選択し、手順に従って、前のセクションでダウンロードして解凍した **template.json** ファイルを読み込みます。

7. **template.json** ファイル内で、検索名とストレージ アカウント名の既定値を設定して、ターゲットの検索とストレージ アカウントに名前を付けます。 

8. **template.json** ファイル内の **location** プロパティを編集して、検索とストレージ サービスの両方のターゲット リージョンを指定します。 この例では、ターゲット リージョンを `centralus` に設定します。

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### <a name="deploy-the-template"></a>テンプレートのデプロイ

1. **template.json** ファイルを保存します。

2. プロパティ値を入力または選択します。

- **サブスクリプション**:Azure サブスクリプションを選択します。

- **[リソース グループ]** : **[新規作成]** を選択して、リソース グループに名前を付けます。

- **[場所]** :Azure の場所を選択します。

3. **[上記の使用条件に同意する]** チェック ボックスをオンにして、 **[Select Purchase]\(購入の選択\)** ボタンをクリックします。

## <a name="verifying-your-services-status-in-new-region"></a>新しいリージョンでのサービスの状態の確認

移動を確認するために、新しいリソース グループを開くと、サービスが新しいリージョンで一覧表示されます。

ソース リージョンからターゲット リージョンにデータを移動するには、この記事のガイドラインで[新しいストレージ アカウントへのデータの移動](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account)について参照してください。

## <a name="clean-up-resources-in-your-original-region"></a>元のリージョンのリソースをクリーンアップする

変更をコミットしてサービス アカウントの移動を完了するには、元のサービス アカウントを削除します。

## <a name="next-steps"></a>次のステップ

[インデックスを作成する](https://docs.microsoft.com/azure/search/search-get-started-portal)

[スキルセットを作成する](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[ナレッジ ストアを作成する](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

