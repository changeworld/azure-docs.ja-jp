---
title: Azure Chaos Studio の一般的な問題のトラブルシューティング
description: Azure Chaos Studio の使用時の一般的な問題のトラブルシューティングについて説明します
author: c-ashton
ms.service: chaos-studio
ms.author: cashton
ms.topic: troubleshooting
ms.date: 11/01/2021
ms.custom: template-troubleshooting, ignite-fall-2021
ms.openlocfilehash: afe37f4e7600f248f6ea3be50b0a1972946e381d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089811"
---
# <a name="azure-chaos-studio-troubleshooting"></a>Azure Chaos Studio のトラブルシューティング

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

### <a name="how-do-i-collect-agent-logs-on-a-linux-virtual-machine"></a>Linux 仮想マシンでエージェント ログはどのように収集できますか?

VM で `journalctl -u azure-chaos-agent` コマンドを実行します。
