---
title: Azure Database for PostgreSQL - Hyperscale (Citus) - 予定メンテナンス - Azure portal
description: Azure portal から Azure Database for PostgreSQL - Hyperscale (Citus) の予定メンテナンス設定を構成する方法について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 04dd37e3e9abbfbec7badb036802e645cc7732b0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108012"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – Hyperscale (Citus) の予定メンテナンス設定の管理

Azure サブスクリプションの Hyperscale (Citus) サーバー グループごとにメンテナンス オプションを指定できます。 オプションには、これから実行されるメンテナンス イベントと、終了したメンテナンス イベントのメンテナンス スケジュールと通知設定が含まれます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- [Azure Database for PostgreSQL - Hyperscale (Citus) サーバー グループ](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>メンテナンス スケジュール オプションを指定する

1. [Hyperscale (Citus) サーバー] ページの **[設定]** 見出しで、 **[メンテナンス]** を選択して、予定メンテナンス オプションを開きます。
2. 既定の (システム管理) スケジュールでは、週のランダムに選択された曜日に 30 分の時間枠で、午後 11 時から午前 7 時 (サーバー グループの [Azure リージョンの時刻](https://go.microsoft.com/fwlink/?linkid=2143646)) までの間にメンテナンスが開始されます。 このスケジュールをカスタマイズする場合は、 **[カスタム スケジュール]** を選択します。 その後、望ましい曜日と 30 分間のメンテナンス開始時刻を選択できます。

## <a name="notifications-about-scheduled-maintenance-events"></a>スケジュールされたメンテナンス イベントに関する通知

Azure Service Health を使用して、Hyperscale (Citus) サーバー グループの今後および過去の予定メンテナンスに関する[通知を確認](../service-health/service-notifications.md)できます。 また、Azure Service Health でアラートを[設定](../service-health/resource-health-alert-monitor-guide.md)して、メンテナンス イベントに関する通知を受け取ることもできます。

## <a name="next-steps"></a>次のステップ

* [Azure Database for PostgreSQL – Hyperscale (Citus) での予定メンテナンス](concepts-hyperscale-maintenance.md)について確認する
* [Azure Service Health](../service-health/overview.md) について確認する
