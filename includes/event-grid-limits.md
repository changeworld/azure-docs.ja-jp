---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 04/30/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2425d9455606f5eabba4b89cfa238b7a928be30e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285772"
---
####

次の制限は、Event Grid システムのトピックとカスタム トピックに適用されます。イベント ドメインには適用*されません*。

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりのカスタム トピック数 | 100 |
| トピックあたりのイベント サブスクリプション数 | 500 |
| カスタム トピックの発行レート (イングレス) | 5,000 イベント/秒/トピック |

####

次の制限は、イベント ドメインにのみ適用されます。

| リソース | 制限 |
| --- | --- |
| イベント ドメインあたりのトピック数 | パブリック プレビュー時に 1,000 件 |
| ドメイン内のトピックあたりのイベント サブスクリプション数 | パブリック プレビュー時に 50 件 |
| ドメイン スコープ イベント サブスクリプション数 | パブリック プレビュー時に 50 件 |
| イベント ドメインの発行率 (イングレス) | パブリック プレビュー時に 1 秒あたり 5,000 イベント |