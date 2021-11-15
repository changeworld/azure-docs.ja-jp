---
title: Azure Active Directory のログを Azure Monitor ログにストリーミングする | Microsoft Docs
description: Azure Active Directory のログを Azure Monitor ログと統合する方法について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/09/2021
ms.author: markvi
ms.reviewer: besiler
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc4a93d102cd87097fed198f08240da7ec10e36
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336356"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Azure AD ログを Azure Monitor ログと統合する

この記事の手順に従って、Azure Active Directory (Azure AD) のログを Azure Monitor に統合します。

Azure Monitor ログに Azure AD アクティビティ ログを統合すると、次のようなタスクを実行できます。

 * 自分の Azure AD サインイン ログと、Microsoft Defender for Cloud が発行したセキュリティ ログを比較します。
  
 * Azure Application Insights からのアプリケーション パフォーマンス データを相関させることによって、アプリケーションのサインイン ページでのパフォーマンス ボトルネックのトラブルシューティングを行います。

 * Identity Protection の危険なユーザーとリスク検出ログを分析して、環境内の脅威を見つけます (パブリック プレビュー)
 
 * 認証に Active Directory 認証ライブラリ (ADAL) を使用するアプリケーションからサインインを識別します。 [ADAL は、サポート終了間近です](../develop/msal-migration.md)。

この Microsoft Ignite セッション ビデオには、実用的なシナリオで Azure AD に Azure Monitor ログを使用する利点が示されています。

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

## <a name="supported-reports"></a>サポートされるレポート

監査アクティビティ ログとサインイン アクティビティ ログを Azure Monitor ログにルーティングして、さらに詳しく分析できます。 

* **[監査ログ]** :テナント内で実行されたすべてのタスクの履歴は、[監査ログ アクティビティ レポート](concept-audit-logs.md)で把握できます。
* **サインイン ログ**:監査ログによって報告されたタスクをだれが実行したかは、[サインイン アクティビティ レポート](concept-sign-ins.md)で判断することができます。
* **プロビジョニング ログ**:[プロビジョニング ログ](../app-provisioning/application-provisioning-log-analytics.md)を利用することで、サードパーティ製アプリケーションで作成、更新、削除されたユーザーを監視できます。 
* **危険なユーザー ログ (パブリック プレビュー)** : [危険なユーザー ログ](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users)を使用して、ユーザーのリスク レベルと修復アクティビティにおける変化をモニターできます。 
* **リスク検出ログ (パブリック プレビュー)** : [リスク検出ログ](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections)を使用して、ユーザーのリスク検出をモニターし、組織で検出されたリスク アクティビティの傾向を分析できます。 


## <a name="prerequisites"></a>前提条件 

この機能を使用するには、次が必要です。

* Azure サブスクリプション。 Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure AD テナント。
* Azure AD テナントの "*グローバル管理者*" または "*セキュリティ管理者*" であるユーザー。
* Azure サブスクリプションの Log Analytics ワークスペース。 [Log Analytics ワークスペースの作成方法](../../azure-monitor/logs/quick-create-workspace.md)を確認してください。

## <a name="licensing-requirements"></a>ライセンスの要件

この機能を使用するには、Azure AD Premium P1 または P2 テナントが必要です。 テナントのライセンスの種類は、**Azure Active Directory** の「 **[概要](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)** 」ページで確認できます。

![テナント情報](./media/howto-integrate-activity-logs-with-log-analytics/tenant-information.png)
 
アクティビティ データが Premium テナントに格納される期間を知りたい場合は、「[Azure AD にデータが保存される期間](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)」を参照してください。

## <a name="send-logs-to-azure-monitor"></a>ログを Azure Monitor に送信する

1. [Azure portal](https://portal.azure.com) にサインインします。 

2. **[Azure Active Directory]**  >  **[診断設定]**  ->  **[Add diagnostic setting]\(診断設定の追加\)** を選択します。 **[監査ログ]** または **[サインイン]** ページから **[エクスポート設定]** を選択して、診断設定の構成ページに移動することもできます。  
    
3. **[診断設定]** メニューで **[Send to Log Analytics workspace]\(Log Analytics ワークスペースに送信\)** チェック ボックスをオンにして、 **[構成]** を選択します。

4. ログ送信先の Log Analytics ワークスペースを選択するか、表示されたダイアログ ボックスで新しいワークスペースを作成します。  

5. 次の一部、またはすべてを実行します。
    * 監査ログを Log Analytics ワークスペースに送信するには、 **[AuditLogs]** チェックボックスをオンにします。 
    * サインイン ログを Log Analytics ワークスペースに送信するには、 **[SignInLogs]** チェックボックスをオンにします。
    * 非対話型ユーザー サインイン ログを Log Analytics ワークスペースに送信するには、 **[NonInteractiveUserSignInLogs]** チェック ボックスをオンにします。
    * サービス プリンシパル サインイン ログを Log Analytics ワークスペースに送信するには、 **[ServicePrincipleSignInLogs]** チェック ボックスをオンにします。
    * マネージド ID のサインイン ログを Log Analytics ワークスペースに送信するには、 **[anagedIdentitySignInLogs]** チェック ボックスをオンにします。
    * プロビジョニング ログを Log Analytics ワークスペースに送信するには、 **[ProvisioningLogs]** チェック ボックスをオンにします。
    * Active Directory フェデレーション サービス (ADFS) のサインイン ログを Log Analytics ワークスペースに送信するには、 **[ADFSSignInLogs]** を選択します。
    * 危険なユーザー ログを Log Analytics ワークスペースに送信するには、 **[RiskyUsers]** チェック ボックスをオンにします。 (パブリック プレビュー)
    * リスク検出ログを Log Analytics ワークスペースに送信するには、 **[UserRiskEvents]** チェック ボックスをオンにします。 (パブリック プレビュー)

6. **[保存]** を選択して設定を保存します。

    ![診断設定](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. 約 15 分後に、イベントが Log Analytics ワークスペースにストリーミングされていることを確認します。

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログ を使用して Azure AD アクティビティ ログを分析する](howto-analyze-activity-logs-log-analytics.md)
* [Azure Active Directory ログ分析用のビューのインストールと使用](howto-install-use-log-analytics-views.md)
