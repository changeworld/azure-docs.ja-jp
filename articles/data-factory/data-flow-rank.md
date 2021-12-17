---
title: マッピング データ フローでの順位変換
description: Azure Data Factory または Synapse Analytics パイプラインで、マッピング データ フローの順位変換を使用して、順位列を生成する方法について説明します。
titleSuffix: Azure Data Factory & Azure Synapse
author: kromerm
ms.author: makromer
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3af0355a02badb5aaa5d5b13a51b74392c931d8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059882"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>マッピング データ フローでの順位変換 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

順位変換を使用して、ユーザーが指定した並べ替え条件に基づいて順序付けされた順位を生成します。 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>構成

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="順位設定":::

**大文字と小文字の区別をしない:** 並べ替え列の型が文字列の場合、大文字と小文字の違いが順位付けで考慮されます。 

**高密度:** 有効にすると、順位列は高密度で順位付けされます。 各順位カウントは連続した数値になり、順位値は相互に関連付けられた後にスキップされません。

**順位列:** 生成された順位列の名前。 この列は長整数型になります。

**並べ替え条件:** 並べ替えの基準となり、その列内で順序が並べ替えられる列を選択します。 順序によって並べ替えの優先順位が決まります。

上記の構成では、入力されたバスケットボール データを取得し、"pointsRanking" という順位列を作成します。 列 *PTS* の最大値を持つ行は、*pointsRanking* の値が 1 になります。

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>例

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="順位設定":::

次のコード スニペットには、上記の順位構成に対するデータ フロー スクリプトが含まれています。

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>次の手順

[フィルター変換](data-flow-filter.md)を使用して、順位の値に基づいて行をフィルター処理します。
