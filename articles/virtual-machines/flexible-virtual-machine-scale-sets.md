---
title: Azure の仮想マシン スケール セットのフレキシブル オーケストレーション
description: Azure の仮想マシン スケール セットのフレキシブル オーケストレーション モードを使用する方法について説明します。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/13/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 3403cebab79b49f3a22353bec0a03b68b5a24f0f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432835"
---
# <a name="flexible-orchestration-for-virtual-machine-scale-sets-in-azure"></a>Azure の仮想マシン スケール セットのフレキシブル オーケストレーション

**適用対象:** :heavy_check_mark: フレキシブル スケール セット

フレキシブル オーケストレーションを使用した仮想マシン スケール セットでは、[均一オーケストレーション モードの仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)のスケーラビリティと[可用性セット](availability-set-overview.md)のリージョンの可用性の保証を組み合わせることができます。

Azure Virtual Machine Scale Sets では、負荷分散が行われる VM のグループを作成して管理することができます。 需要または定義されたスケジュールに応じて、VM インスタンスの数を自動的に増減させることができます。 スケール セットには、次のような主な利点があります。
- 複数の VM の作成と管理が容易である
- 複数の可用性ゾーンまたは障害ドメインに VM を分散することにより、高可用性とアプリケーションの回復性が提供される
- リソースの需要の変化に応じた、アプリケーションの自動スケーリングを可能にする
- 大規模に動作する

Azure ではフレキシブル オーケストレーションを使用することで、Azure VM エコシステム全体で統合されたエクスペリエンスを実現できます。 フレキシブル オーケストレーションでは、リージョン内の障害ドメインまたは可用性ゾーン内で VM を分散することで、(最大 1,000 個の VM まで) 高可用性を保証します。 これにより、以下のようなクォーラムベースまたはステートフル ワークロードを実行するのに欠かせない障害ドメインを分離したまま、アプリケーションをスケールアウトできます。
- クォーラムベースのワークロード
- オープンソース データベース
- ステートフル アプリケーション
- 高可用性と大きいスケールが必要なサービス
- 仮想マシンの種類の混在、またはスポットとオンデマンドの VM の併用が求められるサービス
- 既存の可用性セット アプリケーション

> [!IMPORTANT]
> この記事では、フレキシブル オーケストレーション モードの仮想マシン スケール セットについて説明します。すべての新しいスケール セットのデプロイにこれを使用することをお勧めします。 均一スケール セットに関する情報にアクセスするには、[均一オーケストレーション モードの仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)に関するドキュメントを参照してください。

[オーケストレーション モード](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md)での均一スケール セットとフレキシブル スケール セットの違いについて確認してください。

> [!CAUTION]
> オーケストレーション モードは、スケール セットを作成するときに定義され、後で変更または更新することはできません。


## <a name="why-use-virtual-machine-scale-sets"></a>仮想マシン スケール セットを使用する理由
冗長性とパフォーマンスの向上を実現するには、アプリケーションを複数のインスタンスに分散するのが一般的です。 顧客は、アプリケーション インスタンスのいずれかに要求を分散するロード バランサー経由でアプリケーションにアクセスできます。 メンテナンスの実行またはアプリケーション インスタンスの更新が必要な場合、使用可能な別のアプリケーション インスタンスに顧客を分散する必要があります。 顧客の需要の増加に対応するには、アプリケーションを実行するアプリケーション インスタンスの数を増加させることが必要な場合があります。

Azure Virtual Machine Scale Sets は、多数の VM で実行されるアプリケーションの管理機能、リソースの自動スケーリング、トラフィックの負荷分散を備えています。 スケール セットには、次のような主な利点があります。

- **複数の VM の作成と管理が容易である**
    - アプリケーションを実行する VM が多数ある場合、環境全体で一貫した構成を維持することが重要です。 アプリケーションのパフォーマンスについて高い信頼性を実現するには、VM サイズ、ディスク構成、アプリケーション インストールがすべての VM で一致している必要があります。
    - スケール セットでは、すべての VM インスタンスが同一のベース OS イメージと構成から作成されます。 この方法を使用すると、追加の構成タスクまたはネットワーク管理を行うことなく、数百台の VM を容易に管理できます。
    - スケール セットでは、基本のレイヤー 4 トラフィック分散を実現する [Azure Load Balancer](../load-balancer/load-balancer-overview.md) と、より高度なレイヤー 7 トラフィック分散と TLS 終了を実現する [Azure Application Gateway](../application-gateway/overview.md) がサポートされています。

