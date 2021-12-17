---
title: Azure Database for MySQL - フレキシブル サーバー - 予定メンテナンス - Azure portal
description: Azure portal から Azure Database for MySQL - フレキシブル サーバーの予定メンテナンス設定を構成する方法について説明します。
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: bf6eb62e75a378db8fae70f2317ccd4f072a7f75
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460647"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>Azure Database for MySQL の予定メンテナンス設定の管理 - フレキシブル サーバー

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Azure サブスクリプションのフレキシブル サーバーごとにメンテナンス オプションを指定できます。 オプションには、これから実行されるメンテナンス イベントと、終了したメンテナンス イベントのメンテナンス スケジュールと通知設定が含まれます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- [Azure Database for MySQL - フレキシブル サーバー](quickstart-create-server-portal.md)

## <a name="specify-maintenance-schedule-options"></a>メンテナンス スケジュール オプションを指定する

1. [MySQL サーバー] ページの **[設定]** 見出しで、 **[メンテナンス]** を選択して、スケジュールされたメンテナンス オプションを開きます。
2. 既定の (システム管理) スケジュールでは、週のランダムに選択された曜日に 60 分の時間枠で、午後 11 時から午前 7 時 (ローカル サーバーの時刻) までの間にメンテナンスが開始されます。 このスケジュールをカスタマイズする場合は、 **[カスタム スケジュール]** を選択します。 それから望ましい曜日と 60 分間のメンテナンス開始時刻を選択します。

## <a name="notifications-about-scheduled-maintenance-events"></a>スケジュールされたメンテナンス イベントに関する通知

Azure Service Health を使用して、フレキシブル サーバーでこれから実行される予定メンテナンスと、実行済みの予定メンテナンスに関する[通知を確認](../../service-health/service-notifications.md)できます。 また、Azure Service Health でアラートを[設定](../../service-health/resource-health-alert-monitor-guide.md)して、メンテナンス イベントに関する通知を受け取ることもできます。

## <a name="next-steps"></a>次のステップ

* [Azure Database for MySQL での予定メンテナンス - フレキシブル サーバー](concepts-maintenance.md)について確認する
* [Azure Service Health](../../service-health/overview.md) について確認する
