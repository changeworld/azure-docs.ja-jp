---
title: QnA Maker サービスのセットアップ方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: QnA Maker サービスのセットアップ方法
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce452dd686529e017b4eae4717eadb044b389409
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376320"
---
# <a name="create-a-qna-maker-service"></a>QnA Maker サービスを作成する

QnA Maker のナレッジ ベースを作成する前に、まず Azure で QnA Maker サービスをセットアップしておく必要があります。 QnA Maker サービスは、サブスクリプション内で新しいリソースを作成することが認可されているユーザーであれば、だれでもセットアップできます。

このセットアップによって、いくつかの Azure リソースがデプロイされます。 これらのリソースが連動してナレッジ ベースのコンテンツを管理し、エンドポイントを通じて Q&A 機能を提供します。

1. [Azure Portal](<https://portal.azure.com>) にログインします。

2.  **[新しいリソースの追加]** をクリックし、検索で「qna maker」と入力して、QnA Maker リソースを選択します。

    ![新しい QnA Maker サービスの作成](../media/qnamaker-how-to-setup-service/create-new-resource.png)

3.  使用条件を読んで **[作成]** をクリックします。

    ![新しい QnA Maker サービスの作成](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

4. **QnA Maker** で、適切なレベルとリージョンを選択します。

    ![新しい QnA Maker サービスの作成](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * この QnA Maker サービスを識別する一意の名前を **[名前]** に入力します。 ナレッジ ベースが関連付けられることになる QnA Maker のエンドポイントも、この名前によって識別されます。
    * QnA Maker リソースのデプロイ先となる**サブスクリプション**を選択します。
    * QnA Maker 管理サービス (ポータルおよび Management API) の**管理価格レベル**を選択します。 SKU の価格の詳細については、[こちら](https://aka.ms/qnamaker-pricing)を参照してください。
    * この QnA Maker リソースのデプロイ先として、新しい**リソース グループ** (推奨) を作成するか、または既存のリソース グループを使用します。
    * Azure Search サービスの**検索価格レベル**を選択します。 Free レベル オプションが灰色表示されている場合、ご利用のサブスクリプションには、既に Free Azure Search レベルがデプロイされていることを意味します。 その場合、Basic Azure Search レベルから開始する必要があります。 Azure Search 価格の詳細については、[こちら](https://azure.microsoft.com/en-us/pricing/details/search/)を参照してください。
    * Azure Search データのデプロイ先となる**検索場所**を選択します。 Azure Search に使用する場所の選択肢は、顧客データの保管場所の制限に基づいて与えられます。
    * **[アプリ名]** で App Service に名前を付けます。
    * App Service の既定のレベルは Standard (S1) になります。 このプランは、作成後に変更することができます。 App Service の価格の詳細については、[こちら](https://azure.microsoft.com/en-in/pricing/details/app-service/)を参照してください。
    * App Service のデプロイ先となる **Web サイトの場所**を選択します。

        > [!NOTE]
        > 検索場所は Web サイトの場所と異なっていてもかまいません。

    * **Application Insights** を有効にするかどうかを選択します。 **Application Insights** を有効にした場合、トラフィック、チャットのログ、エラーに関するテレメトリが QnA Maker によって収集されます。
    * **[App Insights の場所]** で、Application Insights リソースのデプロイ先を選択します。

5. すべてのフィールドを確認したら、**[作成]** をクリックして、ご利用のサブスクリプションへの、これらのサービスのデプロイを開始します。 この処理は完了までに数分かかります。

6.  デプロイが完了すると、サブスクリプションに次のリソースが作成されていることがわかります。

    ![新しい QnA Maker サービスの作成](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースの作成と発行](../Quickstarts/create-publish-knowledge-base.md)