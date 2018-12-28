---
title: インクルード ファイル
description: インクルード ファイル
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 7d905550114bb76a0a091146b3972bab4a652022
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429998"
---
| リソース | 既定の制限 |
| --- | --- |
| データ処理量 |1000 TB/ファイアウォール/月<sup>1</sup> |
|ルール|10k - すべての規則の種類が結合される|
|グローバル ピアリング|サポートされていません。 リージョンごとに少なくとも 1 つのファイアウォールを展開する必要があります。|
|1 つのネットワーク ルールの最大ポート|15<br>ポートの範囲 (例: 2 - 10) は 2 とカウントされます。
|AzureFirewallSubnet の最小サイズ |/26|
|ネットワークおよびアプリケーション ルールのポート範囲|0-64,000。 現在、この制限を緩和するように取り組んでいます。|
|


<sup>1</sup> これらの制限値を大きくする必要がある場合は、Azure サポートにお問い合せください。
