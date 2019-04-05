---
title: インクルード ファイル
description: インクルード ファイル
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/14/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 3e6bde63c5f759ec711b243fc25fa0ed8bde634a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58016473"
---
| リソース | 既定の制限 |
| --- | --- |
| データ スループット |30 Gbps<sup>1</sup> |
|ルール|10,000 個。すべての規則の種類が結合されます。|
|グローバル ピアリング|サポートされていません。 リージョンごとに少なくとも 1 つのファイアウォールを展開する必要があります。|
|AzureFirewallSubnet の最小サイズ |/26|
|ネットワークおよびアプリケーション ルールのポート範囲|0-64,000。 現在、この制限を緩和するように取り組んでいます。|
|


<sup>1</sup>これらの制限値を引き上げる必要がある場合は、Azure サポートにお問い合せください。
