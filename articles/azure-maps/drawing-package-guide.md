---
title: Microsoft Azure Maps Creator (プレビュー) の Drawing パッケージ ガイド
description: Azure Maps Conversion サービス用に Drawing パッケージを準備する方法
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: eb74ffb55536b18e48a4b5dcea83fac4db20abe0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727988"
---
# <a name="conversion-drawing-package-guide"></a>Conversion Drawing パッケージ ガイド

このガイドでは、特定の CAD コマンドを使用して [Azure Maps Conversion サービス](/rest/api/maps/v2/conversion)用の Drawing パッケージを準備し、Conversion サービス用に DWG ファイルとマニフェスト ファイルを正しく準備する方法について説明します。

最初に、Drawing パッケージが .zip 形式で、次のファイルが含まれていることを確認します。

* DWG 形式の 1 つ以上の描画ファイル。
* DWG ファイルと施設のメタデータを記述するマニフェスト ファイル。

このガイドと共に参照する独自のパッケージがない場合、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)をダウンロードできます。

CAD ソフトウェアを選択して、施設の描画ファイルを開いて準備できます。 ただし、このガイドは、Autodesk の AutoCAD® ソフトウェアを使用して作成されています。 このガイドで参照されているコマンドは、Autodesk の AutoCAD® ソフトウェアを使用して実行することを意図しています。  

>[!TIP]
>このガイドで説明されていない描画パッケージの要件の詳細については、「[Drawing パッケージの要件](drawing-requirements.md)」を参照してください。

## <a name="glossary-of-terms"></a>用語集

このガイドを読む際に簡単に参照できるように、重要な用語と定義を次に示します。

| 用語  | 定義 |
|:-------|:------------|
| レイヤー | 描画ファイルからの AutoCAD DWG レイヤー。|
| Entity | 描画ファイルからの AutoCAD DWG レイヤー。 |
| Level  |設定した高度にある建物の領域。 たとえば、ビルのフロアなどです。    |
| 特徴量 | ジオメトリをより多くのメタデータ情報と組み合わせたオブジェクト。 |
| 地物クラス | 地物の一般的なブループリント。 たとえば、*ユニット* は地物クラスであり、*オフィス* は地物です。 |

## <a name="step-1-dwg-file-requirements"></a>手順 1: DWG ファイルの要件

Conversion サービス用の施設の描画ファイルを準備する場合、次の暫定的な要件と推奨事項に従う必要があります。

* 施設の描画ファイルは DWG 形式で保存する必要があります。これは、Autodesk の AutoCAD® ソフトウェアのネイティブ ファイル形式です。

* Conversion サービスは、AutoCAD DWG ファイル形式で機能します。 AC1032 は、DWG ファイルの内部形式のバージョンです。内部 DWG ファイル形式のバージョンには  AC1032 を選択することをお勧めします。

* DWG ファイルには、1 つのフロアのみを含むことができます。 施設のフロアは、個別の DWG ファイルに含める必要があります。  そのため、施設に 5 つのフロアがある場合、5 つの個別の DWG ファイルを作成する必要があります。

## <a name="step-2-prepare-the-dwg-files"></a>手順 2: DWG ファイルを準備する

このガイドのこの部分では、CAD コマンドを使用して、DWG ファイルが Conversion サービスの要件を満たしていることを確認する方法について説明します。

CAD ソフトウェアを選択して、施設の描画ファイルを開いて準備できます。 ただし、このガイドは、Autodesk の AutoCAD® ソフトウェアを使用して作成されています。 このガイドで参照されているコマンドは、Autodesk の AutoCAD® ソフトウェアを使用して実行することを意図しています。  

### <a name="bind-external-references"></a>外部参照のバインド

施設の各フロアは、1 つの DWG ファイルとして提供する必要があります。 外部参照がない場合、それ以上何も行う必要はありません。 ただし、外部参照がある場合、1 つの描画にバインドする必要があります。 外部参照をバインドするには、`XREF` コマンドを使用できます。 バインド後、各外部参照描画がブロック参照として追加されます。 これらのレイヤーのいずれかを変更する必要がある場合、`XPLODE` コマンドを使用してブロック参照を分解してください。

### <a name="unit-of-measurement"></a>測定の単位

描画は、任意の測定単位を使用して作成できます。 ただし、すべての描画で同じ測定単位を使用する必要があります。 そのため、施設の 1 つのフロアがミリを使用している場合、他のすべてのフロア (描画) もミリ単位にする必要があります。 `UNITS` コマンドを使用して、測定単位を確認または変更できます。

次の画像は、Autodesk の AutoCAD® ソフトウェア内の [描画単位] ウィンドウを示しています。このウィンドウを使用して測定単位を確認できます。  

:::image border="true" type="content" source="./media/drawing-package-guide/units.png" alt-text="Autodesk の AutoCAD® ソフトウェア内の [描画単位] ウィンドウ":::