- **高可用性とアプリケーションの回復性を実現する**
    - スケール セットは、複数のインスタンスのアプリケーションを実行するために使用されます。 これらの VM インスタンスの 1 つに問題があっても、他のいずれかの VM インスタンスを通じて、顧客は最小限の中断で引き続きアプリケーションにアクセスできます。
    - 可用性を高めるために、[Availability Zones](../availability-zones/az-overview.md) を使用して、単一のデータセンターまたは複数のデータセンター内で、スケール セットの VM インスタンスを自動的に分散できます。

- **リソースの需要の変化に応じた、アプリケーションの自動スケーリングを可能にする**
    - アプリケーションに対する顧客の需要は、終日にわたってまたは一週間の中で変化することがあります。 スケール セットでは、顧客の需要に対応するために、アプリケーションの需要の増加に応じて VM インスタンスの数を自動的に増やしたり、需要の減少に応じて VM インスタンスの数を減らしたりできます。
    - また、自動スケーリングを行うと、需要が少ないときに、アプリケーションを実行する不要な VM インスタンスの数を最小限に抑えることができます。その一方で、需要の増加に応じて VM インスタンスが自動的に追加されるため、顧客は満足できるレベルのパフォーマンスの提供を受け続けることができます。 この機能は、必要に応じてコストを削減し、Azure リソースを効率的に作成するのに役立ちます。

- **大規模に動作する**
    - スケール セットでは、Azure Compute Gallery (以前の Shared Image Gallery) を通して標準のマーケットプレース イメージとカスタム イメージ用の最大 1,000 個の VM インスタンスがサポートされます。 マネージド イメージを使用してスケール セットを作成する場合、制限は 600 個の VM インスタンスです。
    - 運用環境のワークロードで最高のパフォーマンスを実現するには、[Azure Managed Disks](../virtual-machines/managed-disks-overview.md) を使用してください。



## <a name="get-started-with-flexible-orchestration-mode"></a>フレキシブル オーケストレーション モードの開始

[Azure potal](flexible-virtual-machine-scale-sets-portal.md)、[Azure CLI](flexible-virtual-machine-scale-sets-cli.md)、[Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md)、または [ARM テンプレート](flexible-virtual-machine-scale-sets-rest-api.md)を使用して、スケール セットでフレキシブル オーケストレーション モードを開始します。 

> [!IMPORTANT]
> 明示的な送信ネットワーク接続があることを確認します。 詳細については、「[Azure の仮想ネットワークと仮想マシン](../virtual-network/network-overview.md)」を参照し、Azure のネットワークの[ベスト プラクティス](../virtual-network/concepts-and-best-practices.md)に従っていることを確認してください。


## <a name="add-instances-with-autoscaling-or-manually"></a>自動スケールまたは手動でインスタンスを追加する
フレキシブル オーケストレーションを使用した仮想マシン スケール セットは、複数の VM を管理するためのシン オーケストレーション レイヤーとして機能します。 スケール セットによって管理される VM を追加するには、いくつかの方法があります。

- **インスタンス数の設定**

    フレキシブル オーケストレーションを使用したスケール セットを作成する場合は、スケールアウトに使用するテンプレートを記述する VM プロファイルまたはテンプレートを定義します。その後、容量パラメーターを設定して、スケール セットによって管理される VM インスタンスの数を増減させることができます。 

- **メトリックまたはスケジュールによる自動スケール** 

    または、自動スケール ルールを設定して、メトリックまたはスケジュールに基づいて容量を増減することができます。 [自動スケールを使用する仮想マシン スケール セット](..\virtual-machine-scale-sets\virtual-machine-scale-sets-autoscale-overview.md)に関する記事を参照してください。 

- **VM の作成時にスケール セットを指定する**

    VM を作成するときに、必要に応じて仮想マシン スケール セットに追加するように指定できます。 VM は、VM の作成時にのみスケール セットに追加できます。 新しく作成された VM は、デプロイ方法に関係なく、フレキシブル スケール セットと同じリソース グループ内に配置する必要があります。

フレキシブル オーケストレーション モードは、[メモリ保持更新またはライブ マイグレーション](../virtual-machines/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)をサポートする VM SKU で使用でき、これには Azure にデプロイされる全 IaaS VM の 90% が含まれます。 基本的に、B、D、E、F シリーズの VM など、汎用サイズのファミリが対象となります。 G、H、L、M、N シリーズの VM など、メモリ保持更新をサポートしない VM SKU やファミリに対するオーケストレーションをフレキシブル モードで行うことは、現時点ではできません。 特定の VM SKU がサポートされているかどうかは、[Compute Resource SKU API](/rest/api/compute/resource-skus/list) を使用して調べることができます。

