---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c202ba1d7363af9791daa801f0c447c49a80859b
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378585"
---
>[!NOTE]
> 2019 年 7 月 1 日より後に作成されたリソースのエンドポイントでは、次に示すカスタム サブドメイン形式を使用します。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)」を参照してください。 

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](../../cognitive-services-apis-create-account.md) を使用して、Ink Recognizer 用のリソースを作成します。

リソースの作成後、[Azure portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) でリソースを開き、 **[クイック スタート]** をクリックして、エンドポイントとキーを取得します。

以下の 2 つの[環境変数](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)を作成します。

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - リソースのエンドポイント。 以下のようになります。 <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT` - 要求を認証するためのサブスクリプション キー。   
