---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334227"
---
## <a name="azure-cognitive-service-resource-types"></a>Azure Cognitive Service のリソースの種類

> [!NOTE]
> サブスクリプションの所有者は、リソース グループとサブスクリプション用の Cognitive Services リソースの作成を無効にすることができます。これを行うには、[Azure ポリシー](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)を適用し、[許可されていないリソースの種類] ポリシー定義を割り当て、ターゲットのリソースの種類として **Microsoft.CognitiveServices/accounts** を指定します。

2 つの異なるリソースを使用して、Azure Cognitive Services にアクセスすることができます:マルチサービス リソース、または単一サービス リソース。 これらのサブスクリプションでは、一度に 1 つのサービスまたは複数のサービスに接続できます。

### <a name="multi-service-resource"></a>マルチサービス リソース

>[!WARNING]
> 現在のところ、以下のサービスではマルチサービス キーがサポートされて**いません**。QnA Maker、Speech Services、Custom Vision、および Anomaly Detector。

マルチサービスの Cognitive Services リソースのサブスクライブ:
* 単一の Azure リソースをほとんどの Azure Cognitive Services に使用できます。
* ご利用の複数のサービスの課金を統合します。 追加情報については、「[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)」を参照してください。

### <a name="single-service-resource"></a>単一サービス リソース

単一サービス リソース (Computer Vision や Speech Services など) は、指定されたサービスに限定されます。