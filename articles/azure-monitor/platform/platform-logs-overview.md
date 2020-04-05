---
title: Azure プラットフォーム ログの概要 | Microsoft Docs
description: Azure リソースの操作に関する高頻度の豊富なデータを提供する、Azure Monitor のログの概要。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659322"
---
# <a name="overview-of-azure-platform-logs"></a>Azure プラットフォーム ログの概要
プラットフォーム ログでは、Azure リソースとそれらが依存している Azure プラットフォームの詳細な診断情報と監査情報が提供されます。 これらは自動的に生成されますが、特定のプラットフォーム ログを保持するための 1 つ以上の送信先に転送するように構成する必要があります。 この記事では、提供される情報の内容や収集と分析のための構成方法など、プラットフォーム ログの概要について説明します。

## <a name="types-of-platform-logs"></a>プラットフォーム ログの種類
次の表に、Azure のさまざまなレイヤーで使用できる特定のプラットフォーム ログを示します。

| ログ | レイヤー | 説明 |
|:---|:---|:---|
| リソース ログ | Azure リソース | Azure リソース ("*データ プレーン*") 内で実行された操作に関する分析情報を提供します。たとえば、Key Vault からのシークレットの取得や、データベースに対する要求などです。 リソース ログの内容は、Azure サービスとリソースの種類によって異なります。<br><br>*リソース ログは、以前は診断ログと呼ばれていました。*  |
| アクティビティ ログ | Azure サブスクリプション | Service Health イベントの更新に加えて、外部 ("*管理プレーン*") からサブスクリプションの各 Azure リソースに対する操作についての分析情報を提供します。 アクティビティ ログを使用して、サブスクリプションのリソースに対して行われるすべての書き込み操作 (PUT、POST、DELETE) について、"_何を_"、"_誰が_"、"_いつ_" 行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。  Azure サブスクリプションごとに 1 つのアクティビティ ログがあります。 |
| Azure Active Directory ログ | Azure テナント |  サインイン アクティビティの履歴と、特定のテナントに対して Azure Active Directory で行われた変更の監査証跡が含まれます。 Azure Active Directory ログの詳細については、「[Azure Active Directory レポートの概要](../../active-directory/reports-monitoring/overview-reports.md)」を参照してください。   |

> [!NOTE]
> Azure アクティビティ ログは、主に Azure Resource Manager で発生したアクティビティを記録します。 クラシック/RDFE モデルを使用しているリソースは追跡されません。 一部のクラシック リソース タイプでは、Azure Resource Manager にプロキシ リソース プロバイダー (例: Microsoft.ClassicCompute) が存在します。 これらのプロキシ リソース プロバイダーを使用して Azure Resource Manager 経由でクラシック リソース タイプを操作すると、その操作がアクティビティ ログに表示されます。 Azure Resource Manager プロキシの外部でクラシック リソース タイプを操作すると、そのアクションは操作ログにのみ記録されます。 操作ログは、ポータルの別のセクションで参照できます。

![プラットフォーム ログの概要](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>プラットフォーム ログの表示
さまざまな Azure プラットフォーム ログを表示および分析するための各種オプションがあります。

- Azure portal のアクティビティ ログを表示し、PowerShell と CLI からイベントにアクセスします。 詳細については、「[Azure アクティビティ ログ イベントを表示して取得する](activity-log-view.md)」を参照してください。 
- Azure Portal で Azure Active Directory のセキュリティ レポートとアクティビティ レポートを表示します。 詳細については、「[Azure Active Directory レポートの概要](../../active-directory/reports-monitoring/overview-reports.md)」  を参照してください。
- リソース ログは、サポートされている Azure リソースによって自動的に生成されますが、[送信先](#destinations)に送信しない限り表示できません。 

## <a name="destinations"></a>変換先
監視要件に応じて、次の表に示す 1 つ以上の送信先にプラットフォーム ログを送信できます。 [診断設定を作成する](diagnostic-settings.md)ことによって、プラットフォーム ログの送信先を構成します。

| 宛先 | シナリオ | References |
|:---|:---|:---|:---|
| Log Analytics ワークスペース | 他の監視データと共にログを分析し、ログ クエリやアラートなどの Azure Monitor 機能を活用します。 | [アクティビティ ログとリソース ログ](resource-logs-collect-workspace.md)<br>[Azure Active Directory ログ](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | 監査、静的分析、バックアップのためにログをアーカイブします。 |[アクティビティ ログとリソース ログ](archive-diagnostic-logs.md)<br>[Azure Active Directory ログ](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| イベント ハブ | サード パーティのログ システムやテレメトリ システムにログをストリーミングします。  |[アクティビティ ログとリソース ログ](resource-logs-stream-event-hubs.md)<br>[Azure Active Directory ログ](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>次のステップ

* [さまざまなカテゴリのアクティビティ ログ スキーマを確認する](activity-log-schema.md)
* [さまざまな Azure サービスのリソース ログ スキーマを確認する](diagnostic-logs-schema.md)
