---
title: Azure ExpressRoute 回線のメンテナンスのアラートを確認および構成する方法
description: Azure portal の Service Health のページで、ExpressRoute 回線のメンテナンスのカスタム アラートを構成する方法について説明します。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: how-to
ms.date: 05/10/2021
ms.author: mialdrid
ms.openlocfilehash: 93335c6f163e016c619fd4803a595ffcc3418a9d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735267"
---
# <a name="how-to-view-and-configure-alerts-for-azure-expressroute-circuit-maintenance"></a>Azure ExpressRoute 回線のメンテナンスのアラートを確認および構成する方法

ExpressRoute では Azure Service Health を使用して、計画している ExpressRoute 回線のメンテナンスの通知を行います。 Service Health により、Azure portal で、計画している、あるいは実施済みのメンテナンスを確認することや、各自の条件に合うようアラートと通知を構成することができます。 Azure Service Health の詳細は「[Azure Service Health とは](../service-health/overview.md)」をご覧ください。

> [!NOTE]
> * メンテナンス アクティビティの期間、またはいずれかの接続に影響を与える計画外のイベント時に、Microsoft では、AS パス プリペンディングを使用して、正常な接続にトラフィックをドレインする方法を優先します。 Microsoft によりパス プリペンドが構成されている場合にトラフィックを正常なパスにルーティングできること、およびサービスの中断が発生しないように、必要なルート アドバタイズが適切に構成されていること確認する必要があります。 
> * ステートフル デバイスで ExpressRoute BGP 接続を終了すると、Microsoft または ExpressRoute プロバイダーによる計画または計画外のメンテナンス期間にフェールオーバーの問題が発生する可能性があります。 設定をテストして、トラフィックが適切にフェールオーバーされることを確認し、可能な場合は、ステートレス デバイスで BGP セッションを終了する必要があります。
>

## <a name="view-planned-maintenance"></a>計画しているメンテナンスを確認する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Service Health のページに移動します。 

    :::image type="content" source="./media/maintenance-alerts/search-service-health.png" alt-text="Service Health のページを検索する操作のスクリーンショット。"::: 

1. ページ左側の *[有効なイベント]* の **[計画メンテナンス]** を選択します。 このページで、フィルターを使用してサブスクリプション、Azure リージョン、Azure サービスを絞り込み、メンテナンス イベントを個別に表示できます。

    :::image type="content" source="./media/maintenance-alerts/view-maintenance.png" alt-text="Service Health の計画メンテナンス ページのスクリーンショット。" lightbox="./media/maintenance-alerts/view-maintenance-expanded.png"::: 

1. *[サービス]* のドロップダウン メニューから **ExpressRoute** を選択し、ExpressRoute 関連のメンテナンスだけを表示します。 次に、リストから問題を選択してイベントの概要を確認します。 実行中のメンテナンスの詳細を確認するには **[Issues updates]\(問題の最新情報\)** タブを選択します。

    :::image type="content" source="./media/maintenance-alerts/summary.png" alt-text="ExpressRoute のメンテナンスの概要のスクリーンショット。" lightbox="./media/maintenance-alerts/summary-expanded.png":::

## <a name="view-past-maintenance"></a>過去のメンテナンスを表示する

1. 過去のメンテナンス イベントを表示するには、ページ左側の *[履歴]* セクションの **[正常性の履歴]** を選択します。 

    :::image type="content" source="./media/maintenance-alerts/health-history.png" alt-text="Service Health で [正常性の履歴] を選択する操作のスクリーンショット。" lightbox="./media/maintenance-alerts/health-history-expanded.png"::: 

1. このページで、フィルターを使用してサブスクリプションと Azure リージョンを絞り込み、メンテナンス イベントを個別に表示できます。 正常性の履歴のイベントをさらに絞り込むには、正常性の履歴の種類を選択し、過去の期間を指定します。 計画している ExpressRoute 回路のメンテナンスを絞り込むには、正常性イベントの種類を **[計画メンテナンス]** に設定します。

    :::image type="content" source="./media/maintenance-alerts/past-maintenance.png" alt-text="正常性の履歴のページに表示された過去のメンテナンスのスクリーンショット。" lightbox="./media/maintenance-alerts/past-maintenance-expanded.png"::: 

## <a name="create-alerts-and-notifications-for-maintenance-events"></a>メンテナンス イベントのアラートと通知を作成する

1. Azure Service Health では、メンテナンス イベントのカスタム アラートを使用できます。 ExpressRoute 回路のメンテナンスのアラートを構成するには、ページ左側の **[アラート]** セクションの *[正常性アラート]* に移動します。 構成済みのアラートの表がここに表示されます。

1.  新しいアラートを作成するには、ページ上部の **[サービス正常性アラートの追加]** を選択します。

    :::image type="content" source="./media/maintenance-alerts/health-alerts.png" alt-text="Service Health で正常性アラートを選択する操作のスクリーンショット。" lightbox="./media/maintenance-alerts/health-alerts-expanded.png"::: 

1. 次の情報を選択または入力してアラート ルールを作成します。

    | カテゴリ | 設定 | 値 | 
    | --- | -------- | ----- |
    | **Condition** | サブスクリプション | 目的のサブスクリプションを選択します。 |
    |               | サービス | *ExpressRoute \ ExpressRoute 回路* |
    |               | リージョン | リージョンを選択します。全リージョンの正常性イベントを表示したい場合は "*グローバル*" のままにします。
    |               | イベントの種類 | *[計画メンテナンス]* を選択します。 |
    | **アクション** | アクション グループ名 | "*アクション グループ*" では、通知の種類と宛先を指定します。 アクション グループの作成と管理に関する情報は「[Azure Portal でのアクション グループの作成および管理](../azure-monitor/alerts/action-groups.md)」をご覧ください。 |
    | **アラート ルールの詳細** | アラート ルール名 | アラート ルールを特定するための "*名前*" を入力します。 |
    |                        | 説明 | このアラート ルールの機能の説明を入力します。 | 
    |                        | リソース グループにアラート ルールを保存します | このアラート ルールを作成する "*リソース グループ*" を選択します。 |
    |                        | 作成後にアラート ルールを有効にする | 作成後にこのアラート ルールを有効にするには、このチェック ボックスをオンにします。 |

1. **[アラート ルールの作成]** を選択して構成を保存します。

    :::image type="content" source="./media/maintenance-alerts/create-alert-rule.png" alt-text="[アラート ルールの作成] ページのスクリーンショット。"::: 

## <a name="next-steps"></a>次の手順

* [Azure ExpressRoute](expressroute-introduction.md)、[ネットワーク分析情報](../azure-monitor/insights/network-insights-overview.md)、[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) の詳細を参照する
* [メトリックをカスタマイズ](expressroute-monitoring-metrics-alerts.md)して[接続モニター](../network-watcher/connection-monitor-overview.md)を作成する

