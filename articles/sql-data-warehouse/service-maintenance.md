---
title: 計画メンテナンス - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL データ ウェアハウスに計画メンテナンス イベントを準備する方法について説明します。
services: sql-data-warehouse
author: antvgski
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a273ad4c256267865d3af324f0ef755a6cb75c5c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799893"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Azure SQL データ ウェアハウスのメンテナンスの計画

Azure SQL データ ウェアハウスに計画メンテナンス イベントを準備する方法について説明します。

## <a name="what-is-a-planned-maintenance-event"></a>計画メンテナンス イベントとは
計画メンテナンス イベントは、メンテナンス操作のためにデータ ウェアハウスをオフラインにする必要がある場合の時間枠のことです。 これらのイベントは、サービスのアップグレード、新しい機能、または修正プログラムを適用する機会になります。 

## <a name="frequency"></a>頻度
平均すると、各月に、1 つ以上の計画メンテナンス イベントが発生します。 

## <a name="notifications-and-downtime"></a>通知およびダウンタイム
各計画メンテナンス イベントの前に通知を受信します。 メンテナンス イベントは、実行中のクエリをすべてキャンセルし、データ ウェアハウスをオフラインにします。 各データ ウェアハウスの予想されるダウンタイムは、約 30 分です。 メンテナンスが完了すると、通知を受信できるはずです。 

## <a name="setting-up-alerts"></a>アラートの設定

計画メンテナンス ログ アラートを設定するために、[Azure Monitor](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) を使用することをお勧めします。 必要なメンテナンスが業務に与える影響を最小限に抑えるよう計画を立てるうえで、アラートが役立つ場合があります。 

通知を設定するには、[ログ アラートに関する手順](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)を使用してください。 

## <a name="next-steps"></a>次の手順
監視の詳細については、[ワークロードの監視](sql-data-warehouse-manage-monitor.md)に関するページを参照してください。
