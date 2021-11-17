---
title: Azure Arc 対応サーバーを Microsoft Sentinel にオンボードする
description: Azure Arc 対応サーバーを Microsoft Sentinel に追加し、そのセキュリティ状態を予防的に監視する方法について説明します。
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 9a7f7fb8edb6edf18d9f8d5fed0608520cb3b80f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306493"
---
# <a name="onboard-azure-arc-enabled-servers-to-microsoft-sentinel"></a>Azure Arc 対応サーバーを Microsoft Sentinel にオンボードする

この記事の目的は、Azure Arc 対応サーバーを [Microsoft Sentinel](../../sentinel/overview.md) にオンボードして、セキュリティ関連イベントの収集を開始できるよう支援することです。 Microsoft Sentinel は、企業全体でのアラートの検出、脅威の可視化、予防的ハンティング、脅威への対応のための単一ソリューションを提供します。

## <a name="prerequisites"></a>前提条件

始める前に、次の要件を満たしていることを確認します。

- [Log Analytics ワークスペース。](../../azure-monitor/logs/data-platform-logs.md) Log Analytics ワークスペースの詳細については、「[Azure Monitor ログのデプロイの設計](../../azure-monitor/logs/design-logs-deployment.md)」を参照してください。

- [サブスクリプションで有効になっている](../../sentinel/quickstart-onboard.md) Microsoft Sentinel。

- マシンまたはサーバーが Azure Arc 対応サーバーに接続されている。

## <a name="onboard-azure-arc-enabled-servers-to-microsoft-sentinel"></a>Azure Arc 対応サーバーを Microsoft Sentinel にオンボードする

Microsoft Sentinel には、Microsoft ソリューション用のすぐに使用できるコネクタが多数用意されており、リアルタイムの統合が実現します。 物理マシンと仮想マシンの場合、ログを収集して Microsoft Sentinel に転送する Log Analytics エージェントをインストールできます。 Azure Arc 対応サーバーでは、以下の方法を使用した Log Analytics エージェントのデプロイがサポートされています。

- VM 拡張機能フレームワークの使用。

    Azure Arc 対応サーバーのこの機能を使用すると、Azure 以外の Windows や Linux のサーバーに、Log Analytics エージェントの VM 拡張機能をデプロイできます。 VM 拡張機能は、ハイブリッド コンピューターまたは Azure Arc 対応サーバーで管理されているサーバーで、次の方法を使用して管理できます。

    - [Azure Portal](manage-vm-extensions-portal.md)
    - [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [Resource Manager テンプレート](manage-vm-extensions-template.md)

- Azure Policy の使用。

    この方法を利用し、Azure Policy の "[Log Analytics エージェントを Linux または Windows Azure Arc マシンにデプロイする](../../governance/policy/samples/built-in-policies.md#monitoring)" という組み込みポリシーを使用して、Azure Arc 対応サーバーに Log Analytics エージェントがインストールされているかどうかを監査します。 エージェントがインストールされていない場合は、修復タスクを使用して自動的にそれがデプロイされます。 または、Azure Monitor for VMs を使用してマシンを監視する予定の場合は、代わりに "[Azure Monitor for VMs を有効にする](../../governance/policy/samples/built-in-initiatives.md#monitoring)" というイニシアティブを使用して Log Analytics エージェントのインストールと構成を行います。

Azure Policy を使用して、Windows または Linux 用の Log Analytics エージェントをインストールすることをお勧めします。

Arc 対応サーバーが接続されると、Microsoft Sentinel へのデータのストリーミングが開始され、操作を開始する準備が整います。 [組み込みのブック](../../sentinel/get-visibility.md)でログを表示したり、Log Analytics でクエリを作成して[データを調査](../../sentinel/investigate-cases.md)したりできます。

## <a name="next-steps"></a>次のステップ

[Microsoft Sentinel を使用した脅威の検出](../../sentinel/detect-threats-built-in.md)の概要。
