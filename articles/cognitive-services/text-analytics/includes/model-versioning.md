---
title: モデルのバージョン管理
titleSuffix: Azure Cognitive Services
description: V3 エンドポイントでモデル バージョンを指定する
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021127"
---
Text Analytics API のバージョン 3 では、データに使用する Text Analytics モデルを選択できます。 要求でモデルのバージョンを選択するには、省略可能な `model-version` パラメーターを使用します。 このパラメーターが指定されていない場合、API の既定値は最新の安定したモデルのバージョンである `latest` になります。

使用可能なモデル バージョン:
* `2019-10-01` (`latest`)

v3 エンドポイントからの各応答には、使用されたモデルのバージョンを指定する `model-version` フィールドが含まれています。

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