### <a name="alignment"></a>Alignment

施設の各フロアは、個々の DWG ファイルとして提供されます。 その結果、互いに積み上げ時にフロアが完全に整列されない可能性があります。 Azure Maps Conversion サービスでは、すべての描画を物理空間に合わせて配置する必要があります。 配置を確認するには、複数のフロアにまたがるエレベーターや列などの参照ポイントを使用します。 すべてのフロアを表示するには、新しい描画を開き、`XATTACH` コマンドを使用してすべてのフロアの描画を読み込みます。 配置の問題を修正する必要がある場合、参照ポイントと `MOVE` コマンドを使用して、必要なフロアを再配置できます。

### <a name="layers"></a>レイヤー

描画の各レイヤーに、1 つの特徴クラスのエンティティが含まれている必要があります。 レイヤーに壁のエンティティが含まれている場合、ユニットやドアなどの他の特徴を含めることはできません。  ただし、特徴クラスは複数のレイヤーに分割できます。 たとえば、壁エンティティを含む描画に 3 つのレイヤーを含めることができます。

さらに、各レイヤーにはサポートされているエンティティ型の一覧が含まれ、その他の型は無視されます。 たとえば、ユニット ラベル レイヤーが単一行テキストのみをサポートしている場合、同じレイヤー上の複数行テキストまたはポリラインは無視されます。

レイヤーと特徴クラスの理解を深めるには、「[Drawing パッケージの要件](drawing-requirements.md)」を参照してください。

### <a name="exterior-layer"></a>Exterior レイヤー

1 つのレベルの特徴は、1 つまたは複数のレイヤーから作成されます。 このレベルの特徴は、レベルの境界を定義します。 外部レイヤーのエンティティがレイヤーの要件を満たしていることを確認することが重要です。 たとえば、閉じたポリラインはサポートされていますが、開いたポリラインはサポートされていません。 外部レイヤーが複数の線分で構成されている場合、1 つの閉じたポリラインとして提供する必要があります。 複数の線分を結合するには、すべての線分を選択し、`JOIN` コマンドを使用します。

次の画像はサンプル パッケージから抽出したもので、施設の外部レイヤーを赤で示しています。 見やすくするためにユニット レイヤーをオフにしています。

:::image border="true" type="content" source="./media/drawing-package-guide/exterior.png" alt-text="施設の外部レイヤー。":::

### <a name="unit-layer"></a>Unit レイヤー

ユニットは、オフィス、廊下、階段、エレベーターなど、建物内の移動可能なスペースです。各ユニットを表すためには、多角形、閉じたポリライン、円、閉じた楕円などの閉じたエンティティ型が必要です。 そのため、壁とドアだけでは、ユニットを表すエンティティが存在しないため、ユニットを作成できません。  

次の画像は[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)から抽出したもので、ユニット ラベル レイヤーおよびユニット レイヤーを赤で示しています。 見やすくするために他のすべてのレイヤーをオフにしています。 また、各ユニットが閉じたポリラインであることを示すために 1 つのユニットが選択されています。  

:::image border="true" type="content" source="./media/drawing-package-guide/unit.png" alt-text="施設のユニット レイヤー。":::

### <a name="unit-label-layer"></a>ユニット ラベル レイヤー

