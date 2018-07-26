---
title: パターン ベースのエンティティでのロールの使用方法について - Azure | Microsoft Docs
description: パターン ベースのエンティティでロールを使用して、コンテキスト エンティティ サブタイプに名前を指定する方法を説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: d2692cdce9da7428bd7b30c4feaf7347792618f5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222705"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>パターンのエンティティ ロールはコンテキスト サブタイプです
ロールは、[パターン](luis-concept-patterns.md)でのみ使用されるエンティティの名前付きコンテキスト サブタイプです。

たとえば、発話 `buy a ticket from New York to London` では、ロンドンとニューヨークは両方とも都市ですが、文章内での意味はそれぞれ異なります。 ニューヨークは出発地、ロンドンは目的地です。 

ロールでは、こうした違いに対して名前が付けられます。

|エンティティ|Role|目的|
|--|--|--|
|Location|origin|飛行機が離陸する場所|
|Location|destination|飛行機が着陸する場所|

## <a name="how-are-roles-used-in-patterns"></a>パターンでのロールの使用方法
パターンのテンプレートの発話では、ロールが発話内で使用されます。 

```
buy a ticket from {Location:origin} to {Location:destination}
```

## <a name="role-syntax-in-patterns"></a>パターンでのロールの構文
エンティティとロールは、かっこ `{}` で囲まれています。 エンティティとロールは、コロンで区切られています。 

## <a name="roles-versus-hierarchical-entities"></a>ロールと階層エンティティ
階層エンティティはロールと同じコンテキスト情報を提供しますが、提供先は**意図**の発話だけになります。 同様に、ロールも階層エンティティと同じコンテキスト情報を提供します。ただし、その情報は、**パターン**でのみ提供されます。

|コンテキストでの学習|使用される場所|
|--|--|
|階層エンティティ|意図|
|roles|パターン|

## <a name="next-steps"></a>次の手順

* [ロール](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)を追加する方法を確認します
