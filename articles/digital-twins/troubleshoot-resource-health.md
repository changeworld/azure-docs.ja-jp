---
title: 'トラブルシューティング: リソースの正常性'
titleSuffix: Azure Digital Twins
description: Azure Resource Health を使用して Azure Digital Twins インスタンスの状態を確認する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 9c816e613fe6f495de9abb57e2ac041960bffdbe
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131450"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Azure Digital Twins のトラブルシューティング: リソース ヘルス

[Azure Service Health](../service-health/index.yml) は、Azure リソースに影響を与えるサービスの問題を診断したりサポートを受けたりするのに役立つ一連のエクスペリエンスです。 **リソース正常性** や **サービス正常性**、**状態** の情報を保持し、現在と過去の正常性情報をレポートします。

## <a name="use-azure-resource-health"></a>Azure Resource Health の使用

[Azure Resource Health](../service-health/resource-health-overview.md) は、Azure Digital Twins インスタンスが稼働しているかどうかを監視するために役立ちます。 また、リージョンで発生した停電がインスタンスの正常性に影響を与えているかどうかを確認するためにも使用できます。

インスタンスの正常性を確認するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Digital Twins インスタンスに移動します。 その名前をポータルの検索バーに入力して、検索することができます。 

2. インスタンスのメニューから、[サポートとトラブルシューティング] の下にある **[リソース正常性]** を選択します。 リソース正常性の履歴を確認できるページが表示されます。 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="[リソース正常性] ページが表示されているスクリーンショット。過去 9 日間の日次レポートが表示された [正常性の履歴] セクションがあります。":::

上の図では、このインスタンスは "**使用可能**" と示されており、過去 9 日間分あります。 "使用可能" 状態と、表示される可能性のあるその他の状態の種類の詳細については、「[Resource Health の概要](../service-health/resource-health-overview.md)」を参照してください。

さまざまな種類の Azure リソースにおけるリソースの正常性の詳細については、「[Azure Resource Health で利用できるリソースの種類と正常性チェック](../service-health/resource-health-checks-resource-types.md)」を参照してください。

## <a name="use-azure-service-health"></a>Azure Service Health を使用する

[Azure Service Health](../service-health/service-health-overview.md) は、特定のリージョンにおける Azure Digital Twins サービスの正常性をチェックしたり、進行中のサービスの問題や今後の計画メンテナンスなどのイベントを認識したりするのに役立ちます。

サービス正常性をチェックするには、[Azure portal](https://portal.azure.com) にサインインして **Service Health** サービスに移動します。 ポータルの検索バーに「サービス正常性」と入力すると見つかります。 

その後、サブスクリプション、リージョン、サービスでサービスの問題をフィルター処理できます。

Azure Service Health の詳細については、「[Service Health の概要](../service-health/service-health-overview.md)」を参照してください。

## <a name="use-azure-status"></a>[Azure の状態] を使用する

[[Azure の状態]](../service-health/azure-status-overview.md) ページには、Azure サービスおよびリージョンの正常性のグローバルなビューが提供されます。 Azure Service Health と Azure Resource Health は、特定のリソースにパーソナライズされていますが、Azure の状態はスコープが広いので、広範囲に影響するインシデントを把握するのに役立ちます。

Azure の状態をチェックするには、[[Azure の状態]](https://status.azure.com/status/) ページに移動します。 Azure サービス、そしてリージョンごとの正常性インジケーターの表が表示されます。 Azure Digital Twins を確認するには、ページ内の表から Azure Digital Twins に対応するエントリを検索します。

[Azure の状態] ページの使用について詳しくは、「[Azure の状態の概要](../service-health/azure-status-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins インスタンスを監視するその他の方法については、次の記事を参照してください。
* [トラブルシューティング: メトリック](troubleshoot-metrics.md)
* [トラブルシューティング: 診断ログ](troubleshoot-diagnostics.md)
* [トラブルシューティング: アラート](troubleshoot-alerts.md)
