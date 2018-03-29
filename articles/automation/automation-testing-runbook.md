---
title: Azure Automation での Runbook のテスト
description: Azure Automation で Runbook を発行する前にテストを行い、期待どおりに動作することを確認できます。  この記事では、Runbook のテスト方法と出力の見方について説明します。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 531fffe1ed24016d47708a729a3ee7642a1db64a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="testing-a-runbook-in-azure-automation"></a>Azure Automation での Runbook のテスト
Runbook のテスト時には [ドラフト バージョン](automation-creating-importing-runbook.md#publishing-a-runbook) が実行され、そのバージョンで行われるすべてのアクションが完了します。 ジョブ履歴は作成されませんが、[テスト出力] ウィンドウに[出力](automation-runbook-output-and-messages.md#output-stream)および[警告とエラー](automation-runbook-output-and-messages.md#message-streams) ストリームが表示されます。 [詳細ストリーム](automation-runbook-output-and-messages.md#message-streams)のメッセージは、[$VerbosePreference 変数](automation-runbook-output-and-messages.md#preference-variables)が [Continue] に設定されている場合のみ [出力] ウィンドウに表示されます。

ドラフト バージョンの実行中でも、Runbook ではワークフローを通常どおり実行して、環境内のリソースに対するすべてのアクションを行います。 このため、Runbook をテストするのは非運用環境のリソースのみにしてください。

[Runbook の種類](automation-runbook-types.md) ごとのテスト手順は同じであり、Azure ポータルのテキスト エディターでのテストとグラフィカル エディターでのテストに違いはありません。  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Azure ポータルで Runbook をテストするには
Azure ポータルではすべての [種類の Runbook](automation-runbook-types.md) を使用することができます。

1. [テキスト エディター](automation-edit-textual-runbook.md)または[グラフィカル エディター](automation-graphical-authoring-intro.md)のどちらかで、ドラフト バージョンの Runbook を開きます。
2. **[テスト]** ボタンをクリックして [テスト] ブレードを開きます。
3. Runbook にパラメーターがある場合は左側のウィンドウに表示され、そこでテストに使用する値を指定することができます。
4. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) に対してテストを実施する場合は、**[実行設定]** を **[ハイブリッド worker]** に変更して対象グループの名前を選択します。  それ以外の場合は、既定の **[Azure]** をそのまま使用してクラウドでテストを実行します。
5. **[開始]** をクリックしてテストを開始します。
6. Runbook が [PowerShell ワークフロー](automation-runbook-types.md#powershell-workflow-runbooks)または[グラフィカル](automation-runbook-types.md#graphical-runbooks)の場合、[出力] ウィンドウの下にあるボタンを使用して、テスト中に Runbook を停止または中断することができます。 Runbook は、中断される場合は中断前に現在のアクティビティを完了します。 Runbook を中断した後で、停止または再開できます。
7. [出力] ウィンドウで Runbook の出力を確認します。

## <a name="next-steps"></a>次の手順
* Runbook を作成またはインポートする方法については、「 [Azure Automation での Runbook の作成またはインポート](automation-creating-importing-runbook.md)
* グラフィカル作成の詳細については、「 [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)
* PowerShell ワークフロー Runbook の使用を開始するには、「[最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。
* ステータス メッセージやエラーを返すように Runbook を構成する方法と、推奨されるベスト プラクティスの詳細については、「[Azure Automation での Runbook の出力およびメッセージ](automation-runbook-output-and-messages.md)」を参照してください

