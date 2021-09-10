---
title: Azure の仮想マシン スケール セットのフレキシブル オーケストレーション
description: Azure の仮想マシン スケール セットのフレキシブル オーケストレーション モードを使用する方法について説明します。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/11/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: bf52db4950fd14e15cbd52d94b2e4ffbb9d225bb
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123314550"
---
# <a name="preview-flexible-orchestration-for-virtual-machine-scale-sets-in-azure"></a>プレビュー: Azure の仮想マシン スケール セットのフレキシブル オーケストレーション

**適用対象:** :heavy_check_mark: フレキシブル スケール セット

フレキシブル オーケストレーションを使用した仮想マシン スケール セットでは、[仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)のスケーラビリティと[可用性セット](availability-set-overview.md)のリージョンの可用性の保証を組み合わせることができます。

Azure Virtual Machine Scale Sets では、負荷分散が行われる VM のグループを作成して管理することができます。 需要または定義されたスケジュールに応じて、VM インスタンスの数を自動的に増減させることができます。 スケール セットには、次のような主な利点があります。
- 複数の VM の作成と管理が容易である
- 複数の障害ドメインに VM を分散することにより、高可用性とアプリケーションの回復性が提供される
- リソースの需要の変化に応じた、アプリケーションの自動スケーリングを可能にする
- 大規模に動作する

Azure ではフレキシブル オーケストレーションを使用することで、Azure VM エコシステム全体で統合されたエクスペリエンスを実現できます。 フレキシブル オーケストレーションでは、リージョン内の障害ドメインまたは可用性ゾーン内で VM を分散することで、(最大 1,000 個の VM まで) 高可用性を保証します。 これにより、以下のようなクォーラムベースまたはステートフル ワークロードを実行するのに欠かせない障害ドメインを分離したまま、アプリケーションをスケールアウトできます。
- クォーラムベースのワークロード
- オープンソース データベース
- ステートフル アプリケーション
- 高可用性と大きいスケールが必要なサービス
- 仮想マシンの種類の混在、またはスポットとオンデマンドの VM の併用が求められるサービス
- 既存の可用性セット アプリケーション

[オーケストレーション モード](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md)での均一スケール セットとフレキシブル スケール セットの違いについて確認してください。


> [!IMPORTANT]
> フレキシブル オーケストレーション モードの仮想マシン スケール セットは現在、パブリック プレビュー段階です。 後述するパブリック プレビュー機能を使用するためには、オプトイン手順が必要です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


> [!CAUTION]
> オーケストレーション モードは、スケール セットを作成するときに定義され、後で変更または更新することはできません。


## <a name="register-for-flexible-orchestration-mode"></a>フレキシブル オーケストレーション モードの登録
フレキシブル オーケストレーション モードで仮想マシン スケール セットをデプロイするには、最初にプレビュー機能に対してサブスクリプションを登録しておく必要があります。 登録が完了するまでに数分かかる場合があります。 登録には Azure portal、Azure PowerShell または Azure CLI を使用できます。

### <a name="azure-portal"></a>Azure portal

スケール セット プレビューのフレキシブル オーケストレーション モードでは、次の手順でリンクされている "*プレビュー*" の Azure portal を使用します。 

1. https://preview.portal.azure.com で、Azure Portal にログインします。
1. **[サブスクリプション]** に移動します。
1. サブスクリプションの名前を選択して、フレキシブル オーケストレーション モードでスケール セットを作成するサブスクリプションの詳細ページに移動します。
1. **[設定]** の下のメニューで、 **[プレビュー機能]** を選択します。
1. 有効にするオーケストレーターの 4 つの機能 (*VMOrchestratorSingleFD*、*VMOrchestratorMultiFD*、*VMScaleSetFlexPreview*、*SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview*) を選択します。
1. **[登録]** を選択します。

サブスクリプションに対して機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。 

1. **[設定]** の下のメニューで、 **[リソース プロバイダー]** を選択します。
1. [`Microsoft.compute`] を選択します。
1. **[再登録]** を選択します。


