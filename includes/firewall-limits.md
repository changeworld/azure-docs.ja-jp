---
title: インクルード ファイル
description: インクルード ファイル
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238426"
---
| Resource | 既定の制限 |
| --- | --- |
| データ スループット |30 Gbps<sup>1</sup> |
|ルール|10,000 個。すべての規則の種類が結合されます。|
|AzureFirewallSubnet の最小サイズ |/26|
|ネットワークおよびアプリケーション ルールのポート範囲|0-64,000。 現在、この制限を緩和するように取り組んでいます。|
|ルート テーブル|AzureFirewallSubnet には既定で、NextHopType の値が **Internet** に設定された 0.0.0.0/0 ルートがあります。<br><br>Azure Firewall には、インターネットへの直接接続が必要です。 AzureFirewallSubnet が BGP 経由のオンプレミス ネットワークへの既定のルートを学習する場合は、インターネットへの直接接続を保持するために、**NextHopType** の値を **Internet** に設定した 0.0.0.0/0 UDR でこれを上書きする必要があります。 既定では、Azure Firewall はオンプレミス ネットワークへの強制トンネリングをサポートしません。<br><br>ただし、オンプレミス ネットワークへの強制トンネリングが必要な構成の場合、Microsoft は状況に応じてサポートします。 サポートにお問い合わせいただければ、お客様の状況を確認させていただきます。 認められた場合は、Microsoft にて貴社のサブスクリプションをホワイトリストに登録し、必要なファイアウォールのインターネット接続を確保いたします。|

<sup>1</sup>これらの制限値を引き上げる必要がある場合は、Azure サポートにお問い合せください。
