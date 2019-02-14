---
title: エンティティのロール
titleSuffix: Azure Cognitive Services
description: ロールは、パターンでのみ使用されるエンティティの名前付きコンテキスト サブタイプです。 たとえば、発話 `buy a ticket from New York to London` では、ロンドンとニューヨークは両方とも都市ですが、文章内での意味はそれぞれ異なります。 ニューヨークは出発地、ロンドンは目的地です。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: 958194d49cd403caeaf9dd21dd90a02cab098e45
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881459"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>パターンのエンティティ ロールはコンテキスト サブタイプです
ロールは、[パターン](luis-concept-patterns.md)でのみ使用されるエンティティの名前付きコンテキスト サブタイプです。

たとえば、発話 `buy a ticket from New York to London` では、ロンドンとニューヨークは両方とも都市ですが、文章内での意味はそれぞれ異なります。 ニューヨークは出発地、ロンドンは目的地です。 

ロールでは、こうした違いに対して名前が付けられます。

|エンティティ|Role|目的|
|--|--|--|
|Location|origin|飛行機が離陸する場所|
|Location|destination|飛行機が着陸する場所|
|事前構築済みの datetimeV2|to|終了日|
|事前構築済みの datetimeV2|from|開始日|

## <a name="how-are-roles-used-in-patterns"></a>パターンでのロールの使用方法
パターンのテンプレートの発話では、ロールが発話内で使用されます。 

|エンティティ ロールのあるパターン|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>パターンでのロールの構文
エンティティとロールは、かっこ `{}` で囲まれています。 エンティティとロールは、コロンで区切られています。 


[!INCLUDE [H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="example-role-for-entities"></a>エンティティのロールの例

ロールは、発話内のエンティティのコンテキストから学習した配置にすぎません。 これが最も有用なのは、発話内に同じ種類のエンティティが複数含まれている場合です。 エンティティの種類の例として最もわかりやすいのは、出発地と到着地の区別です。 場所は、多数のさまざまなエンティティの種類で表すことができます。 

ユース ケースの例は、従業員をある部署から別の部署に異動させることです。ここでは、各部署が一覧の項目です。 例:  

`Move [PersonName] from [Department:from] to [Department:to]` 

返される予測では、両方の部署エンティティが JSON 応答内に返され、それぞれにロール名が含まれます。 

## <a name="roles-with-prebuilt-entities"></a>ロールと事前構築済みエンティティ

ロールを事前構築済みエンティティと共に使用して、発話内の事前構築済みエンティティのさまざまなインスタンスに意味を与えます。 

### <a name="roles-with-datetimev2"></a>ロールと datetimeV2

事前構築済みエンティティ datetimeV2 は、発話内のさまざまな日付と時刻の解釈に能力を発揮します。 事前構築済みエンティティの既定の解釈とは異なる日付と日付範囲を指定できます。 

## <a name="next-steps"></a>次の手順

* [ロール](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)を追加する方法を確認します
