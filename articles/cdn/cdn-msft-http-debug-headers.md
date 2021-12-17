---
title: Microsoft からの Azure CDN に対するデバッグ HTTP ヘッダー | Microsoft Docs
description: デバッグ キャッシュ要求ヘッダーでは、要求された資産に適用されるキャッシュ ポリシーに関する追加情報が提供されます。 これらのヘッダーは、Microsoft からの Azure CDN に固有のものです。
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: duau
ms.openlocfilehash: 5253d700f271ed18f6e1eae5488184da8ed6db7f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450554"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Microsoft からの Azure CDN に対するデバッグ HTTP ヘッダー
デバッグ応答ヘッダー `X-Cache` では、コンテンツ提供元の CDN スタックのレイヤーに関する詳細が提供されます。 このヘッダーは、Microsoft からの Azure CDN に固有のものです。

### <a name="response-header-format"></a>応答ヘッダーの形式

ヘッダー | 説明
-------|------------
X-Cache:TCP_HIT | このヘッダーは、コンテンツが CDN エッジ キャッシュから提供されているときに返されます。 
X-Cache: TCP_REMOTE_HIT | このヘッダーは、コンテンツが CDN リージョン キャッシュ (配信元シールド レイヤー) から提供されているときに返されます。
X-Cache: TCP_MISS | このヘッダーは、キャッシュ ミスがあり、コンテンツが配信元から提供されているときに返されます。
X-Cache: PRIVATE_NOSTORE | このヘッダーは、Cache-Control 応答ヘッダーが private または no-store のいずれかに設定されているため、要求をキャッシュできないときに返されます。
X-Cache: CONFIG_NOCACHE | このヘッダーは、要求が CDN プロファイルにキャッシュされないように構成されている場合に返されます。
X-Cache: N/A | このヘッダーは、署名された URL とルール セットによって拒否された要求の場合に返されます。

Azure CDN でサポートされている HTTP ヘッダーの詳細については、「[Front Door からバックエンド](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend)」を参照してください。
