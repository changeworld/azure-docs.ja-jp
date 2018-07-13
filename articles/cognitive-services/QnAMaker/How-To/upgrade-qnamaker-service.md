---
title: QnA Maker サービスのアップグレード - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: QnA Maker サービスのアップグレード方法
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e8d3713d6729c4e30da9a64a382e9d5a647dfefd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376288"
---
# <a name="upgrade-your-qna-maker-service"></a>QnA Maker サービスのアップグレード
QnA Maker スタックは、その初回作成後に、個々のコンポーネントを必要に応じてアップグレードすることができます。 依存コンポーネントと SKU 選択の詳細については、[こちら](https://aka.ms/qnamaker-docs-capacity)を参照してください。

## <a name="upgrade-qna-maker-management-sku"></a>QnA Maker 管理 SKU のアップグレード
QnA Maker 管理 SKU をアップグレードするには、次の手順に従います。
1. Azure portal の QnA Maker リソースに移動し、**[価格レベル]** を選択します。

    ![QnA Maker リソース](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. 適切な SKU を選択し、**[選択]** を押します。

    ![QnA Maker の価格](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>App Service のアップグレード
App Service は、[スケールアップ](https://docs.microsoft.com/azure/app-service/web-sites-scale)またはスケールダウンすることができます。

1. Azure portal で App Service リソースに移動し、必要に応じて **[スケールアップ]** と **[スケールダウン]** のどちらかのオプションを選択します。

    ![QnA Maker App Service のスケーリング](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Azure Search サービスのアップグレード
現在、Azure Search SKU のインプレース アップグレードを実行することはできません。 ただし、必要な SKU で新しい Azure Search リソースを作成し、その新しいリソースにデータを復元して、QnA Maker スタックにリンクすることはできます。

1. Azure portal で新しい Azure Search リソースを作成し、必要な SKU を選択します。

    ![QnA Maker Azure Search リソース](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. 元の Azure Search リソースのインデックスを新しいリソースに復元します。 [こちら](https://github.com/pchoudhari/QnAMakerBackupRestore)でバックアップ復元のサンプル コードを参照してください。

3. データが復元されたら、新しい Azure Search リソースに移動し、**[キー]** を選択して、**[名前]** と **[管理者キー]** を書き留めます。

    ![QnA Maker Azure Search キー](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. 新しい Azure Search リソースを QnA Maker スタックにリンクするために、QnA Maker App サービスに移動します。

    ![QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. **[アプリケーションの設定]** を選択し、**[AzureSearchName]** フィールドと **[AzureSearchAdminKey]** フィールドを手順 3. で書き留めた値に置き換えます。

    ![QnA Maker App Service の設定](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. App Service を再起動します。

    ![QnA Maker App Service の再起動](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker API の使用](../Quickstarts/csharp.md)
