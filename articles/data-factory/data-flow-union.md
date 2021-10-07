---
title: マッピング データ フローの和集合変換
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Synapse Analytics 内でのマッピング データ フローの新しい分岐変換について説明します
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 14cf10c51ffe470ff249ff4953f0edd8990aab1b
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059684"
---
# <a name="union-transformation-in-mapping-data-flow"></a>マッピング データ フローの和集合変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

和集合では、複数のデータ ストリームの SQL Union を和集合変換からの新しい出力として使用し、これらのストリームを 1 つに結合します。 各入力ストリームのすべてのスキーマは、結合キーを必要とすることなく、データ フロー内で結合されます。

構成された各行の横の「+」アイコンを選択することにより、データ フロー内の既存の変換からのストリームとソース データの両方を含む、設定テーブルの n 個のストリームを結合できます。

以下は、マッピング データ フローにおける和集合変換の短いビデオ チュートリアルです。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

:::image type="content" source="media/data-flow/union.png" alt-text="和集合変換":::

ここでは、複数のソース (この例では、3 つの異なるソース ファイル) の異種のメタデータを結合して、1 つのストリームにまとめることができます。

:::image type="content" source="media/data-flow/union111.png" alt-text="和集合変換の概要":::

これを行うには、追加するすべてのソースを含めて、[Union Settings]\(和集合設定\) で行を追加します。 共通の参照や結合キーは必要ありません。

:::image type="content" source="media/data-flow/unionsettings.png" alt-text="和集合変換の設定":::

[Select transformation after your Union]\(和集合の後に変換を選択\) を設定すると、重複するフィールド、またはヘッダーのないソースからの名前のないフィールドを名前変更できます。 次の例では、[検査] をクリックして、 3 つの異なるソースからの合計 132 個の列を含む結合されたメタデータを表示します。

:::image type="content" source="media/data-flow/union333.png" alt-text="和集合変換の最終設定":::

## <a name="name-and-position"></a>名前と位置

"名前による和集合" を選択すると、各列の値は、新しい連結メタデータ スキーマで各ソースの対応する列にドロップされます。

"位置による和集合" を選択すると、各列の値は対応するそれぞれのソースの元の位置にドロップされ、以下に示すように、各ソースのデータが同じストリームに追加されたデータの新しい結合ストリームが生成されます。

:::image type="content" source="media/data-flow/unionoutput.png" alt-text="和集合の出力":::

## <a name="next-steps"></a>次のステップ

[結合](data-flow-join.md)変換や[存在](data-flow-exists.md)変換など、同様の変換について調べます。
