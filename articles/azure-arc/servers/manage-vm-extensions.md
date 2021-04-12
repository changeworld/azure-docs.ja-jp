---
title: Azure Arc 対応サーバーを使用した VM 拡張機能の管理
description: Azure Arc 対応サーバーを使用すると、Azure 以外の VM でのデプロイ後構成と自動化タスクを提供する仮想マシン拡張機能のデプロイを管理できます。
ms.date: 03/01/2021
ms.topic: conceptual
ms.openlocfilehash: 039c52ccbee03636da0f5acc0fc5844be9b646f5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687908"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーを使用した仮想マシン拡張機能の管理

仮想マシン (VM) 拡張機能は、Azure VM でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 たとえば、仮想マシンでソフトウェアのインストールやウイルス対策保護が必要な場合や、そこでスクリプトを実行するために、VM 拡張機能を使用できます。

Azure Arc 対応サーバーを使用すると、Azure VM 拡張機能を Azure 以外の Windows や Linux の VM にデプロイして、ハイブリッド マシンのライフサイクルを通じた管理を簡素化できます。 VM 拡張機能は、ハイブリッド コンピューターまたは Arc 対応サーバーで管理されているサーバーで、次の方法を使用して管理できます。

- [Azure Portal](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager テンプレート](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>主な利点

Azure Arc 対応サーバーによる VM 拡張機能のサポートには、次のような主な利点があります。

- Log Analytics エージェント VM 拡張機能を有効にすることで、[Azure Monitor のログ](../../azure-monitor/logs/data-platform-logs.md)を使用して分析用のログ データを収集します。 これは、さまざまな種類のソースのデータに対して複雑な分析を行うときに便利です。

- [Azure Monitor for VMs](../../azure-monitor/vm/vminsights-overview.md) を使用して、Windows VM および Linux VM のパフォーマンスを分析し、それらのプロセスや、他のリソースおよび外部プロセスへの依存関係を監視します。 これは、Log Analytics エージェントと Dependency Agent の両方の VM 拡張機能を有効にすることで実現されます。

- スクリプトをダウンロードし、カスタム スクリプト拡張機能を使用してハイブリッド接続マシンで実行します。 この拡張機能は、展開後の構成、ソフトウェアのインストール、その他の構成タスクや管理タスクに役立ちます。

- [Azure Key Vault](../../key-vault/general/overview.md) に保存されている証明書を自動的に更新します。

## <a name="availability"></a>可用性

VM 拡張機能は、[サポートされているリージョン](overview.md#supported-regions)の一覧でのみ使用できます。 これらのリージョンのいずれかにマシンをオンボードしてください。

## <a name="extensions"></a>拡張機能

このリリースでは、Windows および Linux マシンで次の VM 拡張機能がサポートされています。

Azure Connected Machine エージェント パッケージと拡張機能エージェント コンポーネントの詳細については、[エージェントの概要](agent-overview.md#agent-component-details)に関する記事を参照してください。

### <a name="windows-extensions"></a>Windows の拡張機能

|拡張子 |Publisher |Type |追加情報 |
|----------|----------|-----|-----------------------|
|Azure Defender が統合された脆弱性スキャナー |Qualys |WindowsAgent.AzureSecurityCenter |[Azure およびハイブリッドのマシンに対する Azure Defender の統合された脆弱性評価ソリューション](../../security-center/deploy-vulnerability-assessment-vm.md)|
|カスタム スクリプト拡張機能 |Microsoft.Compute | CustomScriptExtension |[Windows カスタム スクリプト拡張機能](../../virtual-machines/extensions/custom-script-windows.md)|
|Log Analytics エージェント |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Windows 用 Log Analytics VM 拡張機能](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor for VMs (分析情報) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [Windows 用 Dependency Agent 仮想マシン拡張機能](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault 証明書の同期 | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [Windows 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure Monitor エージェント |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[Azure Monitor エージェントをインストールする (プレビュー)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Linux の拡張機能

|拡張子 |Publisher |Type |追加情報 |
|----------|----------|-----|-----------------------|
|Azure Defender が統合された脆弱性スキャナー |Qualys |LinuxAgent.AzureSecurityCenter |[Azure およびハイブリッドのマシンに対する Azure Defender の統合された脆弱性評価ソリューション](../../security-center/deploy-vulnerability-assessment-vm.md)|
|カスタム スクリプト拡張機能 |Microsoft.Azure.Extensions |CustomScript |[Linux カスタム スクリプト拡張機能バージョン 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Log Analytics エージェント |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Linux 用 Log Analytics VM 拡張機能](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor for VMs (分析情報) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[Linux 用 Dependency Agent 仮想マシン拡張機能](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault 証明書の同期 | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [Linux 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure Monitor エージェント |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[Azure Monitor エージェントをインストールする (プレビュー)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>前提条件

この機能は、サブスクリプション内の次の Azure リソース プロバイダーに依存します。

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

まだ登録されていない場合は、「[Azure リソース プロバイダーを登録する](agent-overview.md#register-azure-resource-providers)」の手順に従ってください。

ネットワークまたはシステム要件があるかどうかを把握するために、前の表で参照されている各 VM 拡張機能のドキュメントを確認してください。 これにより、その VM 拡張機能に依存する Azure サービスまたは機能で接続の問題が発生しないようにすることができます。

### <a name="log-analytics-vm-extension"></a>Log Analytics VM 拡張機能

Linux 用の Log Analytics エージェント VM 拡張機能を使用するには、ターゲット マシンに Python 2.x がインストールされている必要があります。

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM 拡張機能 (プレビュー)

Key Vault VM 拡張機能 (プレビュー) では、次の Linux オペレーティング システムはサポートされていません。

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
