---
title: クエリの要件
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931667"
---
クエリ内で `@StartTime` パラメーターを使用して、特定のタイムスタンプのメトリック データを取得します。 これは、`yyyy-MM-ddTHH:mm:ss` 形式の文字列に置き換えられます。 

> [!IMPORTANT]
> 必ず、**1 つのタイムスタンプ**のメトリック データのみがクエリから返されるようにしてください。 Metrics Advisor は、すべてのタイムスタンプに対してクエリを実行して、対応するメトリック データを取得します。 たとえば、"*日単位*" の細分性でメトリックを検索する場合、クエリには、1 回の実行で 1 つのタイムスタンプ (`2020-06-21T00:00:00Z` など) のみを含める必要があります。 
