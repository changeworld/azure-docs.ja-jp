---
title: パターン ベースのエンティティでのロールの使用方法について - Azure | Microsoft Docs
description: パターン ベースのエンティティでロールを使用して、コンテキスト エンティティ サブタイプに名前を指定する方法を説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: ab6100e33fb767528b87c6afde4c5ef275fc7c81
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2018
ms.locfileid: "35379063"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>パターンのエンティティ ロールはコンテキスト サブタイプです
ロールは、[パターン](luis-concept-patterns.md)でのみ使用されるエンティティの名前付きコンテキスト サブタイプです。

たとえば、発話 `buy a ticket from New York to London` では、ロンドンとニューヨークは両方とも都市ですが、文章内での意味はそれぞれ異なります。 ニューヨークは出発地、ロンドンは目的地です。 

ロールでは、こうした違いに対して名前が付けられます。

|エンティティ|役割|目的|
|--|--|--|
|リージョン|origin|飛行機が離陸する場所|
|リージョン|destination|飛行機が着陸する場所|

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
