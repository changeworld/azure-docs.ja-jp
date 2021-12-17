---
title: 'Azure Peering Service: 接続テレメトリにアクセスする方法 '
description: このチュートリアルでは、接続テレメトリにアクセスする方法について説明します。
services: peering-service
author: gthareja
ms.service: peering-service
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: gatharej
ms.openlocfilehash: 2b019596c87ef3beca1ff26a9366250d188bdfbc
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202679"
---
# <a name="tutorial-accessing-peering-service-connection-telemetry"></a>チュートリアル: Peering Service 接続テレメトリにアクセスする

 このチュートリアルでは、Peering Service 接続のテレメトリにアクセスする方法について説明します。
 
 接続テレメトリは、お客様の場所と Microsoft ネットワークの間の接続に関して収集された分析情報を提供します。 この記事では、特定の Azure Peering Service 接続の待ち時間レポートとプレフィックスの状態を表示する方法について説明します。 

Peering Service 接続テレメトリにアクセスするには、Azure portal で Peering Service 接続を作成する必要があります。 接続を作成する方法については、[Azure portal を使用して Peering Service 接続を作成する](azure-portal.md)方法に関するページを参照してください。


## <a name="view-a-latency-report"></a>待ち時間レポートを表示する

特定の Peering Service 接続の待ち時間レポートを表示するには、次の手順を実行します。

1. 左側のペインで **[すべてのリソース]** を選択し、Peering Service 接続を選択します。 次に、 **[プレフィックス]** の下にある **[開く]** を選択します。 

   ![Peering Service 接続を選択する](./media/peering-service-measure/peering-service-measure-menu.png)

2. この Peering Service 接続に関連付けられているすべてのプレフィックスの待ち時間レポート ページが表示されます。 **Peering Service 接続では、/24 以上のプレフィックスについてのみ、待ち時間データがサポートされます。**

      ![待ち時間レポート ページ](./media/peering-service-measure/peering-service-latency-report.png)

3. このページに表示されるレポートは、既定では 1 時間ごとに更新されます。 さまざまなタイムラインのレポートを表示するには、 **[次に指定する直近の期間のデータを表示する]** から適切なオプションを選択します。 

## <a name="view-prefix-state-report"></a>プレフィックス状態レポートを表示する

1. 特定のプレフィックスのイベントを表示するには、プレフィックス名を選択し、左側のペインで **[Prefix Events]\(プレフィックス イベント\)** を選択します。 キャプチャされたイベントが表示されます。


   ![プレフィックス イベント](./media/peering-service-measure/peering-service-prefix-event.png)

 **[Prefix Events]\(プレフィックス イベント\)** の一覧にキャプチャされる可能性のあるイベントの一部を、次に示します。

| **プレフィックス イベント** | **イベントの種類**|**理由**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Information|プレフィックスのアナウンスを受信した|
| PrefixWithdrawalEvent|警告| プレフィックスの取り消しを受信した |
| PrefixBackupRouteAnnouncementEvent |Information|プレフィックス バックアップ ルートのアナウンスを受信した |
| PrefixBackupRouteWithdrawalEvent|警告|プレフィックス バックアップ ルートの取り消しを受信した |
| PrefixActivePath |Information| 現在のプレフィックスのアクティブ ルート   |
| PrefixBackupPath | Information|現在のプレフィックスのバックアップ ルート   |
| PrefixOriginAsChangeEvent|Critical| 異なる配信元自律システム番号 (アクティブ ルート用) で、正確なプレフィックスを受信した| 
| PrefixBackupRouteOriginAsChangeEvent  | エラー|異なる配信元自律システム番号 (バックアップ ルート用) で、プレフィックスを受信した  |

## <a name="next-steps"></a>次のステップ

- Peering Service 接続の詳細については、「[Peering Service 接続](connection.md)」を参照してください。
- Peering Service 接続のテレメトリの詳細については、「[Peering Service 接続のテレメトリ](connection-telemetry.md)」を参照してください。
