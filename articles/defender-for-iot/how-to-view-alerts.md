---
title: '[アラート] ページからのアラートのフィルター処理と管理'
description: さまざまなカテゴリに従ってアラートを表示し、検索機能を使用して目的のアラートを見つけることができます。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/22/2021
ms.topic: how-to
ms.openlocfilehash: 391d1872124c7332bdaa6008a244490b47df4bf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781587"
---
# <a name="filter-and-manage-alerts-from-the-alerts-page"></a>[アラート] ページからのアラートのフィルター処理と管理 

この記事では、センサーによってトリガーされたアラートを表示し、アラート ツールで管理する方法について説明します。

アーカイブ済みまたは固定されたアラートなど、さまざまなカテゴリに基づいてアラートを表示できます。 IP または MAC アドレスに基づくアラートなど、目的のアラートを検索することもできます。  

センサー ダッシュボードからアラートを表示することもできます。

アラートを表示するには、次の手順に従います。

- サイド メニューから、 **[アラート]** を選択します。 [アラート] ウィンドウには、センサーによって検出されたアラートが表示されます。

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="[アラート] 画面の表示。":::

## <a name="view-alerts-by-category"></a>アラートをカテゴリ別に表示する

アラートは、 **[アラート]** メイン ビューからさまざまなカテゴリに応じて表示できます。 アラートを選択して詳細を確認し、イベントを管理します。

| 並べ替えの種類 | [説明] |
|--|--|
| **重要なアラート** | 重要度順に並べ替えられたアラート。 |
| **固定されたアラート** | 詳細な調査のためにユーザーが固定したアラート。 固定されたアラートはアーカイブされず、固定フォルダーに 14 日間保存されます。 |
| **最近のアラート** | 時刻順に並べ替えられたアラート。 |
| **確認済みのアラート** | 確認済みで未処理の、またはミュートおよびミュート解除されたアラート。 |
| **アーカイブ済みのアラート** | システムによって自動的にアーカイブされたアラート。 管理者ユーザーのみがアクセスできます。 |

## <a name="search-for-alerts-of-interest"></a>目的のアラートを検索する

[アラート] メイン ビューには、目的のアラートを見つけるのに役立つさまざまな検索機能が用意されています。

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="アラートの学習のスクリーンショット。":::

### <a name="text-search"></a>テキスト検索

[自由検索] オプションを使用すると、テキスト、数字、または文字でアラートを検索できます。

検索するには:

- [自由検索] フィールドに必要なテキストを入力し、キーボードの Enter キーを押します。

検索をクリアするには:

- [自由検索] フィールドのテキストを削除し、キーボードの Enter キーを押します。

### <a name="device-group-or-device-ip-address-search"></a>デバイス グループまたはデバイス IP アドレスの検索

特定の IP アドレスまたはデバイス グループを参照するアラートを検索します。 デバイス グループはデバイス マップに作成されます。

高度なフィルターを使用するには:

1. **[アラート]** メイン ビューで **[高度なフィルター]** を選択します。

2. デバイス グループまたはデバイスを選択します。

3. **[確認]** を選択します。

4. 検索をクリアするには、 **[すべてクリア]** を選択します。

### <a name="security-versus-operational-alert-search"></a>セキュリティと操作のアラートの検索

操作とセキュリティのアラートの表示を切り替えます。

- **セキュリティ** アラートは、潜在的なマルウェア トラフィック、ネットワークの異常、ポリシー違反、およびプロトコル違反を表します。

- **操作** アラートは、ネットワークの異常、ポリシー違反、およびプロトコル違反を表します。

いずれのオプションも選択されていない場合は、すべてのアラートが表示されます。

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="[アラート] 画面のセキュリティ。":::

## <a name="alert-page-options"></a>[アラート] ページのオプション

アラート メッセージには、次のアクションが用意されています。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: を選択すると、アラートを確認します。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: を選択すると、アラートの確認を取り消します。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: を選択すると、アラートを固定します。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: を選択すると、アラートの固定を解除します。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: を選択すると、すべてのアラートを確認します。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: を選択すると、すべてのアラートを学習および確認します。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: を選択すると、.csv ファイルにアラート情報をエクスポートします。 複数のデバイスを対象とするアラートごとに個別の行にアラート情報をエクスポートするには、 **[拡張アラートのエクスポート]** オプションを使用します。

## <a name="alert-pop-up-window-options"></a>アラート ポップアップ ウィンドウのオプション

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: アイコンを選択すると、アラート レポートを PDF ファイルとしてダウンロードします。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: アイコンを選択すると、PCAP ファイルをダウンロードします。 このファイルは、無料のネットワーク プロトコル アナライザーである Wireshark を使用して表示できます。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: を選択すると、関連するアラート パケットだけを含むフィルター済みの PCAP ファイルをダウンロードします。これにより、出力ファイルのサイズが減少し、より焦点を絞った分析が可能になります。 このファイルは Wireshark を使用して表示できます。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: アイコンを選択すると、イベント タイムラインにアラートを表示します。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: アイコンを選択すると、アラートを固定します。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: アイコンを選択すると、アラートの固定を解除します。

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: を選択すると、トラフィックを承認します (セキュリティ アナリストと管理者のみ)。

- デバイスを選択すると、デバイス マップに表示されます。

## <a name="next-steps"></a>次のステップ

[アラート イベントの管理](how-to-manage-the-alert-event.md)

[アラート ワークフローの高速化](how-to-accelerate-alert-incident-response.md)
