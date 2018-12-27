---
title: インクルード ファイル
description: インクルード ファイル
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/30/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: f1fd32fc02bee47d229093680166e6a13a5ba721
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2018
ms.locfileid: "49458852"
---
| リソース | 既定の制限 |
| --- | --- |
| データ処理量 |1000 TB/ファイアウォール/月<sup>1</sup> |
|ルール|10k - すべての規則の種類が結合される|
|VNET ピアリング|ハブとスポークの実装の場合、最大 50 個のスポーク VNET。|
|グローバル ピアリング|サポートされていません。 リージョンごとに少なくとも 1 つのファイアウォールを展開する必要があります。|
|1 つのネットワーク ルールの最大ポート|15<br>ポートの範囲 (例: 2 - 10) は 2 とカウントされます。


<sup>1</sup> これらの制限値を大きくする必要がある場合は、Azure サポートにお問い合せください。
