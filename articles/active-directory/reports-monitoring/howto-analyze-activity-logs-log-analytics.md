---
title: Log Analytics を使用して Azure Active Directory アクティビティ ログを分析する方法 (プレビュー) | Microsoft Docs
description: Log Analytics を使用して Azure Active Directory アクティビティ ログを分析する方法について説明します (プレビュー)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2f0f5341ba6818f375d463638979c68689519227
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624201"
---
# <a name="analyze-azure-ad-activity-logs-with-log-analytics-preview"></a>Log Analytics を使用した Azure AD アクティビティ ログの分析 (プレビュー)

[Azure AD アクティビティ ログを Log Analytics と統合](howto-integrate-activity-logs-with-log-analytics.md)した後、Log Analytics の機能を使用して、環境に対する洞察を得ることができます。 [Azure AD アクティビティ ログ用の Log Analytics ビュー](howto-install-use-log-analytics-views.md)をインストールして、環境での監査およびサインイン イベントに関する事前構築済みレポートへのアクセス権を取得することもできます。

この記事では、Log Analytics ワークスペースでの Azure AD アクティビティ ログを分析する方法について説明します。 

## <a name="prerequisites"></a>前提条件 

理解するには、次の内容が必要です。

* Azure サブスクリプションの Log Analytics ワークスペース。 [Log Analytics ワークスペースの作成方法](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)を確認してください。
* 最初に、[Azure AD アクティビティ ログを Log Analytics ワークスペースにルーティングする](howto-integrate-activity-logs-with-log-analytics.md)手順を完了する。

## <a name="navigate-to-the-log-analytics-workspace"></a>Log Analytics ワークスペースに移動する

1. [Azure Portal](https://portal.azure.com) にサインインします。 

2. **[Azure Active Directory]** を選択し、**[監視]** セクションから **[ログ]** を選択し、Log Analytics ワークスペースを開きます。 ワークスペースは既定のクエリで開きます。

    ![既定のクエリ](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Azure AD アクティビティ ログのスキーマを表示する

ログは、ワークスペース内の **AuditLogs** テーブルと **SigninLogs** テーブルにプッシュされます。 これらのテーブルのスキーマを表示するには:

1. 以前のセクションの既定のクエリ ビューから、**[スキーマ]** を選択し、ワークスペースを展開します。 

2. **[ログ管理]** セクションを展開し、続いて **[AuditLogs]** または **[SignInLogs]** のどちらかを展開してログ スキーマを表示します。
    ![監査ログ](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![サインイン ログ](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Azure AD アクティビティ ログのクエリを実行する

ワークスペースにログが用意されたので、これでこれらに対してクエリを実行できます。 たとえば、過去 1 週間に使用された最上位のアプリケーションを取得するには、既定のクエリを次のものに置き換えて、**[実行]** を選択します

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

過去 1 週間にわたる最上位の監査イベントを取得するには、次のクエリを使用します。

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Azure AD アクティビティ ログ データに関するアラート

クエリでアラートを設定することもできます。 たとえば、10 個を超えるアプリケーションが過去 1 週間に使用された場合にアラートを構成するには:

1. ワークスペースから、**[Set alert]**(アラートの設定) を選択して **[ルールの作成]** ページを開きます。 
    ![[Set alert]](./media/howto-analyze-activity-logs-log-analytics/setalert.png)(アラートの設定)

2. アラートに作成された既定の**アラートの条件**を選択し、既定のメトリックの **[しきい値]** を 10 に更新します。 
    ![[アラートの条件]](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. アラートの名前と説明を入力して、重大度を選択します。 この例では、これを **[情報]** に設定できます。

4. 信号が発生したときにアラートする **[アクション グループ]** を選択します。 電子メールまたはテキスト メッセージを使用してチームに通知することも、Webhook、Azure Functions、または Logic Apps を使用してアクションを自動化することもできます。 [Azure portal でのアラート グループの作成および管理](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)の詳細を理解します。

5. アラートを構成し終えたら、**[アラートの作成]** を選択して有効にします。 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Azure AD アクティビティ ログの事前構築済みビューをインストールし使用する

Azure AD アクティビティ ログの事前構築済み Log Analytics ビューをダウンロードすることもできます。 ビューには、監査およびサインイン イベントに関する一般的なシナリオに関連した複数のレポートが表示されます。 前のセクションで説明されている手順を使用して、レポートに表示されるデータのいずれかについてアラートすることもできます。

* **[Azure AD Account Provisioning Events]\(Azure AD アカウントのプロビジョニング イベント\)**: プロビジョニングされた新しいユーザーとプロビジョニング エラーの数、更新されたユーザーと更新エラーの数、プロビジョニング解除されたユーザーと該当するエラーの数など、プロビジョニング アクティビティの監査に関連するレポートを表示します。    
* **[Sign-ins Events]\(サインイン イベント\)**: このビューは、経時的にサインイン数を追跡する概要ビューだけでなく、アプリケーション、ユーザー、デバイスごとのサインインなど、サインイン アクティビティの監視に関連する最も重要なレポートを表示します。
* **[Users Performing Consent]\(ユーザ―実行の同意\)**: このビューは、すべての同意ベースのアプリケーションに対して、アプリゲーションごとのサインインだけでなく、ユーザーによる同意、同意したユーザーによるサインインなど、ユーザーの同意に関連するレポートを表示します。 

Azure AD のアクティビティ ログ用の Log Analytics ビューをインストールして使用する方法については、[こちら](howto-install-use-log-analytics-views.md)を参照してください。 


## <a name="next-steps"></a>次の手順

* [Log Analytics のクエリの概要](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Create and manage alert groups in the Azure portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Azure Active Directory 用の Log Analytics ビューのインストールと使用](howto-install-use-log-analytics-views.md)
