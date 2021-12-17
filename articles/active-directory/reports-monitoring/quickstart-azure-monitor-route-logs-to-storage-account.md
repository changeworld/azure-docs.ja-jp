---
title: チュートリアル - ディレクトリのログをストレージ アカウントにアーカイブする | Microsoft Docs
description: Azure Active Directory のログをストレージ アカウントにプッシュするよう Azure Diagnostics を設定する方法について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/05/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f170cd4f3530ea7391d2fbd039263c29540321f8
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995466"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>チュートリアル:Azure AD のログを Azure ストレージ アカウントにアーカイブする

このチュートリアルでは、Azure Active Directory (Azure AD) のログを Azure ストレージ アカウントにルートするよう Azure Monitor の診断設定をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件 

この機能を使用するには、次が必要です。

* Azure ストレージ アカウントが付属した Azure サブスクリプション。 Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure AD テナント。
* Azure AD テナントの "*グローバル管理者*" または "*セキュリティ管理者*" であるユーザー。

## <a name="archive-logs-to-an-azure-storage-account"></a>ログを Azure ストレージ アカウントにアーカイブする

1. [Azure portal](https://portal.azure.com) にサインインします。 

2. **[Azure Active Directory]**  >  **[監視]**  >  **[監査ログ]** の順に選択します。 

3. **[データ設定のエクスポート]** を選択します。 

4. **[診断設定]** ウィンドウで、次のいずれかの操作を実行します。
    1. 既存の設定を変更するには、更新する診断設定の横にある **[設定の編集]** を選択します。
    1. 新しい設定を追加するには、 **[断設定を追加する]** を選択します。  

    最大で 3 つの設定を作成できます。

     ![設定のエクスポート](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. **[診断設定]** ペインが表示されたら、新しい設定を作成している場合は、目的がわかるような設定の名前を入力します (例: "*Azure ストレージ アカウントに送信する*")。 既存の設定の名前を変更することはできません。

6. **[宛先の詳細]** で、 **[ストレージ アカウントへのアーカイブ]** チェック ボックスをオンにします。 

7. **[サブスクリプション]** ドロップダウン メニューで Azure サブスクリプションを選択し、 **[ストレージ アカウント]** ドロップダウン メニューでログをルーティングするストレージ アカウントを選択します。

8. **[カテゴリの詳細]** で、関連するすべてのカテゴリを選択します。

    次のいずれかまたは両方を実行します。
    1. 監査ログをストレージ アカウントに送信するには、 **[AuditLogs]** チェック ボックスをオンにします。
    
    1. サインイン ログをストレージ アカウントに送信するには、 **[SignInLogs]** チェック ボックスをオンにします。

    ![診断設定](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

9. カテゴリを選択した後、 **[リテンション期間の日数]** フィールドに、ログ データに必要な保持日数を入力します。 既定ではこの値は *0* になっています。つまり、ログはストレージ アカウントに無期限に保持されます。 別の値を設定すると、その選択した日数より古いイベントが自動的にクリーンアップされます。
 
10. **[保存]** を選択して設定を保存します。

11. ウィンドウを閉じて、[診断設定] ペインに戻ります。

## <a name="next-steps"></a>次のステップ

* [Azure Monitor で監査ログのスキーマを解釈する](./overview-reports.md)
* [Azure Monitor でサインイン ログのスキーマを解釈する](reference-azure-monitor-sign-ins-log-schema.md)
* [よく寄せられる質問と既知の問題](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
