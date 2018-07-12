---
title: Azure サービス正常性通知とは
description: サービス正常性通知を使用すると、Microsoft Azure によって発行されるサービスの正常性に関するメッセージを表示できます。
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2017
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: d6a87b17041c4ce6cf41da863354ef5a2a37141c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264444"
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

プロパティ名 | 説明
-------- | -----------
channels | **Admin** または **Operation** のいずれかの値。
correlationId | 通常は文字列形式の GUID。 同じアクションに属するイベントは、通常、同じ correlationId を共有します。
eventDataId | イベントの一意識別子。
eventName | イベントのタイトル。
level | イベントのレベル
resourceProviderName | 影響を受けるリソースのリソース プロバイダーの名前。
resourceType| 影響を受けるリソースの種類。
subStatus | 通常は対応する REST 呼び出しの HTTP 状態コードですが、サブステータスを示す他の文字列が含まれる場合もあります。 例: OK (HTTP 状態コード: 200)、Created (HTTP 状態コード: 201)、Accepted (HTTP 状態コード: 202)、No Content (HTTP 状態コード: 204)、Bad Request (HTTP 状態コード: 400)、Not Found (HTTP 状態コード: 404)、Conflict (HTTP 状態コード: 409)、Internal Server Error (HTTP 状態コード: 500)、Service Unavailable (HTTP 状態コード: 503)、Gateway Timeout (HTTP 状態コード: 504)。
eventTimestamp | イベントに対応する要求を処理する Azure サービスによってイベントが生成されたときのタイムスタンプ。
submissionTimestamp | イベントがクエリで使用できるようになったときのタイムスタンプ。
subscriptionId | このイベントが記録された Azure サブスクリプション。
status | 操作の状態を説明する文字列。 一般的な値は、**Started**、**In Progress**、**Succeeded**、**Failed**、**Active**、**Resolved** です。
operationName | 操作の名前。
category | このプロパティは常に **ServiceHealth** です。
resourceId | 影響を受けるリソースのリソース ID。
Properties.title | この通信のローカライズされたタイトル。 既定は英語です。
Properties.communication | HTML マークアップによる通信のローカライズされた詳細。 既定は英語です。
Properties.incidentType | **ActionRequired**、**Information**、**Incident**、**Maintenance**、**Security** のいずれかの値。
Properties.trackingId | このイベントに関連付けられているインシデント。 インシデントに関連するイベントを関連付けるために使用します。
Properties.impactedServices | インシデントの影響を受けるサービスやリージョンが記述された、エスケープされた JSON BLOB。 このプロパティには、(それぞれ **ServiceName** を持つ) サービスのリストと、(それぞれ **RegionName** を持つ) 影響を受けたリージョンのリストが含まれます。
Properties.defaultLanguageTitle | 英語で行われる通信。
Properties.defaultLanguageContent | HTML マークアップまたはプレーン テキストとして英語で行われる通信。
Properties.stage | **Incident** および **Security** で使用可能な値は **Active**、**Resolved** または **RCA** です。 **ActionRequired** または **Information** で使用可能な値は **Active** のみです。 **Maintenance** で使用可能な値: **Active**、**Planned**、**InProgress**、**Canceled**、**Rescheduled**、**Resolved**、**Complete**。
Properties.communicationId | このイベントが関連付けられている通信。

### <a name="details-on-service-health-level-information"></a>サービス正常性レベルの詳細情報
  <ul>
    <li><b>操作が必要</b> (properties.incidentType == ActionRequired) <dl>
            <dt>情報</dt>
            <dd>既存のサービスへの影響を防ぐために管理者の操作が必要です</dd>
        </dl>
    </li>
    <li><b>メンテナンス</b> (properties.incidentType == Maintenance) <dl>
            <dt>警告</dt>
            <dd>緊急のメンテナンス<dd>
            <dt>情報</dt>
            <dd>標準的な計画済みメンテナンス</dd>
        </dl>
    </li>
    <li><b>情報</b> (properties.incidentType == Information) <dl>
            <dt>情報</dt>
            <dd>既存のサービスへの影響を防ぐために管理者の操作が必要です</dd>
        </dl>
    </li>
    <li><b>セキュリティ</b> (properties.incidentType == Security) <dl>
            <dt>エラー</dt>
            <dd>複数の領域にまたがり多様なサービスを利用する際に発生する問題で、範囲は多岐にわたり多くのお客様に影響します。</dd>
            <dt>警告</dt>
            <dd>特定のサービス、あるいは特定の領域を利用した際に発生する問題で、そうしたサービスを利用される一部のお客様に影響します。</dd>
            <dt>情報</dt>
            <dd>管理操作あるいは待機時間に影響し、サービスの可用性には影響しない問題です。</dd>
        </dl>
    </li>
    <li><b>サービスに関する問題</b> (properties.incidentType == Incident) <dl>
            <dt>エラー</dt>
            <dd>複数の領域にまたがり多様なサービスを利用する際に発生する問題で、範囲は多岐にわたり多くのお客様に影響します。</dd>
            <dt>警告</dt>
            <dd>特定のサービス、あるいは特定の領域を利用した際に発生する問題で、そうしたサービスを利用される一部のお客様に影響します。</dd>
            <dt>情報</dt>
            <dd>管理操作あるいは待機時間に影響し、サービスの可用性には影響しない問題です。</dd>
        </dl>
    </li>
  </ul>

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
