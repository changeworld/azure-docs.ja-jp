---
title: "Azure Event Hubs のパフォーマンス管理とスケール管理のベスト プラクティス | Microsoft Docs"
description: "Azure Event Hubs のパフォーマンスとスケールのベスト プラクティス"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/09/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 920378e21985ae6bb63914e6c065314aaf979214
ms.lasthandoff: 04/12/2017

---

# <a name="-event-hubs-best-practices-for-managing-performance-and-scale"></a>🔧 Event Hubs のパフォーマンス管理とスケール管理のベスト プラクティス

> [!NOTE]
> 
> このトピックはまだ記述されていません。 
>
> 皆様のご協力を歓迎いたします。このコンテンツの範囲と取り上げ方についてのご意見をお待ちしています。 [GitHub](https://github.com/Azure/azure-event-hubs/issues/306) でこの件の状況を確認し、ご意見をお寄せいただけます。
> 
> 投稿方法については、[GitHub](https://github.com/Microsoft/azure-docs/blob/master/contributor-guide/contributor-guide-index.md) をご覧ください。

この記事では、次の内容を取り上げます。

- パーティション分割
- スループット単位
- Parallelism
- 一般的なボトルネック


