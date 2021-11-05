---
title: フレキシブル オーケストレーションでデプロイとリソースを仮想マシン スケール セットに移行する
description: フレキシブル オーケストレーションでデプロイとリソースを仮想マシン スケール セットに移行する方法について説明します。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/14/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: b4662c2fd6c5a0950bc3b2b6f336fabab12fc1aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008481"
---
# <a name="migrate-deployments-and-resources-to-virtual-machine-scale-sets-in-flexible-orchestration"></a>フレキシブル オーケストレーションでデプロイとリソースを仮想マシン スケール セットに移行する 

**適用対象:** :heavy_check_mark: フレキシブル スケール セット

可用性セットと同様に、仮想マシン スケール セットを使用すると、複数の障害ドメイン間で仮想マシンを分散させることができます。 フレキシブル オーケストレーションを使用した仮想マシン スケール セットでは、[均一オーケストレーション モードの仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)のスケーラビリティと[可用性セット](availability-set-overview.md)のリージョンの可用性の保証を組み合わせることができます。 この記事では、仮想マシン スケール セットをフレキシブル オーケストレーション モードに切り替えるときの移行に関する考慮事項について説明します。 

## <a name="update-availability-set-deployments-templates-and-scripts"></a>可用性セットのデプロイ テンプレートとスクリプトを更新する

まず、[Azure CLI](flexible-virtual-machine-scale-sets-cli.md)、[Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md)、または [ARM テンプレート](flexible-virtual-machine-scale-sets-rest-api.md)を使用して、自動スケーリング プロファイルを使用しない仮想マシン スケール セットを作成する必要があります。 Azure portal では、自動スケーリング プロファイルを使用した仮想マシンスケール セットのみ作成できます。 自動スケーリング プロファイルを必要としない場合や、[Azure portal](flexible-virtual-machine-scale-sets-portal.md) を使用してスケール セットを作成する場合は、初期容量を 0 に設定できます。 
 
仮想マシン スケール セットの障害ドメイン数を指定する必要があります。 リージョン (非ゾーン) デプロイの場合、仮想マシン スケール セットでは、可用性セットと同じ障害ドメイン保証が提供されます。 ただし、最大 1000 インスタンスまでスケールアップできます。 複数の可用性ゾーンにインスタンスを分散するゾーン デプロイの場合は、障害ドメイン数を 1 に設定する必要があります。

更新ドメインは、フレキシブル オーケストレーション モードでは非推奨となりました。 汎用 SKU を使用したほとんどのプラットフォームの更新はライブ マイグレーションで実行され、インスタンスの再起動は必要ありません。 プラットフォームのメンテナンスでインスタンスの再起動が必要になった場合、障害ドメインによって、更新プログラムが障害ドメインに適用されます。  
    
仮想マシン スケール セットのフレキシブル オーケストレーションでは、複数の可用性ゾーンにまたがるインスタンスのデプロイもサポートされています。 複数の可用性ゾーンに分散するように VM のデプロイを更新することを検討してください。 

このプロセスの最後の手順は、仮想マシンの作成です。 可用性セットを指定する代わりに、仮想マシン スケール セットを指定します。 必要に応じて、VM を配置する可用性ゾーンまたは障害ドメインを指定できます。 


## <a name="migrate-existing-availability-set-vms"></a>既存の可用性セットの VM を移行する 

現在、可用性セット内の既存のインスタンスを仮想マシン スケール セットに直接移動するための自動ツールはありません。 ただし、既存のインスタンスをフレキシブル スケール セットに移行するために使用できる方法がいくつかあります。 

### <a name="bluegreen-or-side-by-side-migration"></a>ブルー/グリーンまたはサイドバイサイドの移行 

1. 同じリソース グループ、仮想ネットワーク、ロード バランサーなどの類似した構成を持つ新しいスケール セットの仮想マシン インスタンスを、可用性の VM として使用します 
1. データやネットワーク トラフィックなどを移行して、新しいスケール セット インスタンスを使用します 
1. アプリケーションに対してスケール セットの VM を実行したまま、元の可用性セットの仮想マシンを割り当て解除または削除します

### <a name="replace-vm-instances"></a>VM インスタンスの置換 

1. 仮想マシンから保持するパラメーター (名前、NIC ID、OS とデータ ディスクの ID、VM 構成設定、障害ドメインの配置など) をメモします 
1. 可用性セットの仮想マシンを削除します。 VM の NIC とディスクは削除されません  
1. 元の VM のパラメーターを使用して、新しい仮想マシン オブジェクトを作成します 
    - NIC ID 
    - OS とデータ ディスク 
    - 障害ドメインの配置 
    - その他の VM 設定 


