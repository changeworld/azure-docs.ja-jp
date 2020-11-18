---
title: Azure Arc 対応サーバーを使用した VM 拡張機能の管理
description: Azure Arc 対応サーバーを使用すると、Azure 以外の VM でのデプロイ後構成と自動化タスクを提供する仮想マシン拡張機能のデプロイを管理できます。
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: 7682f6c8631bbaf2310d501d7cee6aecb2311226
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358033"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーを使用した仮想マシン拡張機能の管理

仮想マシン (VM) 拡張機能は、Azure VM でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 たとえば、仮想マシンでソフトウェアのインストールやウイルス対策保護が必要な場合、あるいは、仮想マシン内でスクリプトを実行するために、VM 拡張機能を使用できます。

Azure Arc 対応サーバーを使用すると、Azure VM 拡張機能を Azure 以外の Windows VM と Linux VM にデプロイし、オンプレミス、エッジ、その他のクラウド環境のハイブリッド マシンのライフサイクルを通した管理を簡素化できます。 VM 拡張機能は、ハイブリッド コンピューターまたは Arc 対応サーバーで管理されているサーバーで、次の方法を使用して管理できます。

- [Azure Portal](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager テンプレート](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>主な利点

Azure Arc 対応サーバーによる VM 拡張機能のサポートには、次のような主な利点があります。

- [Azure Automation State Configuration](../../automation/automation-dsc-overview.md) を使用して、構成を一元的に保存し、DSC VM 拡張機能によって有効にされているハイブリッド接続マシンの望ましい状態を維持します。

- Log Analytics エージェント VM 拡張機能によって有効にされる [Azure Monitor のログ](../../azure-monitor/platform/data-platform-logs.md)を使用して、分析用のログ データを収集します。 これは、さまざまなソースからのデータについて複雑な分析を実行するときに便利です。

- [Azure Monitor for VMs](../../azure-monitor/insights/vminsights-overview.md) を使用して、Windows VM および Linux VM のパフォーマンスを分析し、それらのプロセスや、他のリソースおよび外部プロセスへの依存関係を監視します。 これは、Log Analytics エージェントと Dependency Agent の両方の VM 拡張機能を有効にすることで実現されます。

- スクリプトをダウンロードし、カスタム スクリプト拡張機能を使用してハイブリッド接続マシンで実行します。 この拡張機能は、展開後の構成、ソフトウェアのインストール、その他の構成タスクや管理タスクに役立ちます。

- [Azure Key Vault](../../key-vault/general/overview.md) に保存されている証明書を自動的に更新します。

## <a name="availability"></a>可用性

VM 拡張機能は、[サポートされているリージョン](overview.md#supported-regions)の一覧でのみ使用できます。 これらのリージョンのいずれかにマシンをオンボードしてください。

## <a name="extensions"></a>拡張機能

このリリースでは、Windows および Linux マシンで次の VM 拡張機能がサポートされています。

|拡張機能 |OS |Publisher |関連情報 |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Windows カスタム スクリプト拡張機能](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft.PowerShell|[Windows PowerShell DSC 拡張機能](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics エージェント |Windows |Microsoft.EnterpriseCloud.Monitoring |[Windows 用 Log Analytics VM 拡張機能](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [Windows 用 Dependency Agent 仮想マシン拡張機能](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Key Vault | Windows | Microsoft.Compute | [Windows 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-windows.md) |
|CustomScript|Linux |Microsoft.Azure.Extension |[Linux カスタム スクリプト拡張機能バージョン 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Linux 用 PowerShell DSC 拡張機能](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics エージェント |Linux |Microsoft.EnterpriseCloud.Monitoring |[Linux 用 Log Analytics VM 拡張機能](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [Linux 用 Dependency Agent 仮想マシン拡張機能](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Key Vault | Linux | Microsoft.Compute | [Linux 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-linux.md) |

Azure Connected Machine エージェント パッケージと拡張機能エージェント コンポーネントの詳細については、[エージェントの概要](agent-overview.md#agent-component-details)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

この機能は、サブスクリプション内の次の Azure リソース プロバイダーに依存します。

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

まだ登録されていない場合は、「[Azure リソースプロバイダーを登録する](agent-overview.md#register-azure-resource-providers)」の手順に従ってください。

### <a name="log-analytics-vm-extension"></a>Log Analytics VM 拡張機能

Linux 用の Log Analytics エージェント VM 拡張機能を使用するには、ターゲット マシンに Python 2.x がインストールされている必要があります。

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM 拡張機能 (プレビュー)

Key Vault VM 拡張機能 (プレビュー) では、次の Linux オペレーティング システムはサポートされません。

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

Azure Key Vault VM 拡張機能 (プレビュー) のデプロイは、以下を使用した場合にのみサポートされます。

- Azure CLI
- Azure PowerShell
- Azure Resource Manager テンプレート

機能拡張をデプロイする前に、次の作業を完了する必要があります。

1. [コンテナーと証明書 (自己署名証明書またはインポート証明書) を作成](../../key-vault/certificates/quick-create-portal.md)します。

2. Azure Arc 対応サーバーに証明書シークレットへのアクセスを許可します。 [RBAC プレビュー](../../key-vault/general/rbac-guide.md)を使用する場合は、Azure Arc リソースの名前を検索し、それを **Key Vault Secrets User (プレビュー)** ロールに割り当てます。 [Key Vault アクセス ポリシー](../../key-vault/general/assign-access-policy-portal.md)を使用する場合は、Azure Arc リソースのシステム割り当て ID にシークレットの **Get** アクセス許可を割り当てます。

### <a name="connected-machine-agent"></a>Connected Machine エージェント

お使いのマシンが、Azure Connected Machine エージェントに対する Windows および Linux オペレーティング システムの[サポートされているバージョン](agent-overview.md#supported-operating-systems)と一致することを確認します。

Windows および Linux のこの機能でサポートされる Connected Machine エージェントの最小バージョンは 1.0 リリースです。

マシンを必要なエージェントのバージョンにアップグレードするには、「[エージェントのアップグレード](manage-agent.md#upgrading-agent)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure CLI](manage-vm-extensions-cli.md)、[Azure PowerShell](manage-vm-extensions-powershell.md)、[Azure portal](manage-vm-extensions-portal.md)、または [Azure Resource Manager テンプレート](manage-vm-extensions-template.md)を使用して、VM 拡張機能をデプロイ、管理、および削除できます。