```azurecli-interactive
az vm list-skus -l eastus --size standard_d2s_v3 --query "[].capabilities[].[name, value]" -o table
```

> [!IMPORTANT]
> ネットワークの動作は、スケールセット内で仮想マシンを作成する方法によって異なります。 詳細については、「 [スケーラブルなネットワーク接続](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity)」を参照してください。


## <a name="features"></a>特徴
次の表に、フレキシブル オーケストレーション モードの機能と、該当するドキュメントへのリンクを示します。

### <a name="basic-setup"></a>基本的なセットアップ

| 特徴量 | スケール セットのフレキシブル オーケストレーションによるサポート対象 |
|---|---|
| 仮想マシンのタイプ  | 標準の Azure IaaS VM (Microsoft.compute/virtualmachines)  |
| 最大インスタンス数 (FD の保証あり)  | 1000  |
| サポートされている SKU  | D シリーズ、E シリーズ、F シリーズ、A シリーズ、B シリーズ、Intel、AMD。特殊な SKU (G、H、L、M、N) はサポートされていません |
| VM、NIC、ディスクに対する完全な制御権  | はい  |
| 必要な RBAC アクセス許可  | コンピューティング VMSS 書き込み、コンピューティング VM 書き込み、ネットワーク |
| Accelerated Networking  | はい  |
| スポットのインスタンスと価格   | はい。スポットと通常の両方の優先度のインスタンスを使用できます  |
| オペレーティング システムの混在  | はい。同一のフレキシブル スケール セット内で Linux と Windows が共存できます  |
| ディスクの種類  | マネージド ディスクのみ、すべてのストレージの種類  |
| 書き込みアクセラレータ   | いいえ  |
| 近接配置グループ   | はい。[近接配置グループのドキュメント](../virtual-machine-scale-sets/proximity-placement-groups.md)をお読みください |
| Azure 専用ホスト   | いいえ  |
| マネージド ID  | ユーザー割り当て ID のみ  |
| グループに対する既存の VM の追加/削除  | いいえ  |
| Service Fabric  | いいえ  |
| Azure Kubernetes Service (AKS) / AKE  | いいえ  |
| UserData  | Partial、UserData を個々の VM に指定できます。 |


### <a name="autoscaling-and-instance-orchestration"></a>自動スケールとインスタンスのオーケストレーション

| 特徴量 | スケール セットのフレキシブル オーケストレーションによるサポート対象 |
|---|---|
| セット内の VM の一覧表示 | はい |
| 自動スケーリング (手動、メトリック ベース、スケジュール ベース) | Yes |
| VM インスタンスを削除する際の NIC とディスクの自動削除 | Yes |
| ポリシーのアップグレード (VM スケール セット) | いいえ。アップグレード ポリシーは、作成中に null または [] である必要があります |
| 自動 OS 更新 (VM スケール セット) | いいえ |
| ゲスト内セキュリティ パッチ | はい |
| 終了通知 (VM スケール セット) | はい。[終了通知のドキュメント](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md)をお読みください |
| アプリケーション正常性の監視 | アプリケーション正常性拡張機能 |
| インスタンスの修復 (VM スケール セット) | はい。[インスタンスの修復のドキュメント](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md)をお読みください |
| インスタンス保護 | いいえ。[Azure リソースのロック](../azure-resource-manager/management/lock-resources.md)を使用します |
| スケールイン ポリシー | いいえ |
| VMSS インスタンス ビューの取得 | いいえ |
| VM バッチ操作 (すべて開始、すべて停止、サブセットの削除など) | いいえ (VM API を使用して各インスタンスで操作をトリガーできます) |

### <a name="high-availability"></a>高可用性 

| 特徴量 | スケール セットのフレキシブル オーケストレーションによるサポート対象 |
|---|---|
| 可用性 SLA | 障害ドメイン間で分散しているインスタンスの場合は 99.95%。複数のゾーンに分散しているインスタンスの場合は 99.99%。 |
| 可用性ゾーン | インスタンスが 1 つ、2 つ、または 3 つの可用性ゾーンに分散して配置されるように指定します |
| 特定の可用性ゾーンに対する VM の割り当て | はい |
| 障害ドメイン - 最大拡散 (Azure によってインスタンスが最大限に拡散されます) | Yes |
| 障害ドメイン - 固定拡散 | 2 から 3 個の FD (リージョンの最大 FD 数によって異なります)。ゾーンのデプロイの場合は 1 個 |
| 特定の障害ドメインへの VM の割り当て | はい |
| 更新ドメイン | 非推奨 (プラットフォームのメンテナンスが FD ごとに実行されました) |
| メンテナンス実行 | VM API を使用して各インスタンスのメンテナンスをトリガーします |