### <a name="azure-powershell"></a>Azure PowerShell
[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) コマンドレットを使用して、サブスクリプションでのプレビューを有効にします。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMScaleSetFlexPreview -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview -ProviderNamespace Microsoft.Compute
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
[az feature register](/cli/azure/feature#az_feature_register) を使用して、サブスクリプションでのプレビューを有効にします。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```


## <a name="get-started-with-flexible-orchestration-mode"></a>フレキシブル オーケストレーション モードの開始

[Azure potal](flexible-virtual-machine-scale-sets-portal.md)、[Azure CLI](flexible-virtual-machine-scale-sets-cli.md)、[Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md)、または [ARM テンプレート](flexible-virtual-machine-scale-sets-rest-api.md)を使用して、スケール セットでフレキシブル オーケストレーション モードを開始します。 


## <a name="add-instances-with-autoscaling-or-manually"></a>自動スケールまたは手動でインスタンスを追加する
フレキシブル オーケストレーションを使用した仮想マシン スケール セットは、複数の VM を管理するためのシン オーケストレーション レイヤーとして機能します。 スケール セットによって管理される VM を追加するには、いくつかの方法があります。

- **インスタンス数の設定**

    フレキシブル オーケストレーションを使用したスケール セットを作成する場合は、スケールアウトに使用するテンプレートを記述する VM プロファイルまたはテンプレートを定義します。その後、容量パラメーターを設定して、スケール セットによって管理される VM インスタンスの数を増減させることができます。 

- **メトリックまたはスケジュールによる自動スケール** 

    または、自動スケール ルールを設定して、メトリックまたはスケジュールに基づいて容量を増減することができます。 [自動スケールを使用する仮想マシン スケール セット](..\virtual-machine-scale-sets\virtual-machine-scale-sets-autoscale-overview.md)に関する記事を参照してください。 

- **VM の作成時にスケール セットを指定する**

    VM を作成するときに、必要に応じて仮想マシン スケール セットに追加するように指定できます。 VM は、VM の作成時にのみスケール セットに追加できます。


## <a name="explicit-network-outbound-connectivity-required"></a>明示的なネットワーク送信接続が必要 

既定のネットワーク セキュリティを強化するために、フレキシブル オーケストレーションを使用した仮想マシン スケール セットでは、自動スケール プロファイルを使用して暗黙的に作成されたインスタンスで、次のいずれかの方法を使用して送信接続を明示的に定義する必要があります。 

- ほとんどのシナリオでは、[NAT Gateway をサブネットに接続する](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md)ことをお勧めします。
- 高度なセキュリティ要件を持つシナリオの場合や、Azure Firewall またはネットワーク仮想アプライアンス (NVA) を使用する場合は、カスタムのユーザー定義ルートをファイアウォール経由のネクスト ホップとして指定できます。 
- インスタンスは、Standard SKU Azure Load Balancer のバックエンド プールにあります。 
- インスタンスのネットワーク インターフェイスにパブリック IP アドレスをアタッチします。 

単一インスタンスの VM と仮想マシン スケール セットで均一オーケストレーションを使用すると、送信接続が自動的に提供されます。 

明示的な送信接続を必要とする一般的なシナリオには、次のものがあります。 

- Windows VM のアクティブ化では、VM インスタンスから Windows アクティブ化キー管理サービス (KMS) への送信接続が定義されている必要がある。 詳細については、[Windows VM のアクティブ化に関する問題のトラブルシューティング](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)に関する記事を参照してください。  
- ストレージ アカウントまたは Key Vault にアクセスする。 Azure サービスへの接続は、[プライベート リンク](../private-link/private-link-overview.md)を使用して確立することもできます。 

セキュリティで保護された送信接続の定義の詳細については、「[Azure での既定の送信アクセス](https://aka.ms/defaultoutboundaccess)」を参照してください。


## <a name="assign-fault-domain-during-vm-creation"></a>VM 作成時の障害ドメインの割り当て
フレキシブル オーケストレーションのスケール セットでは障害ドメインの数を選択できます。 既定では、フレキシブル スケール セットに VM を追加すると、Azure によってインスタンスが障害ドメインに対して均一に分散されます。 Azure に障害ドメインの割り当てを任せることが推奨されますが、高度またはトラブルシューティングのシナリオでは、この既定の動作をオーバーライドして、インスタンスが配置される障害ドメインを指定できます。

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

## <a name="instance-naming"></a>インスタンスの命名
VM を作成し、それをフレキシブル スケール セットに追加する場合、Azure の名前付け規則内においてインスタンス名を完全に制御できます。 自動スケーリングを通じて自動的に VM がスケール セットに追加される場合、プレフィックスを付加します。名前の最後には、Azure によって一意の番号が追加されます。 

## <a name="list-scale-sets-vm-api-changes"></a>スケール セット VM API の変更の一覧表示
Virtual Machine Scale Sets では、スケール セットに属するインスタンスを一覧表示できます。 フレキシブル オーケストレーションでは、Virtual Machine Scale Sets VM のリスト コマンドを使用して、スケール セット VM の ID を一覧表示できます。 その後、Virtual Machine Scale Sets VM の取得コマンドを呼び出して、スケール セットでの VM インスタンスの動作について詳細を取得できます。 VM の完全な詳細を取得するには、標準の VM 取得コマンドまたは [Azure Resource Graph](../governance/resource-graph/overview.md) を使用します。


## <a name="query-instances-for-power-state"></a>インスタンスの稼働状態についてのクエリ
推奨されるのは、Azure Resource Graph を使用して仮想マシン スケール セット内のすべての VM に対してクエリを実行する方法です。 Azure Resource Graph には、サブスクリプション全体で大規模に Azure リソースに対してクエリを実行する効率的な機能が備わっています。

```
| where type =~ 'Microsoft.Compute/virtualMachines'
| where properties.virtualMachineScaleSet contains "demo"
| extend powerState = properties.extended.instanceView.powerState.code
| project name, resourceGroup, location, powerState
| order by resourceGroup desc, name desc
```

[Azure Resource Graph](../governance/resource-graph/overview.md) を使用したリソースへのクエリ実行は、Azure リソースに対してクエリを実行するのに便利で効率的な方法です。リソース プロバイダーへの API 呼び出しを最小限に抑えられます。 Azure Resource Graph で得られるのは最終的に一貫したキャッシュであり、新しいまたは更新されたリソースが最大 60 秒間反映されない場合があります。 次のようにすることができます。
- リソース グループまたはサブスクリプション内の VM を一覧表示する。
- 展開オプションを使用して、サブスクリプション内のすべての VM のインスタンス ビュー (障害ドメインの割り当て、稼働およびプロビジョニングの状態) を取得する。
- VM の取得 API およびコマンドを使用して、単一インスタンスのモデルとインスタンスのビューを取得する。


## <a name="scale-sets-vm-batch-operations"></a>スケール セット VM のバッチ操作
仮想マシン スケール セット VM API ではなく標準の VM コマンドを使用して、インスタンスを起動、停止、再起動、削除します。 仮想マシン スケール セット VM のバッチ操作 (すべて起動、すべて停止、すべて再イメージ化など) は、フレキシブル オーケストレーション モードでは使用されません。


## <a name="monitor-application-health"></a>アプリケーションの正常性を監視する
アプリケーションの稼働状況の監視では、アプリケーションから Azure にハートビートを提供し、自分のアプリケーションが正常であるのか、それとも異常であるのかを判断できます。 Azure による異常な VM インスタンスの自動置換を利用できます。 フレキシブル スケール セット インスタンスの場合、仮想マシンにアプリケーション正常性拡張機能をインストールして構成する必要があります。 均一スケール セット インスタンスの場合、アプリケーション正常性拡張機能を使用することも、Azure Load Balancer のカスタム正常性プローブを使用して正常性を測定することもできます。


## <a name="retrieve-boot-diagnostics-data"></a>ブート診断データの取得
標準の VM API およびコマンドを使用して、インスタンスのブート診断データとスクリーンショットを取得します。 Virtual Machine Scale Sets VM のブート診断 API およびコマンドは、フレキシブル オーケストレーション モードのインスタンスでは使用されません。


## <a name="vm-extensions"></a>VM 拡張機能
均一オーケストレーション モードのインスタンス向けの拡張機能でなく、標準の仮想マシン向けの拡張機能を使用します。


## <a name="features"></a>フィーチャー
次の表に、フレキシブル オーケストレーション モードの機能と、該当するドキュメントへのリンクを示します。

| 機能 | フレキシブル オーケストレーション (プレビュー) によるサポート対象 |
|-|-|
| 仮想マシンのタイプ | 標準の Azure IaaS VM (Microsoft.compute /virtualmachines) |
| 最大インスタンス数 (FD 可用性の保証あり) | 1000 |
| サポートされている SKU | D シリーズ、E シリーズ、F シリーズ、A シリーズ、B シリーズ、Intel、AMD |
| 可用性ゾーン | 必要に応じて、すべてのインスタンスが単一の可用性ゾーンに配置されるように指定します |
| 障害ドメイン - 最大分散 (Azure によってインスタンスが最大限に分散されます) | Yes |
| 障害ドメイン - 固定分散 | 2-3 個の FD (リージョンの最大 FD 数によって異なります)、ゾーンのデプロイの場合は 1 個の FD |
| 更新ドメイン | 非推奨。プラットフォームのメンテナンスが FD ごとに実行されました |
| 可用性 SLA | なし (プレビュー期間中) |
| VM、NIC、ディスクに対する完全な制御権 | はい |
| 特定の障害ドメインへの VM の割り当て | はい |
| Accelerated Networking | いいえ (プレビュー期間中) |
| ゲスト内セキュリティ パッチ | はい |
| スポットのインスタンスと価格  | はい。スポットと通常の両方の優先度のインスタンスを使用できます |
| オペレーティング システムの混在 | はい。同一のフレキシブル スケール セット内で Linux と Windows が共存できます |
| アプリケーション正常性の監視 | アプリケーション正常性拡張機能 |
| Ultra SSD ディスク  | はい |
| 近接配置グループ  | はい。[近接配置グループのドキュメント](../virtual-machine-scale-sets/proximity-placement-groups.md)をお読みください |
| Azure Load Balancer Standard SKU | はい |
| セット内の VM の一覧表示 | はい |
| Azure Backup | はい |
| 終了通知 (VM スケール セット) | はい。[終了通知のドキュメント](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md)をお読みください |
| インスタンスの修復 (VM スケール セット) | はい。[インスタンスの修復のドキュメント](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md)をお読みください |
| 自動スケーリング | Yes |
| VM インスタンスを削除する際の NIC とディスクの削除 | Yes |
| ポリシーのアップグレード (VM スケール セット) | いいえ |
| イメージ ベースの OS の自動更新 | いいえ |
| Infiniband  | いいえ |
| 書き込みアクセラレータ  | いいえ |
| Azure 専用ホスト  | いいえ |
| Basic SLB  | いいえ |
| Application Gateway | はい |
| メンテナンス コントロール  | いいえ |
| Azure アラート | いいえ |
| VM Insights | いいえ |
| Azure Site Recovery |  はい。PowerShell を使用します |
| Service Fabric | いいえ |
| Azure Kubernetes Service (AKS) | No |
| 信頼された Virtual Machines (プレビュー) | Yes |
| 拡張機能の自動更新 | Yes |


### <a name="unsupported-parameters"></a>サポートされていないパラメーター

仮想マシン スケール セットのフレキシブル オーケストレーションのパブリック プレビュー期間は、次の仮想マシン スケール セットのパラメーターは現在サポートされていません。
- 1 つの配置グループ - `singlePlacementGroup=False` を選択する必要があります。
- 複数ゾーンのデプロイ - リージョンにデプロイするか、すべての VM を 1 つのゾーンにデプロイできます
- 特殊な SKU を使用したデプロイ: G、H、L、M、N シリーズ VM ファミリ
- VMSS のオーバープロビジョニング
- イメージベースの OS の自動アップグレード
- SLB 正常性プローブを使用したアプリケーションの正常性 - インスタンスでアプリケーション正常性拡張機能を使用してください
- 仮想マシン スケール セットのアップグレード ポリシー - null または空にする必要があります
- Azure Dedicated Host へのデプロイ
- 非管理対象ディスク
- 仮想マシン スケール セットのスケールイン ポリシー
- 仮想マシン スケール セットのインスタンス保護
- 高速ネットワーク
- Basic Load Balancer
- Standard Load Balancer NAT プール経由のポートの転送 - 特定のインスタンスに対して NAT 規則を構成できます


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>フレキシブル オーケストレーションでのスケール セットのトラブルシューティング
ご自分のトラブルシューティング シナリオに適した解決策を見つけてください。

<!-- error -->
### <a name="invalidparameter-parameter-virtualmachineprofile-is-not-allowed"></a>InvalidParameter. Parameter 'virtualMachineProfile' is not allowed.

```
InvalidParameter. Parameter 'virtualMachineProfile' is not allowed.
```

**原因:** サブスクリプションがフレキシブル オーケストレーション モードのパブリック プレビューに登録されていません。

**解決策:** サブスクリプションの VMScaleSetPublicPreview 機能を登録する必要があります。 上記の手順に従って、フレキシブル オーケストレーション モードのパブリック プレビューに登録します。


<!-- error -->
### <a name="badrequest-creating-a-virtual-machine-with-a-public-ip-address-via-networkinterfaceconfigurations"></a>BadRequest. Creating a Virtual Machine with a Public IP Address via NetworkInterfaceConfigurations

```
BadRequest. Creating a Virtual Machine with a Public IP Address via NetworkInterfaceConfigurations during the Public Preview of VM NetworkInterfaceConfigurations initially requires the feature 'Microsoft.Compute/SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview'.
```

**原因:** サブスクリプションがフレキシブル オーケストレーション モードのパブリック プレビューに登録されていません。

**解決策:** サブスクリプションに `SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview` 機能を登録する必要があります。 上記の手順に従って、フレキシブル オーケストレーション モードのパブリック プレビューに登録します。


<!-- error -->
### <a name="invalidparameter-the-value-false-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-true"></a>InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**原因:** サブスクリプションがフレキシブル オーケストレーション モードのパブリック プレビューに登録されていません。

**解決策:** 上記の手順に従って、フレキシブル オーケストレーション モードのパブリック プレビューに登録します。


<!-- error -->
### <a name="invalidparameter-the-specified-fault-domain-count-2-must-fall-in-the-range-1-to-1"></a>InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**原因:** 選択したリージョンまたはゾーンで `platformFaultDomainCount` パラメーターが無効です。

**解決策:** 有効な `platformFaultDomainCount` 値を選択する必要があります。 ゾーン デプロイの場合、`platformFaultDomainCount` の最大値は 1 です。 ゾーンが指定されていないリージョン デプロイの場合、`platformFaultDomainCount` の最大値はリージョンによって異なります。 スクリプトでリージョンあたりの障害ドメインの最大数を判断するには、[VM の可用性の管理](../virtual-machines/availability.md)に関するページを参照してください。


<!-- error -->
### <a name="operationnotallowed-deletion-of-virtual-machine-scale-set-is-not-allowed-as-it-contains-one-or-more-vms-please-delete-or-detach-the-vms-before-deleting-the-virtual-machine-scale-set"></a>OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**原因:** 1 つ以上の仮想マシンが関連付けられているフレキシブル オーケストレーション モードのスケール セットを削除しようとしています。

**解決策:** フレキシブル オーケストレーション モードのスケール セットに関連付けられているすべての仮想マシンを削除すると、スケール セットを削除できます。


<!-- error -->
### <a name="invalidparameter-the-value-true-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-false"></a>InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**原因:** サブスクリプションがフレキシブル オーケストレーション モードのプレビューに登録されているものの、`singlePlacementGroup` パラメーターが *True* に設定されています。

**解決策:** `singlePlacementGroup` を *False* に設定する必要があります。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure portal を使用したスケール セットのフレキシブル オーケストレーションモード。](flexible-virtual-machine-scale-sets-portal.md)
