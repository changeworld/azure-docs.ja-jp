---
title: Azure Application Insights のスマート検出 – 既定の通知受信者に対する今後の変更 | Microsoft Docs
description: トレースのテレメトリで異常なパターンを検出するために、Azure Application Insights でアプリケーション トレースを監視します。
services: application-insights
author: harelbr
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 02/12/2019
ms.author: harelbr
ms.openlocfilehash: 4bcbed82a78caff62a9459ecb44c6513f367f6b7
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458199"
---
# <a name="smart-detection-e-mail-notification-change"></a>スマート検出の電子メール通知に関する変更

お客様からのフィードバックに基づいて、2019 年 4 月 1 日付けで、スマート検出から電子メール通知を受信する既定のロールが変更されます。

## <a name="what-is-changing"></a>何が変わるのですか?

現在、スマート検出の電子メール通知は、既定では_サブスクリプションの所有者_、_サブスクリプションの共同作成者_、および_サブスクリプションの閲覧者_の各ロールに送信されています。 これらのロールには、多くの場合、監視に積極的に関与していないユーザーが含まれているため、多数のユーザーが不必要に通知を受信する原因になっています。 このエクスペリエンスを改善するために、電子メール通知は既定では[監視閲覧者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)ロールと[監視共同作業者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)ロールのみに送信されるようにするための変更が進行しています。

## <a name="scope-of-this-change"></a>この変更の範囲

この変更は、以下を除くすべてのスマート検出ルールに影響します。

* プレビューとしてマークされているスマート検出ルール。 これらのスマート検出ルールでは、現在、電子メール通知はサポートされていません。

* 失敗の異常ルール。 このルールは、クラシック アラートから統合アラート プラットフォームに移行された後、新しい既定のロールへの適用が開始されます (詳細については、[こちら](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)をご覧ください)。

## <a name="how-to-prepare-for-this-change"></a>この変更に備える方法は?

スマート検出の電子メール通知が関連するユーザーに確実に送信されるようにするには、それらのユーザーを[監視閲覧者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)ロールと[監視共同作業者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)ロールに割り当てる必要があります。 割り当ては、サブスクリプション レベルで行う (サブスクリプション内のすべての Application Insights リソースに影響します) か、Application Insights リソース レベルで行う (特定のリソースのみに影響します) 必要があります。

Azure portal を使用してユーザーを監視閲覧者または監視共同作業者に割り当てるには、「[ロールの割り当てを追加する](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)」で説明されている手順に従います。 ユーザーが割り当てられるロールとして、必ず "_監視閲覧者_" または "_監視共同作業者_" を選択してください。

> [!NOTE]
> ルール設定の "_追加の電子メール受信者_" オプションを使用して構成されるスマート検出通知の特定の受信者は、この変更による影響を受けません。 これらの受信者は、引き続き電子メール通知を受信します。

この変更に関する質問や懸念がある場合は、ご遠慮なく[お問い合わせ](mailto:smart-alert-feedback@microsoft.com)ください。

## <a name="next-steps"></a>次の手順

スマート検出の詳細を確認します。

- [失敗の異常](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [メモリ リーク](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [パフォーマンスの異常](../../azure-monitor/app/proactive-performance-diagnostics.md)