## <a name="update-uniform-scale-sets-deployment-templates-and-scripts"></a>均一スケール セットのデプロイ テンプレートとスクリプトを更新する

フレキシブル オーケストレーションを使用するように、均一仮想マシン スケール セットのデプロイ テンプレートとスクリプトを更新します。 テンプレートの次の要素を変更して、プロセスを正常に完了させます。 

- `LoadBalancerNATPool` を削除 (フレキシブルでは無効) 
- オーバープロビジョニング パラメーターを削除 (フレキシブルでは無効) 
- `upgradePolicy` を削除 (フレキシブルではまだ無効) 
- コンピューティング API のバージョンを **2021-03-01** に更新 
- オーケストレーション モード `flexible` を追加 
- `platformFaultDomainCount` を設定 (必須) 
- `singlePlacementGroup` を false に設定 (必須) 
- **2021-11-01** 以降のネットワーク API バージョンを追加 
- IP `configuration.properties.primary` を *true* に設定 (アウトバウンド規則に必須)


## <a name="migrate-existing-uniform-scale-sets"></a>既存の均一スケール セットを移行する 

現在、既存のインスタンスを直接移動したり、均一スケール セットをフレキシブル仮想マシン スケール セットにアップグレードしたりするための自動ツールはありません。 ただし、既存のインスタンスをフレキシブル スケール セットに移行するには、次の方法を使用できます。

### <a name="bluegreen-or-side-by-side-migration"></a>ブルー/グリーンまたはサイドバイサイドの移行 

1. 同じリソース グループ、仮想ネットワーク、ロード バランサーなどの類似した構成を持つ新しいフレキシブル オーケストレーション モードのスケール セットを、均一オーケストレーション モードの元のスケール セットとして使用します
1. データやネットワーク トラフィックなどを移行して、新しいスケール セット インスタンスを使用します 
1. アプリケーションに対してスケール セットの仮想マシンを実行したまま、元の均一スケール セットの仮想マシンをスケールダウンまたは削除します


## <a name="flexible-scale-sets-considerations"></a>フレキシブル スケール セットに関する考慮事項 

フレキシブル オーケストレーションを使用した仮想マシン スケール セットでは、[均一オーケストレーションの仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)のスケーラビリティと可用性セットのリージョンの可用性の保証を組み合わせることができます。 フレキシブル オーケストレーション モードを使用することを決定する際の主な考慮事項を次に示します。 

### <a name="create-scalable-network-connectivity"></a>スケーラブルなネットワーク接続を作成する 
<!-- the following is an important link to use in FLEX documentation to reference this section:
/virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity
-->

ネットワーク送信アクセスの動作は、スケール セット内での仮想マシンの作成方法によって異なります。 **手動で追加された VM インスタンス** には、既定の送信接続アクセスがあります。 **暗黙的に作成された VM インスタンス** には、既定のアクセスがあります。 

既定のネットワーク セキュリティを強化するために、**自動スケール プロファイルを使用して暗黙的に作成された仮想マシン インスタンスには、既定の送信アクセスはありません**。 暗黙的に作成された VM インスタンスで仮想マシン スケール セットを使用するには、次のいずれかの方法で送信アクセスを明示的に定義する必要があります。 

- ほとんどのシナリオでは、[NAT Gateway をサブネットに接続する](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md)ことをお勧めします。
- 高度なセキュリティ要件を持つシナリオの場合や、Azure Firewall またはネットワーク仮想アプライアンス (NVA) を使用する場合は、カスタムのユーザー定義ルートをファイアウォール経由のネクスト ホップとして指定できます。 
- インスタンスは、Standard SKU Azure Load Balancer のバックエンド プールにあります。 
- インスタンス ネットワーク インターフェイスにパブリック IP アドレスを接続します。 

明示的な送信接続を必要とする一般的なシナリオには、次のものがあります。 

- Windows VM のアクティブ化では、VM インスタンスから Windows アクティブ化キー管理サービス (KMS) への送信接続が定義されている必要がある。 詳細については、[Windows VM のアクティブ化に関する問題のトラブルシューティング](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)に関する記事を参照してください。  
- ストレージ アカウントまたは Key Vault にアクセスする。 Azure サービスへの接続は、[プライベート リンク](../private-link/private-link-overview.md)を使用して確立することもできます。 
- Windows の更新プログラム。
- Linux パッケージ マネージャーへのアクセス。 

