---
title: Azure Sentinel の新着情報のアーカイブ
description: 6 か月前以前の、Azure Sentinel の新機能と変更点の説明。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: 1e629b52b7904d2f671c9d472916ba0a2185a986
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104751"
---
# <a name="archive-for-whats-new-in-azure-sentinel"></a>Azure Sentinel の新着情報のアーカイブ

「[Azure Sentinel の新着情報](whats-new.md)」という主要リリース ノート ページには、過去 6 か月間の更新情報が含まれていますが、このページにはそれ以前の項目が含まれています。

以前に提供された機能については、[Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)を参照してください。

記載されている機能は、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。


> [!TIP]
> Microsoft の脅威ハンティング チームは、クエリ、プレイブック、ブック、およびノートブックを [Azure Sentinel コミュニティ](https://github.com/Azure/Azure-Sentinel) に投稿します。これには、ユーザーのチームが適応して使用できる特定の[ハンティング クエリ](https://github.com/Azure/Azure-Sentinel)などが含まれます。
>
> ユーザーも投稿することができます。 [Azure Sentinel Threat Hunters GitHub コミュニティ](https://github.com/Azure/Azure-Sentinel/wiki)にぜひご参加ください。
>

## <a name="november-2020"></a>2020 年 11 月

- [Azure Sentinel でプレイブックの正常性を監視する](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender コネクタ (パブリック プレビュー)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Azure Sentinel でプレイブックの正常性を監視する

Azure Sentinel プレイブックは、[Azure Log Apps](../logic-apps/index.yml) に組み込まれたワークフローに基づいています。これは、タスク、ビジネス プロセス、ワークフローのスケジュール、自動化、オーケストレーションに役立つクラウド サービスです。 プレイブックは、インシデントの作成時、またはインシデントのトリアージおよび操作時に自動的に呼び出すことができます。 

プレイブックの正常性、パフォーマンス、使用状況に関する分析情報を提供するために、**プレイブックの正常性監視** という名前の [ブック](../azure-monitor/visualize/workbooks-overview.md)を追加しました。 

**プレイブックの正常性監視** ブックを使用して、プレイブックの正常性の監視や、成功または失敗した実行量の異常の検出を行います。 

**プレイブックの正常性監視** ブックは、Azure Sentinel テンプレート ギャラリーで入手できるようになりました。

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="プレイブックの正常性監視ブックのサンプル":::

詳細については、次を参照してください。

- [Logic Apps のドキュメント](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Azure Monitor のドキュメント](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender コネクタ (パブリック プレビュー)
 
Azure Sentinel 用の Microsoft 365 Defender コネクタを使用すると、Microsoft 365 Defender から Azure Sentinel に高度なハンティング ログ (未加工のイベント データの種類) をストリーミングできます。 

[Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) と [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) セキュリティ保護の統合により、Microsoft 365 Defender コネクタを使用して、Microsoft Defender for Endpoint の高度なハンティング イベントを収集し、Azure Sentinel ワークスペースの新しい用途別のテーブルに直接ストリーミングできるようになりました。 

Azure Sentinel テーブルは、Microsoft 365 Defender ポータルで使用されるものと同じスキーマに基づいて構築されており、高度なハンティング ログの完全なセットに完全にアクセスすることができます。 

詳細については、「[Microsoft 365 Defender から Azure Sentinel にデータを接続する](connect-microsoft-365-defender.md)」を参照してください。

> [!NOTE]
> Microsoft 365 Defender は、以前は Microsoft Threat Protection または MTP と呼ばれていました。 Microsoft Defender for Endpoint は、以前は Microsoft Defender Advanced Threat Protection または MDATP と呼ばれていました。
> 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Azure Sentinel をオンボードする](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[アラートの視覚化](quickstart-get-visibility.md)
