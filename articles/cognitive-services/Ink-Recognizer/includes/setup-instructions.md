---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: ede1fb4bd2a9a6e6536959053e3ca4d8e4a82f87
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327361"
---
>[!NOTE]
> 2019 年 7 月 1 日より後に作成されたリソースのエンドポイントでは、次に示すカスタム サブドメイン形式を使用します。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)」を参照してください。 

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](../../cognitive-services-apis-create-account.md) を使用して、Ink Recognizer 用のリソースを作成します。

リソースの作成後、[Azure portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) でリソースを開き、 **[クイック スタート]** をクリックして、エンドポイントとキーを取得します。

以下の 2 つの[環境変数](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)を作成します。

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - 要求を認証するためのサブスクリプション キー。 

* `INK_RECOGNITION_ENDPOINT` - リソースのエンドポイント。 以下のようになります。 <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
