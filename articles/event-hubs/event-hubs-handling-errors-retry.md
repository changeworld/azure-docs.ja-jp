---
title: "Azure Event Hubs のエラー処理に関するベスト プラクティス | Microsoft Docs"
description: "Azure Event Hubs によるエラー処理と再試行"
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
ms.openlocfilehash: 200bc0828574bca72739dea5badb22da129cd896
ms.lasthandoff: 04/12/2017

---

# <a name="-event-hubs-best-practices-for-handling-errors-and-retry"></a>🔧 Event Hubs のエラー処理と再試行に関するベスト プラクティス

> [!NOTE]
> 
> このトピックはまだ記述されていません。 
>
> 皆様のご協力を歓迎いたします。このコンテンツの範囲と取り上げ方についてご指南ください。 [GitHub](https://github.com/Azure/azure-event-hubs/issues/305) でこの件の状況を確認し、ご意見をお寄せいただけます。
> 
> 投稿方法については、[GitHub](https://github.com/Microsoft/azure-docs/blob/master/contributor-guide/contributor-guide-index.md) をご覧ください。

この記事では、次の内容を取り上げます。

- どのようなエラーがありますか。
- エラーが出るとどうなりますか。
- 再試行する必要があるエラーとは、どのような種類のエラーでしょうか。 再試行の必要がないエラーとはどのようなものでしょうか。
- カスタムの再試行ポリシー

