<properties
   pageTitle="Hybrid Runbook Worker: Runbook ジョブが中断状態で終了する | Microsoft Azure"
   description="Hybrid Runbook Worker ジョブ終了エラーの現象の原因と解決方法。"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2016"
   ms.author="magoedte" />

# Hybrid Runbook Worker: Runbook ジョブが中断状態で終了する

## 概要

Runbook は、3 回実行を試みると短時間中断されます。Runbook の正常な完了を中断する可能性がある条件があり、関連するエラー メッセージに理由を示す追加情報は含まれません。この記事では、Hybrid Runbook Worker の Runbook 実行エラーに関連する問題のトラブルシューティング手順を説明します。

この記事で Azure の問題に対処できない場合は、[MSDN と Stack Overflow の Azure フォーラム](https://azure.microsoft.com/support/forums/)を参照してください。問題をこれらのフォーラムまたは [Twitter の @AzureSupport](https://twitter.com/AzureSupport) に投稿できます。また、[Azure サポート](https://azure.microsoft.com/support/options/) サイトの **[サポートの要求]** を選択して、Azure サポート要求を提出することもできます。

## 症状

Runbook の実行が失敗し、返されるエラーは "プロセスが異常停止されたため、ジョブ操作 'Activate' を実行できません。このジョブ操作は 3 回試行されました。" です。


## 原因

このエラーには以下のような複数の原因が考えられます。

  1. Hybrid Worker がプロキシまたはファイアウォールの内側にある
  2. Hybrid Worker を実行しているコンピューターが、ハードウェアの最小[要件](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements)を満たしていない
  3. Runbook がローカル リソースで認証できない


## 原因 1: Hybrid Runbook Worker がプロキシまたはファイアウォールの内側にある

Hybrid Runbook Worker を実行しているコンピューターがファイアウォールまたはプロキシ サーバーの内側にあり、発信ネットワーク アクセスが許可されていないか、または正しく構成されていない可能性があります。

### 解決策

コンピューターがポート 443、9354、および 30000 ～ 30199 で *.cloudapp.net に発信アクセスできることを確認します。

## 原因 2: コンピューターが最小ハードウェア要件を満たしていない

Hybrid Runbook Worker を実行するコンピューターは、この機能をホストするよう指定する前に、ハードウェアの最小要件を満たしている必要があります。満たしていない場合、他のバックグラウンド プロセスのリソース使用状況および実行中の Runbook による競合によっては、コンピューターが過負荷になり、Runbook ジョブが遅延またはタイムアウトします。

### 解決策 

最初に、Hybrid Runbook Worker の機能を実行するように指定されているコンピューターがハードウェアの最小要件を満たしていることを確認します。満たしている場合は、CPU とメモリの使用状況を監視して、Hybrid Runbook Worker プロセスのパフォーマンスと Windows の間の相関関係を調べます。メモリまたは CPU に負荷がかかる場合は、リソースのボトルネックを解消してエラーを解決するには、アップグレード、プロセッサの追加、またはメモリの増設が必要である可能性があります。または、最小要件を満たす異なるコンピューティング リソースを選択し、ワークロードがさらに多くのリソースを必要とすることを示したときは拡張します。

## 原因 3: Runbook がローカル リソースで認証できない

### 解決策

**Microsoft-SMA** のイベント ログで "*Win32 Process Exited with code [4294967295]*" という説明の対応するイベントを確認します。このエラーの原因は、Runbook で認証が構成されていないか、または Hybrid Worker グループの Run As 資格情報が指定されていません。「[Runbook のアクセス許可](automation-hybrid-runbook-worker.md#runbook-permissions)」を参照して、Runbook の認証を正しく構成してあることを確認します。


 

<!---HONumber=AcomDC_0817_2016-->