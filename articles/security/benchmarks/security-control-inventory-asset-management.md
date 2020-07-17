---
title: Azure セキュリティ コントロール - インベントリと資産の管理
description: Azure セキュリティ コントロール インベントリと資産の管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aece7278d134145e00b027c184f3817d5566e1e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408377"
---
# <a name="security-control-inventory-and-asset-management"></a>セキュリティ コントロールインベントリと資産の管理

インベントリと資産の管理の推奨事項は、すべての Azure リソースのアクティブな管理 (インベントリ、追跡、修正) に関連する問題への対処に重点を置いているため、承認されたリソースにのみアクセス権が付与され、承認されていない管理されていないリソースが特定され、削除されます。

## <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された Asset Discovery ソリューションを使用する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.1 | 1.1、1.2、1.3、1.4、9.1、12.1 | Customer |

Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。  テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Azure サブスクリプションを表示する方法](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC について](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.2 | 1.5 | Customer |

メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

- [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.3 | 1.6 | Customer |

必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

- [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.4 | 2.1 | Customer |

組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成します。

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.5 | 2.3、2.4 | Customer |

Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。  環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.6 | 2.3、2.4 | Customer |

Azure 仮想マシンのインベントリを使用して、Virtual Machines 上のすべてのソフトウェアに関する情報の収集を自動化します。 ソフトウェアの名前、バージョン、発行元、および更新時刻は、Azure portal から入手できます。 インストール日やその他の情報にアクセスするには、ゲストレベルの診断を有効にし、Windows イベント ログを Log Analytics ワークスペースに取り込みます。

- [Azure 仮想マシンのインベントリを有効にする方法](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.7 | 2.5 | Customer |

Azure Security Center のファイルの整合性の監視 (Change Tracking) と仮想マシンのインベントリを使用して、Virtual Machines にインストールされているすべてのソフトウェアを特定します。 承認されていないソフトウェアを削除するための独自のプロセスを実装することができます。 サードパーティ製のソリューションを使用して、承認されていないソフトウェアを特定することもできます。

- [ファイルの整合性の監視を使用する方法](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring)

- [Azure Change Tracking を理解する](https://docs.microsoft.com/azure/automation/change-tracking)

- [Azure 仮想マシンのインベントリを有効にする方法](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.8 | 2.6 | Customer |

Azure Security Center の適応型アプリケーション制御を使用して、Azure Virtual Machines 上で承認されたソフトウェアのみを実行し、すべての承認されていないソフトウェアが実行されないようにします。

- [Azure Security Center の適応型アプリケーション制御を使用する方法](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.9 | 2.6 | Customer |

Azure Policy を使用して、環境内でプロビジョニングできるサービスを制限します。

- [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.10 | 2.7 | Customer |

Azure Security Center の適応型アプリケーション制御を使用して、規則が適用されるかどうかを指定します。

この要件を満たしていない場合は、サードパーティ製ソリューションを実装します。

- [Azure Security Center の適応型アプリケーション制御を使用する方法](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.11 | 2.9 | Customer |

"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.12 | 2.9 | Customer |

スクリプトの種類に基づき、オペレーティング システム固有の構成またはサードパーティのリソースを使用して、ユーザーの Azure コンピューティング リソース内でスクリプトを実行する機能を制限します。  また、Azure Security Center の適応型アプリケーション制御を利用して、Azure Virtual Machines 上で承認されたソフトウェアのみを実行し、すべての承認されていないソフトウェアが実行されないようにすることもできます。

- [Windows 環境で PowerShell スクリプトの実行を制御する方法](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Azure Security Center の適応型アプリケーション制御を使用する方法](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 6.13 | 2.9 | Customer |

業務に必要であっても、組織のリスクが高くなる可能性があるソフトウェアは、独自の仮想マシンや仮想ネットワーク内に隔離し、Azure Firewall またはネットワーク セキュリティ グループで十分に保護する必要があります。

- [仮想ネットワークを作成する方法](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [セキュリティ構成を使用して NSG を作成する方法](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)


## <a name="next-steps"></a>次のステップ

- 次のセキュリティ コントロールを参照してください。[セキュリティで保護された構成](security-control-secure-configuration.md)