ユニットに name プロパティを追加する場合、ユニット ラベル用に別のレイヤーを追加する必要があります。 ラベルは、ユニットの境界内にある単一行のテキスト エンティティとして指定する必要があります。 対応する unit プロパティをマニフェスト ファイルに追加する必要があります。ここで、`unitName` はテキストのコンテンツと一致します。  サポートされているすべての unit プロパティについては、「[`unitProperties`](#unitproperties)」を参照してください。

### <a name="door-layer"></a>Door レイヤー

ドアは省略可能です。 ただし、ユニットのエントリ ポイントを指定する場合はドアを使用できます。 ドア レイヤーでサポートされているエンティティ型の場合、ドアは任意の方法で描画できます。 ドアはユニットの境界と重なっている必要があります。その後、ユニットの重なり合うエッジは、ユニットの開口部として処理されます。  

次の画像は[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)から抽出したもので、ユニット境界に描画されたドア (赤で描画) を含むユニットを示しています。

:::image border="true" type="content" source="./media/drawing-package-guide/door.png" alt-text="施設のドア レイヤー。":::

### <a name="wall-layer"></a>Wall レイヤー

壁レイヤーは、壁や列などの施設の物理的な範囲を表すことを意図しています。 Azure Maps Conversion サービスは、経路に障害がある物理構造としての壁を認識します。 この点を念頭に置くと、壁は、見ることはできるが通り抜けることはできない物理的な構造と考える必要があります。 見えないものは、このレイヤーではキャプチャされません。 壁に内壁または内部の列がある場合、外部をキャプチャするだけで済みます。  

## <a name="step-3-prepare-the-manifest"></a>手順 3: マニフェストを準備する

Drawing パッケージのマニフェストは JSON ファイルです。 マニフェストは、施設の DWG ファイルとメタデータを読み取る方法を Azure Maps Conversion サービスに指示します。 この情報の例としては、各 DWG レイヤーに含まれる具体的な情報や施設の地理的な場所などがあります。

変換を正常に実行するには、すべての「required」プロパティを定義する必要があります。 サンプル マニフェスト ファイルは[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)内にあります。 このガイドでは、マニフェストでサポートされるプロパティについては説明しません。 マニフェストのプロパティの詳細については、「[マニフェスト ファイルのプロパティ](drawing-requirements.md#manifest-file-requirements)」を参照してください。

### <a name="building-levels"></a>ビルド レベル

ビルド レベルでは、どのレベルでどの DWG ファイルを使用するかを指定します。 レベルには、各レベルの垂直方向の順序を示すレベル名と序数が必要です。 すべての施設には、施設の地上階となる序数 0 が必要です。 図面が施設のいくつかのフロアを占有する場合でも、序数 0 を指定する必要があります。 たとえば、15 階から 17 階をそれぞれ序数 0 から 2 として定義できます。

次の例は[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)から抽出したものです。 この施設には地下、地上階、レベル 2 の 3 つのレベルがあります。 ファイル名には、.zip Drawing パッケージ内のマニフェスト ファイルを基準としたファイルの完全なファイル名とパスが含まれています。  

```json
    "buildingLevels": { 
      "levels": [ 
       { 
           "levelName": "Basement", 
           "ordinal": -1, 
           "filename": "./Basement.dwg" 
            }, { 

            "levelName": "Ground", 
            "ordinal": 0, 
            "filename": "./Ground.dwg" 
            }, { 

            "levelName": "Level 2", 
            "ordinal": 1, 
             "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
```

### <a name="georeference"></a>georeference

`georeference` オブジェクトを使用して、施設の地理的な場所および施設の回転量を指定します。 描画の始点は、`georeference` オブジェクトで指定された緯度と経度と一致する必要があります。 真北と図面の垂直 (Y) 軸との間の時計回りの角度 (度単位)。  

### <a name="dwglayers"></a>dwgLayers

`dwgLayers` オブジェクトを使用して、特徴クラスを検索できる DWG レイヤー名を指定します。 プロパティが変換された施設を受け取るには、正しいレイヤー名を指定することが重要です。 たとえば、DWG 壁レイヤーは、ユニット レイヤーとしてではなく、壁レイヤーとして指定する必要があります。 図面には、家具や配管などの他のレイヤーを含めることができます。ただし、マニフェストに指定されていない場合は、Azure Maps Conversion サービスによって無視されます。  

マニフェストの `dwgLayers` オブジェクトの次の例。  

```json
"dwgLayers": { 
        "exterior": [ 
            "OUTLINE" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    } 
```

次の画像は、Autodesk の AutoCAD®ソフトウェアで表示される、対応する DWG 描画のレイヤーを示しています。

:::image border="true" type="content" source="./media/drawing-package-guide/layer.png" alt-text="Autodesk の AutoCAD® ソフトウェアの DWG レイヤー":::

### <a name="unitproperties"></a>unitProperties

`unitProperties` オブジェクトを使用すると、DWG ファイルでは実行できないユニットのその他のプロパティを定義できます。 例としては、ユニットのディレクトリ情報やユニットのカテゴリの種類などがあります。 unit プロパティは、`unitLabel` レイヤーのラベルと一致している `unitName` オブジェクトを持つことによってユニットに関連付けられています。  

次の画像は[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)から抽出したものです。 これには、マニフェストの unit プロパティに関連付けられているユニット ラベルが表示されます。

:::image border="true" type="content" source="./media/drawing-package-guide/unit-property.png" alt-text="マニフェストの unity プロパティに関連付けられるユニット ラベル":::

次のスニペットは、ユニットに関連付けられている unit プロパティ オブジェクトを示しています。  

```json
 "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
```

## <a name="step-4-prepare-the-drawing-package"></a>手順 4: 描画パッケージを準備する

これで、Azure Maps Conversion サービスの要件を満たすために、すべての DWG 図面が準備されました。 また、施設を説明するためにマニフェスト ファイルも作成されています。 すべてのファイルを 1 つのアーカイブ ファイルに圧縮して、`.zip` 拡張子を付ける必要があります。 マニフェスト ファイルの名前は `manifest.json` であり、zip 形式のパッケージのルート ディレクトリに配置されていることが重要です。 ファイル名にマニフェストへの相対パスが含まれている場合、zip 形式のパッケージの任意のディレクトリに他のすべてのファイルを含めることができます。 描画パッケージの例については、「[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Creator 屋内マップを作成する](tutorial-creator-indoor-maps.md)
