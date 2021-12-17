---
title: クイック スタート:Azure NetApp Files と NFS ボリュームを設定する
description: クイック スタート - 簡単に Azure NetApp Files を設定し、ボリュームを作成する方法について説明します。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 10/04/2021
ms.custom: devx-track-azurecli, subject-armqs
ms.openlocfilehash: bd8821c898c4fa94963bb240658aa8118a104905
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407895"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>クイック スタート:Azure NetApp Files を設定し、NFS ボリュームを作成する

この記事では、簡単に Azure NetApp Files を設定し、NFS ボリュームを作成する方法について説明します。 

このクイック スタートでは、次の項目を設定します。

- NetApp リソース プロバイダーの登録
- NetApp アカウント
- 容量プール
- Azure NetApp Files の NFS ボリューム

Azure サブスクリプションをお持ちでない場合は、開始する前に <bpt id="p1">[</bpt>無料アカウント<ept id="p1">](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)</ept> を作成してください。

NFS ボリュームで有効にできるすべての機能と関連する考慮事項については、<bpt id="p1">[</bpt>NFS ボリュームの作成<ept id="p1">](azure-netapp-files-create-volumes.md)</ept>に関する記事を参照してください。 

## <a name="register-for-netapp-resource-provider"></a>NetApp リソース プロバイダーを登録する

> [!NOTE]
> 登録プロセスは、完了するまでに時間がかかることがあります。
>

# <a name="portal"></a><bpt id="p1">[</bpt>ポータル<ept id="p1">](#tab/azure-portal)</ept>

