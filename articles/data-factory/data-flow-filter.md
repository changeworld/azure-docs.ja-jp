---
title: Azure Data Factory の Mapping Data Flow のフィルター変換
description: Azure Data Factory の Mapping Data Flow のフィルター変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b7e7b123560aae3a2d3086c8536969297d31f7ba
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271407"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Azure Data Factory の Mapping Data Flow のフィルター変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

フィルター変換では、行のフィルター処理が実行されます。 フィルター条件を定義する式を作成します。 テキスト ボックスをクリックして、式ビルダーを起動します。 式ビルダーの中で、現在のデータ ストリームから次の変換に渡すことを許可する行を制御する (フィルター処理する) フィルター式を作成します。

oan_status 列をフィルター処理:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Movies デモの year 列をフィルター処理:

```
year > 1980
```
