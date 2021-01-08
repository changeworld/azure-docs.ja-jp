---
title: インクルード ファイル
description: インクルード ファイル
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 0c3cdc21425e0634a725000efb27b3cde0ccd718
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844721"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| メトリック アラート (クラシック) |サブスクリプションごとに 100 個のアクティブなアラート ルール。 | サポートに問い合わせます |
| メトリック アラート |Azure パブリック、Azure China 21Vianet、および Azure Government クラウド内でサブスクリプションごとに 5,000 個のアクティブな警告ルール。 この制限に達した場合は、[同じ種類のマルチリソース アラート](../articles/azure-monitor/platform/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)を使用できるかどうかを調べます。<br/>アラート ルールあたり 5,000 個のメトリック時系列。 | サポートに問い合わせます。 |
| アクティビティ ログ アラート | サブスクリプションごとに 100 個のアクティブなアラート ルール (増やすことはできません)。 | 既定値と同じ |
| ログ アラート | サブスクリプションごとに 512 個のアクティブなアラート ルール。 リソースごとに 200 個のアクティブなアラート ルール。 | サポートに問い合わせます |
| アラート ルールとアクション ルールの説明の長さ| ログ検索アラートは 4096 文字<br/>その他はすべて 2048 文字 | 既定値と同じ |
