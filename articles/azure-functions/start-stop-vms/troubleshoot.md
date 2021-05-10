---
title: VM の開始/停止のトラブルシューティング (プレビュー)
description: この記事では、Azure VM の VM の開始/停止 (プレビュー) 機能を使用して、発生した問題をトラブルシューティングする方法について説明します。
services: azure-functions
ms.subservice: ''
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 3c379c1eb36fc19368630188f1b584e1d8a7b8ad
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111102"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>VM の開始/停止 (プレビュー) に関する一般的な問題のトラブルシューティング

この記事では、VM の開始/停止 (プレビュー) をインストールして構成しようとしたときに発生する可能性がある問題のトラブルシューティングと解決に関する情報を提供します。 一般的な情報については、[VM の開始/停止の概要](overview.md)に関する記事を参照してください。

## <a name="general-validation-and-troubleshooting"></a>一般的な検証とトラブルシューティング

このセクションでは、スケジュール シナリオに関する一般的な問題をトラブルシューティングして、根本原因の特定に役立てる方法について説明します。

### <a name="azure-dashboard"></a>Azure ダッシュボード

Azure 共有ダッシュボードを確認することで開始できます。 VM の開始/停止 v2 (プレビュー) の一部としてデプロイされた Azure 共有ダッシュボードは、VM で実行された各操作の状態をすばやく簡単に確認する方法です。 VM で最近実行された操作をすべて表示するには、 **[Recently attempted actions on VMs]\(VMで最近試行されたアクション\)** タイルを参照してください。 Application Insights リソースからデータをプルするため、データがレポートに表示されるまでに約 5 分の待機時間が発生します。

### <a name="logic-apps"></a>Logic Apps

開始/停止シナリオのサポートを有効にした Logic Apps に応じて、その実行履歴を確認して、スケジュールされたスタートアップ/シャットダウン シナリオが 1 つ以上のターゲット VM で正常に完了しなかった理由を特定できます。 詳細については、[Logic Apps の実行履歴](../../logic-apps/monitor-logic-apps.md#review-runs-history) に関する記事を参照してください。

### <a name="azure-storage"></a>Azure Storage

VM の開始/停止 v2 (プレビュー) に使用される Azure ストレージ アカウントの **requestsstoretable** テーブルに書き込まれている、VM 上で実行された操作の詳細を確認できます。 それらのレコードを表示するには、こちらの手順を実行します。

1. Azure portal でストレージ アカウントに移動し、左側のペインの [アカウント] で [**Storage Explorer (プレビュー)] を選択します。
1. **[テーブル]** を選択し、 **[requeststoretable]** を選択します。
1. テーブル内の各レコードは、ロジック アプリのシナリオで定義されているターゲット スコープに基づいて Azure VM に対して実行された開始/停止アクションを表します。 レコードのプロパティ (TIMESTAMP、ACTION、TARGETTOPLEVELRESOURCENAME など) のいずれか 1 つを使用して結果をフィルター処理できます。

### <a name="azure-functions"></a>Azure Functions

VM の開始と停止の実行を担当する Azure Functions に関する最新の呼び出しの詳細を確認できます。 まず、実行フローを確認してみましょう。

**Scheduled** と **Sequenced** シナリオの両方の実行フローは、同じ関数によって制御されます。 実行されるシナリオはペイロード スキーマによって決定されます。 **Scheduled** シナリオでは、実行フローは - **Scheduled** HTTP > **VirtualMachineRequestOrchestrator** Queue > **VirtualMachineRequestExecutor** Queue です。

ロジック アプリから、**Scheduled** HTTP 関数がペイロード スキーマを使用して呼び出されます。 **Scheduled** HTTP 関数で要求が受信されると、その情報が **Orchestrator** キュー関数に送信されます。これにより、各 VM でアクションを実行するための複数のキューが作成されます。

呼び出しの詳細を表示するには、こちらの手順を実行します。

1. Azure portal で、**Azure Functions** に移動します。
1. 一覧から VM の開始/停止 v2 (プレビュー) の関数アプリを選択します。
1. 左側のペインで、 **[関数]** を選択します。
1. 一覧には、各シナリオに関連付けられたいくつかの関数が表示されます。 **Scheduled** HTTP 関数を選択します。
1. 左側のペインで **[監視]** を選択します。
1. 最新の実行トレースを選択すると、詳細なログ記録の、呼び出しの詳細とメッセージ セクションが表示されます。
1. 実行フローの確認の一部として前述されている関数ごとに、同じ手順を繰り返します。

Azure Functions の監視の詳細については、「[Application Insights で Azure Functions テレメトリを分析する](../../azure-functions/analyze-telemetry-data.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Functions とロジック アプリの監視の詳細を確認してください。

* [Azure Functions を監視する](../../azure-functions/functions-monitoring.md)。

* [Azure Functions の監視を構成する方法](../../azure-functions/configure-monitoring.md)。

* [ロジック アプリの監視](../../logic-apps/monitor-logic-apps.md)。