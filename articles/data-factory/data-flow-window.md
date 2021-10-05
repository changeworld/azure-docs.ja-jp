---
title: マッピング データ フローでのウィンドウ変換
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics パイプラインでマッピング データ フロー ウィンドウ変換を使用する方法を説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: a913e7a7c5e3ce24df649cb0f1a670650992ac52
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059665"
---
# <a name="window-transformation-in-mapping-data-flow"></a>マッピング データ フローでのウィンドウ変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

ウィンドウ変換では、データ ストリームに含まれる列のウィンドウ ベースの集計を定義します。 式ビルダーで、データまたは時間ウィンドウに基づくさまざまな種類の集計 (SQL OVER 句の LEAD、LAG、NTILE、CUMEDIST、RANK など) を定義できます。 これらの集計を含む新しいフィールドが出力に生成されます。 省略可能な group-by フィールドを含めることもできます。

:::image type="content" source="media/data-flow/windows1.png" alt-text="メニューから選択された [ウィンドウ化] を示しているスクリーンショット。":::

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IAVu]

## <a name="over"></a>Over
ウィンドウ変換のために列データのパーティション分割を設定します。 SQL では Over 句の ```Partition By``` がこれに相当します。 パーティション分割に使用する計算または式を作成する場合は、列名にポインターを合わせて、[計算列] を選択します。

:::image type="content" source="media/data-flow/windows4.png" alt-text="[Over] タブが選択されている [ウィンドウ化設定] を示しているスクリーンショット。":::

## <a name="sort"></a>並べ替え
Over 句の別の部分では、```Order By``` を設定します。 これにより、データの並べ替え順序が設定されます。 この列フィールドの計算値を並べ替えるための式を作成することもできます。

:::image type="content" source="media/data-flow/windows5.png" alt-text="[並べ替え] タブが選択されている [ウィンドウ化設定] を示しているスクリーンショット。":::

## <a name="range-by"></a>Range By
次に、ウィンドウ枠を無制限または制限ありに設定します。 無制限のウィンドウ枠を設定するには、スライダーを両端の [Unbounded]\(無制限\) に設定します。 [Unbounded]\(無制限\) と [現在の行] の間の設定にする場合は、オフセットの開始値と終了値を設定する必要があります。 どちらの値も正の整数になります。 相対値か、自分のデータの値を使用できます。

ウィンドウのスライダーでは 2 つの値を設定します。現在の行よりも前の値と、現在の行より後の値です。 開始と終了のオフセットは、スライダーの 2 つのセレクターと一致します。

:::image type="content" source="media/data-flow/windows6.png" alt-text="[Range by]\(範囲\) タブが選択されている [ウィンドウ化設定] を示しているスクリーンショット。":::

## <a name="window-columns"></a>ウィンドウの列
最後に、式ビルダーを使って、RANK、COUNT、MIN、MAX、DENSE RANK、LEAD、LAG など、データ ウィンドウで使用する集計を定義します。

:::image type="content" source="media/data-flow/windows7.png" alt-text="ウィンドウ化アクションの結果を示しているスクリーンショット。":::

式ビルダーを通じて Data Flow 記述言語で使用できる集計関数と分析関数の全一覧については、「[マッピング データ フローでのデータ変換式](data-flow-expression-functions.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

単純なグループ別集計が必要な場合は、[集計変換](data-flow-aggregate.md)を使用してください。
