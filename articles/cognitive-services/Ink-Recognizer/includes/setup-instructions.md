---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369087"
---
>[!NOTE]
> 2019 年 7 月 1 日より後に作成されたリソースのエンドポイントでは、次に示すカスタム サブドメイン形式を使用します。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](../../cognitive-services-custom-subdomains.md)」を参照してください。 

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](../../cognitive-services-apis-create-account.md) を使用して、Ink Recognizer 用のリソースを作成します。

リソースの作成後、 [Azure portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) でリソースを開き、 **[クイック スタート]** をクリックして、エンドポイントとキーを取得します。

以下の 2 つの[環境変数](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)を作成します。

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - 要求を認証するためのサブスクリプション キー。 

* `INK_RECOGNITION_ENDPOINT` - リソースのエンドポイント。 以下のようになります。 <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`