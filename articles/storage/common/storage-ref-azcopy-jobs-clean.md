---
title: azcopy jobs clean | Microsoft Docs
description: この記事では、azcopy jobs clean コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518191"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

すべてのジョブのログ ファイルとプラン ファイルをすべて削除します

```
azcopy jobs clean [flags]
```

## <a name="examples"></a>例

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>オプション

**-h, --help**                clean のヘルプを表示します。

**--with-status** string   この状態のジョブのみが削除されます。使用できる値は次のとおりです。Canceled、Completed、Failed、InProgress、All (既定値は "All")

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

**--cap-mbps uint32**      転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。

**--output-type** string   コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は 'text' です (既定値 "text")。

## <a name="see-also"></a>関連項目

- [azcopy jobs](storage-ref-azcopy-jobs.md)
