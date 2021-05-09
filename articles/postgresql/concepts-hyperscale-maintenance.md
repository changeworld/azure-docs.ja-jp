---
title: 予定メンテナンス - Azure Database for PostgreSQL - Hyperscale (Citus)
description: この記事では、Azure Database for PostgreSQL - Hyperscale (Citus) での予定メンテナンス機能について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 336b8ff034122373c9bd824d76c110c808e73010
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108315581"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – Hyperscale (Citus) での予定メンテナンス

Azure Database for PostgreSQL - Hyperscale (Citus) では、マネージド データベースを安全で安定した最新状態に保持するために定期的なメンテナンスが実行されます。  メンテナンス中に、サーバー グループ内のすべてのノードが新しい機能、更新プログラム、およびパッチを取得します。

Hyperscale (Citus) の予定メンテナンスの主な機能は次のとおりです。

* 更新プログラムは、サーバー グループ内のすべてのノードで同時に適用されます
* 今後のメンテナンスに関する通知は、5 日前に Azure Service Health に投稿されます
* 通常、サーバー グループの正常なメンテナンス イベントの間隔は 30 日間以上です
* メンテナンスを開始する希望の曜日と時間帯をサーバー グループごとに個別に定義できます

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>メンテナンス期間の選択および今後のメンテナンスに関する通知

特定の曜日とその日の時間帯の間でのメンテナンスをスケジュールできます。 また、システムで自動的に曜日と時間帯が選択されるようにすることもできます。 どちらの場合でも、メンテナンスを実行する 5 日前に通知されます。 また、メンテナンスが開始されたときと、正常に完了したときに通知されるようにすることもできます。

今後の予定メンテナンスに関する通知は、5 日前に Azure Service Health に投稿されますが、以下の方法も可能です。

* 特定のアドレスにメールで送信する
* Azure Resource Manager のロールにメールで送信する
* テキスト メッセージ (SMS) でモバイル デバイスに送信する
* 通知として Azure アプリにプッシュする
* 音声メッセージとして配信する

メンテナンス スケジュールの設定を指定する場合、曜日と時間帯を選択できます。 指定しない場合、システムでは、サーバー グループのリージョンの時刻で午後 11 時から午前 7 時までの時間が選択されます。 Azure サブスクリプションの Hyperscale (Citus) サーバー グループごとに異なるスケジュールを定義できます。

> [!IMPORTANT]
> 通常、サーバー グループの正常な予定メンテナンス イベントの間隔は 30 日間以上です。
>
> ただし、重大な脆弱性などのクリティカルな緊急更新プログラムの場合、通知期間は 5 日未満になる可能性があります。 過去 30 日間に予定メンテナンスが正常に実行された場合でも、重要な更新プログラムがサーバーに適用されることがあります。

スケジュール設定はいつでも更新できます。 Hyperscale (Citus) サーバー グループのメンテナンスがスケジュールされている場合に、スケジュールを更新すると、既存のイベントは元のスケジュールどおりに実行されます。 設定の変更は、既存のイベントが正常に完了した後で有効になります。

メンテナンスが失敗するか、取り消されると、システムによって通知が作成されます。
現在のスケジュール設定に基づいてメンテナンスが再試行され、次回のメンテナンス イベントの 5 日前に通知が送信されます。

## <a name="next-steps"></a>次のステップ

* [メンテナンス スケジュールを変更する](howto-hyperscale-maintenance.md)方法を確認する
* Azure Service Health を使用して[今後のメンテナンスに関する通知を受け取る](../service-health/service-notifications.md)方法を確認する
* [今後の予定メンテナンス イベントに関する通知を設定する](../service-health/resource-health-alert-monitor-guide.md)方法を確認する