ポータルを使用して登録手順を行う場合は、前述のように Cloud Shell セッションを開き、次の Azure CLI の手順を実行します。

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a><bpt id="p1">[</bpt>PowerShell<ept id="p1">](#tab/azure-powershell)</ept>

このハウツー記事には、Azure PowerShell モジュール Az バージョン 2.6.0 以降が必要です。 現在のバージョンを調べるには、<ph id="ph1">`Get-Module -ListAvailable Az`</ph> を実行します。 インストールまたはアップグレードする必要がある場合は、<bpt id="p1">[</bpt>Azure PowerShell モジュールのインストール<ept id="p1">](/powershell/azure/install-Az-ps)</ept>に関するページを参照してください。 必要に応じて、PowerShell セッションで Cloud Shell コンソールを使用することもできます。

1. PowerShell コマンド プロンプト (または PowerShell Cloud Shell セッション) で、Azure NetApp Files に対して承認されているサブスクリプションを指定します。
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Azure リソース プロバイダーを登録します。
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a><bpt id="p1">[</bpt>Azure CLI<ept id="p1">](#tab/azure-cli)</ept>

Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="template"></a><bpt id="p1">[</bpt>テンプレート<ept id="p1">](#tab/template)</ept>

[なし] :

Azure portal、PowerShell、または Azure CLI を使用して [NetApp リソース プロバイダーに登録](azure-netapp-files-register.md)します。

---

## <a name="create-a-netapp-account"></a>NetApp アカウントを作成する

# <a name="portal"></a><bpt id="p1">[</bpt>ポータル<ept id="p1">](#tab/azure-portal)</ept>

1. Azure portal の [検索] ボックスに「<bpt id="p1">**</bpt>Azure NetApp Files<ept id="p1">**</ept>」と入力し、表示された一覧から <bpt id="p2">**</bpt>[Azure NetApp Files]<ept id="p2">**</ept> を選択します。

      ![Azure NetApp Files を選択する](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. <bpt id="p1">**</bpt>[+ 作成]<ept id="p1">**</ept> をクリックし、新しい NetApp アカウントを作成します。

3. [新しい NetApp アカウント] ウィンドウで、次の情報を指定します。
   1. アカウント名に「<bpt id="p1">**</bpt>myaccount1<ept id="p1">**</ept>」と入力します。
   2. サブスクリプションを選択します。
   3. <bpt id="p1">**</bpt>[新規作成]<ept id="p1">**</ept> を選択し、新しいリソース グループを作成します。 リソース グループ名に「<bpt id="p1">**</bpt>myRG1<ept id="p1">**</ept>」と入力します。 <bpt id="p1">**</bpt>[OK]<ept id="p1">**</ept> をクリックします。
   4. アカウントの場所を選択します。

      ![[新しい NetApp アカウント] ウィンドウ](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)

      ![リソース グループ ウィンドウ](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> をクリックし、新しい NetApp アカウントを作成します。

# <a name="powershell"></a><bpt id="p1">[</bpt>PowerShell<ept id="p1">](#tab/azure-powershell)</ept>

1. 他の例でも参照できるように、いくつかの変数を定義します。

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ```

    > [!NOTE]
    > サポートされているリージョンの一覧については、「<bpt id="p1">[</bpt>リージョン別の利用可能な製品<ept id="p1">](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)</ept>」を参照してください。
    > コマンド ライン ツールでサポートされているリージョン名を取得するには、<ph id="ph1">`Get-AzLocation | select Location`</ph> を使用してください。
    >

1. <bpt id="p1">[</bpt>New-AzResourceGroup<ept id="p1">](/powershell/module/az.resources/new-azresourcegroup)</ept> コマンドを使用して、新しいリソース グループを作成します。

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. <bpt id="p1">[</bpt>New-AzNetAppFilesAccount<ept id="p1">](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount)</ept> コマンドを使用して Azure NetApp Files アカウントを作成します。

    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a><bpt id="p1">[</bpt>Azure CLI<ept id="p1">](#tab/azure-cli)</ept>

1. 他の例でも参照できるように、いくつかの変数を定義します。

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ```

    > [!NOTE]
    > サポートされているリージョンの一覧については、「<bpt id="p1">[</bpt>リージョン別の利用可能な製品<ept id="p1">](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)</ept>」を参照してください。
    > コマンド ライン ツールでサポートされているリージョン名を取得するには、<ph id="ph1">`az account list-locations --query "[].{Region:name}" --out table`</ph> を使用してください。
    >

2. <bpt id="p1">[</bpt>az group create<ept id="p1">](/cli/azure/group#az_group_create)</ept> コマンドを使って新しいリソース グループを作成します。

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. <bpt id="p1">[</bpt>az netappfiles account create<ept id="p1">](/cli/azure/netappfiles/account#az_netappfiles_account_create)</ept> コマンドを使用して Azure NetApp Files アカウントを作成します。

    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```

# <a name="template"></a><bpt id="p1">[</bpt>テンプレート<ept id="p1">](#tab/template)</ept>

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

次のコード スニペットでは、<bpt id="p1">[</bpt>Microsoft.NetApp/netAppAccounts<ept id="p1">](/azure/templates/microsoft.netapp/netappaccounts)</ept> リソースを使用して Azure Resource Manager テンプレート (ARM テンプレート) で NetApp アカウントを作成する方法を示しています。 コードを実行するには、GitHub のリポジトリから<bpt id="p1">[</bpt>完全な ARM テンプレート<ept id="p1">](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json)</ept>をダウンロードしてください。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json" range="177-183":::

<!-- Block begins with "type": "Microsoft.NetApp/netAppAccounts", -->

---

## <a name="create-a-capacity-pool"></a>容量プールの作成

# <a name="portal"></a><bpt id="p1">[</bpt>ポータル<ept id="p1">](#tab/azure-portal)</ept>

1. Azure NetApp Files 管理ブレードで、自分の NetApp アカウント (<bpt id="p1">**</bpt>myaccount1<ept id="p1">**</ept>) を選択します。

    ![NetApp アカウントを選択する](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)

2. 自分の NetApp アカウントの Azure NetApp Files 管理ブレードで、 <bpt id="p1">**</bpt>[容量プール]<ept id="p1">**</ept> をクリックします。

    ![[容量プール] をクリックする](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)

3. <bpt id="p1">**</bpt>[+ プールの追加]<ept id="p1">**</ept> をクリックします。

    ![[プールの追加] をクリックする](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. 容量プールの情報を指定します。
    * プール名として「<bpt id="p1">**</bpt>mypool1<ept id="p1">**</ept>」と入力します。
    * サービス レベルに <bpt id="p1">**</bpt>[Premium]<ept id="p1">**</ept> を選択します。
    * プール サイズとして <bpt id="p1">**</bpt>4 (TiB)<ept id="p1">**</ept> を選択します。
    * <bpt id="p1">**</bpt>[自動]<ept id="p1">**</ept> QoS タイプを使用します。

5. <bpt id="p1">**</bpt>Create<ept id="p1">**</ept> をクリックしてください。

# <a name="powershell"></a><bpt id="p1">[</bpt>PowerShell<ept id="p1">](#tab/azure-powershell)</ept>

1. 今後の参照用に新しい変数を定義する

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. <bpt id="p1">[</bpt>New-AzNetAppFilesPool<ept id="p1">](/powershell/module/az.netappfiles/new-aznetappfilespool)</ept> を使用して新しい容量プールを作成する

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a><bpt id="p1">[</bpt>Azure CLI<ept id="p1">](#tab/azure-cli)</ept>

1. 今後の参照用に新しい変数を定義する

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. <bpt id="p1">[</bpt>az netappfiles pool create<ept id="p1">](/cli/azure/netappfiles/pool#az_netappfiles_pool_create)</ept> を使用して新しい容量プールを作成する

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

# <a name="template"></a><bpt id="p1">[</bpt>テンプレート<ept id="p1">](#tab/template)</ept>

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

次のコード スニペットでは、<bpt id="p1">[</bpt>Microsoft.NetApp/netAppAccounts/capacityPools<ept id="p1">](/azure/templates/microsoft.netapp/netappaccounts/capacitypools)</ept> リソースを使用して Azure Resource Manager テンプレート (ARM テンプレート) で容量プールを作成する方法を示しています。 コードを実行するには、GitHub のリポジトリから<bpt id="p1">[</bpt>完全な ARM テンプレート<ept id="p1">](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json)</ept>をダウンロードしてください。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json" range="184-196":::

<!-- LN 185, block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools", -->

---

## <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Azure NetApp Files の NFS ボリュームを作成する

# <a name="portal"></a><bpt id="p1">[</bpt>ポータル<ept id="p1">](#tab/azure-portal)</ept>

1. 自分の NetApp アカウントの Azure NetApp Files 管理ブレードで、 <bpt id="p1">**</bpt>[ボリューム]<ept id="p1">**</ept> をクリックします。

    ![[ボリューム] をクリックする](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)

2. <bpt id="p1">**</bpt>[+ ボリュームの追加]<ept id="p1">**</ept> をクリックします。

    ![[ボリュームの追加] をクリックする](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)

3. [ボリュームの作成] ウィンドウで、ボリュームの情報を指定します。
   1. ボリューム名として「<bpt id="p1">**</bpt>myvol1<ept id="p1">**</ept>」と入力します。
   2. 容量プールを選択します (<bpt id="p1">**</bpt>mypool1<ept id="p1">**</ept>)。
   3. クォータの既定値を使用します。
   4. 仮想ネットワークで <bpt id="p1">**</bpt>[新規作成]<ept id="p1">**</ept> をクリックし、新しい Azure 仮想ネットワーク (Vnet) を作成します。  次の情報を入力します。
       * Vnet 名として「<bpt id="p1">**</bpt>myvnet1<ept id="p1">**</ept>」と入力します。
       * 10.7.0.0/16 など、自分の設定用のアドレス空間を指定します。
       * サブネット名として「<bpt id="p1">**</bpt>myANFsubnet<ept id="p1">**</ept>」と入力します。
       * 10.7.0.0/24 など、サブネット アドレス範囲を指定します。 専用サブネットは他のリソースと共有できません。
       * サブネットの委任に <bpt id="p1">**</bpt>[Microsoft.NetApp/volumes]<ept id="p1">**</ept> を選択します。
       * <bpt id="p1">**</bpt>[OK]<ept id="p1">**</ept> をクリックして Vnet を作成します。
   5. サブネットで、新しく作成した Vnet (<bpt id="p1">**</bpt>myvnet1<ept id="p1">**</ept>) を委任サブネットとして選択します。

      ![ボリューム ウィンドウを作成する](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)

      ![仮想ネットワーク ウィンドウを作成する](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)

4. <bpt id="p1">**</bpt>[プロトコル]<ept id="p1">**</ept> をクリックし、次のアクションを実行します。
    * ボリュームのプロトコルの種類として <bpt id="p1">**</bpt>[NFS]<ept id="p1">**</ept> を選択します。
    * ボリュームのエクスポート パスを作成する際に使用するファイル パスとして「<bpt id="p1">**</bpt>myfilepath1<ept id="p1">**</ept>」と入力します。
    * ボリュームの NFS バージョン ( <bpt id="p1">**</bpt>[NFSv3]<ept id="p1">**</ept> または <bpt id="p2">**</bpt>[NFSv4.1]<ept id="p2">**</ept> ) を選択します。
      NFS のバージョンについては、「<bpt id="p1">[</bpt>考慮事項<ept id="p1">](azure-netapp-files-create-volumes.md#considerations)</ept>」および「<bpt id="p2">[</bpt>ベスト プラクティス<ept id="p2">](azure-netapp-files-create-volumes.md#best-practice)</ept>」を参照してください。

    ![クイック スタートの NFS プロトコルを指定する](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. <bpt id="p1">**</bpt>[確認および作成]<ept id="p1">**</ept> をクリックし、作成するボリュームの情報を表示します。  

6. <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> をクリックして、ボリュームを作成します。 
    作成されたボリュームが [ボリューム] ブレードに表示されます。

    ![ボリュームが作成されました](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)

# <a name="powershell"></a><bpt id="p1">[</bpt>PowerShell<ept id="p1">](#tab/azure-powershell)</ept>

1. <bpt id="p1">[</bpt>New-AzDelegation<ept id="p1">](/powershell/module/az.network/new-azdelegation)</ept> コマンドを使用して、"Microsoft.NetApp/volumes" へのサブネットの委任を作成します。

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. <bpt id="p1">[</bpt>New-AzVirtualNetworkSubnetConfig<ept id="p1">](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)</ept> コマンドを使用して、サブネットの構成を作成します。

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. <bpt id="p1">[</bpt>New-AzVirtualNetwork<ept id="p1">](/powershell/module/az.network/new-azvirtualnetwork)</ept> コマンドを使用して、仮想ネットワークを作成します。

    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. <bpt id="p1">[</bpt>New-AzNetAppFilesVolume<ept id="p1">](/powershell/module/az.netappfiles/new-aznetappfilesvolume)</ept> コマンドを使用して、ボリュームを作成します。

    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a><bpt id="p1">[</bpt>Azure CLI<ept id="p1">](#tab/azure-cli)</ept>

1. 後で使用するために、いくつかの変数を定義します。

    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. <bpt id="p1">[</bpt>az network vnet create<ept id="p1">](/cli/azure/network/vnet#az_network_vnet_create)</ept> コマンドを使用して、サブネットがない仮想ネットワークを作成します。

    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. <bpt id="p1">[</bpt>az network vnet subnet create<ept id="p1">](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)</ept> コマンドを使用して、委任されたサブネットを作成します。

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. <bpt id="p1">[</bpt>az netappfiles volume create<ept id="p1">](/cli/azure/netappfiles/volume#az_netappfiles_volume_create)</ept> コマンドを使用して、ボリュームを作成します。

    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

# <a name="template"></a><bpt id="p1">[</bpt>テンプレート<ept id="p1">](#tab/template)</ept>

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

次のコード スニペットでは、Azure Resource Manager テンプレート (ARM テンプレート) で VNet を設定し、Azure NetApp Files ボリュームを作成する方法を示しています。 VNet の設定では、<bpt id="p1">[</bpt>Microsoft.Network/virtualNetworks<ept id="p1">](/azure/templates/Microsoft.Network/virtualNetworks)</ept> リソースを使用します。 ボリュームの作成では、<bpt id="p1">[</bpt>Microsoft.NetApp/netAppAccounts/capacityPools/volumes<ept id="p1">](/azure/templates/microsoft.netapp/netappaccounts/capacitypools/volumes)</ept> リソースを使用します。 コードを実行するには、GitHub のリポジトリから<bpt id="p1">[</bpt>完全な ARM テンプレート<ept id="p1">](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json)</ept>をダウンロードしてください。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json" range="148-176":::

<!-- Block begins with  "type": "Microsoft.Network/virtualNetworks", -->

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.netapp/anf-nfs-volume/azuredeploy.json" range="197-229":::

<!-- Block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes", -->

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

# <a name="portal"></a><bpt id="p1">[</bpt>ポータル<ept id="p1">](#tab/azure-portal)</ept>

手順が終了していて必要な場合、リソース グループを削除することができます。 リソース グループの削除操作は、元に戻すことができません。

> [!IMPORTANT]
> リソース グループ内のすべてのリソースが完全に削除され、元に戻すことはできません。

1. Azure portal の [検索] ボックスに「<bpt id="p1">**</bpt>Azure NetApp Files<ept id="p1">**</ept>」と入力し、表示された一覧から <bpt id="p2">**</bpt>[Azure NetApp Files]<ept id="p2">**</ept> を選択します。

2. サブスクリプションの一覧で、削除するリソース グループ (myRG1) をクリックします。

    ![リソース グループに移動する](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. [リソース グループ] ページで、 <bpt id="p1">**</bpt>[リソース グループの削除]<ept id="p1">**</ept> をクリックします。

    ![[リソース グループの削除] ボタンが強調表示されているスクリーンショット。](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png)

    ウィンドウが開き、リソース グループと共に削除されるリソースに関する警告が表示されます。

4. リソース グループの名前 (myRG1) を入力して、リソース グループとその中のすべてのリソースを完全に削除することを確認してから、 <bpt id="p1">**</bpt>[削除]<ept id="p1">**</ept> をクリックします。

    ![リソース グループの削除の確認](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png )

# <a name="powershell"></a><bpt id="p1">[</bpt>PowerShell<ept id="p1">](#tab/azure-powershell)</ept>

手順が終了していて必要な場合、リソース グループを削除することができます。 リソース グループの削除操作は、元に戻すことができません。

> [!IMPORTANT]
> リソース グループ内のすべてのリソースが完全に削除され、元に戻すことはできません。

1. <bpt id="p1">[</bpt>Remove-AzResourceGroup<ept id="p1">](/powershell/module/az.resources/remove-azresourcegroup)</ept> コマンドを使用して、リソース グループを削除します。

    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a><bpt id="p1">[</bpt>Azure CLI<ept id="p1">](#tab/azure-cli)</ept>

手順が終了していて必要な場合、リソース グループを削除することができます。 リソース グループの削除操作は、元に戻すことができません。

> [!IMPORTANT]
> リソース グループ内のすべてのリソースが完全に削除され、元に戻すことはできません。

1. <bpt id="p1">[</bpt>az group delete<ept id="p1">](/cli/azure/group#az_group_delete)</ept> コマンドを使用して、リソース グループを削除します。

    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```

# <a name="template"></a><bpt id="p1">[</bpt>テンプレート<ept id="p1">](#tab/template)</ept>

[なし] :

Azure portal、PowerShell、または Azure CLI を使用してリソース グループを削除します。

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> <bpt id="p1">[</bpt>Azure NetApp Files のストレージ階層<ept id="p1">](azure-netapp-files-understand-storage-hierarchy.md)</ept>

> [!div class="nextstepaction"]
> <bpt id="p1">[</bpt>Azure NetApp Files のサービス レベル<ept id="p1">](azure-netapp-files-service-levels.md)</ept>

> [!div class="nextstepaction"]
> <bpt id="p1">[</bpt>NFS ボリュームを作成する<ept id="p1">](azure-netapp-files-create-volumes.md)</ept>

> [!div class="nextstepaction"]
> <bpt id="p1">[</bpt>Azure NetApp Files を使用したソリューション アーキテクチャ<ept id="p1">](azure-netapp-files-solution-architectures.md)</ept>
