---
title: Windows Virtual Desktop 向けの Monitor (プレビュー) のトラブルシューティング - Azure
description: Windows Virtual Desktop 向けの Azure Monitor に関するイシューのトラブルシューティング方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465694"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Windows Virtual Desktop 向けの Azure Monitor (プレビュー) のトラブルシューティング

>[!IMPORTANT]
>Windows Virtual Desktop 向けの Azure Monitor は現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Windows Virtual Desktop 向けの Azure Monitor (プレビュー) の既知のイシューと、一般的な問題の解決策について説明します。

## <a name="the-configuration-workbook-isnt-working-properly"></a>構成ブックが正しく動作しない

Azure Monitor 構成ブックが動作しない場合は、次のリソースを使用して、手動でその部分を設定できます。

- 診断を手動で有効にするか、Log Analytics ワークスペースにアクセスする場合は、[Log Analytics への Windows Virtual Desktop 診断の送信](diagnostics-log-analytics.md)に関する記事をご覧ください。
- Log Analytics 拡張機能をホストに手動でインストールする場合は、「[Windows 用の Log Analytics 仮想マシン拡張機能](../virtual-machines/extensions/oms-windows.md)」をご覧ください。
- 新しい Log Analytics ワークスペースを設定する場合は、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/learn/quick-create-workspace.md)」をご覧ください。
- パフォーマンス カウンターを追加または削除する場合は、「[パフォーマンス カウンターの構成](../azure-monitor/platform/data-sources-performance-counters.md)」をご覧ください。
- Log Analytics ワークスペースのイベントを構成する場合は、「[Log Analytics エージェントを使用して Windows イベント ログのデータソースを収集する](../azure-monitor/platform/data-sources-windows-events.md)」をご覧ください。

または、リソースが不足しているか、必要なアクセス許可がないことが原因で問題が発生しているおそれがあります。

サブスクリプションに Windows Virtual Desktop リソースがない場合、それは *[サブスクリプション]* パラメーターに表示されません。

正しいサブスクリプションに対する読み取りアクセスがない場合、それは *[サブスクリプション]* パラメーターに表示されず、ダッシュボードにデータが表示されません。 このイシューを解決するには、サブスクリプションの所有者に連絡し、読み取りアクセスを依頼してください。

## <a name="my-data-isnt-displaying-properly"></a>自分のデータが正しく表示されない

データが正しく表示されない場合は、Azure Monitor の構成プロセスで何らかの問題が発生しているおそれがあります。 まず、「[Windows Virtual Desktop 向けの Azure Monitor を使用してデプロイを監視する](azure-monitor.md)」の説明に従って、構成ブックのすべてのフィールドを入力していることを確認します。 新規でも既存の環境でも、設定はいつでも変更できます。 カウンターまたはイベントに入力されていない場合は、それらに関連付けられているデータが Azure portal に表示されません。

情報がすべて入力されていても、データが正しく表示されない場合は、クエリまたはデータソースに問題があるおそれがあります。 

設定エラーが表示されず、予期したデータが表示されない場合は、15 分間待機して、フィードを更新することをお勧めします。 Azure Monitor にはログ データの入力に 15 分の待機時間があります。 詳細については、「[Azure Monitor でのログ データ インジェスト時間](../azure-monitor/platform/data-ingestion-time.md)」をご覧ください。

最終的に、情報がすべて入力されていても、データが表示されない場合は、クエリまたはデータソースに問題があるおそれがあります。 該当する場合は、問題の解決をサポートに依頼する必要があります。

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Windows Virtual Desktop 向けの Azure Monitor をカスタマイズしたい

Windows Virtual Desktop 向けの Azure Monitor は、Azure Monitor ブックを使用します。 ブックを使用すると、Windows Virtual Desktop ブック テンプレートのコピーを保存して、独自のカスタマイズを行うことができます。

カスタマイズしたテンプレートは、製品グループが元のテンプレートを更新しても更新されません。 これは、ブック ツールでの仕様であり、更新を採用するには、更新されたテンプレートのコピーを保存して、カスタマイズを再ビルドする必要があります。 詳細については、「[ブックベースの分析情報のトラブルシューティング](../azure-monitor/insights/troubleshoot-workbooks.md)」および[ブックの概要](../azure-monitor/platform/workbooks-overview.md)に関するページをご覧ください。

## <a name="i-cant-interpret-the-data"></a>データを解釈できない

データの用語の詳細については、「[Windows Virtual Desktop 向けの Azure Monitor の用語集](azure-monitor-glossary.md)」をご覧ください。

## <a name="the-data-i-need-isnt-available"></a>必要なデータを使用できない

イシューの診断に役立つデータ ポイントが見つかりませんか。 フィードバックをお送りください。

- フィードバックを残す方法については、「[Windows Virtual Desktop のトラブルシューティングの概要、フィードバック、およびサポート](troubleshoot-set-up-overview.md)」をご覧ください。
- また、Windows Virtual Desktop についてのフィードバックは、[Windows Virtual Desktop フィードバック ハブ](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)、または [UserVoice フォーラム](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)にお寄せいただくこともできます。

## <a name="known-issues"></a>既知の問題

現在認識されていて修正に向けて動いているイシューを以下に示します。

- 現在、監視するサブスクリプション、リソース グループ、ホスト プールを一度に 1 つしか選択できません。 このため、ユーザー レポートのページを使用してユーザーのエクスペリエンスを理解するときに、ユーザーが使用している正しいホスト プールを所有していること、データにビジュアルが入力されていないことを確認する必要があります。

- 現在、ブックのカスタム テンプレートを保存していない限り、お気に入りの設定を Azure Monitor に保存することはできません。 これは、IT 管理者が Windows Virtual Desktop 向けの Azure Monitor を開くたびに、サブスクリプション名、リソース グループ名、ホスト プール設定を入力する必要があることを意味します。

- 現在、Windows Virtual Desktop 向けの Azure Monitor から Excel にデータをエクスポートする方法はありません。

- 選択したサブスクリプション内のすべての製品について、重大度 1 の Azure Monitor アラートがすべて概要ページに表示されます。 サブスクリプション内の他の製品からのアラートが Windows Virtual Desktop に影響を与える可能性があるため、これは仕様になっています。 現時点では、クエリは重大度 1 のアラートに制限されており、優先度の高い重大度 0 のアラートは概要ページから除外されています。

- 一部のエラー メッセージはユーザーにとってわかりやすい言い回しではありません。また、すべてのエラー メッセージがドキュメントに記載されているわけではありません。

## <a name="next-steps"></a>次のステップ

データの解釈方法がわからない場合や、一般的な用語の詳細については、「[Windows Virtual Desktop 向けの Azure Monitor の用語集](azure-monitor-glossary.md)」をご覧ください。 Windows Virtual Desktop 向けの Azure Monitor を設定して使用する方法については、「[Windows Virtual Desktop 向けの Azure Monitor を使用してデプロイを監視する](azure-monitor.md)」をご覧ください。