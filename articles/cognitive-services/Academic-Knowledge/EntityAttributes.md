---
title: Academic Graph エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で Academic Graph と共に使用できるエンティティ属性について説明します。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a203fdf6562dabb1b9d6e8ab5bb8f46ff6d5dc27
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902792"
---
# <a name="entity-attributes"></a>エンティティの属性

Academic Graph は、7 種類のエンティティで構成されます。 すべてのエンティティにエンティティ ID とエンティティ型があります。

## <a name="common-entity-attributes"></a>共通のエンティティ属性
Name    |説明                |type       | [操作]
------- | ------------------------- | --------- | ----------------------------
ID      |エンティティ ID                  |Int64      |等しい
Ty      |エンティティの種類                |enum   |等しい

## <a name="entity-type-enum"></a>エンティティ型の列挙
Name                                                            |value
----------------------------------------------------------------|-----
[論文](PaperEntityAttributes.md)                               |0
[著者](AuthorEntityAttributes.md)                             |1
[ジャーナル](JournalEntityAttributes.md)                           |2
[会議 (シリーズ)](JournalEntityAttributes.md)                 |3
[会議 (インスタンス)](ConferenceInstanceEntityAttributes.md)    |4
[組織](AffiliationEntityAttributes.md)                   |5
[研究分野](FieldsOfStudyEntityAttributes.md)                      |6

