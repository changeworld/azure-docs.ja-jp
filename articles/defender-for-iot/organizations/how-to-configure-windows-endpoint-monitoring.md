---
title: Windows エンドポイント監視を構成する
description: Windows エンドポイント監視 (WMI) を使用して、デバイス上の解決されたデータを強化します。
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 7b59c0b5dcb14b57ee5e4e12c2dc3e925e30d37d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281837"
---
# <a name="configure-windows-endpoint-monitoring-wmi"></a>Windows エンドポイント監視 (WMI) を構成する

Windows エンドポイント監視機能を使用して、Windows システムを選択的にプローブするように Microsoft Defender for IoT を構成できます。 これにより、Service Pack レベルなど、デバイスに関して焦点を絞った正確な情報が得られます。

特定の範囲とホストのプローブを構成して、必要な頻度でのみ実行されるように構成できます。 選択的プローブを実行するには Windows Management Instrumentation (WMI) を使用します。これは、Windows システムを管理するための Microsoft の標準スクリプト言語です。

> [!NOTE]
> - 一度に実行できるスキャンは 1 つだけです。
> - ドメインまたはローカルの管理者特権を持つユーザーを使用すると、最良の結果を得ることができます。
> - WMI 構成を開始する前に、センサーからスキャンされるサブネットへの送信トラフィック (UDP ポート 135 および 1024 を超えるすべての TCP ポートを使用) を開くファイアウォール規則を構成します。

プローブが完了すると、ログをエクスポートするオプションから、すべてのプローブ試行を含むログ ファイルを入手できます。 ログには、プローブされたすべての IP アドレスが含まれます。 各 IP アドレスについて、ログに成功と失敗の情報が表示されます。 また、エラー コードも含まれます。これは例外から抽出されたフリーの文字列です。 最後のログのスキャンのみがシステムに保持されます。

スケジュールされたスキャンまたは手動スキャンを実行できます。 スキャンが完了すると、CSV ファイルで結果を表示できます。

**前提条件**

センサーからスキャンされるサブネットへの送信トラフィック (UDP ポート 135 および 1024 を超えるすべての TCP ポートを使用) を開くファイアウォール規則を構成します。

## <a name="perform-an-automatic-scan"></a>自動スキャンを実行する

このセクションでは、自動スキャンを実行する方法について説明します。

**自動スキャンを実行するには:**

1. サイド メニューで、 **[システム設定]** を選択します。

2. **[Windows Endpoint Monitoring]\(Windows エンドポイント監視\)** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: を選択します。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Windows エンドポイント監視の選択を示すスクリーンショット。":::

3. **[スキャンのスケジュール]** ペインで、次のようにオプションを構成します。

      - **[By fixed intervals (in hours)]\(一定間隔 (時間単位)\)** :スキャンのスケジュールを間隔 (時間単位) で設定します。

      - **[By specific times]\(特定時刻\)** :特定の時刻にスキャンのスケジュールを設定し、 **[Save Scan]\(スキャンの保存\)** を選択します。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="[Save Scan]\(スキャンの保存\) ボタンを示すスクリーンショット。":::

4. スキャン範囲を定義するには、 **[Set scan ranges]\(スキャン範囲の設定\)** を選択します。

5. IP アドレスの範囲を設定し、ユーザーとパスワードを追加します。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="ユーザーとパスワードの追加を示すスクリーンショット。":::

6. IP 範囲をスキャンから除外するには、その範囲の横の **[無効化]** を選択します。

7. 範囲を削除するには、その範囲の横の :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: を選択します。

8. **[保存]** を選択します。 **[Edit Scan Ranges Configuration]\(スキャン範囲構成の編集\)** ダイアログ ボックスが閉じて、範囲の数が **[Scan Ranges]\(スキャン範囲\)** ペインに表示されます。

## <a name="perform-a-manual-scan"></a>手動スキャンを実行する

**手動スキャンを実行するには:**

1. サイド メニューで、 **[システム設定]** を選択します。

2. **[Windows Endpoint Monitoring]\(Windows エンドポイント監視\)** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: を選択します。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Windows エンドポイント監視設定画面を示すスクリーンショット。":::

3. **[操作]** ペインで、 **[スキャンの開始]** を選択します。 ステータス バーが **[操作]** ペインに表示され、スキャン プロセスの進行状況を示します。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="[スキャンの開始] ボタンを示すスクリーンショット。":::

## <a name="view-scan-results"></a>スキャンの結果を表示する

**スキャン結果を表示するには:**

1. スキャンが完了したら、 **[操作]** ペインで **[View Scan Results]\(スキャン結果の表示\)** を選択します。 スキャン結果を含む CSV ファイルがコンピューターにダウンロードされます。
