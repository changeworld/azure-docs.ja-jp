---
title: Microsoft からの Azure CDN に対するデバッグ HTTP ヘッダー | Microsoft Docs
description: デバッグ キャッシュ要求ヘッダーでは、要求された資産に適用されるキャッシュ ポリシーに関する追加情報が提供されます。 これらのヘッダーは、Microsoft からの Azure CDN に固有のものです。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 159c4ac852e1caee3794962a262f84baee8b3487
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111959243"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Microsoft からの Azure CDN に対するデバッグ HTTP ヘッダー
デバッグ応答ヘッダー `X-Cache` では、コンテンツ提供元の CDN スタックのレイヤーに関する詳細が提供されます。 このヘッダーは、Microsoft からの Azure CDN に固有のものです。

### <a name="response-header-format"></a>応答ヘッダーの形式

ヘッダー | 説明
-------|------------
X-Cache:TCP_HIT | このヘッダーは、コンテンツが CDN エッジ キャッシュから提供されているときに返されます。 
X-Cache: TCP_REMOTE_HIT | このヘッダーは、コンテンツが CDN リージョン キャッシュ (配信元シールド レイヤー) から提供されているときに返されます。
X-Cache: TCP_MISS | このヘッダーは、キャッシュ ミスがあり、コンテンツが配信元から提供されているときに返されます。 

Azure CDN でサポートされている HTTP ヘッダーの詳細については、「[Front Door からバックエンド](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend)」を参照してください。