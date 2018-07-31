---
title: チュートリアル - Azure Active Directory のログを Azure ストレージ アカウントにアーカイブする (プレビュー) | Microsoft Docs
description: Azure Active Directory のログをストレージ アカウントにプッシュするよう Azure 診断を設定する方法について説明します (プレビュー)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240245"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>チュートリアル: Azure Active Directory のログを Azure ストレージ アカウントにアーカイブする (プレビュー)

このチュートリアルでは、Azure Active Directory のログを Azure ストレージ アカウントにルートするよう Azure Monitor の診断設定をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件 

必要なもの:

* Azure ストレージ アカウントが付属した Azure サブスクリプション。 Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure Active Directory テナント。
* そのテナントのグローバル管理者またはセキュリティ管理者であるユーザー。

## <a name="archive-logs-to-an-azure-storage-account"></a>ログを Azure ストレージ アカウントにアーカイブする

1. [Azure Portal](https://portal.azure.com) にサインインします。 
2. **[Azure Active Directory]** -> **[アクティビティ]** -> **[監査ログ]** の順にクリックします。 
3. **[エクスポート設定]** をクリックして [診断設定] ブレードを開きます。 **[設定の編集]** をクリックして既存の設定を変更するか、**[Add diagnostic setting]\(診断設定の追加\)** をクリックして新しい設定を追加します。 最大で 3 つの設定を作成できます。 
    ![エクスポート設定](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "エクスポート設定")

4. 目的を忘れないように、設定にわかりやすい名前を追加します。 たとえば、"Azure ストレージ アカウントに送信" などにします。 
5. **[Archive to a storage account]\(ストレージ アカウントにアーカイブ\)** チェックボックスをオンにし、**[ストレージ アカウント]** をクリックして Azure ストレージ アカウントを選択します。 
6. ログをルートする Azure サブスクリプションとストレージ アカウントを選択し、**[OK]** をクリックして構成を閉じます。
7. **[AuditLogs]** チェックボックスをオンにして、監査ログをストレージ アカウントに送信します。 
8. **[SignInLogs]** チェックボックスをオンにして、サインイン ログをストレージ アカウントに送信します。
9. スライダーを使用してログ データのリテンション期間を設定します。 既定ではこの値は "0" になっており、ログはストレージ アカウントに無期限に保持されます。 別の値を設定すると、その選択した日数より古いイベントが自動的にクリーンアップされます。
10. **[保存]** をクリックして設定を保存します。
    ![診断設定](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "診断設定")

11. 約 15 分後、ログが対象のストレージ アカウントにプッシュされていることを確認します。 Azure portal に移動し、**[ストレージ アカウント]** をクリックして前に使用したストレージ アカウントを選択し、**[BLOB]** をクリックします。 
12. **[監査ログ]** については、**[insights-log-audit]** をクリックします。 **[サインイン ログ]** については、**[insights-logs-signin]** をクリックします。
    ![ストレージ アカウント](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "ストレージ アカウント")

## <a name="next-steps"></a>次の手順

* [Azure Monitor で監査ログのスキーマを解釈する](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Azure Monitor でサインイン ログのスキーマを解釈する](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [よく寄せられる質問と既知の問題](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)