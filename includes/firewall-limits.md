---
title: インクルード ファイル
description: インクルード ファイル
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 2/4/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 9097f2b70a80431cf3302555ad2c835338cf8d8e
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736047"
---
| リソース | 既定の制限 |
| --- | --- |
| データ処理量 |1000 TB/ファイアウォール/月<sup>1</sup> |
|ルール|10k - すべての規則の種類が結合される|
|グローバル ピアリング|サポートされていません。 リージョンごとに少なくとも 1 つのファイアウォールを展開する必要があります。|
|AzureFirewallSubnet の最小サイズ |/26|
|ネットワークおよびアプリケーション ルールのポート範囲|0-64,000。 現在、この制限を緩和するように取り組んでいます。|
|


<sup>1</sup> これらの制限値を大きくする必要がある場合は、Azure サポートにお問い合せください。
