---
title: Azure Data Factory Data Flow の派生列変換
description: Azure Data Factory Data Flow の派生列変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aee005aed52563e214e24148af2563fd7869de76
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271431"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Azure Data Factory Data Flow の派生列変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

データ フロー内に新しい列を生成したり、既存のフィールドを変更したりするには、派生列変換を使用します。

![派生列](media/data-flow/dc1.png "派生列")

単一の派生列変換で複数の派生列アクションを実行できます。 単一の変換手順で複数の列を変換するには、[列の追加] をクリックします。

[列] フィールドで、新しい派生値で上書きする既存の列を選択するか、または [新しい列の作成] をクリックして新しく派生した値で新しい列を生成します。

式のテキスト ボックス内をクリックすると式ビルダーが開き、式の関数を使って派生列の式を作成できます。
