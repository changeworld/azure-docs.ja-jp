---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845981"
---
次の制限は、Azure Event Grid システムのトピックとカスタム トピックに適用されます。イベント ドメインには適用*されません*。

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりのカスタム トピック数 | 100 |
| トピックあたりのイベント サブスクリプション数 | 500 |
| カスタム トピックの発行レート (イングレス) | 5,000 イベント/秒/トピック |
| 発行要求数 | 1 秒あたり 250 |
| イベント サイズ | 1 MB (複数の 64 KB イベントとして課金されます) |

次の制限は、イベント ドメインにのみ適用されます。

| リソース | 制限 |
| --- | --- |
| イベント ドメインあたりのトピック数 | 100,000 |
| ドメイン内のトピックあたりのイベント サブスクリプション数 | 500 |
| ドメイン スコープ イベント サブスクリプション数 | 50 |
| イベント ドメインの発行率 (イングレス) | 1 秒あたり 5,000 イベント |
| 発行要求数 | 1 秒あたり 250 |
| Azure サブスクリプションあたりのイベント ドメイン数 | 100 |