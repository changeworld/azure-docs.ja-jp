---
title: Azure Monitor (プレビュー) を使用して Azure Active Directory のログを Log Analytics と統合する方法 | Microsoft Docs
description: Azure Monitor (プレビュー) を使用して Azure Active Directory のログを Log Analytics と統合する方法について説明します
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 10/01/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bc918d0ee3b3b435905546507287dc655f68e8c9
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429781"
---
# <a name="integrate-azure-ad-logs-with-log-analytics-using-azure-monitor-preview"></a>Azure Monitor (プレビュー) を使用して Azure AD のログを Log Analytics と統合する

Log Analytics を使用すると、データのクエリを行って、特定のイベントを検索し、傾向を分析して、さまざまなデータ ソース間の相関を実行できます。 Log Analytics に Azure AD アクティビティ ログを統合すると、次のようなタスクを実行できます。

 * Azure Security Center によって公開されたセキュリティ ログに対して Azure AD のサインイン ログを比較します

 * Azure Application Insights からのアプリケーション パフォーマンス データを相関させることによって、アプリケーションのサインイン ページでのパフォーマンス ボトルネックのトラブルシューティングを行います。  

Ignite セッションの次のビデオでは、実用的なユーザー シナリオで Azure AD ログに対して Log Analytics を使用する利点が実演されています。

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

この記事では、Azure Monitor を使用して Azure Active Directory (Azure AD) のログを Log Analytics と統合する方法について説明します。

## <a name="supported-reports"></a>サポートされるレポート

監査アクティビティ ログとサインイン アクティビティ ログを Log Analytics にルーティングして、さらに詳しく分析できます。 

* **監査ログ**: テナント内で実行されたすべてのタスクの履歴は、[監査ログ アクティビティ レポート](concept-audit-logs.md)で把握できます。
* **サインイン ログ**: 監査ログによって報告されたタスクをだれが実行したかは、[サインイン アクティビティ レポート](concept-sign-ins.md)で判断することができます。

> [!NOTE]
> 現時点では、B2C 関連の監査およびサインインのアクティビティ ログはサポートされません。
>

## <a name="prerequisites"></a>前提条件 

この機能を使用するには、次が必要です。

* Azure サブスクリプション。 Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure AD テナント。
* Azure AD テナントの "*グローバル管理者*" または "*セキュリティ管理者*" であるユーザー。
* Azure サブスクリプションの Log Analytics ワークスペース。 [Log Analytics ワークスペースの作成方法](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)を確認してください。

## <a name="send-logs-to-log-analytics"></a>ログを Log Analytics に送信する

1. [Azure Portal](https://portal.azure.com) にサインインします。 

2. **[Azure Active Directory]** > **[診断設定]** -> **[Add diagnostic setting]\(診断設定の追加\)** を選択します。 **[監査ログ]** または **[サインイン]** ページから **[エクスポート設定]** を選択して、診断設定の構成ページに移動することもできます。  
    
3. **[診断設定]** メニューで **[Log Analytics への送信]** チェック ボックスをオンにして、**[構成]** を選択します。

4. ログ送信先の Log Analytics ワークスペースを選択するか、表示されたダイアログ ボックスで新しいワークスペースを作成します。  

5. 次のいずれかまたは両方を実行します。
    * 監査ログを Log Analytics ワークスペースに送信するには、**[AuditLogs]** チェックボックスをオンにします。 
    * サインイン ログを Log Analytics ワークスペースに送信するには、**[SignInLogs]** チェックボックスをオンにします。

6. **[保存]** を選択して設定を保存します。

    ![診断設定](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. 約 15 分後に、イベントが Log Analytics ワークスペースにストリーミングされていることを確認します。

## <a name="next-steps"></a>次の手順

* [Log Analytics で Azure AD のアクティビティ ログを分析する](howto-analyze-activity-logs-log-analytics.md)
* [Azure Active Directory 用の Log Analytics ビューのインストールと使用](howto-install-use-log-analytics-views.md)
