---
title: クエリの要件
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 68b5946cb21bef1c8979a94c8780682cfe9defa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376304"
---
クエリ内で `@StartTime` パラメーターを使用して、特定のタイムスタンプのメトリック データを取得します。 これは、`yyyy-MM-ddTHH:mm:ss` 形式の文字列に置き換えられます。 

> [!IMPORTANT]
> 必ず、**1 つのタイムスタンプ**のメトリック データのみがクエリから返されるようにしてください。 Metrics Advisor は、すべてのタイムスタンプに対してクエリを実行して、対応するメトリック データを取得します。 たとえば、"*日単位*" の細分性でメトリックを検索する場合、クエリには、1 回の実行で 1 つのタイムスタンプ (`2020-06-21T00:00:00Z` など) のみを含める必要があります。 
