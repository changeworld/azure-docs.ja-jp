---
title: Azure の仮想マシン スケール セットのオーケストレーション モード
description: Azure の仮想マシン スケール セットのフレキシブルおよび均一オーケストレーション モードを使用する方法について説明します。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex, devx-track-azurepowershell
ms.openlocfilehash: 7983ae912d29f2a27d35b261d1654205fe503651
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305072"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>プレビュー: Azure の仮想マシン スケール セットのオーケストレーション モード


**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

仮想マシン スケール セットは、プラットフォームによって管理される仮想マシンの論理グループを提供します。 スケール セットを使用して、仮想マシン構成モデルを作成し、CPU またはメモリの負荷に基づいて追加のインスタンスを自動的に追加または削除し、最新の OS バージョンに自動的にアップグレードします。 従来、スケール セットでは、スケール セットの作成時に提供された VM 構成モデルを使用して仮想マシンを作成できます。スケール セットで管理できるのは、構成モデルに基づいて暗黙的に作成された仮想マシンのみです。 

スケール セットのオーケストレーション モードを使用すると、スケール セットでの仮想マシン インスタンスの管理方法をより詳細に制御できます。

> [!IMPORTANT]
> オーケストレーション モードは、スケール セットを作成するときに定義され、後で変更または更新することはできません。


## <a name="scale-sets-with-uniform-orchestration"></a>均一オーケストレーションによるスケール セット
同一インスタンスの大規模なステートレス ワークロード用に最適化されています。

均一オーケストレーションによる仮想マシン スケール セットでは、仮想マシン プロファイルまたはテンプレートを使用して目的の容量にスケールアップします。 個々の仮想マシン インスタンスを一部管理またはカスタマイズできますが、均一では同一の VM インスタンスを使用します。 個々の均一 VM インスタンスは、仮想マシン スケール セット VM API コマンドを介して公開されます。 個々のインスタンスには、標準の Azure IaaS VM API コマンド、Azure Resource Manager タグ付け RBAC アクセス許可などの Azure 管理機能、Azure Backup、Azure Site Recovery との互換性はありません。 均一オーケストレーションでは、インスタンスを 100 個未満に構成した場合に、障害ドメインの高可用性の保証が得られます。 均一オーケストレーションは一般提供されていて、メトリックベースの自動スケーリング、インスタンス保護、自動 OS アップグレードなど、あらゆるスケール セット管理およびオーケストレーションをサポートしています。


## <a name="scale-sets-with-flexible-orchestration"></a>フレキシブル オーケストレーションによるスケール セット
同一または複数の仮想マシンの種類を使用して、大規模に高可用性を実現します。

Azure ではフレキシブル オーケストレーションを使用することで、Azure VM エコシステム全体で統合されたエクスペリエンスを実現できます。 フレキシブル オーケストレーションでは、リージョン内の障害ドメインまたは可用性ゾーン内で VM を分散することで、(最大 1,000 個の VM まで) 高可用性を保証します。 これにより、以下のようなクォーラムベースまたはステートフル ワークロードを実行するのに欠かせない障害ドメインを分離したまま、アプリケーションをスケールアウトできます。
- クォーラムベースのワークロード
- オープンソース データベース
- ステートフル アプリケーション
- 高可用性と大規模を要件とするサービス
- 仮想マシンの種類の混在、またはスポットとオンデマンドの VM の併用が求められるサービス
- 既存の可用性セット アプリケーション

> [!IMPORTANT]
> フレキシブル オーケストレーション モードの仮想マシン スケール セットは現在、パブリック プレビュー段階です。 後述するパブリック プレビュー機能を使用するためには、オプトイン手順が必要です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>フレキシブル オーケストレーション モードの変更点
フレキシブル オーケストレーションの主なメリットの 1 つは、スケール セットの子仮想マシンではなく標準の Azure IaaS VM を介してオーケストレーション機能を利用できる点です。 つまり、フレキシブル オーケストレーション インスタンスを管理する際には、(均一オーケストレーションで使用する仮想マシン スケール セット VM API ではなく) すべての標準 VM API を使用できます。 プレビュー期間中、フレキシブル オーケストレーションと均一オーケストレーションにおけるインスタンス管理には、いくつかの違いがあります。 一般に、可能な場合は標準の Azure IaaS VM API を使用することをお勧めします。 このセクションでは、フレキシブル オーケストレーションによって VM インスタンスを管理する際のベスト プラクティスの例を紹介します。

### <a name="assign-fault-domain-during-vm-creation"></a>VM 作成時の障害ドメインの割り当て
フレキシブル オーケストレーションのスケール セットでは障害ドメインの数を選択できます。 既定では、フレキシブル スケール セットに VM を追加すると、Azure によってインスタンスが障害ドメインに対して均一に分散されます。 Azure に障害ドメインの割り当てを任せることが推奨されますが、高度またはトラブルシューティングのシナリオでは、この既定の動作をオーバーライドして、インスタンスが配置される障害ドメインを指定できます。

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>インスタンスの命名
VM を作成し、それをフレキシブル スケール セットに追加する場合、Azure の名前付け規則内においてインスタンス名を完全に制御できます。 自動スケーリングを通じて自動的に VM がスケール セットに追加される場合、プレフィックスを付加します。名前の最後には、Azure によって一意の番号が追加されます。

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

