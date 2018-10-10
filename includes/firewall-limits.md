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
ms.openlocfilehash: 0a4a66543b323b1dbe56158851dafcb1dfe695f0
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47186144"
---
| リソース | 既定の制限 |
| --- | --- |
| データ処理量 |1000 TB/ファイアウォール/月<sup>1</sup> |
|ルール|10,000 個のアプリケーション ルール、10,000 個のネットワーク ルール|
|VNET ピアリング|ハブとスポークの実装の場合、最大 50 個のスポーク VNET。|
|グローバル ピアリング|サポートされていません。 リージョンごとに少なくとも 1 つのファイアウォールを展開する必要があります。|
|1 つのネットワーク ルールの最大ポート|15<br>ポートの範囲 (例: 2 - 10) は 2 とカウントされます。


<sup>1</sup> これらの制限値を大きくする必要がある場合は、Azure サポートにお問い合せください。
