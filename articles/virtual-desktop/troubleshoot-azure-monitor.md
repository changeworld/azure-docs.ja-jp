---
title: Monitor for Windows Virtual Desktop のトラブルシューティング - Azure
description: Windows Virtual Desktop 向けの Azure Monitor に関するイシューのトラブルシューティング方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a104f4d33e1bd38e130101b34d3fd2021de27cd2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445484"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor for Windows Virtual Desktop のトラブルシューティング

この記事では、Azure Monitor for Windows Virtual Desktop の既知のイシューと、一般的な問題の解決策について説明します。

## <a name="issues-with-configuration-and-setup"></a>構成と設定に関する問題

構成ブックで設定の自動化が正常に動作していない場合は、これらのリソースを使用してご自身の環境を手動で設定できます。

- 診断を手動で有効にするか、Log Analytics ワークスペースにアクセスする場合は、[Log Analytics への Windows Virtual Desktop 診断の送信](diagnostics-log-analytics.md)に関する記事をご覧ください。
- Log Analytics 拡張機能をセッション ホストに手動でインストールする場合は、「[Windows 用の Log Analytics 仮想マシン拡張機能](../virtual-machines/extensions/oms-windows.md)」をご覧ください。
- 新しい Log Analytics ワークスペースを設定する場合は、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)」をご覧ください。
- パフォーマンス カウンターを追加、削除、または削除する場合は、「[パフォーマンス カウンターの構成](../azure-monitor/agents/data-sources-performance-counters.md)」をご覧ください。
- Log Analytics ワークスペースの Windows イベント ログを構成する場合は、「[Log Analytics エージェントを使用して Windows イベント ログのデータソースを収集する](../azure-monitor/agents/data-sources-windows-events.md)」をご覧ください。

## <a name="my-data-isnt-displaying-properly"></a>自分のデータが正しく表示されない

データが正しく表示されない場合は、次の一般的な解決策を試してください。

- まず、「[Azure Monitor for Windows Virtual Desktop を使用してデプロイを監視する](azure-monitor.md)」の説明に従って、構成ブックを正しく設定していることを確認します。 カウンターまたはイベントに入力されていない場合は、それらに関連付けられているデータが Azure portal に表示されません。
- 自分のアクセス許可を確認し、リソース所有者に連絡して不足しているアクセス許可を要請してください。Windows Virtual Desktop を監視するすべてのユーザーには、次のアクセス許可が必要です。
    - Windows Virtual Desktop リソースを保持する Azure サブスクリプションへの読み取りアクセス
    - Windows Virtual Desktop セッション ホストを保持するサブスクリプションのリソース グループへの読み取りアクセス 
    - 使用しているすべての Log Analytics ワークスペースへの読み取りアクセス
- Azure Monitor と Log Analytics がポータルにデータを送信できるように、お使いのサーバーのファイアウォールで送信ポートを開く必要がある場合があります。 これを行う方法については、次の記事を参照してください。
      - [Azure Monitor の送信ポート](../azure-monitor/app/ip-addresses.md)
      - [Log Analytics のファイアウォールの要件](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)。 
- 最近のアクティビティのデータが表示されない場合。 15 分間待機して、フィードを更新することをお勧めします。 Azure Monitor にはログ データの入力に 15 分の待機時間があります。 詳細については、「[Azure Monitor でのログ データ インジェスト時間](../azure-monitor/logs/data-ingestion-time.md)」をご覧ください。

情報がすべて入力されていても、データが正しく表示されない場合は、クエリまたはデータソースに問題があるおそれがあります。 「[既知の問題と制限事項](#known-issues-and-limitations)」をご確認ください。 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Windows Virtual Desktop 向けの Azure Monitor をカスタマイズしたい

Windows Virtual Desktop 向けの Azure Monitor は、Azure Monitor ブックを使用します。 ブックを使用すると、Windows Virtual Desktop ブック テンプレートのコピーを保存して、独自のカスタマイズを行うことができます。

仕様により、カスタム ブック テンプレートでは、製品グループの更新プログラムが自動的に適用されることはありません。 詳細については、「[ブックベースの分析情報のトラブルシューティング](../azure-monitor/insights/troubleshoot-workbooks.md)」および[ブックの概要](../azure-monitor/visualize/workbooks-overview.md)に関するページをご覧ください。

## <a name="i-cant-interpret-the-data"></a>データを解釈できない

データの用語の詳細については、「[Windows Virtual Desktop 向けの Azure Monitor の用語集](azure-monitor-glossary.md)」をご覧ください。

## <a name="the-data-i-need-isnt-available"></a>必要なデータを使用できない

より多くのパフォーマンス カウンターまたは Windows イベント ログを監視する場合は、それらから自分の Log Analytics ワークスペースに診断情報を送信できるようにして、 **[ホスト診断: ホスト ブラウザー]** で監視できます。 

- パフォーマンス カウンターを追加するには、「[パフォーマンス カウンターの構成](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)」を参照してください
- Windows イベントを追加するには、「[Windows イベント ログの構成](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)」を参照してください

イシューの診断に役立つデータ ポイントが見つかりませんか。 フィードバックをお送りください。

- フィードバックを残す方法については、「[Windows Virtual Desktop のトラブルシューティングの概要、フィードバック、およびサポート](troubleshoot-set-up-overview.md)」をご覧ください。
- また、Windows Virtual Desktop についてのフィードバックは、[Windows Virtual Desktop フィードバック ハブ](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)にお寄せいただくこともできます。

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項

現在認識され、修正に向けて対処中の問題と制限事項を以下に示します。

- 一度に監視できるホスト プールは 1 つのみです。 
- お気に入りの設定を保存するには、ブックのカスタム テンプレートを保存する必要があります。 カスタム テンプレートでは、製品グループの更新プログラムが自動的に適用されることはありません。
- 構成ブックでは、選択内容の読み込み時に "クエリに失敗しました" というエラーが表示されることがあります。 クエリを更新し、必要に応じて選択内容を再入力すると、エラーが自動的に解決されます。 
- 一部のエラー メッセージはユーザーにとってわかりやすい言い回しではありません。また、エラー メッセージの中にはドキュメントで説明されていないものもあります。
- 合計セッション パフォーマンス カウンターでは、セッションがわずかにオーバーカウントされることがあり、セッションの合計数が最大セッション数を超えているように見える場合があります。
- 使用可能なセッション数には、ホスト プールのスケーリング ポリシーは反映されません。   
- 矛盾するまたは予期しない接続時間が発生することがあります。 まれに、接続の完了イベントが失われたり、これが一部の表示やメトリックに影響を与えたりする可能性があります。
- 接続までの時間には、ユーザーが資格情報を入力するのにかかる時間が含まれます。これは経験と相関しますが、場合によっては、偽のピークが表示されることがあります。 
    

## <a name="next-steps"></a>次のステップ

- 開始するには、「[Azure Monitor for Windows Virtual Desktop を使用してデプロイを監視する](azure-monitor.md)」をご覧ください。
- データ ストレージのコストを見積もり、測定、管理には、「[Azure Monitor コストを見積もる](azure-monitor-costs.md)」をご覧ください。
- Windows Virtual Desktop 向けの Azure Monitor に関連する用語と概念の詳細については、[用語集](azure-monitor-glossary.md)をご覧ください。
