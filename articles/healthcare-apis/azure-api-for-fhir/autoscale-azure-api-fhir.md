---
title: Azure API for FHIR の自動スケーリング
description: この記事では、Azure API for FHIR の自動スケーリング機能について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: zxue
ms.openlocfilehash: cc5fd2dd91c1edc39886938d91ebb54bcbc3ef0d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780862"
---
# <a name="autoscale-for-azure-api-for-fhir"></a>Azure API for FHIR の自動スケーリング 

マネージド サービスとしての Azure API for FHIR を使用すると、顧客は FHIR 準拠のヘルスケア データを保持し、サービス API を介してそれを安全に交換できます。 さまざまなトランザクション ワークロードに対応するために、顧客は手動スケーリングまたは自動スケーリングを使用できます。

## <a name="what-is-autoscale"></a>自動スケールとは何ですか。

既定では、Azure API for FHIR は手動スケーリングに設定されています。 このオプションは、トランザクション ワークロードが既知で一貫性がある場合に適しています。 顧客は、ポータルを介してスループット `RU/s` を最大 10,000 まで調整し、制限を引き上げる要求を送信できます。 

自動スケーリングを使用すると、顧客はさまざまなワークロードを実行でき、スループット `RU/s` は手動による調整なしで自動的にスケールアップおよびスケールダウンされます。

## <a name="how-to-enable-autoscale"></a>自動スケーリングを有効にする方法

自動スケーリング機能を有効にするには、1 回限りのサポート チケットを作成して要求できます。 Microsoft サポート チームは、サポートの優先度に基づいて自動スケーリング機能を有効にします。

> [!NOTE]
> 自動スケーリング機能は、Azure portal からは利用できません。

## <a name="how-to-adjust-the-maximum-throughput-rus"></a>最大スループット RU/秒 を調整する方法

自動スケーリングが有効になっている場合、システムは初期の `Tmax` 値を計算して設定します。 スケーラビリティは、最大スループット `RU/s` 値または `Tmax` によって制御され、`0.1 *Tmax` (または 10% `Tmax`) から `Tmax RU/s` の間で実行されます。 

最大 `RU/s` または `Tmax` 値を引き上げて、サービスによってサポートされる上限まで高くすることができます。 サービスがビジー状態の場合、スループット `RU/s` は `Tmax` 値にスケールアップされます。 サービスがアイドル状態の場合、スループット `RU/s` は 10% `Tmax` 値にスケールダウンされます。
 
`RU/s` または `Tmax` の最大値を減らすこともできます。 最大 `RU/s` を引き下げる場合、設定できる最小値は `MAX (4000, highest max RU/s ever provisioned / 10, current storage in GB * 400)` であり、最も近い 1000 `RU/s` に丸められます。

* **例 1**: 1 GB のデータを持っており、プロビジョニングされた最大 `RU/s` は 10,000 です。 最小値は Max (**4000**, 10,000/10, 1x400) = 4000 です。 最初の数値 **4000** が使用されます。
* **例 2**: 20 GB のデータを持っており、プロビジョニングされた最大 `RU/s` は 100,000 です。 最小値は Max (4000, **100,000/10**, 20x400) = 10,000 です。 2 番目の数値 **100,000/10 =10,000** が使用されます。
* **例 3**: 80 GB のデータを持っており、プロビジョニングされた最大 RU/秒は 300,000 です。 最小値は Max (4000, 300,000/10, **80x400**) = 32,000 です。 3 番目の数値 **80x400=32,000** が使用されます。

有効な数値であり、10,000 `RU/s` 以下の場合、ポータルを介して最大 `RU/s` または `Tmax` 値を調整できます。 10,000 を超える `Tmax` 値を要求するサポート チケットを作成できます。

## <a name="what-is-the-cost-impact-of-autoscale"></a>自動スケーリングのコストへの影響

自動スケーリング機能では、プロビジョニングされたスループット ユニットが自動的に管理されるため、コストが発生します。 このコストの上昇は、ストレージとランタイムのコストには適用されません。 価格の詳細については、「[Azure API for FHIR の価格](https://azure.microsoft.com/pricing/details/azure-api-for-fhir/)」をご覧ください。

>[!div class="nextstepaction"]
>[Azure API for FHIR について](overview.md)
