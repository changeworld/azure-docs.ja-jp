---
title: Azure ディスク プール (プレビュー) をデプロイする
description: Azure ディスク プールのデプロイ方法を説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: c4c010d1c142a2f2e09c0a60122f446ab7df6530
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135369"
---
# <a name="deploy-an-azure-disk-pool-preview"></a>Azure ディスク プール (プレビュー) をデプロイする

この記事では、Azure ディスク プール (プレビュー) をデプロイおよび構成する方法について説明します。 ディスク プールをデプロイする前に、[概念](disks-pools.md)および[計画](disks-pools-planning.md)に関する記事をお読みください。

ディスク プールを正常に動作させるには、次の手順を完了する必要があります。
- プレビュー版を利用するにはサブスクリプションを登録します。
- ディスク プールにサブネットを委任します。
- ディスク リソースを管理するため、ディスク プールのロールベースのアクセス制御 (RBAC) アクセス許可のリソース プロバイダーを割り当てます。
- ディスク プールを作成します。
    - ディスクをディスク プールに追加します。


## <a name="prerequisites"></a>前提条件

ディスク プールを正常にデプロイするには、次が必要です。

- ディスク プールに追加するマネージド ディスクのセット。
- ディスク プール用にデプロイされた専用サブネットを使用する仮想ネットワーク。
    - 送信ポート 53、443、5671 が開いている必要があります。
    - ディスク プールに必要な送信依存関係がネットワーク設定によってブロックされていないことを確認します。 [Azure PowerShell モジュール](/powershell/module/az.diskpool/get-azdiskpooloutboundnetworkdependencyendpoint?view=azps-6.6.0)または [Azure CLI](/cli/azure/disk-pool?view=azure-cli-latest#az_disk_pool_list_outbound_network_dependency_endpoint) のいずれかを使用して、すべての送信依存関係の完全な一覧を取得できます。

Azure PowerShell モジュールを使用する場合は、[バージョン 6.1.0 以降](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true)をインストールしてください。

Azure CLI を使用する場合は、[最新バージョン](/cli/azure/disk-pool)をインストールしてください。

## <a name="register-your-subscription-for-the-preview"></a>プレビュー版を利用するためにサブスクリプションを登録する

サブスクリプションを **Microsoft.StoragePool** プロバイダーに登録し、ディスク プールを作成して使用できるようにします。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. Azure portal メニューで、**サブスクリプション** を検索して選択します。
1. ディスク プールで使用するサブスクリプションを選択します。
1. 左側のメニューの **[設定]** で、 **[リソース プロバイダー]** を選択します。
1. リソース プロバイダー **Microsoft.StoragePool** を探し、 **[登録]** を選択します。

サブスクリプションが登録されると、ディスク プールをデプロイできるようになります。

## <a name="delegate-subnet-permission"></a>サブネット アクセス許可を委任する

ディスク プールをクライアント マシンで使用するには、サブネットを Azure ディスク プールに委任する必要があります。 ディスク プールを作成するときは、仮想ネットワークと委任されたサブネットを指定します。 新しいサブネットを作成するか、既存のサブネットを使用して **Microsoft.StoragePool/diskPools** リソース プロバイダーに委任することができます。

1. Azure portal の [仮想ネットワーク] ペインにアクセスし、ディスク プールで使用する仮想ネットワークを選択します。
1. [仮想ネットワーク] ペインから **[サブネット]** 、 **[+ サブネット]** の順に選択します。
1. **[サブネットの追加]** ペインで次の必須フィールドを完了して、新しいサブネットを作成します。      - サブネットの委任: [Microsoft.StoragePool] を選択します

サブネットの委任の詳細については、「[サブネットの委任を追加または削除する](../virtual-network/manage-subnet-delegation.md)」をご覧ください。

## <a name="assign-storagepool-resource-provider-permissions"></a>StoragePool リソース プロバイダーのアクセス許可を割り当てる

ディスクをディスク プールで使用できるようにするには、次の要件を満たしている必要があります。

- **StoragePool** リソース プロバイダーは、ディスク プール内のすべてのマネージド ディスクに対して **読み取り** アクセス許可と **書き込み** アクセス許可を含む RBAC ロールを割り当てる必要があります。
- ディスク プールと同じ可用性ゾーン内の Premium SSD、Standard SSD、または Ultra Disk のいずれかである必要があります。
    - Ultra Disk の場合、ディスク セクターのサイズは 512 バイトである必要があります。
- Premium または Standard SSD と Ultra Disk の両方を含むディスク プールを構成することはできません。 Ultra Disk 用に構成されたディスク プールは、Ultra Disk のみを格納できます。 同様に、Premium または Standard の SSD 用に構成されたディスク プールは、Premium および Standard の SSD のみを格納できます。
- maxShares の値が 2 以上の共有ディスクである必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ディスクを含むリソース グループまたは各ディスクを検索して選択します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当ての追加 (プレビュー)]** を選択し、ロールの一覧で **[Disk Pool Operator]\(ディスク プール オペレーター\)** を選択します。

    代わりに、独自のカスタム ロールを作成することもできます。 ディスク プールのカスタム ロールを機能させるには、**Microsoft.Compute/disks/write** と **Microsoft.Compute/disks/read** の RBAC アクセス許可が必要です。

