---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274636"
---
## <a name="azure-cognitive-service-resource-types"></a>Azure Cognitive Service のリソースの種類

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
2 つの異なるリソースを使用して、Azure Cognitive Services にアクセスすることができます:マルチサービス リソース、または単一サービス リソース。 これらのサブスクリプションでは、単一のコグニティブ サービスまたは一度に複数のコグニティブ サービスに接続できます。

### <a name="multi-service-resource"></a>マルチサービス リソース

マルチサービスの Cognitive Services リソースのサブスクライブ:
* 単一の Azure リソースをほとんどの Azure Cognitive Services に使用できます。
* 複数の Azure Cognitive Services で使用できる単一のキーを取得します。
* ご利用の複数のサービスの課金を統合します。 追加情報については、「[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)」を参照してください。

>[!WARNING]
> 現在のところ、以下のサービスではマルチサービス キーがサポートされて**いません**。QnA Maker、Speech Services、Custom Vision、および Anomaly Detector。

### <a name="single-service-resource"></a>単一サービス リソース

単一サービスの Cognitive Services リソースのサブスクライブ:
* リソースを作成する特定のコグニティブ サービス (Computer Vision や Speech Services など) を使用できます。
* リソースを作成するコグニティブ サービスに固有のキーを取得します。