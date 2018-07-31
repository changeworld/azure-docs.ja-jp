---
title: チュートリアル - Azure Active Directory ログを Azure イベント ハブにストリームする (プレビュー) | Microsoft Docs
description: Azure Active Directory のログをイベント ハブにプッシュするよう Azure 診断を設定する方法について説明します (プレビュー)
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
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240219"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>チュートリアル - Azure Active Directory ログを Azure イベント ハブにストリームする (プレビュー)

このチュートリアルでは、Azure Active Directory のログを Azure イベント ハブにストリームするよう Azure Monitor の診断設定をセットアップする方法について説明します。 このメカニズムを使用して、ログを Splunk や QRadar などのサードパーティ製の SIEM ツールに統合します。

## <a name="prerequisites"></a>前提条件 

必要なもの:

* Azure サブスクリプション。 Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure Active Directory テナント
* そのテナントのグローバル管理者またはセキュリティ管理者であるユーザー
* Azure サブスクリプション内の Event Hubs 名前空間とイベント ハブ。 [作成する方法はこちら](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md)をご覧ください。

## <a name="archive-logs-to-event-hub"></a>ログをイベント ハブにアーカイブする

1. [Azure Portal](https://portal.azure.com) にサインインします。 
2. **[Azure Active Directory]** -> **[アクティビティ]** -> **[監査ログ]** の順にクリックします。 
3. **[エクスポート設定]** をクリックして [診断設定] ブレードを開きます。 **[設定の編集]** をクリックして既存の設定を変更するか、**[Add diagnostic setting]\(診断設定の追加\)** をクリックして新しい設定を追加します。 最大で 3 つの設定を作成できます。 
    ![エクスポート設定](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "エクスポート設定")

4. **[Stream to an event hub]\(イベント ハブにストリーム\)** チェックボックスをオンにし、**[Event Hub/Configure]\(イベント ハブ/構成\)** をクリックします。
5. ログをルートする Azure サブスクリプションと Event Hubs 名前空間を選択します。 サブスクリプションと Event Hubs 名前空間は、両方ともログのストリーム元である Active Directory テナントと関連付けられている必要があります。 Event Hubs 名前空間内にログの送信先となるイベント ハブを指定することもできます。 イベント ハブを指定しない場合、イベント ハブは名前空間内に既定の名前 **insights-logs-audit** で作成されます。
6. **[OK]** をクリックしてイベント ハブの構成を終了します。
7. **[AuditLogs]** チェックボックスをオンにして、監査ログをストレージ アカウントに送信します。 
8. **[SignInLogs]** チェックボックスをオンにして、サインイン ログをストレージ アカウントに送信します。
9. **[保存]** をクリックして設定を保存します。
    ![診断設定](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "診断設定")

10. 約 15 分後にイベントがイベント ハブに表示されることを確認します。 これを行うには、ポータルからイベント ハブに移動し、**受信メッセージ**の数がゼロより大きい値になっていることを確認します。 
    ![監査ログ](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "監査ログ")


## <a name="access-data-from-event-hubs"></a>Event Hubs からデータにアクセスする

イベント ハブにデータが表示されたら、次の 2 つの方法でアクセスできます。

* **データを読み取るようにサポートされる SIEM ツールを構成する**: ほとんどのツールは、イベント ハブからデータを読み取るために、イベント ハブ接続文字列と、Azure サブスクリプションへの特定のアクセス許可が必要です。 Azure Monitor と統合できるツールの一覧 (一部) を次に示します。
    1. **Splunk**: Azure AD のログを Splunk と統合する方法について詳しくは、[Azure Monitor を使用して Azure Active Directory のログを Splunk と統合する方法](reporting-azure-monitor-diagnostics-splunk-integration.md)に関する記事をご覧ください。
    
    2. **IBM QRadar**: Microsoft Azure DSM および Microsoft Azure Event Hub Protocol は、[IBM サポート Web サイト](http://www.ibm.com/support)からダウンロードすることができます。 Azure との統合の詳細については、[こちら](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)を参照してください。
    
    3. **SumoLogic**: [こちらの指示](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)に従ってイベント ハブのデータを使用するように SumoLogic を設定してください。 

* **データの読み取るようにカスタム ツールを設定する**: 現在お使いの SIEM がまだ Azure Monitor 診断でサポートされていない場合は、Event Hub API を使用してカスタム ツールを設定できます。 詳しくは、[Event Hub API](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph) に関する記事をご覧ください。


## <a name="next-steps"></a>次の手順

* [Azure Monitor 診断を使用して Azure Active Directory のログを Splunk と統合する](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [Azure Monitor 診断で監査ログのスキーマを解釈する](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Azure Monitor 診断でサインイン ログのスキーマを解釈する](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)