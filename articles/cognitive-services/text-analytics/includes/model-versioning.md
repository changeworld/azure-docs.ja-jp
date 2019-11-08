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
ms.openlocfilehash: 5a06e26e5f1640024e343c714db3df134422115c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488644"
---
Text Analytics API のバージョン 3 では、データに使用する Text Analytics モデルを選択できます。 要求でモデルのバージョンを選択するには、省略可能な `model-version` パラメーターを使用します。 このパラメーターが指定されていない場合、API の既定値は最新の安定したモデルのバージョンである `latest` になります。

使用可能なモデル バージョン:
* `2019-10-01` (`latest`)

v3 エンドポイントからの各応答には、使用されたモデルのバージョンを指定する `model-version` フィールドが含まれています。

```json
{
    “documents”: […]
    “errors”: []
    “model-version”: “2019-10-01”
}
```
