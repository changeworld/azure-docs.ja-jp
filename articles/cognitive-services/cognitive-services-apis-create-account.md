---
title: Azure portal で Cognitive Services リソースを作成する
titleSuffix: Azure Cognitive Services
description: Azure portal でリソースを作成し、サブスクライブすることによって、Azure Cognitive Services の使用を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274677"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Azure portal を使用して Cognitive Services リソースを作成する

このクイック スタートでは、Azure portal を使用して Azure Cognitive Services リソースを作成します。 Cognitive Services リソースが正常に作成されると、エンドポイントと、アプリケーションを認証するために使用できるキーが取得されます。

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>新しい Azure Cognitive Services リソースを作成する

Cognitive Services リソースを作成する前に、リソースを格納するための Azure リソース グループを用意する必要があります。 新しいリソースを作成するときに、新しいリソース グループを作成するか、既存のものを使用するかを選択できます。 この記事では、新しいリソース グループを作成する方法を示します。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[+リソースの作成]** をクリックします。

    ![Cognitive Services APIs の選択](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. 既に説明したように、Cognitive Services リソースは 2 つの方法で作成できます (マルチサービス リソースを使用するか、単一サービス リソースを使用します)。

    #### <a name="multi-service-resourcetabmultiservice"></a>[マルチサービス リソース](#tab/multiservice)

    マルチサービス リソースを作成するには、検索バーに「**Cognitive Services**」と入力します。

    ![Cognitive Services を検索する](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    [Cognitive Services] ページで、 **[作成]** を選択します。

    ![Cognitive Services アカウントを作成する](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[単一サービス リソース](#tab/singleservice)

    使用できるすべての Cognitive Services を表示するには、**Azure Marketplace** の **[AI + 機械学習]** を選択します。 関心があるサービスが表示されない場合は、 **[すべて表示]** をクリックし、**Cognitive Services** までスクロールします。 Cognitive Services のカタログ全体を表示するには、 **[詳細]** をクリックします。

    関心のあるサービスを選択した後、 **[作成]** をクリックします。
    
    ![Cognitive Services APIs の選択](media/cognitive-services-apis-create-account/azureMarketplace.png)

    ***
3. **[作成]** ページで、次の情報を指定します。

    #### <a name="multi-service-resourcetabmultiservice"></a>[マルチサービス リソース](#tab/multiservice)

    |    |    |
    |--|--|
    | **Name** | Cognitive Sservices リソースのわかりやすい名前。 例: *MyCognitiveServicesResource*。 |
    | **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
    | **Location** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 Azure Cognitive Services を呼び出すときに必要になることがあるので、Azure の場所は記憶しておいてください。 |
    | **[価格レベル]** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
    | **リソース グループ** | Cognitive Services リソースを含むことになる Azure リソース グループ。 新しいグループを作成することも、既存のグループに追加することもできます。 |

    ![リソース作成画面](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    **Create** をクリックしてください。

    #### <a name="single-service-resourcetabsingleservice"></a>[単一サービス リソース](#tab/singleservice)

    |    |    |
    |--|--|
    | **Name** | Cognitive Sservices リソースのわかりやすい名前。 例: *TextAnalyticsResource*。 |
    | **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
    | **Location** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 Azure Cognitive Services を呼び出すときに必要になることがあるので、Azure の場所は記憶しておいてください。 |
    | **[価格レベル]** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
    | **リソース グループ** | Cognitive Services リソースを含むことになる Azure リソース グループ。 新しいグループを作成することも、既存のグループに追加することもできます。 |

    ![リソース作成画面](media/cognitive-services-apis-create-account/resource_create_screen.png)

    **Create** をクリックしてください。

    ***

## <a name="get-the-keys-for-your-resource"></a>リソースのキーを取得する

リソースが正常に作成されると、画面の右上にポップアップ通知が表示されます。 通知の **[リソースに移動]** をクリックして、作成したコグニティブ サービス リソースを確認します。 

![コグニティブ サービス リソースに移動](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

開かれた [クイックスタート] ウィンドウで、エンドポイントとキーにアクセスできます。

![キーとエンドポイントを取得する](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>価格レベルと請求

価格レベル (および請求される金額) は、認証情報を使用して送信するトランザクションの数に基づきます。 各価格レベルにより、以下が指定されます。
* 1 秒あたりに許可されるトランザクションの最大数 (TPS)。
* 価格レベル内で有効にされるサービス機能。
* 事前に定義された数のトランザクションのコスト。 この量を超えると、サービスの「[価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)」で指定されている追加料金が発生します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのグループに含まれている他のリソースも削除されます。

Azure Portal を使用してリソース グループを削除するには:

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、 **[リソース グループ]** を選択して、リソース グループの一覧を表示します。
2. 削除するリソース グループを見つけて、一覧の右側にある [詳細] ボタン (...) を右クリックします。
3. **[リソース グループの削除]** を選択し、確認します。

## <a name="see-also"></a>関連項目

* [Azure Cognitive Services に対する要求の認証](authentication.md)
* [Azure Cognitive Services とは](Welcome.md)
* [自然言語のサポート](language-support.md)
* [Docker コンテナーのサポート](cognitive-services-container-support.md)
