---
title: Azure メンテナンス スケジュール (プレビュー) |Microsoft Docs
description: メンテナンス スケジューリングにより、お客様は、Azure SQL Data Warehouse サービスが新しい機能をロールアウト、アップグレード、パッチするために必要な、スケジュールされたメンテナンス イベントを計画することができます。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: c2ed79673af3563ae62f516057a174770cda99e9
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427864"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>メンテナンス スケジュールを使用してサービスの更新とメンテナンスを管理する

Azure SQL Data Warehouse のメンテナンス スケジュール機能のプレビューが開始されました。 この機能は、Service Health 計画メンテナンス通知、リソース ヘルス チェック モニター、Azure SQL Data Warehouse メンテナンス スケジューリング サービスを統合します。

メンテナンス スケジューリングを使用することで、新しい機能、アップグレード、パッチを受信するのに都合がいい時間枠をスケジュールできます。 お客様は、7 日間におけるプライマリおよびセカンダリのメンテナンス期間を選択します。 たとえば、土曜日の 22:00 から日曜日の 01:00 をプライマリ ウィンドウとし、水曜日の 19:00 から 22:00 をセカンダリ ウィンドウとします。 プライマリ メンテナンス期間でメンテナンスを実行できなかった場合、SQL Data Warehouse は、セカンダリ メンテナンス期間で再度メンテナンスを試みます。

新しく作成されたすべてのAzure SQL Data Warehouse インスタンスには、デプロイ時にシステム定義のメンテナンス スケジュールが適用されます。 デプロイが完了するとすぐに、スケジュールを編集できます。

メンテナンス期間はそれぞれ 3 時間から 8 時間です。 その期間内は、任意のタイミングでメンテナンスが実行される可能性があります。 このサービスがお使いのデータ ウェアハウスに新しいコードをデプロイする際に、接続が一時的に失われることが予想されます。 

機能プレビュー中は、プライマリとセカンダリのメンテナンス期間を別々の曜日範囲となるよう明示的に指定してください。 すべてのメンテナンス操作は、予定メンテナンス期間内に完了する必要があります。 事前に通知することなく、指定されたメンテナンス期間の外でメンテナンスは行われません。 スケジュールされたメンテナンス中にデータ ウェアハウスが一時停止されると、再開操作中に更新されます。  


## <a name="alerts-and-monitoring"></a>アラートと監視

Service Health 通知とリソース ヘルス チェック モニターとの統合により、お客様は差し迫ったメンテナンス アクティビティについての情報を得ることができます。 この新しいオートメーションには、Azure Monitor が活用されています。 差し迫ったメンテナンス イベントの通知方法を自分で決めることができます。 また、ダウンタイムを管理し、業務への影響を最小化することに寄与する自動化されたフローも自分で決めることができます。

すべてのメンテナンス イベントは、24 時間前の事前通知が行われます。 インスタンスのダウンタイムを最小化するために、選択したメンテナンス期間の前に、長時間実行されるトランザクションがデータ ウェアハウスにないことを確認してください。 メンテナンスが開始されると、すべてのアクティブなセッションがキャンセルされます。 コミットされていないトランザクションはロールバックされ、データ ウェアハウスの接続が短時間失われます。 データ ウェアハウスの保守が完了すると、すぐに通知が届きます。 

メンテナンスが行われるという事前通知を受けても、その間に SQL Data Warehouse がメンテナンスを実行できない場合は、キャンセル通知が届きます。 メンテナンスは、次の定期メンテナンス期間中に再開されます。
 
すべてのアクティブ メンテナンス イベントが **[Service Health - Planned Maintenance]\(Service Health - 計画メンテナンス\)** セクションに表示されます。 Service Health の履歴には、過去のイベントの記録がすべて含まれます。 アクティブなイベント中に Azure Service Health チェック ポータル ダッシュボードを使用してメンテナンスを監視できます。

### <a name="maintenance-schedule-availability"></a>メンテナンス スケジュールの提供状況

選択したリージョンでメンテナンス スケジューリングが利用できない場合でも、メンテナンス スケジュールはいつでも表示および編集できます。 ご利用のリージョンでメンテナンス スケジューリングが利用可能になると、特定されたスケジュールはすぐにお使いのデータ ウェアハウスでアクティブになります。


## <a name="next-steps"></a>次の手順

- メンテナンス スケジュールを表示する方法について[詳しく知る](viewing-maintenance-schedule.md)。 
- メンテナンス スケジュールを変更する方法について[詳しく知る](changing-maintenance-schedule.md)。
- Azure Monitor を使用してアラートを作成、表示、管理する方法について[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)。
- ログ アラート ルール用の Webhook アクションについて[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。
- Azure Service Health について[詳しく知る](https://docs.microsoft.com/azure/service-health/service-health-overview)。







