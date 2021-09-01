---
title: Azure AD でフェデレーション構成の変更を監視する | Microsoft Docs
description: この記事では、Azure AD を使用してフェデレーション構成の変更を監視する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 7ffc5980eed3268f299ee0073cfa810c17878e53
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229711"
---
# <a name="monitor-changes-to-federation-configuration-in-your-azure-ad"></a>Azure AD でフェデレーション構成の変更を監視する

オンプレミス環境と Azure AD をフェデレーションする際は、オンプレミスの ID プロバイダーと Azure AD との間に信頼関係を構築します。 

この確立された信頼により、Azure AD は、オンプレミスの ID プロバイダーが認証後に発行したセキュリティ トークンを受け入れて、Azure AD によって保護されているリソースへのアクセスを許可します。 

このため、この信頼 (フェデレーション構成) が厳密に監視されていて、異常なアクティビティや疑わしいアクティビティがキャプチャされていることが重要です。

信頼関係を監視するには、フェデレーション構成に変更が加えられたときに通知を受け取るようにアラートを設定することをお勧めします。


## <a name="set-up-alerts-to-monitor-the-trust-relationship"></a>信頼関係を監視するアラートを設定する

信頼関係を監視するアラートを設定するには、次の手順に従います。

1. Azure Log Analytics ワークスペースに送られるように [Azure AD 監査ログを構成します](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。 
2. Azure AD ログ クエリに基づいてトリガーする[警告ルールを作成します](../../azure-monitor/alerts/alerts-log.md)。 
3. アラートの条件が満たされたときに通知を受け取るように、警告ルールに[アクション グループを追加します](../../azure-monitor/alerts/action-groups.md)。  

環境が構成されると、データは次のように送られます。 

 1. Azure AD ログは、テナント内のアクティビティごとに作成されます。  
 2. ログ情報は、Azure Log Analytics ワークスペースに送られます。  
 3. Azure Monitor のバックグラウンド ジョブにより、上記の構成手順 (2) の警告ルールの構成に基づいてログ クエリが実行されます。  
    ```
     AuditLogs 
     |  extend TargetResource = parse_json(TargetResources) 
     | where ActivityDisplayName contains "Set federation settings on domain" or ActivityDisplayName contains "Set domain authentication" 
     | project TimeGenerated, SourceSystem, TargetResource[0].displayName, AADTenantId, OperationName, InitiatedBy, Result, ActivityDisplayName, ActivityDateTime, Type 
     ```
     
 4. クエリの結果がアラート ロジックに一致する場合 (つまり、結果の数が 1 以上である場合)、アクション グループが開始されます。 これが開始されたと想定した場合、フローは手順 5 に続きます。  
 5. アラートの構成中に選択されたアクション グループに通知が送信されます。

 > [!NOTE]
 >  アラートの設定に加えて、Azure AD テナント内で構成されているドメインを定期的に確認し、古いドメイン、認識されないドメイン、不審なドメインを削除することをお勧めします。 




## <a name="next-steps"></a>次のステップ

- [Azure AD ログを Azure Monitor ログと統合する](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [Azure Monitor を使用してログ アラートを作成、表示、管理する](../../azure-monitor/alerts/alerts-log.md)
- [Azure AD Connect を使用して AD FS と Azure AD の信頼を管理する](how-to-connect-azure-ad-trust.md)
- [Active Directory フェデレーション サービスを保護するためのベスト プラクティス](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)