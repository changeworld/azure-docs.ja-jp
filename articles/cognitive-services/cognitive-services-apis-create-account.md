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
ms.openlocfilehash: af01c0c2586ce7df1902a0bcc502c6fd06a5215d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697904"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Azure portal を使用して Cognitive Services リソースを作成する

このクイック スタートでは、Azure portal を使用した Azure Cognitive Services の基本操作について説明します。 Cognitive Services は、ご利用の Azure サブスクリプションに作成した Azure [リソース](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)によって表されます。 リソースの作成後、自動的に生成されたキーとエンドポイントを使用して、自分のアプリケーションの認証を行います。 

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>新しい Azure Cognitive Services リソースを作成する

Cognitive Services リソースを作成する前に、リソースを格納するための Azure リソース グループを用意する必要があります。 新しいリソースを作成するときに、新しいリソース グループを作成するか、既存のものを使用するかを選択できます。 この記事では、新しいリソース グループを作成する方法を示します。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[+リソースの作成]** をクリックします。

    ![Cognitive Services APIs の選択](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. 使用できる Cognitive Services は次の方法で確認できます。
    * 検索バーを使用して、サブスクライブするサービスの名前を入力します。
        * マルチサービス リソースを作成するには、検索バーに「**Cognitive Services**」と入力し、**Cognitive Services** リソースを選択します。

        ![Cognitive Services を検索する](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * 使用できるすべての Cognitive Services を表示するには、**Azure Marketplace** の **[AI + 機械学習]** を選択します。 関心があるサービスが表示されない場合は、 **[すべて表示]** をクリックし、**Cognitive Services** までスクロールします。 Cognitive Services APIs のカタログ全体を表示するには、 **[詳細]** をクリックします。
    
        ![Cognitive Services APIs の選択](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. **[作成]** ページで、次の情報を指定します。

    > [!IMPORTANT]
    > Azure Cognitive Services を呼び出すときに必要になることがあるので、Azure の場所は記憶しておいてください。

    |    |    |
    |--|--|
    | **Name** | Cognitive Sservices リソースのわかりやすい名前。 たとえば、*MyCognitiveServicesAccount* のようにします。 |
    | **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
    | **Location** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
    | **[価格レベル]** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
    | **リソース グループ** | Cognitive Services リソースを含む [Azure リソース グループ](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group)。 新しいグループを作成することも、既存のグループに追加することもできます。 |

    ![リソース作成画面](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-resource"></a>リソースのキーを取得する

リソースを作成した後、Azure ダッシュ ボードにピン留めした場合は、そこからアクセスできます。 それ以外の場合は、 **[リソース グループ]** 内で検索できます。 リソースを選択した後は、 **[リソース管理]** の **[キー]** を選択してキーを取得できます。

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>価格レベルと請求

価格レベル (および請求される金額) は、認証情報を使用して送信するトランザクションの数に基づきます。 各価格レベルにより、以下が指定されます。
* 1 秒あたりに許可されるトランザクションの最大数 (TPS)。
* 価格レベル内で有効化されるサービス機能。
* 事前に定義されたトランザクションの量のコスト。 この量を超えると、サービスの「[価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)」で指定されている追加料金が発生します。

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
