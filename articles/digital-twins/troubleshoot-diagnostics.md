---
title: 診断を設定する
titleSuffix: Azure Digital Twins
description: 診断設定を使用してログ記録を有効にする方法について確認します。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 402eda92d5cc8e5c2566345d31c9ae625b82695c
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809402"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure Digital Twins のトラブルシューティング: 診断ログ

Azure Digital Twins では、リソースの状態に関する情報を提供する、サービス インスタンスの[メトリック](troubleshoot-metrics.md)が収集されます。 これらのメトリックを使用すると、Azure Digital Twins サービスやそれに接続されているリソースの全体的な正常性を評価できます。 これらのユーザー向けの統計情報は、Azure Digital Twins で何が起きているかを確認するのに役立ち、Azure サポートに連絡することなく問題の根本原因の分析を実行する助けとなります。

この記事では、Azure Digital Twins インスタンスからメトリック データの**診断ログ**を有効にする方法について説明します。 これらのログは、サービスに関する問題のトラブルシューティングに役立ちます。

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Azure portal 上で診断設定を有効にする

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Digital Twins インスタンスに移動します。 その名前をポータルの検索バーに入力して、検索することができます。 

2. メニューから **[診断設定]** 、 **[診断設定を追加する]** の順に選択します。

    :::image type="content" source="media/troubleshoot-metrics/diagnostic-settings.png" alt-text="[診断設定] ページと追加するボタンが表示されたスクリーンショット":::

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

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="[診断設定] ページと追加するボタンが表示されたスクリーンショット":::

新しい設定は、10 分ほどで有効になります。 その後、インスタンスの **[診断設定]** ページ上の構成されたターゲットにログが表示されます。 

## <a name="next-steps"></a>次のステップ

* 診断の構成に関する詳細については、"[*Azure リソースからのログ データの収集と使用*](../azure-monitor/platform/platform-logs-overview.md)" に関するページを参照してください。
* Azure Digital Twins のメトリックについては、[*トラブルシューティング: Azure Monitor でメトリックを表示する方法*](troubleshoot-metrics.md)に関するページを参照してください。
