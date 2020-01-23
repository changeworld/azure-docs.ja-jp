---
title: Azure Maps の Mobility Service のデータ構造 | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Mobility Service を介して返される一般的なフィールドとデータ構造について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910721"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure Maps Mobility Service のデータ構造

この記事では、[Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService) の都市圏の概念と、公共輸送機関の停留所や路線についてクエリを実行するときにサービスを介して返されるいくつかの一般的なフィールドについて説明します。 Mobility Service API を使い始める前に、この記事を通し読みすることをお勧めします。 これらの一般的なフィールドについて以下で説明します。

## <a name="metro-area"></a>都市圏

Mobility Service データは、サポートされている都市圏に分かれています。 都市圏は都市の境界に従わず、1 つの都市圏には複数の都市が含まれる場合があります。たとえば、人口密集都市とその周辺都市です。国//地域が 1 つの都市圏になる可能性があります。 

`metroID` は都市圏の ID であり、[Get Metro Area Info API](https://aka.ms/AzureMapsMobilityMetroAreaInfo) を呼び出して、サポートされている輸送の種類と、都市圏の詳細情報 (輸送機関やアクティブなアラートなど) を要求するために使用できます。 Azure Maps Get Metro API を使用して、サポートされている都市圏と metroID を要求できます。 都市圏 ID は変更されることがあります。

**metroID:** 522   **名前:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停留所 ID

輸送機関の停留所は、[General Transit Feed Specification (GFTS)](https://gtfs.org/) ID (stopKey と呼ばれます) と Azure Maps の停留所 ID (stopId と呼ばれます) の 2 種類の ID で参照できます。 長期にわたって停留所を参照する場合は、Azure Maps の停留所 ID を使用することをお勧めします。この ID は安定性が高く、物理的な停留所が存在する限り、ほぼ変更されないためです。 GTFS の停留所 ID は、物理的な停留所に変更がない場合でも、より頻繁に (たとえば、GTFS プロバイダーが変更する必要がある場合や、新しい GTFS バージョンがリリースされた場合に) 更新されます。

始めに、[Get Nearby Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit) を使用して近くの輸送機関の停留所を要求できます。

## <a name="line-groups-and-lines"></a>路線グループと路線

Mobility Service では、[GTFS](https://gtfs.org/) ルートおよび移動データ モデルから継承した変更をより適切に処理するために、路線および路線グループの並列データ モデルを使用します。


### <a name="line-groups"></a>路線グループ

路線グループはエンティティであり、論理的に同じグループの一部であるすべての路線をグループ化します。 通常、路線グループには 2 つの路線が含まれます。1 つはポイント A から B への往路であり、もう 1 つはポイント B から A への復路です。どちらも同じ公共輸送機関に属し、同じ路線番号を持ちます。 ただし、1 つの路線グループに 3 つ以上の路線が含まれている場合や、1 つの路線しか含まれていない場合があります。


### <a name="lines"></a>路線

前述のように、各路線グループは一連の路線から構成されます。 多くの場合、各路線は方向を表し、各路線グループは 2 つの路線で構成されます。 ただし、1 つの路線グループがより多くの路線で構成される場合があります。たとえば、特定の近隣を迂回する場合としない場合がある路線があり、両方が同じ路線番号で運行される場合です。また、1 つの路線グループが 1 つの路線で構成される場合があります。たとえば、1 つの方向を持つ円形の路線です。

始めに、[Get Transit Line API](https://aka.ms/AzureMapsMobilityTransitLine) を使用して路線グループを要求し、後で路線にドリルダウンすることができます。


## <a name="next-steps"></a>次のステップ

Mobility Service を使用して輸送データを要求する方法について説明します。

> [!div class="nextstepaction"]
> [輸送データを要求する方法](how-to-request-transit-data.md)

Mobility Service を使用してリアルタイム データを要求する方法について説明します。

> [!div class="nextstepaction"]
> [リアルタイム データを要求する方法](how-to-request-real-time-data.md)

Azure Maps Mobility Service API のドキュメントを確認します

> [!div class="nextstepaction"]
> [Mobility Service API ドキュメント](https://aka.ms/AzureMapsMobilityService)