1. **[アクセスの割り当て先]** で、 **[User, group, or service principle]\(ユーザー、グループ、またはサービス プリンシパル\)** を選択します。
1. **[+ メンバーの選択]** を選択してから、**StoragePool リソース プロバイダー** を探して選択し、保存します。

## <a name="create-a-disk-pool"></a>ディスク プールを作成する

最適なパフォーマンスを得るには、クライアントと同じ可用性ゾーンにディスク プールをデプロイします。 Azure VMware Solution クラウド用のディスク プールをデプロイする場合、可用性ゾーンを識別するためのガイダンスが必要な場合は、この[フォーム](https://aka.ms/DiskPoolCollocate)に入力してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. **[ディスク プール]** を探して選択します。
1. **[+ 追加]** を選択して、新しいディスク プールを作成します。
1. 要求された詳細を入力し、ディスク プールを使用するクライアントと同じリージョンと可用性ゾーンを選択します。
1. **StoragePool** リソース プロバイダーに委任されたサブネットとそれに関連付けられた仮想ネットワークを選択します。
1. **[次へ]** を選択して、ディスク プールにディスクを追加します。

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool.png" alt-text="ディスク プールを作成するための [基本] ペインのスクリーンショット。":::

### <a name="add-disks"></a>ディスクを追加します

#### <a name="prerequisites"></a>前提条件

ディスクを追加するには、次の要件を満たしている必要があります。

- ディスク プールと同じ可用性ゾーン内の Premium SSD、Standard SSD、または Ultra Disk のいずれかである必要があります。
    - 現在、ポータルで追加できるのは Premium SSD と Standard SSD のみです。 Azure PowerShell モジュールまたは Azure CLI のいずれかで Ultra Disk を追加する必要があります。
    - Ultra Disk の場合、ディスク セクターのサイズは 512 バイトである必要があります。
- maxShares の値が 2 以上の共有ディスクである必要があります。
- Premium または Standard SSD と Ultra Disk の両方を含むディスク プールを構成することはできません。 Ultra Disk 用に構成されたディスク プールは、Ultra Disk のみを格納できます。 同様に、Premium または Standard の SSD 用に構成されたディスク プールは、Premium および Standard の SSD のみを格納できます。
- 追加するディスクを管理するために、ディスク プールのリソース プロバイダーに RBAC アクセス許可を付与する必要があります。

ディスクがこれらの要件を満たす場合、ディスク プールに追加するには、[ディスク プール] ペインで **[+ ディスクの追加]** を選択します。

:::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-disks-blade.png" alt-text="ディスク プールを作成するための [ディスク] ウィンドウのスクリーンショット。強調表示されたディスクを追加します。":::

### <a name="enable-iscsi"></a>ISCSI を有効にする

1. **[iSCSI]** ペインを選択します。
1. **[iSCSI を有効にする]** を選択します。
1. ISCSI ターゲットの名前を入力します。この名前に基づいて、iSCSI ターゲット IQN が生成されます。
    - ACL モードは既定で **[動的]** に設定されます。 ディスク プールを Azure VMware Solution のストレージ ソリューションとして使用するには、ACL モードを **[動的]** に設定する必要があります。
1. **[確認と作成]** を選択します。

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-iscsi-blade.png" alt-text="ディスク プールを作成するための [iSCSI] ペインのスクリーンショット。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

指定されたスクリプトでは、次が実行されます。
- ディスク プールを作成して使用するために必要なモジュールをインストールします。
- ディスクを作成し、これに RBAC アクセス許可を割り当てます。 既にこれを行っている場合は、スクリプトのこれらのセクションをコメントアウトすることができます。
- ディスク プールを作成し、これにディスクを追加します。
- iSCSI ターゲットを作成して有効にします。

スクリプトを実行する前に、このスクリプトの変数を独自の変数に置き換えます。 また、Ultra Disk フォームに入力した場合は、既存の Ultra Disk を使用するように変更する必要もあります。

```azurepowershell
# Install the required module for Disk Pool
Install-Module -Name Az.DiskPool -RequiredVersion 0.3.0 -Repository PSGallery

# Sign in to the Azure account and setup the variables
$subscriptionID = "<yourSubID>"
Set-AzContext -Subscription $subscriptionID
$resourceGroupName= "<yourResourceGroupName>"
$location = "<desiredRegion>"
$diskName = "<desiredDiskName>"
$availabilityZone = "<desiredAvailabilityZone>"
$subnetId='<yourSubnetID>'
$diskPoolName = "<desiredDiskPoolName>"
$iscsiTargetName = "<desirediSCSITargetName>" # This will be used to generate the iSCSI target IQN name
$lunName = "<desiredLunName>"

# You can skip this step if you have already created the disk and assigned proper RBAC permission to the resource group the disk is deployed to
$diskconfig = New-AzDiskConfig -Location $location -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -zone $availabilityZone -MaxSharesCount 2
$disk = New-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -Disk $diskconfig
$diskId = $disk.Id
$scopeDef = "/subscriptions/" + $subscriptionId + "/resourceGroups/" + $resourceGroupName
$rpId = (Get-AzADServicePrincipal -SearchString "StoragePool Resource Provider").id

New-AzRoleAssignment -ObjectId $rpId -RoleDefinitionName "Virtual Machine Contributor" -Scope $scopeDef

# Create a Disk Pool
# If you want to create a disk pool configured for ultra disks, add -AdditionalCapability "DiskPool.Disk.Sku.UltraSSD_LRS" to the command
New-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $subnetId -AvailabilityZone $availabilityZone -SkuName Standard_S1
$diskpool = Get-AzDiskPool -ResourceGroupName $resourceGroupName -Name $DiskPoolName

# Add disks to the Disk Pool
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskId
$lun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $diskId -Name $lunName

# Create an iSCSI Target and expose the disks as iSCSI LUNs
New-AzDiskPoolIscsiTarget -DiskPoolName $diskPoolName -Name $iscsiTargetName -ResourceGroupName $resourceGroupName -Lun $lun -AclMode Dynamic

Write-Output "Print details of the iSCSI target exposed on Disk Pool"

Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName | fl
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

指定されたスクリプトでは、次が実行されます。
- ディスク プールを作成して使用するために必要な拡張機能をインストールします。
- ディスクを作成し、これに RBAC アクセス許可を割り当てます。 既にこれを行っている場合は、スクリプトのこれらのセクションをコメントアウトすることができます。
- ディスク プールを作成し、これにディスクを追加します。
- iSCSI ターゲットを作成して有効にします。

スクリプトを実行する前に、このスクリプトの変数を独自の変数に置き換えます。 また、Ultra Disk フォームに入力した場合は、既存の Ultra Disk を使用するように変更する必要もあります。

```azurecli
# Add disk pool CLI extension
az extension add -n diskpool

#az extension add -s https://azcliprod.blob.core.windows.net/cli-extensions/diskpool-0.2.0-py3-none-any.whl

#Select subscription
az account set --subscription "<yourSubscription>"

##Initialize input parameters
resourceGroupName='<yourRGName>'
location='<desiredRegion>'
zone=<desiredZone>
subnetId='<yourSubnetID>'
diskName='<desiredDiskName>'
diskPoolName='<desiredDiskPoolName>'
targetName='<desirediSCSITargetName>'
lunName='<desiredLunName>'

#You can skip this step if you have already created the disk and assigned permission in the prerequisite step. Below is an example for premium disks.
az disk create --name $diskName --resource-group $resourceGroupName --zone $zone --location $location --sku Premium_LRS --max-shares 2 --size-gb 1024

#You can deploy all your disks into one resource group and assign StoragePool Resource Provider permission to the group
storagePoolObjectId=$(az ad sp list --filter "displayName eq 'StoragePool Resource Provider'" --query "[0].objectId" -o json)
storagePoolObjectId="${storagePoolObjectId%"}"
storagePoolObjectId="${storagePoolObjectId#"}"

