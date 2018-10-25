---
title: Azure Maps でサポートされているマップ スタイル | Microsoft Docs
description: Azure Maps でサポートされているマップ スタイル
author: walsehgal
ms.author: v-musehg
ms.date: 10/02/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c8edaba8de597e3e76e760e1f5109006338a663c
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238822"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps でサポートされているマップ スタイル
Azure Maps では、4 種類の組み込みマップ スタイルがサポートされています。 そのスタイルと説明を以下に示します。

## <a name="road"></a>道路
**道路**マップは標準マップであり、道路、自然および人工の地物、それらの地物のラベルが表示されます。

![道路](./media/supported-map-styles/road.png)

**該当する API:**
* [マップ画像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [マップ タイル](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* JS マップ コントロール

## <a name="satellite"></a>衛星 
**衛星**スタイルは、衛星画像と航空映像の組み合わせです。

![衛星](./media/supported-map-styles/satellite.png)

**該当する API:**
* [衛星タイル](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* JS マップ コントロール

## <a name="satelliteroadlabels"></a>satellite_road_labels
このマップ スタイルは、衛星画像と航空映像の上に道路とラベルが重ねて表示されたハイブリッドです。

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**該当する API:**
* JS マップ コントロール

## <a name="grayscaledark"></a>grayscale_dark
**グレースケール ダーク**は、道路マップ スタイルの濃いバージョンです。

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**該当する API:**
* JS マップ コントロール 