### <a name="list-scale-sets-vm-api-changes"></a>スケール セット VM API の変更の一覧表示
Virtual Machine Scale Sets では、スケール セットに属するインスタンスを一覧表示できます。 フレキシブル オーケストレーションでは、Virtual Machine Scale Sets VM のリスト コマンドを使用して、スケール セット VM の ID を一覧表示できます。 その後、Virtual Machine Scale Sets VM の取得コマンドを呼び出して、スケール セットでの VM インスタンスの動作について詳細を取得できます。 VM の完全な詳細を取得するには、標準の VM 取得コマンドまたは [Azure Resource Graph](../governance/resource-graph/overview.md) を使用します。

### <a name="retrieve-boot-diagnostics-data"></a>ブート診断データの取得
標準の VM API およびコマンドを使用して、インスタンスのブート診断データとスクリーンショットを取得します。 Virtual Machine Scale Sets VM のブート診断 API およびコマンドは、フレキシブル オーケストレーション モードのインスタンスでは使用されません。

### <a name="vm-extensions"></a>VM 拡張機能
均一オーケストレーション モードのインスタンス向けの拡張機能でなく、標準の仮想マシン向けの拡張機能を使用します。


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>フレキシブル、均一、可用性セットの比較
次の表では、フレキシブル オーケストレーション モード、均一オーケストレーション モード、可用性セットを機能ごとに比較しています。

