---
title: データの自動Azure API for FHIR
description: この記事では、データ の自動スケール機能についてAzure API for FHIR。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: zxue
ms.openlocfilehash: cc5fd2dd91c1edc39886938d91ebb54bcbc3ef0d
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713386"
---
# <a name="autoscale-for-azure-api-for-fhir"></a>データの自動Azure API for FHIR 

このAzure API for FHIRサービスとして使用することで、お客様は FHIR 準拠の医療データを保持し、サービス API を介して安全に交換できます。 さまざまなトランザクション ワークロードに対応するために、お客様は手動スケールまたは自動スケーリングを使用できます。

## <a name="what-is-autoscale"></a>自動スケールとは何ですか。

既定では、このAzure API for FHIR手動スケールに設定されます。 このオプションは、トランザクション ワークロードが既知で一貫性がある場合に適しています。 お客様は、ポータルから最大 10,000 のスループットを調整し、制限を引き上げリクエスト `RU/s` を送信できます。 

自動スケーリングを使用すると、お客様はさまざまなワークロードを実行できます。手動で調整することなく、スループットが自動的 `RU/s` にスケールアップおよびスケールダウンされます。

## <a name="how-to-enable-autoscale"></a>自動スケーリングを有効にする方法

自動スケーリング機能を有効にするには、ワンタイム サポート チケットを作成して要求できます。 Microsoft サポート チームは、サポートの優先度に基づいて自動スケーリング機能を有効にします。

> [!NOTE]
> 自動スケール機能は、既定のAzure portal。

## <a name="how-to-adjust-the-maximum-throughput-rus"></a>最大スループット RU/s を調整する方法

自動スケーリングが有効になっている場合、システムは初期値を計算して設定 `Tmax` します。 スケーラビリティは、最大スループット値 () によって管理され、 `RU/s` `Tmax` `0.1 *Tmax` (または 10%) から の間 `Tmax` で実行されます `Tmax RU/s` 。 

最大値または値を増 `RU/s` や `Tmax` して、サービスがサポートする限り高くすることができます。 サービスがビジー状態の場合、スループット `RU/s` は 値にスケールアップ `Tmax` されます。 サービスがアイドル状態の場合、スループット `RU/s` は 10% の値にスケールダウン `Tmax` されます。
 
最大値または値を減 `RU/s` ら `Tmax` して指定できます。 最大値 を下げると、最小値は に設定できます。 は、最も近い `RU/s` `MAX (4000, highest max RU/s ever provisioned / 10, current storage in GB * 400)` 1000 に丸められます `RU/s` 。

* **例 1:** 1 GB のデータを持ち、プロビジョニングされた最大のデータは `RU/s` 10,000 です。 最小値は Max (**4000**, 10,000/10, 1x400) = 4000 です。 最初の数値 **4000 が** 使用されます。
* **例 2:** 20 GB のデータを持ち、プロビジョニングされた最大のデータは `RU/s` 100,000 です。 最小値は Max (4000, **100,000/10**, 20x400) = 10,000 です。 2 番目の **数値 100,000/10 =10,000** が使用されます。
* **例 3:** 80 GB のデータを使用し、プロビジョニングされた RU/s が最も高いのは 300,000 です。 最小値は Max (4000, 300,000/10, **80x400**) = 32,000 です。 3 番目の **数値 80x400=32,000 が** 使用されます。

有効な数値で、 が `RU/s` `Tmax` 10,000 を超える場合は、ポータルから最大値または値を調整できます `RU/s` 。 `Tmax`10,000 を超える値を要求するサポート チケットを作成できます。

## <a name="what-is-the-cost-impact-of-autoscale"></a>自動スケーリングのコストへの影響は何ですか?

自動スケーリング機能では、プロビジョニングされたスループット ユニットを自動的に管理するためのコストが発生します。 このコストの増加は、ストレージとランタイムのコストには適用されません。 価格の詳細については、「価格」 [をAzure API for FHIRしてください](https://azure.microsoft.com/pricing/details/azure-api-for-fhir/)。

>[!div class="nextstepaction"]
>[Azure API for FHIR について](overview.md)
