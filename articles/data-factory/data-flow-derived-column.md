---
title: Azure Data Factory Mapping Data Flow の派生列変換
description: Azure Data Factory Mapping Data Flow の派生列変換を使用してデータをまとめて変換する方法
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: f53e122eb1b2a5b6dabb9a44aef42394d0c7edb6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999486"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>Mapping Data Flow の派生列変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

データ フロー内に新しい列を生成したり、既存のフィールドを変更したりするには、派生列変換を使用します。

![派生列](media/data-flow/dc1.png "派生列")

単一の派生列変換で複数の派生列アクションを実行できます。 単一の変換手順で複数の列を変換するには、[列の追加] をクリックします。

[列] フィールドで、新しい派生値で上書きする既存の列を選択するか、または [新しい列の作成] をクリックして新しく派生した値で新しい列を生成します。

式のテキスト ボックス内をクリックすると式ビルダーが開き、式の関数を使って派生列の式を作成できます。

## <a name="column-patterns"></a>列パターン

列名がソースからの変数になっている場合、名前付きの列を使用せずに列パターンを使用して、派生列の内部で変換をビルドすることが考えられます。 詳しくは、[スキーマの誤差](concepts-data-flow-schema-drift.md)に関する記事をご覧ください。

![列パターン](media/data-flow/columnpattern.png "列パターン")

## <a name="next-steps"></a>次の手順

詳しくは、[変換用の Data Factory 式言語](http://aka.ms/dataflowexpressions)と[式ビルダー](concepts-data-flow-expression-builder.md)に関する記事をご覧ください。
