---
title: パターン ベースのエンティティでのロールの使用方法について
titleSuffix: Azure Cognitive Services
description: ロールは、パターンでのみ使用されるエンティティの名前付きコンテキスト サブタイプです。 たとえば、発話「ニューヨーク発ロンドン着の切符を買う」では、ロンドンとニューヨークは両方とも都市ですが、文章内での意味はそれぞれ異なります。 ニューヨークは出発地、ロンドンは目的地です。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 033e5e5e054b0a29961ad60e72b1466b51d1df0c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035200"
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

|エンティティ ロールのあるパターン|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


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
