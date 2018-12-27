---
title: Azure Active Directory 監視とは  (プレビュー) | Microsoft Docs
description: Azure Active Directory 監視の概要について説明します。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 92e200f358098d5516246d057d1cef406750e326
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264684"
---
# <a name="what-is-azure-active-directory-monitoring-preview"></a>Azure Active Directory 監視とは (プレビュー)

Azure Active Directory (Azure AD) 監視を使用すると、Azure AD のアクティビティ ログを別のエンドポイントにルーティングできるようになります。 その後、それを長期的な使用のために保持したり、サードパーティのセキュリティ情報およびイベント管理 (SIEM) ツールと統合して環境の分析情報を取得したりすることができます。

現時点では、以下のものにログをルーティングすることができます。

- Azure ストレージ アカウント。
- Azure イベント ハブ。Splunk および Sumologic のインスタンスと統合することができます。
- Azure Log Analytics ワークスペース。このワークスペースで、データの分析や、特定のイベントのダッシュボードとアラートの作成を行うことができます。

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="diagnostic-settings-configuration"></a>診断設定を構成する

Azure AD アクティビティ ログの監視設定を構成するには、まず、[Azure portal](https://portal.azure.com) にサインインし、次に **Azure Active Directory** を選択します。 ここからは、次の 2 つの方法で診断設定の構成のページにアクセスすることができます。

* **[監視]** セクションの **[診断設定]** を選択します。

    ![診断設定](./media/overview-monitoring/diagnostic-settings.png)
    
* **[監査ログ]** または **[サインイン]** を選択し、**[設定のエクスポート]** を選択します。 

    ![設定のエクスポート](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>ストレージ アカウントへのログのルーティング

ログを Azure ストレージ アカウントにルーティングすると、[保持ポリシー](reference-reports-data-retention.md)で規定されている既定の保持期間より長くログを保持できます。 データをストレージ アカウントにルーティングする方法については、[こちら](quickstart-azure-monitor-route-logs-to-storage-account.md)を参照してください。

## <a name="stream-logs-to-event-hub"></a>ログをイベント ハブにストリーミングする

ログを Azure イベント ハブにルーティングすることで、Sumologic や Splunk などのサードパーティの SIEM ツールと統合することができます。 この統合によって、Azure AD のアクティビティ ログ データと、SIEM によって管理されている他のデータを組み合わせ、より豊富な環境分析情報を提供することができます。 ログをイベント ハブにストリーム配信する方法については、[こちら](tutorial-azure-monitor-stream-logs-to-event-hub.md)を参照してください。

## <a name="send-logs-to-log-analytics"></a>ログを Log Analytics に送信する

[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) は、別のソースからの監視データを統合し、アプリケーションとリソースの操作に関する分析情報を取得するためのクエリ言語と分析エンジンを提供するソリューションです。 Azure AD のアクティビティ ログを Log Analytics に送信することで、収集したデータに対する迅速な取得、監視、およびアラートを行うことができます。 Log Analytics にデータを送信する方法については、[こちら](howto-integrate-activity-logs-with-log-analytics.md)を参照してください。

Azure AD のアクティビティ ログ用の既製のビューをインストールして、サインインと監査イベントを含む一般的なシナリオを監視することもできます。 Azure AD のアクティビティ ログ用の Log Analytics ビューをインストールして使用する方法については、[こちら](howto-install-use-log-analytics-views.md)を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Monitor でのアクティビティ ログ](concept-activity-logs-azure-monitor.md)
* [ログをイベント ハブにストリーム配信する](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [ログを Log Analytics に送信する](howto-integrate-activity-logs-with-log-analytics.md)