### <a name="networking"></a>ネットワーク 

| 機能 | スケール セットのフレキシブル オーケストレーションによるサポート対象 |
|---|---|
| 既定の送信接続 | いいえ。[明示的な送信接続](../virtual-network/ip-services/default-outbound-access.md)が必要です |
| Azure Load Balancer Standard SKU | はい |
| Application Gateway | はい |
| Infiniband ネットワーク | いいえ |
| Basic SLB | いいえ |
| ネットワーク ポートの転送 | はい (個々のインスタンスの NAT 規則) |

### <a name="backup-and-recovery"></a>バックアップと回復 

| 特徴量 | スケール セットのフレキシブル オーケストレーションによるサポート対象 |
|---|---|
| Azure Backup  | はい |
| Azure Site Recovery | はい (PowerShell を使用) |
| Azure アラート  | はい |
| VM Insights  | 個々の VM にインストールできます |

### <a name="unsupported-parameters"></a>サポートされていないパラメーター

フレキシブル オーケストレーション モードの仮想マシン スケール セットでは、次の仮想マシン スケール セットのパラメーターは現在サポートされていません。
- 1 つの配置グループ - `singlePlacementGroup=False` を選択する必要があります
- 特殊な SKU を使用したデプロイ: G、H、L、M、N シリーズ VM ファミリ
- Ultra ディスク構成: `diskIOPSReadWrite`、`diskMBpsReadWrite`
- VMSS のオーバープロビジョニング
- イメージベースの OS の自動アップグレード
- SLB 正常性プローブを使用したアプリケーションの正常性 - インスタンスでアプリケーション正常性拡張機能を使用してください
- 仮想マシン スケール セットのアップグレード ポリシー - null または空にする必要があります
- Azure Dedicated Host へのデプロイ
- 非管理対象ディスク
- 仮想マシン スケール セットのスケールイン ポリシー
- 仮想マシン スケール セットのインスタンス保護
- Basic Load Balancer
- Standard Load Balancer NAT プール経由のポートの転送 - 特定のインスタンスに対して NAT 規則を構成できます


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>フレキシブル オーケストレーションでのスケール セットのトラブルシューティング
ご自分のトラブルシューティング シナリオに適した解決策を見つけてください。

<!-- error -->
### <a name="invalidparameter-the-specified-fault-domain-count-3-must-fall-in-the-range-1-to-2"></a>InvalidParameter. 指定した障害ドメインの数 3 は、1 から 2 の範囲でなければなりません。

```
InvalidParameter. The specified fault domain count 3 must fall in the range 1 to 2.
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
**原因:** `singlePlacementGroup`パラメーターが *True* に設定されています。

**解決策:** `singlePlacementGroup` を *False* に設定する必要があります。


<!-- error -->
### <a name="outboundconnectivitynotenabledonvm-no-outbound-connectivity-configured-for-virtual-machine"></a>OutboundConnectivityNotEnabledOnVM. 仮想マシンの送信接続が構成されていません。

```
OutboundConnectivityNotEnabledOnVM. No outbound connectivity configured for virtual machine.
```
**原因:** 送信インターネット接続を使用せずに、フレキシブル オーケストレーション モードで仮想マシン スケール セットを作成しようとしています。

**解決策:** お使いのアプリケーションに最適な方法で、仮想マシン スケール セットのセキュリティで保護された送信アクセスを有効にします。 送信アクセスは、サブネット上の NAT ゲートウェイを使用したり、ロード バランサー バックエンド プールにインスタンスを追加したり、インスタンスごとに明示的なパブリック IP を追加したりすることで有効にできます。 高度にセキュリティで保護されたアプリケーションの場合は、ファイアウォールまたは仮想ネットワーク アプリケーションを介してカスタムのユーザー定義ルートを指定できます。 詳細については、[既定の送信アクセス](../virtual-network/ip-services/default-outbound-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure portal を使用したスケール セットのフレキシブル オーケストレーションモード。](flexible-virtual-machine-scale-sets-portal.md)