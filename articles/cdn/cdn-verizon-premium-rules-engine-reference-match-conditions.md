---
title: Azure CDN from Verizon Premium ルール エンジンの一致条件 | Microsoft Docs
description: Azure Content Delivery Network from Verizon Premium ルール エンジンの一致条件に関するリファレンス ドキュメント。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: ecec7c3c5e4cc72ee637eb91033b9f2b0f158f1a
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872554"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN from Verizon Premium ルール エンジンの一致条件

この記事では、Azure Content Delivery Network (CDN) from Verizon Premium [ルール エンジン](cdn-verizon-premium-rules-engine.md)で利用できる一致条件について詳しく説明します。

ルールの 2 番目の部分は、一致条件です。 一致条件とは、要求の特定の種類を識別するものであり、その種類に対して一連の機能が実行されます。

たとえば、以下のような場合に一致条件を使用できます。

- 特定の場所でコンテンツの要求をフィルター処理する。
- 特定の IP アドレスまたは国/地域から生成された要求をフィルター処理する。
- ヘッダー情報別に要求をフィルター処理する。

最新の一致条件については、[Verizon のルール エンジン](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio)に関するドキュメントを参照してください。


## <a name="next-steps"></a>次のステップ

- [Azure Content Delivery Network の概要](cdn-overview.md)
- [ルール エンジンのリファレンス](cdn-verizon-premium-rules-engine-reference.md)
- [ルール エンジンの条件式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [ルール エンジンの機能](cdn-verizon-premium-rules-engine-reference-features.md)
- [規則エンジンを使用した既定の HTTP 動作のオーバーライド](cdn-verizon-premium-rules-engine.md)
