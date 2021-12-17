---
title: Azure Active Directory Identity Protection データのエクスポートと使用
description: Azure Active Directory Identity Protection 内で長期的なデータを使用して調査する方法を説明します
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/30/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f723b6bf700b1dc18bea90b4ed0be00356c716a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300748"
---
# <a name="how-to-export-risk-data"></a>方法: リスク データをエクスポートする

Azure AD により、定義された期間にわたるレポートとセキュリティ シグナルが格納されます。 リスク情報に関しては、これが十分に長い期間ではない可能性があります。

| レポート/シグナル | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| --- | --- | --- | --- |
| 監査ログ | 7 日 | 30 日 | 30 日 |
| サインイン | 7 日 | 30 日 | 30 日 |
| Azure AD MFA の使用状況 | 30 日 | 30 日 | 30 日 |
| リスクの高いサインイン | 7 日 | 30 日 | 30 日 |

組織では、Azure AD 内の診断設定を変更して、**RiskyUsers** と **UserRiskEvents** のデータを Log Analytics ワークスペースに送信すること、データをストレージ アカウントにアーカイブすること、データをイベント ハブにストリーミングすること、またはデータをパートナー ソリューションに送信することにより、データの保存期間を長くすることができます。 これらのオプションは、**Azure portal** >  **[Azure Active Directory]** 、 **[診断設定]**  >  **[設定の編集]** にあります。 診断設定がない場合は、「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](../../azure-monitor/essentials/diagnostic-settings.md)」の記事に記載されている手順に従って作成します。

>[!NOTE]
>RiskyUsers と UserRiskEvents の診断設定は、現在パブリック プレビュー段階にあります。

[ ![既存の構成を表示している Azure AD 内の診断設定画面](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png) ](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png#lightbox)

## <a name="log-analytics"></a>Log Analytics

Log Analytics を使用すると、組織では組み込みクエリまたはカスタムで作成された Kusto クエリを使用してデータを照会できます。詳細については、「[Azure Monitor でログ クエリの使用を開始する](../../azure-monitor/logs/get-started-queries.md)」を参照してください。

有効にすると、Log Analytics へのアクセスが **[Azure portal]**  >  **[Azure AD]**  >  **[Log Analytics]** に表示されます。 Identity Protection 管理者にとって最も関心のあるテーブルは **AADRiskyUsers** と **AADUserRiskEvents** です。

- AADRiskyUsers - Identity Protection の **危険なユーザー** レポートのようなデータを提供します。
- AADUserRiskEvents - Identity Protection の **リスク検出** レポートのようなデータを提供します。

[ ![上位 5 つのイベントを示す AADUserRiskEvents テーブルに対するクエリを表示している Log Analytics ビュー ](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png) ](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png#lightbox)

上の図では、次のクエリが実行され、トリガーされた最新の 5 つのリスク検出が示されています。 

```kusto
AADUserRiskEvents
| take 5
```

もう 1 つのオプションは、AADRiskyUsers テーブルに対してクエリを実行して、危険なユーザーをすべて表示する方法です。

```kusto
AADRiskyUsers
```

> [!NOTE]
> Log Analytics では、ストリーミング中のデータのみが可視化されます。 Azure AD からのイベントの送信を有効にする前のイベントは表示されません。

## <a name="storage-account"></a>ストレージ アカウント

ログを Azure ストレージ アカウントにルーティングすると既定の保持期間より長く保持できます。 詳細については、「[チュートリアル: Azure AD のログを Azure ストレージ アカウントにアーカイブする](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)」の記事をご覧ください。

## <a name="azure-event-hubs"></a>Azure Event Hubs

Azure Event Hubs では、Azure AD Identity Protection のようなソースからの受信データを参照し、リアルタイムの分析と相関関係を提供できます。 詳細については、「[チュートリアル: Azure Active Directory ログを Azure イベント ハブにストリーム配信する](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)」の記事を参照してください

## <a name="other-options"></a>その他のオプション

組織では、さらに処理するために [Azure AD データを Microsoft Sentinel に接続](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)することも選択できます。

組織では、[Microsoft Graph API を使用して、リスク イベントをプログラムで操作](howto-identity-protection-graph-api.md)できます。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory の監視とは](../reports-monitoring/overview-monitoring.md)
- [Azure Active Directory ログ分析用のビューのインストールと使用](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Azure Active Directory (Azure AD) Identity Protection からデータを接続する](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)
- [Azure Active Directory Identity Protection と Microsoft Graph PowerShell SDK](howto-identity-protection-graph-api.md)
- [チュートリアル:Azure Active Directory ログを Azure イベント ハブにストリーム配信する](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
