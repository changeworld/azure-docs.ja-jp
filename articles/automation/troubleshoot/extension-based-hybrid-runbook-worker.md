---
title: Azure Automation で拡張機能ベースの Hybrid Runbook Worker の問題をトラブルシューティングする
description: この記事では、Azure Automation 拡張機能ベースの Hybrid Runbook Worker で発生する問題をトラブルシューティングして解決する方法について説明します。
services: automation
ms.date: 09/28/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cdb8f63c16c9479d27bcb44147d78d1a8e4a7aa
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489891"
---
# <a name="troubleshoot-vm-extension-based-hybrid-runbook-worker-issues-in-automation"></a>Automation で VM 拡張機能ベースの Hybrid Runbook Worker の問題をトラブルシューティングする

この記事では、Azure Automation 拡張機能ベースの Hybrid Runbook Worker に関する問題のトラブルシューティングと解決に関する情報を提供します。 エージェント ベースのワーカーのトラブルシューティングについては、[Automation でのエージェント ベースの Hybrid Runbook Worker の問題のトラブルシューティング](./hybrid-runbook-worker.md)に関するページを参照してください。 一般情報については、「[Hybrid Runbook Worker overview (Hybrid Runbook Worker の概要)](../automation-hybrid-runbook-worker.md)」をご覧ください。

## <a name="general-checklist"></a>一般的なチェックリスト

拡張機能ベースの Hybrid Runbook Worker に関する問題のトラブルシューティングに役立つ確認事項を次に示します。

- OS がサポートされていることと、前提条件が満たされていることを確認します。「[前提条件](../extension-based-hybrid-runbook-worker-install.md#prerequisites)」を参照してください。

- システム割り当てマネージド ID が VM で有効になっているかどうかを確認します。 Azure Vm と Arc が有効になっている Azure マシンでは、システム割り当てマネージド ID が有効になっている必要があります。

- 拡張機能が適切な設定で有効になっているかどうかを確認します。設定ファイルには適切な  `AutomationAccountURL` が必要です。Automation アカウントのプロパティ - `AutomationHybridServiceUrl` を使用して、URL をクロスチェックします。  
  - Windows の場合: 設定ファイルは、 `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin` で確認できます。
  - Linux の場合: 設定ファイルは、 `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/` で確認できます。

- ハイブリッド worker 拡張機能の状態、詳細ステータスに表示されるエラー メッセージを確認します。これには、問題を修正するためのエラー メッセージとそれぞれの推奨事項が含まれています。

- VM でトラブルシューティング ツールを実行すると、出力ファイルが生成されます。 出力ファイルを開き、トラブルシューティング ツールによって特定されたエラーを確認します。
  - Windows の場合: トラブルシューティング ツールは、`C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\TroubleShootWindowsExtension.ps1` にあります。
  - Linux の場合: トラブルシューティング ツールは、`/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/troubleshootLinuxExtension.py` にあります。

- Linux マシンの場合は、ユーザー `hweautomation` が正しいアクセス許可で設定されているかどうかを確認します。  

- ハイブリッド worker プロセスが実行されているかどうかを確認します。
   - Windows の場合:  `Hybrid Worker Service` サービスを確認します。
   - Linux の場合:  `hwd.` サービスを確認します。

- ログ コレクター ツールを実行し、収集されたログを確認します。
   - Windows の場合: ログは、`C:\HybridWorkerExtensionLogs` にあります。 ツールは、`C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\PullLogs.ps1` にあります。 拡張機能のログは `HybridWorkerExtensionHandler.log` です。 worker のログは `SME.log` です。
   - Linux の場合: ログは、`/home/nxautomation/run` にあります。 ツールは、`/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/logcollector.py` にあります。 worker のログは `worker.log` です。 拡張機能の登録ログは `register_log` です。 拡張機能の起動ログは `startup_log` です。 拡張機能のログは `extension_out` です。

## <a name="next-steps"></a>次のステップ

発生している問題がここで見つからないか、またはその問題を解決できない場合は、次のいずれかのチャネルで追加のサポートを受けてみてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure サポートにより、Azure コミュニティの回答、サポート、エキスパートと結び付けられます。
* Azure サポート インシデントを送信する。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートを受ける]** を選択します。