---
title: Azure Event Grid メトリックの表示とアラートの設定
description: この記事では、Azure Portal を使用して Azure Event Grid のトピックとサブスクリプションのメトリックを表示し、それらのアラートを作成する方法について説明します。
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 6f6c119c16452246ec6eeb57ab392b29608938a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598560"
---
# <a name="monitor-event-grid-message-delivery"></a>Event Grid メッセージ配信の監視 
この記事では、ポータルを使用して Event Grid のトピックとサブスクリプションのメトリックを表示し、それらのアラートを作成する方法について説明します。 

> [!IMPORTANT]
> Azure Event Grid でサポートされているメトリックの一覧については、[メトリック](metrics.md)に関する記事を参照してください。

## <a name="view-custom-topic-metrics"></a>カスタム トピック メトリックの表示

カスタム トピックを発行済みの場合は、そのトピックのメトリックを表示できます。 

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. トピックの検索バーに「**Event Grid トピック**」と入力し、ドロップ ダウン リストから **[Event Grid トピック]** を選択します。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid トピック を検索して選択する":::
3. トピックの一覧から対象のカスタム トピックを選択します。 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="カスタム トピックの選択":::
4. カスタム イベント トピックのメトリックが **[Event Grid トピック]** ページに表示されます。 次の図では、リソース グループ、サブスクリプションなどを示す **[Essentials]** セクションが最小化されています。 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="イベント メトリックの表示":::

    サポートされているメトリックを使用してグラフを作成するには、 **[Event Grid トピック]** ページの **[メトリック]** タブを使用します。

    :::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="トピック - メトリック ページ":::

    たとえば、**発行されたイベント** メトリックのメトリック グラフを確認します。

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="発行されたイベント メトリック":::


## <a name="view-subscription-metrics"></a>サブスクリプションのメトリックを表示する
1. 前のセクションの手順に従って、 **[Event Grid トピック]** ページに移動します。 
2. 次の例に示すように、下部のペインからサブスクリプションを選択します。 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="[イベント サブスクリプション] を選択する":::    

    また、Azure Portal の検索バーで「**Event Grid サブスクリプション**」を検索し、 **[トピックの種類]** 、 **[サブスクリプション]** 、 **[場所]** を選択してイベント サブスクリプションを確認することもできます。 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="[Event Grid サブスクリプション] ページからイベント サブスクリプションを選択する":::        

    カスタム トピックの場合は、 **[トピックの種類]** として **[Event Grid トピック]** を選択します。 システム トピックについては、Azure リソースの種類 (**ストレージ アカウント (BLOB、GPv2)** など) を選択します。 
3. サブスクリプションのホームページで、サブスクリプションのメトリックをグラフで確認します。 過去 1 時間、6 時間、12 時間、1 日、7 日間、または 30 日間の **[全般]** 、 **[エラー]** 、 **[待機時間]** メトリックを確認できます。 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="サブスクリプションのホーム ページのメトリック":::    

## <a name="view-system-topic-metrics"></a>システム トピックのメトリックを表示する

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. トピックの検索バーに「**Event Grid システム トピック**」と入力し、ドロップ ダウン リストから **[Event Grid システム トピック]** を選択します。 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Event Grid システム トピックを検索して選択する":::
3. トピックの一覧から対象のシステム トピックを選択します。 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="システム トピックの選択":::
4. システム トピックのメトリックが **[Event Grid システム トピック]** ページに表示されます。 次の図では、リソース グループ、サブスクリプションなどを示す **[Essentials]** セクションが最小化されています。 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="概要ページでシステム トピックのメトリックを表示する":::

    サポートされているメトリックを使用してグラフを作成するには、 **[Event Grid トピック]** ページの **[メトリック]** タブを使用します。

    :::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="システム トピック - メトリック ページ":::

    > [!IMPORTANT]
    > Azure Event Grid でサポートされているメトリックの一覧については、[メトリック](metrics.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- メトリックとアクティビティ ログ操作に関するアラートを作成する方法については、「[アラートの設定](set-alerts.md)」を参照してください。
- イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。
