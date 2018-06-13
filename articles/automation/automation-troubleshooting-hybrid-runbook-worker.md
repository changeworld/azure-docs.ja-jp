---
title: Azure Automation の Hybrid Runbook Worker のトラブルシューティング
description: Azure Automation での Hybrid Runbook Worker の一般的な問題の、現象、原因、および解決方法について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7fe0662d8bf3b52c0de94dbc70126eb813883402
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192620"
---
# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker のトラブルシューティングのヒント

この記事では、Automation Hybrid Runbook Worker で発生することがあるエラーのトラブルシューティングのヘルプを提供し、それらのエラーの考えられる解決策を提案します。

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>Runbook ジョブが中断状態で終了する

Runbook は、3 回実行を試みると短時間中断されます。 Runbook の正常な完了を中断する可能性がある条件があり、関連するエラー メッセージに理由を示す追加情報は含まれません。 この記事では、Hybrid Runbook Worker の Runbook 実行エラーに関連する問題のトラブルシューティング手順を説明します。

この記事で Azure の問題に対処できない場合は、 [MSDN と Stack Overflow の Azure フォーラム](https://azure.microsoft.com/support/forums/)を参照してください。 問題をこれらのフォーラムまたは [Twitter の @AzureSupport](https://twitter.com/AzureSupport) に投稿できます。 また、 **Azure サポート** サイトの [[サポートの要求]](https://azure.microsoft.com/support/options/) を選択して、Azure サポート要求を提出することもできます。

### <a name="symptom"></a>症状

Runbook の実行が失敗し、返されるエラーは "プロセスが異常停止されたため、ジョブ操作 'Activate' を実行できません。 このジョブ操作は 3 回試行されました。" です。

このエラーには以下のような複数の原因が考えられます。

1. Hybrid Worker がプロキシまたはファイアウォールの内側にある
2. Runbook がローカル リソースで認証できない

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>原因 1: Hybrid Runbook Worker がプロキシまたはファイアウォールの内側にある

Hybrid Runbook Worker を実行しているコンピューターがファイアウォールまたはプロキシ サーバーの内側にあり、発信ネットワーク アクセスが許可されていないか、または正しく構成されていない可能性があります。

#### <a name="solution"></a>解決策

コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>原因 2: コンピューターが最小ハードウェア要件を満たしていない

Hybrid Runbook Worker を実行するコンピューターは、この機能をホストするよう指定する前に、ハードウェアの最小要件を満たしている必要があります。 満たしていない場合、他のバックグラウンド プロセスのリソース使用状況および実行中の Runbook による競合によっては、コンピューターが過負荷になり、Runbook ジョブが遅延またはタイムアウトします。

#### <a name="solution"></a>解決策

最初に、Hybrid Runbook Worker の機能を実行するように指定されているコンピューターがハードウェアの最小要件を満たしていることを確認します。 満たしている場合は、CPU とメモリの使用状況を監視して、Hybrid Runbook Worker プロセスのパフォーマンスと Windows の間の相関関係を調べます。 メモリまたは CPU に負荷がかかる場合は、リソースのボトルネックを解消してエラーを解決するには、アップグレード、プロセッサの追加、またはメモリの増設が必要である可能性があります。 または、最小要件を満たす異なるコンピューティング リソースを選択し、ワークロードがさらに多くのリソースを必要とすることを示したときは拡張します。

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>原因 3: Runbook がローカル リソースで認証できない

#### <a name="solution"></a>解決策

**Microsoft-SMA** のイベント ログで " *Win32 Process Exited with code [4294967295]*" という説明の対応するイベントを確認します。 このエラーの原因は、Runbook で認証が構成されていないか、または Hybrid Worker グループの Run As 資格情報が指定されていません。 「[Runbook のアクセス許可](automation-hrw-run-runbooks.md#runbook-permissions) 」を参照して、Runbook の認証を正しく構成してあることを確認します。

## <a name="next-steps"></a>次の手順

Automation のその他の問題のトラブルシューティングのヒントについては、「[Azure Automation の一般的な問題のトラブルシューティング](automation-troubleshooting-automation-errors.md)」を参照してください。