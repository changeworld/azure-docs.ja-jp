---
title: インクルード ファイル
description: インクルード ファイル
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 05/03/2021
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 0a56be93309fd68f73bd542222ff14b8b99f3e5e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108793634"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| メトリック アラート (クラシック) |サブスクリプションごとに 100 個のアクティブなアラート ルール。 | サポートに問い合わせます |
| メトリック アラート |Azure パブリック、Azure China 21Vianet、および Azure Government クラウド内でサブスクリプションごとに 5,000 個のアクティブな警告ルール。 この制限に達した場合は、[同じ種類のマルチリソース アラート](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)を使用できるかどうかを調べます。<br/>アラート ルールあたり 5,000 個のメトリック時系列。 | サポートに問い合わせます。 |
| アクティビティ ログ アラート | サブスクリプションごとに 100 個のアクティブなアラート ルール (増やすことはできません)。 | 既定値と同じ |
| ログ アラート | サブスクリプションごとに 512 個のアクティブなアラート ルール。 リソースごとに 200 個のアクティブなアラート ルール。 | サポートに問い合わせます |
| アラート ルールとアクション ルールの説明の長さ| ログ検索アラートは 4096 文字<br/>その他はすべて 2048 文字 | 既定値と同じ |

### <a name="alerts-api"></a>Alerts API
Azure Monitor アラートには、ユーザーが過剰な数の呼び出しを実行するのを防ぐためのいくつかの調整制限があります。 このような動作によってシステムのバックエンド リソースが過負荷になり、サービスの応答性が損なわれる可能性があります。 次の制限は、顧客を中断から保護し、一貫したサービス レベルを確保するために設計されています。 ユーザーの調整と制限は、極端な使用シナリオにのみ影響するように設計されており、一般的な使用には関係ありません。

| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| GET alertsSummary | サブスクリプションごとに毎分 50 回の呼び出し | 既定値と同じ | 
|   GET alerts (アラート ID の指定なし) | サブスクリプションごとに毎分 100 回の呼び出し | 既定値と同じ | 
|   その他すべての呼び出し | サブスクリプションごとに毎分 1000 回の呼び出し | 既定値と同じ | 

