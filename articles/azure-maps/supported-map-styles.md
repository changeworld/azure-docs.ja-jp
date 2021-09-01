---
title: サポートされている組み込みの Azure Maps マップ スタイル
description: Azure Maps でサポートされる組み込みのマップ スタイル (道路、blank_accessible、衛星、satellite_road_labels、road_shaded_relief、night など) について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 4032dec715fc12b4e8144e6d9eebd51faceaaaaf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735868"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps でサポートされている組み込みのマップ スタイル

Azure Maps は、次に説明するように、いくつかの異なる組み込みマップ スタイルをサポートしています。

>[!important]
>このセクションの手順には、Gen 1 または Gen 2 価格レベルの Azure Maps アカウントが必要です。 価格レベルの詳細については、「[Azure Maps での適切な価格レベルの選択](choose-pricing-tier.md)」を参照してください。

## <a name="road"></a>道路

**道路** マップは、道路を表示する標準のマップです。 自然および人工的な特徴、およびそれらの特徴についてのラベルも表示されます。

![道路マップ スタイル](./media/supported-map-styles/road.png)

**該当する API:**

* [マップ画像](/rest/api/maps/render/getmapimage)
* [マップ タイル](/rest/api/maps/render/getmaptile)
* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="blank-and-blank_accessible"></a>blank と blank_accessible

**blank** と **blank_accessible** マップ スタイルでは、データを視覚化するための空白のキャンバスが提供されます。 基本マップが表示されていない場合でも、**blank_accessible** スタイルでは、マップの場所の詳細を含むスクリーン リーダーの更新が引き続き提供されます。

> [!Note]
> Web SDK では、マップの DIV 要素の CSS `background-color` スタイルを設定することによって、マップの背景色を変更できます。

**該当する API:**

* Web SDK マップ コントロール

## <a name="satellite"></a>衛星

**衛星** スタイルは、衛星画像と航空映像の組み合わせです。

![衛星タイル マップ スタイル](./media/supported-map-styles/satellite.png)

**該当する API:**

