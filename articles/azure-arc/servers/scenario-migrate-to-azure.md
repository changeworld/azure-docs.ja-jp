---
title: Azure Arc 対応サーバーを Azure に移行する
description: オンプレミスまたは他のクラウド環境で実行されている Azure Arc 対応サーバーを Azure に移行する方法について説明します。
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: a524e9930b96d0f18038a2afd43039081010a504
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236265"
---
# <a name="migrate-your-on-premises-or-other-cloud-azure-arc-enabled-server-to-azure"></a>オンプレミスまたは他のクラウドの Azure Arc 対応サーバーを Azure に移行する

この記事は、Azure Arc 対応サーバーで管理されているオンプレミス サーバーまたは仮想マシンを Azure に移行する計画を立てて正常に実行するのに役立ちます。 これらの手順に従うことにより、サポートされているインストール済み VM 拡張機能に基づく Azure Arc 対応サーバー、および Arc サーバー リソース ID に基づく Azure サービスから管理を移行できます。

これらの手順を実行する前に、Azure Migrate の「[Azure への移行に向けてオンプレミスのマシンの準備を整える](../../migrate/prepare-for-migration.md)」という記事を参照して、Azure Migrate を使用するための準備方法に関する要件を把握してください。

この記事では、次の内容について説明します。

* Azure Arc 対応サーバーでサポートされているインストール済み VM 拡張機能のインベントリを作成する。
* Azure Arc 対応サーバーからすべての VM 拡張機能をアンインストールする。
* Azure Arc 対応サーバーで管理されている ID を使用して認証するよう構成されている Azure サービスを特定し、移行後に Azure VM の ID が使用されるようにこれらのサービスを更新する準備をする。
* Azure Arc 対応サーバー リソースに付与されている Azure ロールベースのアクセス制御 (Azure RBAC) のアクセス権を確認して、Azure VM に移行後のユーザーのリソースへのアクセス権を維持する。 
* Azure から Azure Arc 対応サーバー リソース ID を削除し、Azure Arc 対応サーバー エージェントを削除する。
* Azure ゲスト エージェントをインストールする。
* サーバーまたは VM を Azure に移行する。

## <a name="step-1-inventory-and-remove-vm-extensions"></a>手順 1: VM 拡張機能のインベントリを作成して削除する

Azure Arc 対応サーバーにインストールされている VM 拡張機能のインベントリを作成するには、Azure CLI または Azure PowerShell を使用してそれらを一覧表示します。

Azure PowerShell では、`-MachineName` と `-ResourceGroupName` パラメーターを指定して [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) コマンドを使用します。

Azure CLI では、`--machine-name` と `--resource-group` パラメーターを指定して [az connectedmachine extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) コマンドを使用します。 既定では、Azure CLI コマンドの出力形式は JSON (JavaScript Object Notation) です。 既定の出力をリストまたはテーブルなどに変更するには、[az configure --output](/cli/azure/reference-index) を使用します。 出力形式で 1 回のみ変更するために `--output` を任意のコマンドに追加することもできます。

デプロイされている VM 拡張機能を特定したら、[Azure portal](manage-vm-extensions-portal.md)、[Azure PowerShell](manage-vm-extensions-powershell.md)、または [Azure CLI](manage-vm-extensions-cli.md) を使用してそれらを削除できます。 Log Analytics の VM 拡張機能または Dependency Agent の VM 拡張機能が Azure Policy と [VM insights のイニシアティブ](../../azure-monitor/vm/vminsights-enable-policy.md)を使用してデプロイされていた場合は、移行が完了する前に Azure Arc 対応サーバー上で拡張機能の再評価とデプロイが行われないように、[除外を作成](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)する必要があります。

## <a name="step-2-review-access-rights"></a>手順 2: アクセス権を確認する 

