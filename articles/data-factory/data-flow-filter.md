---
title: Azure Data Factory のマッピング データ フローのフィルター変換
description: Azure Data Factory のマッピング データ フローのフィルター変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 2afe079c346a15ec212664ce022ac5e2926b12d4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387806"
---
# <a name="azure-data-factory-filter-transformation"></a>Azure Data Factory のフィルター変換



フィルター変換では、行のフィルター処理が実行されます。 フィルター条件を定義する式を作成します。 テキスト ボックスをクリックして、式ビルダーを起動します。 式ビルダーの中で、現在のデータ ストリームから次の変換に渡すことを許可する行を制御する (フィルター処理する) フィルター式を作成します。 フィルター変換は SQL ステートメントの WHERE 句と考えてください。

## <a name="filter-on-loan_status-column"></a>loan_status 列をフィルター処理:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Movies デモの year 列をフィルター処理:

```
year > 1980
```

## <a name="next-steps"></a>次の手順

列フィルター変換である[選択変換](data-flow-select.md)を試す
