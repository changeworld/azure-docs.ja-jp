---
title: Azure Machine Learning データ準備で可能なフィルター式の例 | Microsoft Doc
description: このドキュメントでは、Azure Machine Learning データ準備で可能なフィルター式の例を示します
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: a389007dea344de461b23b96f2942686005aa119
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982394"
---
# <a name="sample-of-filter-expressions-python"></a>フィルター式のサンプル (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


この付録を読む前に、[Python 機能拡張の概要](data-prep-python-extensibility-overview.md)に関する記事をご覧ください。

## <a name="filter-with-equivalence-test"></a>等価テストを使用したフィルター
(数値) Col2 の値が 4 より大きな行だけがフィルターを通ります。 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>複数列を使用したフィルター 
Col1 が値「**Good**」を含んでおり、Col2 が (数値) 値 1 を含んでいる行だけがフィルターを通ります。 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>null に対するテスト フィルター
Col1 に null 値がある行だけがフィルターを通ります。 
```python
    pd.isnull(row["Col1"])
```
