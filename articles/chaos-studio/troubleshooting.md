---
title: Azure Chaos Studio の一般的な問題のトラブルシューティング
description: Azure Chaos Studio の使用時の一般的な問題のトラブルシューティングについて説明します
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/01/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: 235dccf84d28f159a4ef5337a431c27c0316d6ec
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158921"
---
# <a name="troubleshoot-issues-with-azure-chaos-studio"></a>Azure Chaos Studio に関する問題のトラブルシューティング

Chaos Studio の使用時に、何らかの問題が発生する場合があります。 この記事では、よく発生する問題とトラブルシューティングの手順について詳しく説明します。

## <a name="issues-due-to-prerequisites"></a>前提条件が原因の問題

一部の問題は、前提条件が満たされていないことが原因で発生します。 

### <a name="why-do-agent-based-faults-fail-on-my-linux-virtual-machines"></a>自分の Linux 仮想マシンでは、なぜエージェントベースの障害が失敗するのですか?

[CPU に対する負荷](chaos-studio-fault-library.md#cpu-pressure)、[物理メモリに対する負荷](chaos-studio-fault-library.md#physical-memory-pressure)、[ディスク I/O に対する負荷](chaos-studio-fault-library.md#disk-io-pressure-linux)、および[任意の Stress-ng による負荷](chaos-studio-fault-library.md#arbitrary-stress-ng-stress)の障害では、お使いの仮想マシンに [stress-ng ユーティリティ](https://wiki.ubuntu.com/Kernel/Reference/stress-ng)がインストールされている必要があります。 詳細については、障害の前提条件に関するセクションを参照してください。

### <a name="my-aks-chaos-mesh-faults-are-failing"></a>AKS Chaos Mesh の障害が失敗する

AKS に対して Chaos Mesh の障害を使用する前に、まず Chaos Mesh をインストールする必要があります。 手順については、[AKS に対する Chaos Mesh の障害のチュートリアル](chaos-studio-tutorial-aks.md#set-up-chaos-mesh-on-your-aks-cluster)に関するページを参照してください。

## <a name="experiment-design-and-creation"></a>実験の設計と作成

### <a name="why-do-i-get-the-error-the-microsoftagent-provider-requires-a-managed-identity-when-i-try-to-create-an-experiment"></a>なぜ実験を作成しようとすると ”`The microsoft:agent provider requires a managed identity`” エラーが発生するのですか? 

このエラーは、お使いの仮想マシンにエージェントがデプロイされていない場合に発生します。 インストール手順については、[エージェントベースの障害を使用する実験の作成と実行](chaos-studio-tutorial-agent-based.md)に関するページを参照してください。

### <a name="when-creating-an-experiment-i-get-the-error-the-content-media-type-null-is-not-supported-only-applicationjson-is-supported-what-does-this-mean"></a>実験を作成していると、”`The content media type '<null>' is not supported. Only 'application/json' is supported.`” というエラーが表示されます。これは何を意味しますか?

手動で実験 JSON を作成している場合は、JSON の実験の定義の形式に誤りがあるために発生しています。 中かっこや角かっこ ({} と \[\]) の不一致など、構文エラーが発生していないか確認してください。

## <a name="experiment-execution"></a>実験の実行

### <a name="the-execution-status-of-my-experiment-is-failed-how-do-i-determine-what-went-wrong"></a>実験の実行状態が "失敗" です。 失敗の原因はどのように特定できますか?

[実験] ページで実験名をクリックし、[実験の詳細] ビューに移動します。 [履歴] セクションで、実行インスタンスの [詳細] リンクをクリックして詳細を確認します。

![実験履歴](images/run-experiment-history.png)

### <a name="my-agent-based-fault-failed-with-error-verify-that-the-target-is-correctly-onboarded-and-proper-read-permissions-are-provided-to-the-experiment-msi"></a>エージェントベースのフォールトはエラーで失敗しました: ターゲットが正しくオンボードされており、適切な読み取りアクセス許可が実験用 msi に付与されていることを確認してください。

これは、Azure portal を使用してエージェントをオンボードした場合に発生する可能性があります。この操作には、エージェントベースのターゲットを有効にしても、ユーザー割り当てマネージド ID が仮想マシンまたは仮想マシン スケール セットに割り当てられないという既知の問題があります。

これを解決するには、Azure portal で、仮想マシンまたは仮想マシン スケール セットにアクセスし、 **[ID]** を選択して **[ユーザー割り当て]** タブを開き、ユーザー割り当て ID を仮想マシンに **[追加]** します。 完了後、エージェントが接続するために仮想マシンの再起動が必要な場合があります。

### <a name="how-do-i-collect-agent-logs-on-a-linux-virtual-machine"></a>Linux 仮想マシンでエージェント ログはどのように収集できますか?

VM で `journalctl -u azure-chaos-agent` コマンドを実行します。
