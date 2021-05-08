---
title: 予定メンテナンス - Azure Database for PostgreSQL - Hyperscale (Citus)
description: この記事では、Azure Database for PostgreSQL - Hyperscale (Citus) での予定メンテナンス機能について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027477"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – Hyperscale (Citus) での予定メンテナンス

Azure Database for PostgreSQL - Hyperscale (Citus) では、マネージド データベースを安全で安定した最新状態に保持するために定期的なメンテナンスが実行されます。  メンテナンス中に、サーバー グループ内のすべてのノードが新しい機能、更新プログラム、およびパッチを取得します。

Hyperscale (Citus) の予定メンテナンスの主な機能は次のとおりです。

* 更新プログラムは、サーバー グループ内のすべてのノードで同時に適用されます
* 今後のメンテナンスに関する通知は、5 日前に Azure Service Health に投稿されます
* 通常、サーバー グループの正常なメンテナンス イベントの間隔は 30 日間以上です

## <a name="notification-about-upcoming-maintenance"></a>今後のメンテナンスに関する通知

今後の予定メンテナンスに関する通知は、5 日前に Azure Service Health に投稿されますが、以下の方法も可能です。

* 特定のアドレスにメールで送信する
* Azure Resource Manager のロールにメールで送信する
* テキスト メッセージ (SMS) でモバイル デバイスに送信する
* 通知として Azure アプリにプッシュする
* 音声メッセージとして配信する

> [!IMPORTANT]
> 通常、サーバー グループの正常な予定メンテナンス イベントの間隔は 30 日間以上です。
>
> ただし、重大な脆弱性などのクリティカルな緊急更新プログラムの場合、通知期間は 5 日未満になる可能性があります。 過去 30 日間に予定メンテナンスが正常に実行された場合でも、重要な更新プログラムがサーバーに適用されることがあります。

メンテナンスが失敗するか、取り消されると、システムによって通知が作成されます。
現在のスケジュール設定に基づいてメンテナンスが再試行され、次回のメンテナンス イベントの 5 日前に通知が送信されます。

## <a name="next-steps"></a>次のステップ

* Azure Service Health を使用して[今後のメンテナンスに関する通知を受け取る](../service-health/service-notifications.md)方法を確認する
* [今後の予定メンテナンス イベントに関する通知を設定する](../service-health/resource-health-alert-monitor-guide.md)方法を確認する
