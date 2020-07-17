---
title: モデルのバージョン管理
titleSuffix: Azure Cognitive Services
description: V3 エンドポイントでモデル バージョンを指定する
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77089087"
---
Text Analytics API のバージョン 3 では、お使いのデータに対する最新のモデル バージョンを選択できます。 要求で必要なモデルのバージョンを選択するには、省略可能な `model-version` パラメーターを使用します。 このパラメーターが指定されていない場合、API の既定値は最新の安定したバージョンである `latest` になります。 どの要求でも最新のモデルバージョンを使用できますが、バージョンごとに更新される機能は一部だけです。 次の表では、各モデル バージョンで更新された機能について説明します。

| モデル バージョン           | 更新された機能         | 次のための最新バージョン:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | エンティティの認識                      | エンティティの認識                      |
| `2019-10-01`            | エンティティ認識、センチメント分析  | 言語検出、キー フレーズ抽出、センチメント分析|


v3 エンドポイントからの各応答には、使用されたモデルのバージョンを指定する `model-version` フィールドが含まれています。

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
これらのモデル バージョンの更新の詳細については、[新機能](../whats-new.md)に関する記事を参照してください。
