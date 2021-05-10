---
title: Azure Spot Virtual Machines を使用するスケール セットを作成する
description: コスト削減のために Azure Spot Virtual Machines を使用する Azure 仮想マシン スケール セットを作成する方法を説明します。
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 61bb87d84b96f988ae065a70b85d445fc8b96ccf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762949"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>仮想マシン スケール セット用の Azure Spot Virtual Machines 

スケール セットで Azure Spot Virtual Machines を使用すると、大幅にコストを削減して未使用の容量を利用できます。 Azure で容量の回復が必要になると、Azure インフラストラクチャによって Azure Spot Virtual Machine インスタンスが削除されます。 したがって、Azure Spot Virtual Machine インスタンスは、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断に対応できるワークロードに最適です。

利用可能な容量は、サイズ、リージョン、時刻などによって異なります。 スケール セットに Azure Spot Virtual Machine インスタンスをデプロイすると、利用可能な容量がある場合にのみ Azure によってインスタンスが割り当てられますが、このようなインスタンスには SLA がありません。 Azure スポット仮想マシン スケール セットは 1 つの障害ドメインにデプロイされ、高可用性の保証はありません。


## <a name="pricing"></a>価格

Azure Spot Virtual Machine インスタンスの価格は、リージョンと SKU に基づいて変動します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) および [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) での価格を参照してください。 


変動する価格に対して、小数点以下最大 5 桁を使用して、最大価格を米ドル (USD) で設定することができます。 たとえば、`0.98765` の値は、1 時間あたり $0.98765 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、インスタンスは価格に基づいて排除されません。 インスタンスの価格は、使用可能な容量とクォータがある限り、現在の Azure Spot Virtual Machine の価格または標準インスタンスの価格のいずれか低い方になります。


## <a name="limitations"></a>制限事項

次のサイズは、Azure Spot Virtual Machines ではサポートされていません。
 - B シリーズ
 - 任意のサイズのキャンペーン バージョン (Dv2、NV、NC、H キャンペーン サイズなど)

Azure Spot Virtual Machine は、Microsoft Azure China 21Vianet を除き、任意のリージョンにデプロイできます。

<a name="channel"></a>

