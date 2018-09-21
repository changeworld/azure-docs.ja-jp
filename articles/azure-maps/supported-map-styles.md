---
title: Azure Maps でサポートされているマップ スタイル | Microsoft Docs
description: Azure Maps でサポートされているマップ スタイル
author: walsehgal
ms.author: v-musehg
ms.date: 08/28/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 8f0910e9040c962bae30a33b91a93e71e692dfdb
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713444"
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

## <a name="satelliteroadlabels"></a>Satellite_Road_Labels
このマップ スタイルは、衛星画像と航空映像の上に道路とラベルが重ねて表示されたハイブリッドです。

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**該当する API:**
* JS マップ コントロール

## <a name="grayscaledark"></a>Grayscale_Dark
**グレースケール ダーク**は、道路マップ スタイルの濃いバージョンです。

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**該当する API:**
* JS マップ コントロール 