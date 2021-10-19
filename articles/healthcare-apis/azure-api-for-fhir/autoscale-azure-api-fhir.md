---
title: Azure API for FHIR の自動スケーリング
description: この記事では、Azure API for FHIR の自動スケーリング機能について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: zxue
ms.openlocfilehash: d1aa4a6797fe6d7794f07b5da7832d80ac026e71
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823334"
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

自動スケーリングが有効になっている場合、システムは初期の `Tmax` 値を計算して設定します。 スケーラビリティは、最大スループットの値 (または) によって制御され、 `RU/s` `Tmax` `0.1 *Tmax` (または 10%) との間でスケーリングし `Tmax` `Tmax RU/s` ます。 

最大 `RU/s` または `Tmax` 値を引き上げて、サービスによってサポートされる上限まで高くすることができます。 サービスがビジー状態の場合、スループット `RU/s` は `Tmax` 値にスケールアップされます。 サービスがアイドル状態の場合、スループット `RU/s` は 10% `Tmax` 値にスケールダウンされます。
 
`RU/s` または `Tmax` の最大値を減らすこともできます。 最大 `RU/s` を引き下げる場合、設定できる最小値は `MAX (4000, highest max RU/s ever provisioned / 10, current storage in GB * 400)` であり、最も近い 1000 `RU/s` に丸められます。

* **例 1**: 1 GB のデータを持っており、プロビジョニングされた最大 `RU/s` は 10,000 です。 最小値は Max (**4000**, 10,000/10, 1x400) = 4000 です。 最初の数値 **4000** が使用されます。
* **例 2**: 20 GB のデータを持っており、プロビジョニングされた最大 `RU/s` は 100,000 です。 最小値は Max (4000, **100,000/10**, 20x400) = 10,000 です。 2 番目の数値 **100,000/10 =10,000** が使用されます。
* **例 3**: 80 GB のデータを持っており、プロビジョニングされた最大 RU/秒は 300,000 です。 最小値は Max (4000, 300,000/10, **80x400**) = 32,000 です。 3 番目の数値 **80x400=32,000** が使用されます。

ポータルを使用して、最大 `RU/s` 値または最大値を調整でき `Tmax` ます。これは、有効な数値で、1万を超えていない場合に使用し `RU/s` ます。 10,000 を超える `Tmax` 値を要求するサポート チケットを作成できます。

>[!Note] 
>データストレージのサイズが大きくなるにつれて、システムは、そのストレージレベルをサポートできる次の最大 RU/秒に自動的に最大スループットを引き上げます。


## <a name="how-to-migrate-to-manual-scale"></a>手動スケールに移行する方法

自動スケールを手動スケールに変更し、スループット RU/秒を指定するには、サポートチケットが必要です。 手動スケールの最小値は、に設定できます。は `MAX (400, highest max RU/s ever provisioned / 100, current storage in GB * 40)` 、最も近い1000に丸めら `RU/s` れます。 ここで使用される数値は、自動スケールで使用される番号とは異なります。

変更が完了すると、新しい課金レートは手動スケールに基づいて算出されます。

## <a name="what-is-the-cost-impact-of-autoscale"></a>自動スケーリングのコストへの影響

自動スケーリング機能では、プロビジョニングされたスループット ユニットが自動的に管理されるため、コストが発生します。 このコストの上昇は、ストレージとランタイムのコストには適用されません。 価格の詳細については、「[Azure API for FHIR の価格](https://azure.microsoft.com/pricing/details/azure-api-for-fhir/)」をご覧ください。

>[!div class="nextstepaction"]
>[Azure API for FHIR について](overview.md)