現在サポートされている[オファーの種類](https://azure.microsoft.com/support/legal/offer-details/)は次のとおりです。

-   Enterprise Agreement
-   従量課金制プラン コード ( 003P)
-   スポンサー (0036P および 0136P)
- クラウド サービス プロバイダー (CSP) については、[パートナー センター](/partner-center/azure-plan-get-started)を参照するか、パートナーに直接お問い合わせください。

## <a name="eviction-policy"></a>削除ポリシー

Azure Spot Virtual Machines を使用してスケール セットを作成するときは､削除ポリシーを *[割り当て解除]* (既定値) または *[削除]* に設定できます。 

*[Deallocate]\(割り当て解除\)* ポリシーでは､排除されたインスタンスは [stopped-deallocated]\(停止 - 割り当て解除\) 状態に移行し､排除されたインスタンスはデプロイし直すことができます｡ ただし､割り当てが成功する保証はありません｡ 割り当てを解除された VM は、スケール セットのインスタンス クォータを基にカウントされ、構成しているディスクの料金が課金されます。 

それらが削除されたときにインスタンスを削除する場合は、削除ポリシーを *[削除]* に設定します。 排除ポリシーを削除に設定した場合、スケール セット インスタンスのカウント プロパティを増やすことで、新しい VM を作成できます。 排除された VM は基になっているディスクと共に削除されるので、ストレージが課金されることはありません。 また、スケール セットの自動スケーリング機能を使って、排除された VM の自動的な補正を試みることはできますが、割り当てが成功するという保証はありません。 ディスクのコストとクォータ制限への到達を回避するために、削除ポリシーを [削除] に設定しているときだけ、Azure スポット仮想マシン スケール セットで自動スケーリング機能を使うことをお勧めします。 

ユーザーは、[Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md) を通じて VM 内通知を受け取ることができます。 これにより、VM が排除されつつある場合には通知が送られ、排除される前にジョブを完了し、タスクのシャットダウンを実行するために 30 秒が与えられます。 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>試行と復元 (プレビュー)

この新しいプラットフォーム レベルの機能では、AI を使用して、ターゲットのインスタンス数を維持するために、スケール セット内の削除された Azure Spot Virtual Machine のインスタンスの自動的な復元を試行します。 

> [!IMPORTANT]
> 現在、試行と復元はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

試行と復元のメリット:
- 容量が原因で削除された Azure スポット仮想マシンの復元を試行します。
- 復元された Azure スポット仮想マシンは、容量が原因で削除がトリガーされる確率が低くなることで、より長い期間実行されることが期待されます。
- Azure スポット仮想マシンの有効期間が長くなることで、ワークロードの実行時間が長くなります。
- 従量課金制 VM 用に既に存在するターゲット数を維持する機能と同様に、Azure Spot Virtual Machines で Virtual Machine Scale Sets のターゲット数を維持するのを支援します。

試行と復元は、[自動スケーリング](virtual-machine-scale-sets-autoscale-overview.md)を使用するスケール セットでは無効になっています。 スケール セット内の VM の数は、自動スケーリング規則によって決まります。

### <a name="register-for-try--restore"></a>試行と復元のための登録

試行と復元機能を使用する前に、プレビューのサブスクリプションを登録する必要があります。 登録が完了するまでに数分かかる場合があります。 Azure CLI または PowerShell を使用して、機能の登録を完了することができます。


**CLI の使用**

[az feature register](/cli/azure/feature#az_feature_register) を使用して、サブスクリプションでのプレビューを有効にします。 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

サブスクリプションに対してこの機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**PowerShell の使用** 

[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) コマンドレットを使用して、サブスクリプションでのプレビューを有効にします。 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

サブスクリプションに対してこの機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>配置グループ

配置グループは、Azure 可用性セットに似た構造で、独自の障害ドメインとアップグレード ドメインが備わっています。 既定では、スケール セットは、最大サイズが 100 個の VM である 1 つの配置グループで構成されます。 `singlePlacementGroup` というスケール セット プロパティが *false* に設定されている場合、そのスケール セットは、複数の配置グループで構成することができ、0 ～ 1,000 個の VM が含まれます。 

> [!IMPORTANT]
> HPC で Infiniband を使用している場合を除き、リージョンまたはゾーン全体でのスケーリングを向上させるため、スケール セットのプロパティ `singlePlacementGroup` を *false* に設定して複数の配置グループを有効にすることを強くお勧めします。 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>スケール セットへの Azure Spot Virtual Machines のデプロイ

スケール セットに Azure Spot Virtual Machines をデプロイするには、新しい *Priority* フラグを *Spot* に設定します。 スケール セット内のすべての VM がスポットに設定されます。 Azure Spot Virtual Machines でスケール セットを作成するには、次のいずれかの方法を使います。
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure リソース マネージャーのテンプレート](#resource-manager-templates)

## <a name="portal"></a>ポータル

Azure Spot Virtual Machines を使用するスケール セットを作成するプロセスは、[使用の開始に関する記事](quick-create-portal.md)で詳しく説明されているものと同じです。 スケール セットをデプロイするときに､Spot フラグと排除ポリシーを設定することができます｡![Azure Spot Virtual Machines を使用するスケール セットを作成する](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Azure Spot Virtual Machines を使用するスケール セットを作成するプロセスは、[使用の開始に関する記事](quick-create-cli.md)で詳しく説明されているものと同じです。 "--Priority Spot" を追加し、`--max-price` を追加するだけです。 この例では、`-1` を `--max-price` に使用して、インスタンスが価格に基づいて削除されないようにします。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Azure Spot Virtual Machines を使用するスケール セットを作成するプロセスは、[使用の開始に関する記事](quick-create-powershell.md)で詳しく説明されているものと同じです。
"-Priority Spot" を追加し、[New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) に `-max-price` を指定するだけです。

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1
```

## <a name="resource-manager-templates"></a>Resource Manager テンプレート

Azure Spot Virtual Machines を使用するスケール セットを作成するプロセスは、[Linux](quick-create-template-linux.md) または [Windows](quick-create-template-windows.md) での使用の開始に関する記事で詳しく説明されているものと同じです。 

Azure Spot Virtual Machine テンプレートのデプロイの場合は、`"apiVersion": "2019-03-01"` 以降を使用してください。 

テンプレートで `"virtualMachineProfile":` セクションに `priority`、`evictionPolicy`、`billingProfile` の各プロパティ、および `"Microsoft.Compute/virtualMachineScaleSets"` セクションに `"singlePlacementGroup": false,` を追加します。

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

無効にしたインスタンスを削除するには、`evictionPolicy` パラメーターを `Delete` に変更します。


## <a name="simulate-an-eviction"></a>削除をシミュレートする

Azure スポット仮想マシンの[削除をシミュレート](/rest/api/compute/virtualmachines/simulateeviction)して、突然の削除に対してアプリケーションがどの程度適切に対応するかをテストすることができます。 

次の情報をお客様の情報に置き換えてください。 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` は、シミュレートされた削除が成功したことを意味します。 

## <a name="faq"></a>よく寄せられる質問

**Q:** 作成後、Azure Spot Virtual Machine インスタンスは標準のインスタンスと同じになりますか?

**A:** はい。ただし、Azure Spot Virtual Machines に対する SLA はなく、いつでも排除される可能性があります。


**質問:** 排除された後も容量が必要な場合はどうすればよいですか?

**A:** 容量がすぐに必要な場合は、Azure Spot Virtual Machines ではなく、標準の VM を使用することをお勧めします。


**Q:** Azure Spot Virtual Machine のクォータはどのように管理されますか?

**A:** Azure Spot Virtual Machine インスタンスと標準のインスタンスは、別々のクォータ プールを持ちます。 Azure Spot Virtual Machine クォータは、VM とスケール セット インスタンスの間で共有されます。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。


**Q:** Azure Spot Virtual Machine に追加のクォータを要求することはできますか?

**A:** はい。[標準のクォータ要求プロセス](../azure-portal/supportability/per-vm-quota-requests.md)を通じて、Azure Spot Virtual Machines のクォータを増やす要求を送信することができます。


**Q:** 既存のスケール セットを Azure スポット仮想マシン スケール セットに変換できますか?

**A:** できません｡`Spot` フラグの設定は、作成時にのみ行うことができます｡


**質問:** 優先順位の低いスケール セットに `low` を使用していた場合、代わりに `Spot` を使用する必要がありますか。

**A:** 現時点では `low` と `Spot` の両方が機能しますが、`Spot` の使用に移行し始める必要があります。


**Q:** 通常の VM と Azure Spot Virtual Machines の両方を使用してスケール セットを作成できますか?

**A:** できません｡スケール セットが複数の異なる優先順位をサポートすることはできません｡


**Q:** Azure スポット仮想マシン スケール セットで自動スケーリングを使用できますか?

**A:** はい､できます｡お使いの Azure スポット仮想マシン スケール セットに自動スケーリング規則を設定できます｡ VM が削除された場合､自動スケーリングで新しい Azure Spot Virtual Machines の作成を試みることができます｡ ただし､この容量が保証されるわけではないことを忘れないでください｡ 


**質問:** 自動スケールは両方の排除ポリシー (割り当て解除と削除) で機能しますか ?

**A:** はい。ただし、自動スケールを使用する場合は排除ポリシーを削除に設定することをお勧めします｡ これは､割り当てを解除されたインスタンスが､スケール セットに対する容量数を基にカウントされるためです｡ 自動スケールを使用すると､インスタンスが割り当て解除されて排除されるため､短時間に目標インスタンス数に達する可能性があります｡ また、スケーリング操作はスポット削除の影響を受けることがあります。 たとえば、仮想マシン スケール セット インスタンスは、スケーリング操作中の複数のスポット削除に起因し、設定されている最小カウントを下回ることがあります。 


**質問:** どこで質問を投稿できますか。

**A:** [Q&A](/answers/topics/azure-spot.html) で質問を投稿し、`azure-spot` のタグを付けることができます。 

## <a name="next-steps"></a>次のステップ

価格について詳しくは、[仮想マシン スケール セットの価格のページ](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)をご覧ください。