[Azure PowerShell](../../role-based-access-control/role-assignments-list-powershell.md#list-role-assignments-for-a-resource) を使用して Azure Arc 対応サーバー リソースのロールの割り当てを一覧表示し、他の PowerShell コードを使用して、結果を CSV または別の形式にエクスポートできます。 

Azure Arc 対応サーバーで実行されているアプリケーションまたはプロセスにマネージド ID を使用している場合は、Azure VM にマネージド ID が割り当てられていることを確認する必要があります。 マネージド ID のロールの割り当てを表示するには、Azure PowerShell の `Get-AzADServicePrincipal` コマンドレットを使用できます。 詳細については、「[マネージド ID のロールの割り当ての一覧表示](../../role-based-access-control/role-assignments-list-powershell.md#list-role-assignments-for-a-managed-identity)」を参照してください。 

マシンまたはサーバー内の設定の監査または構成に Azure Policy が使用されている場合は、システム マネージド ID も使用されます。 Azure Arc 対応サーバーでは、ゲスト構成エージェント サービスが含まれており、それによって監査設定の検証が実行されます。 移行後は、手動で、またはゲスト構成拡張機能のポリシーを使用して Azure VM を構成する方法について、[Azure 仮想マシンのデプロイ要件](../../governance/policy/concepts/guest-configuration.md#deploy-requirements-for-azure-virtual-machines)に関する記事を参照してください。

マネージド ID でアクセスされるリソースのロールの割り当てを更新し、新しい Azure VM ID でそれらのサービスに対する認証を行えるようにします。 「[Azure リソースのマネージド ID と Azure 仮想マシン (VM) の連携](../../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md)」を参照して、その方法を確認してください。

## <a name="step-3-disconnect-from-azure-arc-and-uninstall-agent"></a>手順 3: Azure Arc から切断し、エージェントをアンインストールする

次のいずれかの方法を使用して、Azure 内の Azure Arc 対応サーバーのリソース ID を削除します。

   * マシンまたはサーバーで `azcmagent disconnect` コマンドを実行する。

   * Azure portal で選択した登録済み Azure Arc 対応サーバーから、上部のバーの **[削除]** を選択する。

   * [Azure CLI](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) または [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource) を使用する。 `ResourceType` パラメーターには `Microsoft.HybridCompute/machines` を使用します。

次に、「[エージェントを削除する](manage-agent.md#remove-the-agent)」の手順に従って、Azure Arc 対応サーバーの Windows または Linux エージェントを削除します。

## <a name="step-4-install-the-azure-guest-agent"></a>手順 4: Azure ゲスト エージェントをインストールする

オンプレミスから Azure に移行された VM には、Linux または Windows の Azure ゲスト エージェントがインストールされていません。 これらのシナリオでは、手動で VM エージェントをインストールする必要があります。 VM エージェントのインストール方法の詳細については、[Azure 仮想マシン Windows エージェントの概要](../../virtual-machines/extensions/agent-windows.md)または [Azure 仮想マシン Linux エージェントの概要](../../virtual-machines/extensions/agent-linux.md)に関するページを参照してください。

## <a name="step-5-migrate-server-or-machine-to-azure"></a>手順 5: サーバーまたはマシンを Azure に移行する

Azure Migration を使用した移行に進む前に、「[Azure への移行に向けてオンプレミスのマシンの準備を整える](../../migrate/prepare-for-migration.md)」という記事を参照して、Azure Migrate を使用するために必要な要件を確認してください。 Azure への移行を完了するには、ご使用環境に基づく Azure Migrate の[移行オプション](../../migrate/prepare-for-migration.md#next-steps)を確認します。

## <a name="step-6-deploy-azure-vm-extensions"></a>手順 6: Azure VM 拡張機能をデプロイする

移行とすべての移行後の構成手順が完了したら、Azure Arc 対応サーバーにもともとインストールされていた VM 拡張機能に基づいて Azure VM 拡張機能をデプロイできます。 「[Azure 仮想マシンの拡張機能とその機能](../../virtual-machines/extensions/overview.md)」を参照して、拡張機能のデプロイを計画します。 

ゲスト構成ポリシー定義を使ってマシン内の監査設定の使用を再開するには、「[ゲスト構成を有効にする](../../governance/policy/concepts/guest-configuration.md#enable-guest-configuration)」を参照してください。

Log Analytics の VM 拡張機能または Dependency Agent の VM 拡張機能が Azure Policy と [VM insights のイニシアチブ](../../azure-monitor/vm/vminsights-enable-policy.md)を使用してデプロイされていた場合は、前に作成した[除外](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)を削除します。 Azure Policy を使用して Azure 仮想マシンを有効にするには、「[Azure Policy を使用して大規模に Azure Monitor をデプロイする](../../azure-monitor/best-practices.md)」を参照してください。 

## <a name="next-steps"></a>次のステップ

トラブルシューティング情報については、[Connected Machine エージェントのトラブルシューティング](troubleshoot-agent-onboard.md) ガイドに関するページを参照してください。