* [衛星タイル](/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="satellite_road_labels"></a>satellite_road_labels

このマップ スタイルは、衛星画像と航空映像の上に道路とラベルが重ねて表示されたハイブリッドです。

![satellite_road_labels マップ スタイル](./media/supported-map-styles/satellite-road-labels.png)

**該当する API:**

* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="grayscale_dark"></a>grayscale_dark

**グレースケール ダーク** は、道路マップ スタイルの濃いバージョンです。

![gray_scale マップ スタイル](./media/supported-map-styles/grayscale-dark.png)

**該当する API:**

* [マップ画像](/rest/api/maps/render/getmapimage)
* [マップ タイル](/rest/api/maps/render/getmaptile)
* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="grayscale_light"></a>grayscale_light

**グレースケール ライト** は、道路マップ スタイルの薄いバージョンです。

![グレースケール ライト マップ スタイル](./media/supported-map-styles/grayscale-light.jpg)

**該当する API:**
* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="night"></a>night

**night** は、道路マップ スタイルの色の濃いバージョンで、道路と記号が色付きです。

![ナイト マップ スタイル](./media/supported-map-styles/night.png)

**該当する API:**

* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="road_shaded_relief"></a>road_shaded_relief

**道路の影付きレリーフ** は、地球の輪郭で完成する、Azure Maps の主要スタイルです。

![影付きレリーフ マップ スタイル](./media/supported-map-styles/shaded-relief.png)

**該当する API:**

* [マップ タイル](/rest/api/maps/render/getmaptile)
* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** は、他のスタイルと比較してコントラストが強いダーク マップ スタイルです。

![コントラストの強いダーク マップ スタイル](./media/supported-map-styles/high-contrast-dark.png)

**該当する API:**

* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="high_contrast_light"></a>high_contrast_light

**high_contrast_light** は、他のスタイルと比較してコントラストが強いライト マップ スタイルです。

![コントラストの強いライト マップ スタイル](./media/supported-map-styles/high-contrast-light.jpg)

**該当する API:**

* Web SDK マップ コントロール
* Android マップ コントロール
* Power BI 視覚エフェクト

## <a name="map-style-accessibility"></a>マップ スタイルのアクセシビリティ

対話型の Azure Maps マップ コントロールでは、マップ スタイルのベクター タイルを使用して、マップが表示している領域を説明するようにスクリーン リーダーを起動します。 色のコントラストに関して言えば、いくつかのマップ スタイルも完全にアクセス可能に設計されています。 次の表では、各マップ スタイルでサポートされるアクセシビリティ機能の詳細を示しています。

| [Map style] (地図のスタイル)  | 色のコントラスト | スクリーン リーダー | メモ |
|------------|----------------|---------------|-------|
| `blank` | 該当なし | いいえ | 独自のタイルを基本マップとして使用したい、または背景なしでデータを表示したい開発者に役立つ空白のキャンバス。 スクリーン リーダーは、記述についてはベクター タイルに依存しません。  |
| `blank_accessible` | 該当なし  | はい | 内部では、このマップ スタイルではマップのレンダリングに使用されるベクター タイルを読み込み続けますが、そのデータを透過的にします。 このようにして、データは引き続き読み込まれ、スクリーン リーダーを起動するために使用できます。 |
| `grayscale_dark` | 部分的 | はい | このマップ スタイルは主にビジネス インテリジェンス シナリオ用に設計されていますが、気象レーダー画像などのカラフルなレイヤーをオーバーレイする場合にも役立ちます。 |
| `grayscale_light` | 部分的 | はい | このマップ スタイルは、主にビジネス インテリジェンス シナリオ用に設計されています。 |
| `high_contrast_dark` | はい | はい | ダーク設定のハイ コントラスト モードでユーザーが完全にアクセス可能なマップ スタイル。 マップが読み込まれると、ハイ コントラスト設定が自動的に検出されます。 |
| `high_contrast_light` | はい | はい | ライト設定のハイ コントラスト モードでユーザーが完全にアクセス可能なマップ スタイル。 マップが読み込まれると、ハイ コントラスト設定が自動的に検出されます。 |
| `night` | 部分的 | はい | このスタイルは、照明が暗い状況下で、ユーザーが明るいマップによって感覚を超えたくない場合に使用するように設計されています。 |
| `road` | 部分的 | はい | これは、Azure Maps のメインのカラフルな道路マップ スタイルです。 さまざまな色の数と重複する可能性のある色の組み合わせにより、100% アクセス可能にすることはほぼ不可能です。 しかし、このマップ スタイルは定期的なアクセシビリティ テストを経て、ラベルが読みやすくなるように必要に応じて改善されています。 |
| `road_shaded_relief` | 部分的 | はい | これは、メインの道路マップ スタイルとほぼ同じスタイルですが、背景にタイル レイヤーが追加されています。これにより、高レベルで縮小すると、山や土地被覆の色づけで影付きレリーフが追加されます。 |
| `satellite` | 該当なし | はい | 純粋に衛星および航空映像、ラベルなし、または道路線。 ベクター タイルは、バックグラウンドで読み込まれてスクリーン リーダーを起動し、`satellite_with_roads` に切り替えるときの移行をスムーズにします。 |
| `satellite_with_roads` | いいえ | はい | ラベルと道路線がオーバーレイされた衛星および航空映像。 グローバルな規模では、オーバーレイされたデータと画像の間で発生する可能性のある色の組み合わせは無限にあります。 多くの一般的なシナリオではラベルを読み取りやすくすることに重点を置いていますが、場所によっては、背景画像との色のコントラストによってラベルが読みにくくなる場合もあります。 |

## <a name="next-steps"></a>次のステップ

Azure Maps でマップ スタイルを設定する方法について説明します。

> [!div class="nextstepaction"]
> [マップ スタイルを選択する](./choose-map-style.md)