| 機能  | フレキシブル オーケストレーション (プレビュー) によるサポート対象  | 均一オーケストレーション (一般提供) によるサポート対象  | AvSets (一般提供) によるサポート対象  |
|-|-|-|-|
| 仮想マシンのタイプ  | 標準の Azure IaaS VM (Microsoft.compute /virtualmachines)  | スケール セット固有の VM (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | 標準の Azure IaaS VM (Microsoft.compute /virtualmachines)  |
| サポートされている SKU  | D シリーズ、E シリーズ、F シリーズ、A シリーズ、B シリーズ、Intel、AMD  | すべての SKU  | すべての SKU  |
| 可用性ゾーン  | 必要に応じて、すべてのインスタンスが単一の可用性ゾーンに配置されるように指定します  | インスタンスが 1 つ、2 つ、または 3 つの可用性ゾーンに分散して配置されるように指定します  | サポートされていません  |
| 障害ドメイン - 最大拡散 (Azure によってインスタンスが最大限に拡散されます)  | Yes  | はい  | いいえ  |
| 障害ドメイン - 固定拡散  | 2-3 FD (リージョンの最大値によって異なります)、ゾーンのデプロイの場合は 1 FD  | 2、3、5 FD。ゾーンのデプロイの場合は 1、5  | 2-3 FD (リージョンの最大値によって異なります)  |
| 更新ドメイン  | 非推奨。プラットフォームのメンテナンスが FD ごとに実行されました | 5 更新ドメイン  | 最大 20 個の更新ドメイン  |
| 可用性 SLA  | 現時点では行わない  | 単一配置グループの FD>1 の場合は 99.95%複数のゾーンに分散するインスタンスの場合は 99.99%  | 99.95%  |
| VM、NIC、ディスクに対する完全な制御権  | はい  | 仮想マシン スケール セット VM API による制限付きの制御  | Yes  |
| 自動スケーリング (手動、メトリック ベース、スケジュール ベース)  | Yes  | はい  | いいえ  |
| 特定の障害ドメインへの VM の割り当て  | はい  | いいえ  | いいえ  |
| VM インスタンスを削除する際の NIC とディスクの自動削除  | Yes  | はい  | いいえ  |
| ポリシーのアップグレード (VM スケール セット)  | いいえ。アップグレード ポリシーは、作成中に null または [] である必要があります  | 自動、ローリング、手動  | 該当なし  |
| イメージ ベースの OS の自動更新  | いいえ  | はい  | 該当なし  |
| ゲスト内セキュリティ パッチ  | はい  | いいえ  | はい  |
| 終了通知 (VM スケール セット)  | Yes  | はい  | 該当なし  |
| インスタンスの修復 (VM スケール セット)  | Yes  | はい  | 該当なし  |
| Accelerated Networking  | いいえ  | はい  | はい  |
| スポットのインスタンスと価格   | はい。スポットと通常、両方の優先度のインスタンスを使用できます  | はい。インスタンスは、すべてスポットとすべて通常のいずれかにする必要があります  | いいえ。通常の優先度のインスタンスのみ  |
| オペレーティング システムの混在  | はい。同一のフレキシブル スケール セット内で Linux と Windows が共存できます  | いいえ。インスタンスのオペレーティング システムは同一です  | はい。同一のフレキシブル スケール セット内で Linux と Windows が共存できます  |
| アプリケーション正常性の監視  | アプリケーション正常性拡張機能  | アプリケーション正常性拡張機能または Azure ロード バランサー プローブ  | アプリケーション正常性拡張機能  |
| Ultra SSD ディスク   | はい  | はい。ゾーン デプロイの場合のみ  | いいえ  |
| Infiniband   | いいえ  | はい。単一の配置グループのみ  | はい  |
| 書き込みアクセラレータ   | いいえ  | はい  | はい  |
| 近接配置グループ   | はい  | Yes  | はい  |
| Azure 専用ホスト   | いいえ  | はい  | はい  |
| Basic SLB   | いいえ  | はい  | はい  |
| Azure Load Balancer Standard SKU  | はい  | Yes  | はい  |
| Application Gateway  | はい  | Yes  | はい  |
| メンテナンス コントロール   | いいえ  | はい  | はい  |
| セット内の VM の一覧表示  | はい  | はい  | はい。AvSet 内の VM を一覧表示します  |
| Azure アラート  | いいえ  | はい  | はい  |
| VM Insights  | いいえ  | はい  | はい  |
| Azure Backup  | はい  | いいえ  | はい  |
| Azure Site Recovery  | はい (PowerShell を使用)  | いいえ  | はい  |
| Service Fabric  | いいえ  | はい  | いいえ  |
| Azure Kubernetes Service (AKS) / AKE  | いいえ  | はい  | いいえ  |


## <a name="get-started-with-flexible-orchestration-mode"></a>フレキシブル オーケストレーション モードの開始

登録し、仮想マシン スケール セットの[フレキシブル オーケストレーション モード](..\virtual-machines\flexible-virtual-machine-scale-sets.md)を開始します。 


## <a name="frequently-asked-questions"></a>よく寄せられる質問

- **フレキシブル オーケストレーションでは、どのくらいのスケーリングがサポートされますか?**

    フレキシブル オーケストレーション モードでは、最大 1,000 個の VM をスケール セットに追加できます。

- **可用性セットまたは均一オーケストレーションと比較すると、フレキシブル オーケストレーションの可用性はどのくらいですか?**

    | 可用性属性  | フレキシブル オーケストレーション  | 均一オーケストレーション  | 可用性セット  |
    |-|-|-|-|
    | 可用性ゾーンをまたいだデプロイ  | いいえ  | はい  | いいえ  |
    | リージョン内における障害ドメインの可用性の保証  | はい。最大 1,000 個のインスタンスをリージョン内の最大 3 つの障害ドメインに分散できます。 障害ドメインの最大数はリージョンによって異なります  | はい。最大 100 個のインスタンス  | はい。最大 200 個のインスタンス  |
    | 配置グループ  | フレキシブル モードでは常に複数の配置グループ (singlePlacementGroup = false) を使用します  | 単一の配置グループまたは複数の配置グループを選択できます | 該当なし  |
    | 更新ドメイン  | なし。メンテナンスまたはホストの更新は障害ドメインごとに行われます  | 最大 5 つの更新ドメイン  | 最大 20 個の更新ドメイン  |

- **障害ドメインの可用性が保証されるインスタンスの絶対最大数はいくつですか。**

    | 機能  | フレキシブル オーケストレーション (プレビュー) によるサポート対象  | 均一オーケストレーション (一般提供) によるサポート対象  | AvSets (一般提供) によるサポート対象  |
    |-|-|-|-|
    | 最大インスタンス数 (FD 可用性の保証あり)  | 1000  | 3000  | 200  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>フレキシブル オーケストレーションでのスケール セットのトラブルシューティング
ご自分のトラブルシューティング シナリオに適した解決策を見つけてください。

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**原因:** サブスクリプションがフレキシブル オーケストレーション モードのパブリック プレビューに登録されていません。

**解決策:** 上記の手順に従って、フレキシブル オーケストレーション モードのパブリック プレビューに登録します。

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**原因:** 選択したリージョンまたはゾーンで `platformFaultDomainCount` パラメーターが無効です。

**解決策:** 有効な `platformFaultDomainCount` 値を選択する必要があります。 ゾーン デプロイの場合、`platformFaultDomainCount` の最大値は 1 です。 ゾーンが指定されていないリージョン デプロイの場合、`platformFaultDomainCount` の最大値はリージョンによって異なります。 スクリプトでリージョンあたりの障害ドメインの最大数を判断するには、[VM の可用性の管理](../virtual-machines/availability.md)に関するページを参照してください。

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**原因:** 1 つ以上の仮想マシンが関連付けられているフレキシブル オーケストレーション モードのスケール セットを削除しようとしています。

**解決策:** フレキシブル オーケストレーション モードのスケール セットに関連付けられているすべての仮想マシンを削除すると、スケール セットを削除できます。

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**原因:** サブスクリプションがフレキシブル オーケストレーション モードのプレビューに登録されているものの、`singlePlacementGroup` パラメーターが *True* に設定されています。

**解決策:** `singlePlacementGroup` を *False* に設定する必要があります。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [スケール セットにアプリケーションをデプロイする方法を学習する。](virtual-machine-scale-sets-deploy-app.md)
