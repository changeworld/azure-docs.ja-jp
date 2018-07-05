---
title: Azure CDN の現在の Verizon POP リストの取得 | Microsoft Docs
description: REST API を使用して現在の Verizon POP リストを取得する方法を学習します。
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: f796d18a03e14fdf652af72366762e1365523f09
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754413"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Azure CDN の現在の Verizon POP リストの取得

REST API を使用して、Verizon の POP (point of presence) サーバーの IP アドレスのセットを取得できます。 これらの POP サーバーは、Verizon プロファイル (**Azure CDN Standard from Verizon** または **Azure CDN Premium from Verizon**) で Azure Content Delivery Network (CDN) エンドポイントに関連付けられている配信元サーバーに要求を送信します。 この IP アドレスのセットは POP に要求を送信するときにクライアントが認識する IP アドレスとは異なることに注意してください。 

POP リストを取得するための REST API 操作の構文については、「[Edge Nodes - List (エッジ ノード - リスト)](https://docs.microsoft.com/rest/api/cdn/edgenodes/list)」を参照してください。

## <a name="typical-use-case"></a>一般的なユース ケース

セキュリティ上の目的で、この IP リストを使用して、配信元サーバーへの要求が有効な Verizon POP からのみ行われるようにすることができます。 たとえば、CDN エンドポイントの配信元サーバーのホスト名または IP アドレスを発見しただれかが、Azure CDN によって提供されるスケーリングおよびセキュリティ機能をバイパスして配信元サーバーに直接要求を行う可能性があります。 返されるリスト内の IP アドレスを配信元サーバー上の唯一の許可される IP アドレスとして設定することで、このシナリオを回避できます。 確実に最新の POP リストを使用するには、POP リストを少なくとも 1 日に 1 回取得します。 

## <a name="next-steps"></a>次の手順

REST API の詳細については、「[Azure CDN REST API](https://docs.microsoft.com/en-us/rest/api/cdn/)」を参照してください。