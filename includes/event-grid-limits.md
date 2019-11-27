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
ms.openlocfilehash: 890095718833c90a6764d0799d2081b393b1d0bd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887800"
---
次の制限は、Azure Event Grid システムのトピックとカスタム トピックに適用されます。イベント ドメインには適用*されません*。

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりのカスタム トピック数 | 100 |
| トピックあたりのイベント サブスクリプション数 | 500 |
| カスタム トピックの発行レート (イングレス) | 5,000 イベント/秒/トピック |
| 発行要求数 | 1 秒あたり 250 |
| イベント サイズ | 一般提供 (GA) では 64 KB をサポート。 現在のプレビューでは 1 MB をサポート。 |

次の制限は、イベント ドメインにのみ適用されます。

| リソース | 制限 |
| --- | --- |
| イベント ドメインあたりのトピック数 | 100,000 |
| ドメイン内のトピックあたりのイベント サブスクリプション数 | 500 |
| ドメイン スコープ イベント サブスクリプション数 | 50 |
| イベント ドメインの発行率 (イングレス) | 1 秒あたり 5,000 イベント |
| 発行要求数 | 1 秒あたり 250 |
| Azure サブスクリプションあたりのイベント ドメイン数 | 100 |