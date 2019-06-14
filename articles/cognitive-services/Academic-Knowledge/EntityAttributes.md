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
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340181"
---
# <a name="entity-attributes"></a>エンティティの属性

Academic Graph は、7 種類のエンティティで構成されます。 すべてのエンティティにエンティティ ID とエンティティ型があります。

## <a name="common-entity-attributes"></a>共通のエンティティ属性
Name    |説明                |Type       | Operations
------- | ------------------------- | --------- | ----------------------------
Id      |エンティティ ID                  |Int64      |等しい
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

