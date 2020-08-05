---
title: 診断を設定する
titleSuffix: Azure Digital Twins
description: 診断設定を使用してログ記録を有効にする方法について確認します。
author: baanders
ms.author: baanders
ms.date: 7/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 60ee2102a2b725c68e29afec9a44de03e52e9467
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87379595"
---
# <a name="enable-logging-with-diagnostics-settings"></a>診断設定を使用してログ記録を有効にする

インスタンスの診断設定を使用したログ記録を有効にすることにより、[Log Analytics](../azure-monitor/log-query/get-started-portal.md)、[Event Hubs](../event-hubs/event-hubs-about.md) エンドポイント、[Azure Storage](../storage/blobs/storage-blobs-overview.md) にメトリック データを送信することが可能になります。

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Azure portal 上で診断設定を有効にする

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Digital Twins インスタンスに移動します。 その名前をポータルの検索バーに入力して、検索することができます。 

2. メニューから **[診断設定]** 、 **[診断設定を追加する]** の順に選択します。

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="[診断設定] ページと追加するボタンが表示されたスクリーンショット":::

3. 次のページで、以下の値を入力します。
 * **診断設定の名前**:診断設定の名前を付けます。
 * **カテゴリの詳細**:監視する操作を選択し、チェック ボックスをオンにしてそれらの操作の診断を有効にします。 診断設定では、次の操作をレポートできます。
    - DigitalTwinsOperation
    - EventRoutesOperation
    - ModelsOperation
    - QueryOperation
    - AllMetrics
 * **宛先の詳細**:ログの送信先を選択します。 3 つのオプションを自由に組み合わせて選択できます。
    - Log Analytics への送信
    - ストレージ アカウントへのアーカイブ
    - イベント ハブへのストリーミング

    宛先の選択のために追加の詳細情報が必要な場合に、入力を求められることがあります。  
    
4. 新しい設定を保存します。 

    :::image type="content" source="media/how-to-set-up-diagnostics/diagnostic-settings-details.png" alt-text="[診断設定] ページと追加するボタンが表示されたスクリーンショット":::

新しい設定は、10 分ほどで有効になります。 その後、インスタンスの **[診断設定]** ページ上の構成されたターゲットにログが表示されます。 

## <a name="next-steps"></a>次のステップ

* 診断の構成に関する詳細については、"[*Azure リソースからのログ データの収集と使用*](../azure-monitor/platform/platform-logs-overview.md)" に関するページを参照してください。
* Azure Digital Twins メトリックについては、"[*方法: Azure Monitor でメトリックを表示する*](how-to-view-metrics.md)" に関するページを参照してください