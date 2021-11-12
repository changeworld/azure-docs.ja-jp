---
title: Azure Maps Creator サービスの地域スコープ
description: Azure Maps での Azure Maps Creator サービスの地域マッピングについて説明します
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc, references_regions
ms.openlocfilehash: 9941db10efb39a5b68181224f06f1096bce5be7f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310497"
---
# <a name="creator-service-geographic-scope"></a>Creator サービスの地域スコープ

Azure Maps Creator は、地域にスコープが設定されているサービスです。 Creator は、Azure リージョンが指定されると、地域レベルでデプロイされた Creator データのインスタンスを作成するリソース プロバイダー API を提供します。 Azure リージョンから地域へのマッピングは、下の表で説明するようにバックグラウンドで行われます。 Azure リージョンと地域の詳細については、「[Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies)」を参照してください。

## <a name="data-locations"></a>データの場所

ディザスター リカバリーと高可用性に対応するために、Microsoft では同じ地域内の他のリージョンにのみお客様のデータをレプリケートできます。 たとえば、西ヨーロッパのデータは北ヨーロッパにレプリケートされる可能性はありますが、米国にレプリケートされることはありません。  ただし、お客様が選択した地域に関係なく、Microsoft は、お客様またはエンド ユーザーがどの場所から Azure Maps API を介してお客様のデータにアクセスするかを制御したり制限したりすることはありません。  

## <a name="geographic-and-regional-mapping"></a>地域とリージョンのマッピング

次の表は、地域とサポートされている Azure リージョンの間のマッピング、およびそれぞれの地域の API エンドポイントを示しています。 たとえば、米国地域内にある米国西部 2 リージョンに Creator アカウントがプロビジョニングされている場合、変換サービスに対する API 呼び出しはすべて `us.atlas.microsoft.com/conversion/convert` に対して行う必要があります。


| Azure の地域 (geo) | Azure データセンター (リージョン) | API の地域エンドポイント |
|------------------------|----------------------|-------------|
| ヨーロッパ| 西ヨーロッパ、北ヨーロッパ | eu.atlas.microsoft.com |
|アメリカ合衆国 | 米国西部 2、米国東部 2 | us.atlas.microsoft.com |