送信接続の定義付けに関する詳細については、「[Azure での既定の送信アクセス](../virtual-network/ip-services/default-outbound-access.md)」を参照してください。

NIC を明示的に作成する単一インスタンス VM では、既定の送信アクセスが提供されます。 統一オーケストレーション モードの仮想マシン スケール セットには、既定の送信接続も含まれます。 


> [!IMPORTANT]
> 明示的な送信ネットワーク接続があることを確認します。 詳細については、「[Azure の仮想ネットワークと仮想マシン](../virtual-network/network-overview.md)」を参照し、Azure のネットワークの[ベスト プラクティス](../virtual-network/concepts-and-best-practices.md)に従っていることを確認してください。 


### <a name="assign-fault-domain-during-vm-creation"></a>VM 作成時の障害ドメインの割り当て
フレキシブル オーケストレーションのスケール セットでは障害ドメインの数を選択できます。 既定では、フレキシブル スケール セットに VM を追加すると、Azure によってインスタンスが障害ドメインに対して均一に分散されます。 Azure に障害ドメインの割り当てを任せることが推奨されますが、高度またはトラブルシューティングのシナリオでは、この既定の動作をオーバーライドして、インスタンスが配置される障害ドメインを指定できます。

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>インスタンスの命名
VM を作成し、それをフレキシブル スケール セットに追加する場合、Azure の名前付け規則内においてインスタンス名を完全に制御できます。 自動スケーリングを通じて自動的に VM がスケール セットに追加される場合、プレフィックスを付加します。名前の最後には、Azure によって一意の番号が追加されます。 

### <a name="list-scale-sets-vm-api-changes"></a>スケール セット VM API の変更の一覧表示
Virtual Machine Scale Sets では、スケール セットに属するインスタンスを一覧表示できます。 フレキシブル オーケストレーションでは、Virtual Machine Scale Sets VM のリスト コマンドを使用して、スケール セット VM の ID を一覧表示できます。 その後、Virtual Machine Scale Sets VM の取得コマンドを呼び出して、スケール セットでの VM インスタンスの動作について詳細を取得できます。 VM の完全な詳細を取得するには、標準の VM 取得コマンドまたは [Azure Resource Graph](../governance/resource-graph/overview.md) を使用します。


### <a name="query-instances-for-power-state"></a>インスタンスの稼働状態についてのクエリ
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


### <a name="scale-sets-vm-batch-operations"></a>スケール セット VM のバッチ操作
仮想マシン スケール セット VM API ではなく標準の VM コマンドを使用して、インスタンスを起動、停止、再起動、削除します。 仮想マシン スケール セット VM のバッチ操作 (すべて起動、すべて停止、すべて再イメージ化など) は、フレキシブル オーケストレーション モードでは使用されません。


### <a name="monitor-application-health"></a>アプリケーションの正常性を監視する
アプリケーションの稼働状況の監視では、アプリケーションから Azure にハートビートを提供し、自分のアプリケーションが正常であるのか、それとも異常であるのかを判断できます。 Azure による異常な VM インスタンスの自動置換を利用できます。 フレキシブル スケール セット インスタンスの場合、仮想マシンにアプリケーション正常性拡張機能をインストールして構成する必要があります。 均一スケール セット インスタンスの場合、アプリケーション正常性拡張機能を使用することも、Azure Load Balancer のカスタム正常性プローブを使用して正常性を測定することもできます。


### <a name="retrieve-boot-diagnostics-data"></a>ブート診断データの取得
標準の VM API およびコマンドを使用して、インスタンスのブート診断データとスクリーンショットを取得します。 Virtual Machine Scale Sets VM のブート診断 API およびコマンドは、フレキシブル オーケストレーション モードのインスタンスでは使用されません。


### <a name="vm-extensions"></a>VM 拡張機能
均一オーケストレーション モードのインスタンス向けの拡張機能でなく、標準の仮想マシン向けの拡張機能を使用します。


### <a name="protect-instances-from-delete"></a>インスタンスを削除から保護する

フレキシブル オーケストレーション モードの仮想マシン スケール セットには、現在、インスタンス保護オプションが設定されていません。 仮想マシン スケール セットで自動スケーリングを有効にしている場合、一部の VM は、スケールイン プロセス中に削除される危険性があります。 特定の VM インスタンスを削除から保護する場合は、[Azure Resource Manager ロック](../azure-resource-manager/management/lock-resources.md)を使用してください。



## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [均一およびフレキシブル オーケストレーション モードの API の違いを比較します。](../virtual-machine-scale-sets/orchestration-modes-api-comparison.md)