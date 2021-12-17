---
title: Azure 専用ホストをデプロイする
description: VM とスケール セットを専用ホストにデプロイします。
author: brittanyrowe
ms.author: brittanyrowe
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2021
ms.reviewer: brittanyrowe
ms.openlocfilehash: 3bae87c87c7ab8dd1090f40b4e0589c84e2c54ec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557119"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts"></a>VM とスケール セットを専用ホストにデプロイする

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: ユニフォーム スケール セット

この記事では、仮想マシン (VM) とスケール セット インスタンスをホストするための Azure [専用ホスト](dedicated-hosts.md)を作成する方法について説明します。


## <a name="limitations"></a>制限事項

- 専用ホストで使用できるサイズとハードウェアの種類は、リージョンによって異なります。 詳しくは、ホストの[価格のページ](https://aka.ms/ADHPricing) を参照してください。

## <a name="create-a-host-group"></a>ホスト グループを作成する

**ホスト グループ** は、専用ホストのコレクションを表すリソースです。 リージョンと可用性ゾーンにホスト グループを作成し、それにホストを追加します。 高可用性を計画する場合は、追加のオプションがあります。 専用ホストでは、次のいずれかまたは両方のオプションを使用できます。
- 複数の可用性ゾーンにまたがります。 この場合は、使用する各ゾーンにホスト グループを用意する必要があります。
- 物理ラックにマップされる複数の障害ドメインにまたがります。

どちらの場合も、ホスト グループに対して障害ドメイン数を指定する必要があります。 グループ内で障害ドメインをまたがりたくない場合は、障害ドメインの数を 1 にします。

可用性ゾーンと障害ドメインの両方を使用することもできます。

### <a name="portal"></a>[ポータル](#tab/portal)

この例では、1 つの可用性ゾーンと 2 つの障害ドメインを使用してホスト グループを作成します。

1. Azure [portal](https://portal.azure.com) を開きます。
1. 左上隅にある **[リソースの作成]** を選択します。
1. **[ホストグループ]** を検索し、結果から **[ホスト グループ]** を選択します。
1. **[ホスト グループ]** ページで、**[作成]** を選択します。
1. 使用するサブスクリプションを選択し、**[新規作成]** を選択して新しいリソース グループを作成します。
1. **[名前]** として「*myDedicatedHostsRG*」と入力し、**[OK]** を選択します。
1. **[Host group name]\(ホスト グループ名\)** に「*myHostGroup*」と入力します。
1. **[場所]** で、 **[米国東部]** を選択します。
1. **[可用性ゾーン]** で、**[1]** を選択します。
1. **[Fault domain count]\(障害ドメインの数\)** で、**[2]** を選択します。
1. VM とスケール セット インスタンスをこのグループの使用可能なホストに自動的に割り当てるには、 **[Automatic placement]\(自動配置\)** を選択します。
1. **[確認および作成]** を選択して、検証が行われるのを待ちます。
1. "**検証に成功しました**" というメッセージが表示されたら、**[作成]** を選択してホスト グループを作成します。

ホスト グループの作成には少しだけ時間がかかります。


### <a name="cli"></a>[CLI](#tab/cli)

すべてのホスト SKU がすべてのリージョンおよび可用性ゾーンで使用できるわけではありません。 専用ホストのプロビジョニングを開始する前に、ホストの可用性とプランの制限事項を一覧表示できます。

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table
```

この例では、[az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) を使用し、可用性ゾーンと障害ドメインの両方を使用してホスト グループを作成します。

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2
```

`--automatic-placement true` パラメーターを追加すると、VM とスケール セット インスタンスがホスト グループ内のホストに自動的に配置されるようになります。 詳しくは、[手動による配置と自動配置](dedicated-hosts.md#manual-vs-automatic-placement)に関するページをご覧ください。


**その他の例**

[az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) を使用して、可用性ゾーン 1 (障害ドメインなし) にホスト グループを作成することもでいます。

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1
```

以下では、[az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) を使用し、障害ドメインのみを使用してホスト グループを作成します (可用性ゾーンがサポートされていないリージョンで使用する場合)。

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

この例では、[New-AzHostGroup](/powershell/module/az.compute/new-azhostgroup) を使用して、ゾーン 1 にホスト グループを 1 つと障害ドメインを 2 つ作成します。


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


`-SupportAutomaticPlacement true` パラメーターを追加すると、VM とスケール セット インスタンスがホスト グループ内のホストに自動的に配置されるようになります。 詳しくは、[手動による配置と自動配置](dedicated-hosts.md#manual-vs-automatic-placement)に関するページをご覧ください。

---


## <a name="create-a-dedicated-host"></a>専用ホストを作成する

次に、ホスト グループに専用ホストを作成します。 ホストの名前に加えて、ホストの SKU を指定する必要があります。 ホスト SKU では、専用ホストに対してサポートされている VM シリーズとハードウェアの世代がキャプチャされます。

ホスト SKU の詳細と価格については、「[Azure 専用ホストの価格](https://aka.ms/ADHPricing)」を参照してください。

ホスト グループの障害ドメイン数を設定した場合は、ホストの障害ドメインを指定するように求められます。

### <a name="portal"></a>[ポータル](#tab/portal)

1. 左上隅にある **[リソースの作成]** を選択します。
1. "**専用ホスト**" を検索し、結果から **[Dedicated hosts]\(専用ホスト\)** を選択します。
1. **[Dedicated hosts]\(専用ホスト\)** ページで、**[作成]** を選択します。
1. 使用するサブスクリプションを選択します。
1. *[リソース グループ]* として **myDedicatedHostsRG** を選択します。
1. **[インスタンスの詳細]** で、**[名前]** に「*myHost*」と入力し、場所として *[米国東部]* を選択します。
1. **[ハードウェア プロファイル]** で、 **[サイズ ファミリ]** として *[Standard Es3 family - Type 1]\(Standard Es3 ファミリ - Type 1\)* を選択し、 **[ホスト グループ]** として *myHostGrup* を選択し、 **[障害ドメイン]** として *[1]* を選択します。 他のフィールドについては既定値を使用します。
1. 終わったら、**[確認および作成]** を選択して、検証が行われるのを待ちます。
1. "**検証に成功しました**" というメッセージが表示されたら、**[作成]** を選択してホストを作成します。

### <a name="cli"></a>[CLI](#tab/cli)

[az vm host create](/cli/azure/vm/host#az_vm_host_create) を使用してホストを作成します。 ホスト グループの障害ドメイン数を設定した場合は、ホストの障害ドメインを指定するように求められます。

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

この例では、[New-AzHost](/powershell/module/az.compute/new-azhost) を使用してホストを作成し、障害ドメインを 1 に設定します。


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

---

## <a name="create-a-vm"></a>VM を作成する

次に、ホスト上に VM を作成します。

### <a name="portal"></a>[ポータル](#tab/portal)

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。
1. Azure Marketplace リソースの一覧の上にある検索ボックスで、 使用するイメージを検索して選択し、 **[作成]** を選択します。
1. **[基本]** タブの **[プロジェクトの詳細]** で、正しいサブスクリプションが選択されていることを確認し、 **[リソース グループ]** として *myDedicatedHostsRG* を選択します。
1. **[Instance details] (インスタンスの詳細)** で、 **[仮想マシン名]** として「*myVM*」と入力し、 **[場所]** として *[米国東部]* を選択します。
1. **[可用性オプション]** で **[可用性ゾーン]** を選択し、ドロップダウンから *[1]* を選択します。
1. サイズでは、 **[サイズの変更]** を選択します。 使用可能なサイズの一覧で、Esv3 シリーズの 1 つを選択します (**Standard E2s v3** など)。 使用可能なサイズをすべて表示するには、フィルターをクリアすることが必要な場合があります。
1. 必要に応じて、 **[基本]** タブの残りのフィールドを入力します。
1. VM に使用するホストを指定する場合は、ページの上部にある **[詳細設定]** タブを選択し、 **[ホスト]** セクションで、 **[ホスト グループ]** には *myHostGroup* を、 **[ホスト]** には *myHost* を選択します。 そうしない場合、VM は容量のあるホストに自動的に配置されます。
    ![ホスト グループとホストを選択する](./media/dedicated-hosts-portal/advanced.png)
1. 残りの既定値はそのままにして、ページの一番下にある **[Review + create] (確認および作成)** ボタンを選択します。
1. 検証が成功したことを示すメッセージが表示されたら、 **[作成]** を選択します。

VM がデプロイされるまでに数分かかります。


### <a name="cli"></a>[CLI](#tab/cli)

[az vm create](/cli/azure/vm#az_vm_create) を使用して、専用ホスト内に仮想マシンを作成します。 ホスト グループを作成するときに可用性ゾーンを指定した場合は、仮想マシンを作成するときに同じゾーンを使用する必要があります。 イメージやホスト名などの値は、実際の値に置き換えてください。 Windows VM を作成している場合は、`--generate-ssh-keys` を削除してパスワードの入力を求められるようにします。

```azurecli-interactive
az vm create \
   -n myVM \
   --image myImage \
   --host-group myHostGroup \
   --admin-username azureuser \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

特定のホストに VM を配置するには、`--host-group` でホスト グループを指定するのではなく、`--host` を使用します。

> [!WARNING]
> 十分なリソースがないホストに仮想マシンを作成すると、仮想マシンは FAILED 状態で作成されます。


### <a name="powershell"></a>[PowerShell](#tab/powershell)

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用してホストに新しい VM を作成します。この例では、ホスト グループがゾーン 1 にあるため、ゾーン 1 に VM を作成する必要があります。


```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image myImage `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> 十分なリソースがないホストに仮想マシンを作成すると、仮想マシンは FAILED 状態で作成されます。

---

## <a name="create-a-scale-set"></a>スケール セットを作成する

ホストにスケール セットを作成することもできます。

### <a name="portal"></a>[ポータル](#tab/portal)

スケール セットをデプロイするときは、ホスト グループを指定します。

1. "*スケール セット*" を検索し、一覧から **[仮想マシン スケール セット]** を選択します。
1. **[追加]** を選択して、新しいスケール セットを作成します。
1. 通常の場合と同様に、 **[基本]** タブのフィールドを入力します。ただし、専用ホスト用に選択したシリーズの VM サイズを選択してください (**Standard E2s v3** など)。
1. **[詳細設定]** タブで、 **[拡散アルゴリズム]** に **[最大拡散]** を選択します。
1. **[ホスト グループ]** で、ドロップダウンからホスト グループを選択します。 グループを最近作成した場合は、リストに追加されるまでに 1 分ほどかかることがあります。


### <a name="cli"></a>[CLI](#tab/cli)

[az vmss create](/cli/azure/vmss#az_vmss_create) を使用してスケール セットをデプロイするときは、`--host-group` を使用してホスト グループを指定します。 この例では、最新の Ubuntu LTS イメージをデプロイします。 Windows イメージをデプロイする場合は、`--image` の値を置き換え、`--generate-ssh-keys` を削除して、パスワードの入力を求められるようにします。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

スケール セットをデプロイするホストを手動で選択する場合は、`--host` とそのホストの名前を追加します。


### <a name="powershell"></a>[PowerShell](#tab/powershell)

[New-AzVMSS](/powershell/module/az.compute/new-azvmss) を使用してスケールセットをホストにデプロイします。 スケール セットをデプロイするときは、ホスト グループを指定します。

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

スケール セットをデプロイするホストを手動で選択する場合は、`--host` とそのホストの名前を追加します。

---

## <a name="add-an-existing-vm"></a>既存の VM を追加する

既存の VM を専用のホストに追加することはできますが、最初に VM を Stop\Deallocated とする必要があります。 VM を専用のホストに移動する前に、その VM 構成がサポートされていることを確認してください。

- VM サイズは、専用ホストと同じサイズ ファミリである必要があります。 たとえば、専用のホストが DSv3 の場合、VM のサイズは Standard_D4s_v3 になる可能性がありますが、Standard_A4_v2 となる可能性はありません。
- VM は、専用ホストと同じリージョンに配置する必要があります。
- VM は、近接通信配置グループの一部になることはありません。 専用のホストに移動する前に、近接通信配置グループから VM を削除してください。 詳細については、「[近接配置グループからの VM の移動](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)」を参照してください
- VM は、可用性セット内に置くことはできません。
- VM が可用性ゾーン内にある場合は、ホスト グループと同じ可用性ゾーンである必要があります。 VM とホスト グループの可用性ゾーンの設定が一致している必要があります。

### <a name="portal"></a>[ポータル](#tab/portal2)

[ポータル](https://portal.azure.com)を使用して、VM を専用ホストに移動します。

1. VM のページを開きます。
1. **[停止]** を選択して VM の停止\割り当ての解除を行います。
1. 左メニューから **[構成]** を選択します。
1. ドロップダウン メニューからホスト グループとホストを選択します。
1. 完了したら、ページの最上部で **[保存]** を選択します。
1. VM がホストに追加されたら、左メニューから **[概要]** を選択します。
1. ページの最上部で **[開始]** を選択して VM を再起動します。


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

変数の値を実際の情報に置き換えます。

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName

$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName

$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force

Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug

Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```

---


## <a name="check-the-status-of-the-host"></a>ホストの状態を確認する

空き容量を知る必要がある場合は、状態を確認します。

### <a name="portal"></a>[ポータル](#tab/portal)

1. ホストを検索して選択します。
1. ホストの **[概要]** ページを下へスクロールすると、そのホストで利用できるサイズの一覧が表示されます。 以下に示したのは実際の表示例です。

:::image type="content" source="media/dedicated-hosts-portal/host-status.png" alt-text="ホストの概要ページからホストの空き容量を確認する。":::

### <a name="cli"></a>[CLI](#tab/cli)

[az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view) を使用して、ホストの正常性状態と、ホストにデプロイできる仮想マシンの数を確認できます。

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```

出力は次のようになります。

```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


[Get-AzHost](/powershell/module/az.compute/get-azhost) と `-InstanceView` パラメーターを使用して、ホストの正常性状態と、ホストにデプロイできる仮想マシンの数を確認できます。

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

出力は次のようになります。

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           :
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    :
    AllocatableVMs[0]  :
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  :
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  :
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  :
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  :
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  :
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  :
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  :
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  :
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  :
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          :
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          :
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    :
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

---

## <a name="deleting-hosts"></a>ホストを削除する 
仮想マシンがデプロイされていない場合でも、専用ホストに対して課金されます。 コストを節約するには、現在使用していないすべてのホストを削除する必要があります。

ホストを削除できるのは、それを使用している仮想マシンがなくなった場合のみです。

### <a name="portal"></a>[ポータル](#tab/portal)

1. ホストを検索して選択します。
1. 左側のメニューで **[インスタンス]** を選択します。
1. 各仮想マシンを選択して削除します。
1. すべての VM を削除したら、ホストの **[概要]** ページに戻って、トップ メニューから **[削除]** を選択します。
1. ホストが削除されたら、ホスト グループのページを開き、 **[ホスト グループの削除]** を選択します。

### <a name="cli"></a>[CLI](#tab/cli)

 [az vm delete](/cli/azure/vm#az_vm_delete) を使用して VM を削除します。

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

VM を削除した後、[az vm host delete](/cli/azure/vm/host#az_vm_host_delete) を使用してホストを削除できます。

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost
```

すべてのホストを削除したら、[az vm host group delete](/cli/azure/vm/host/group#az_vm_host_group_delete) を使用してホスト グループを削除できます。

```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup
```

また、1 つのコマンドで、リソース グループ全体を削除することもできます。 これにより、すべての VM、ホスト、ホスト グループを含めて、グループ内に作成されたすべてのリソースが削除されます。

```azurecli-interactive
az group delete -n myDHResourceGroup
```


### <a name="powershell"></a>[PowerShell](#tab/powershell)

[Remove-AzVM](/powershell/module/az.compute/remove-azvm) を使用して VM を削除します。

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

VM を削除した後、[Remove-AzHost](/powershell/module/az.compute/remove-azhost) を使用してホストを削除できます。

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

すべてのホストを削除したら、[Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup) を使用してホスト グループを削除できます。

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

また、1 つのコマンド [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループ全体を削除することもできます。 これにより、すべての VM、ホスト、ホスト グループを含めて、グループ内に作成されたすべてのリソースが削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```

---

## <a name="next-steps"></a>次のステップ

- 詳細については、[専用ホスト](dedicated-hosts.md)の概要に関するページを参照してください。

- リージョン内の回復性を最大にするためにゾーンと障害ドメインの両方を使用する、[Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)で使用できるサンプル テンプレートがあります。

