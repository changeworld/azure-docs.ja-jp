---
title: "Azure サービス正常性通知とは | Microsoft Docs"
description: "サービス正常性通知を使用すると、Microsoft Azure によって発行されるサービスの正常性に関するメッセージを表示できます。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: 4a95e9882515e6a2861292829a44847e11f39063
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2018
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Azure Portal を使用したサービス正常性通知の表示

サービス正常性通知は、Azure によって発行され、サブスクリプションのリソースに関する情報を含みます。 これらの通知はアクティビティ ログのイベントのサブクラスであり、アクティビティ ログでも確認できます。 サービス正常性通知には、クラスに応じて、情報のみの通知とアクションが必要になる通知があります。

サービス正常性通知には、複数のクラスがあります。  

- **操作が必要:** Azure がアカウント上に不審な状況を見つけた場合、ユーザーと連携してこれに対応します。 必要なアクションの詳細や、Azure のエンジニアリング チームやサポートに問い合わせる方法が記載された通知が、Azure から送信されます。  
- **支援復旧:** イベントが発生し、引き続きその影響があることをエンジニアが確認している状態です。 Azure エンジニアリング チームが、サービスの復旧のためにユーザーと直接やり取りをする必要があります。  
- **インシデント:** サービスに影響するイベントがサブスクリプションの 1 つ以上のリソースに影響を与えています。  
- **メンテナンス:** サブスクリプションの 1 つ以上のリソースに影響を与える可能性のある計画済みメンテナンス アクティビティに関する情報です。  
- **情報:** リソースの使用状況を改善するのに役立つ可能性のある最適化に関する情報です。 
- **セキュリティ:** Azure で実行されるソリューションに関する緊急のセキュリティ関連情報です。

各サービスの正常性通知には、その範囲とリソースへの影響に関する詳細が含まれます。 詳細には次のものが含まれます。

プロパティ名 | [説明]
-------- | -----------
channels | **Admin** または **Operation** のいずれかの値。
correlationId | 通常は文字列形式の GUID。 同じアクションに属するイベントは、通常、同じ correlationId を共有します。
eventDataId | イベントの一意識別子。
eventName | イベントのタイトル。
level | イベントのレベル。 **Critical**、**Error**、**Warning**、または **Informational** のいずれかの値。
resourceProviderName | 影響を受けるリソースのリソース プロバイダーの名前。
resourceType| 影響を受けるリソースの種類。
subStatus | 通常は対応する REST 呼び出しの HTTP 状態コードですが、サブステータスを示す他の文字列が含まれる場合もあります。 例: OK (HTTP 状態コード: 200)、Created (HTTP 状態コード: 201)、Accepted (HTTP 状態コード: 202)、No Content (HTTP 状態コード: 204)、Bad Request (HTTP 状態コード: 400)、Not Found (HTTP 状態コード: 404)、Conflict (HTTP 状態コード: 409)、Internal Server Error (HTTP 状態コード: 500)、Service Unavailable (HTTP 状態コード: 503)、Gateway Timeout (HTTP 状態コード: 504)。
eventTimestamp | イベントに対応する要求を処理する Azure サービスによってイベントが生成されたときのタイムスタンプ。
submissionTimestamp | イベントがクエリで使用できるようになったときのタイムスタンプ。
subscriptionId | このイベントが記録された Azure サブスクリプション。
status | 操作の状態を説明する文字列。 一般的な値は、**Started**、**In Progress**、**Succeeded**、**Failed**、**Active**、**Resolved** です。
operationName | 操作の名前。
カテゴリ | このプロパティは常に **ServiceHealth** です。
ResourceId | 影響を受けるリソースのリソース ID。
Properties.title | この通信のローカライズされたタイトル。 既定は英語です。
Properties.communication | HTML マークアップによる通信のローカライズされた詳細。 既定は英語です。
Properties.incidentType | **AssistedRecovery**、**ActionRequired**、**Information**、**Incident**、**Maintenance**、**Security** のいずれかの値。
Properties.trackingId | このイベントに関連付けられているインシデント。 インシデントに関連するイベントを関連付けるために使用します。
Properties.impactedServices | インシデントの影響を受けるサービスやリージョンが記述された、エスケープされた JSON BLOB。 このプロパティには、(それぞれ **ServiceName** を持つ) サービスのリストと、(それぞれ **RegionName** を持つ) 影響を受けたリージョンのリストが含まれます。
Properties.defaultLanguageTitle | 英語で行われる通信。
Properties.defaultLanguageContent | HTML マークアップまたはプレーン テキストとして英語で行われる通信。
Properties.stage | **AssistedRecovery**、**ActionRequired**、**Information**、**Incident**、**Security** で使用可能な値: **Active**、**Resolved**。 **Maintenance** で使用可能な値: **Active**、**Planned**、**InProgress**、**Canceled**、**Rescheduled**、**Resolved**、**Complete**。
Properties.communicationId | このイベントが関連付けられている通信。


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Azure Portal でのサービス正常性通知の表示
1.  [Azure Portal](https://portal.azure.com) で、**[モニター]** を選択します。

    ![Azure Portal メニューのスクリーンショット。[モニター] が選択されている](./media/monitoring-service-notifications/home-monitor.png)

    Azure Monitor は、すべての監視設定とデータが 1 つにまとめられた統合ビューです。 最初に開くのは **[アクティビティ ログ]** セクションです。

3.  **[Alerts]**(アラート) を選択します。

    ![モニターのアクティビティ ログのスクリーンショット。[アラート] が選択されている](./media/monitoring-service-notifications/service-health-summary.png)
4. **[+ アクティビティ ログ アラートの追加]** を選択し、今後サービスの通知を受け取るようにアラートを設定します。 詳細については、「[サービス通知のアクティビティ ログ アラートを作成する](monitoring-activity-log-alerts-on-service-notifications.md)」を参照してください。

## <a name="next-steps"></a>次の手順
[サービス正常性通知が投稿されるたびにアラートを受け取る](monitoring-activity-log-alerts-on-service-notifications.md)。  
[アクティビティ ログのアラート](monitoring-activity-log-alerts.md)について詳しく学習します。
