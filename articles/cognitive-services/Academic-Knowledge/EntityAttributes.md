---
title: Academic Graph エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で Academic Graph と共に使用できるエンティティ属性について説明します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 596ce35a0d744691edd75d6071d4758691e0031b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705033"
---
# <a name="entity-attributes"></a>エンティティの属性

Academic Graph は、7 種類のエンティティで構成されます。 すべてのエンティティにエンティティ ID とエンティティ型があります。

## <a name="common-entity-attributes"></a>共通のエンティティ属性
名前    |説明                |Type       | Operations
------- | ------------------------- | --------- | ----------------------------
Id      |エンティティ ID                  |Int64      |等しい
Ty      |エンティティの種類                |enum   |等しい

## <a name="entity-type-enum"></a>エンティティ型の列挙
名前                                                            |value
----------------------------------------------------------------|-----
[論文](PaperEntityAttributes.md)                               |0
[著者](AuthorEntityAttributes.md)                             |1
[ジャーナル](JournalEntityAttributes.md)                           |2
[会議 (シリーズ)](JournalEntityAttributes.md)                 |3
[会議 (インスタンス)](ConferenceInstanceEntityAttributes.md)    |4
[組織](AffiliationEntityAttributes.md)                   |5
[研究分野](FieldsOfStudyEntityAttributes.md)                      |6