az role assignment create --assignee-object-id $storagePoolObjectId --role "Virtual Machine Contributor" --resource-group $resourceGroupName

#Create a disk pool
#To create a disk pool configured for ultra disks, add --additional-capabilities "DiskPool.Disk.Sku.UltraSSD_LRS" to your command
az disk-pool create --name $diskPoolName \
--resource-group $resourceGroupName \
--location $location \
--availability-zones $zone \
--subnet-id $subnetId \
--sku name="Standard_S1" \

#Initialize an iSCSI target. You can have 1 iSCSI target per disk pool
az disk-pool iscsi-target create --name $targetName \
--disk-pool-name $diskPoolName \
--resource-group $resourceGroupName \
--acl-mode Dynamic

#Add the disk to disk pool
diskId=$(az disk show --name $diskName --resource-group $resourceGroupName --query "id" -o json)
diskId="${diskId%"}"
diskId="${diskId#"}"

az disk-pool update --name $diskPoolName --resource-group $resourceGroupName --disks $diskId

#Expose disks added in the Disk Pool as iSCSI Lun 
az disk-pool iscsi-target update --name $targetName \
 --disk-pool-name $diskPoolName \
 --resource-group $resourceGroupName \
 --luns name=$lunName managed-disk-azure-resource-id=$diskId
```
---

## <a name="next-steps"></a>次のステップ

- ディスク プールのデプロイに関する問題が発生した場合は、「[Azure ディスク プール (プレビュー) のトラブルシューティング](disks-pools-troubleshoot.md)」をご覧ください。
- [ディスク プールを Azure VMware Solution ホストにアタッチする (プレビュー)](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md)。
- [ディスク プールを管理する](disks-pools-manage.md)。
