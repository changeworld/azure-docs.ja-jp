---
title: クエリの要件
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: c7d038153385c2d36d48f660a9a4e2bab29a3c45
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111894008"
---
クエリ内で `@StartTime` パラメーターを使用し、1 つのタイムスタンプのメトリック データを取得します。 Metrics Advisor によって、クエリの実行時、パラメーターが `yyyy-MM-ddTHH:mm:ss` 形式の文字列に置換されます。

> [!IMPORTANT]
> クエリからは、各タイムスタンプで、ディメンションの各組み合わせに対して多くても 1 つのレコードが返される必要があります。 また、クエリによって返されるすべてのレコードで、タイムスタンプが同じになっている必要があります。 Metrics Advisor によってタイムスタンプごとにこのクエリが実行され、データが取り込まれます。 詳細と例については、[クエリの FAQ セクション](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)を参照してください。 