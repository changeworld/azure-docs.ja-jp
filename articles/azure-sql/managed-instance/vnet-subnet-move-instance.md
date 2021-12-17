---
title: マネージインスタンスを別のサブネットに移動する
titleSuffix: Azure SQL Managed Instance
description: フェールオーバー中に短時間でダウンタイム (通常は最大10秒) を使用して、Azure SQL Managed Instance を別のサブネットに移動する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma, bonova, srbozovi, wiassaf
ms.date: 09/30/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7adb9886bdeebc8aad7f8c200b21523e53651a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091391"
---
# <a name="move-azure-sql-managed-instance-across-subnets"></a>Azure SQL Managed Instance を複数のサブネットに移動する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

azure SQL Managed Instance は、azure[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)内の専用サブネット内にデプロイする必要があります。 サブネット内にデプロイできるマネージインスタンスの数は、サブネットのサイズ (サブネットの範囲) によって異なります。

この記事では、仮想コアのスケーリングやインスタンスのサービス階層の変更と同様に、マネージインスタンスをあるサブネットから別のサブネットに移動する方法について説明します。 SQLManaged Instance は、移動中に使用できます。ただし、更新の最後にフェールオーバーが発生した場合を除き、短時間で実行されるトランザクションが中断された場合でも、通常は最大で10秒かかります。 

インスタンスを別のサブネットに移動すると、次の仮想クラスター操作がトリガーされます。
- 宛先サブネットは、仮想クラスターを構築またはサイズ変更します。
- 仮想クラスターは、ソースサブネットで削除または最適化されます。 

インスタンスを別のサブネットに移動する前に、次の概念を理解しておくことをお勧めします。 
- [Azure SQL Managed Instance に必要なサブネットのサイズと範囲を決定](vnet-subnet-determine-size.md)します。
- インスタンスを [新しいサブネット](virtual-network-subnet-create-arm-template.md) に移動するか、 [既存のサブネットを使用](vnet-existing-add-subnet.md)するかを選択します。
- [管理操作](management-operations-overview.md)を使用して、新しいマネージインスタンスの自動配置、インスタンスプロパティの更新、またはインスタンスの削除を行います。 これらの管理操作を [監視](management-operations-monitor.md) することができます。 



## <a name="requirements-and-limitations"></a>要件と制限事項 

マネージインスタンスをデプロイするか、別のサブネットに移動するには、移行先サブネットに特定の [ネットワーク要件](connectivity-architecture-overview.md#service-aided-subnet-configuration)が必要です。

### <a name="subnet-readiness"></a>サブネットの準備 

マネージインスタンスを移動する前に、サブネットが **Managed Instance の準備完了** としてマークされていることを確認します。 

Azure portal の **仮想ネットワーク** UI では、マネージインスタンスの前提条件を満たす仮想ネットワークが **Managed Instance の準備完了** として分類されます。 マネージインスタンスが既にデプロイされているサブネットを持つ仮想ネットワークには、仮想ネットワーク名の前にアイコンが表示されます。 マネージインスタンスの準備が整っている空のサブネットにはアイコンがありません。 

" **その他** " とマークされているサブネットは空で、マネージインスタンスに使用できますが、まずは [ネットワーク要件](connectivity-architecture-overview.md#service-aided-subnet-configuration)を満たす必要があります。 これには次のものが含まれます

- Microsoft .Sql/managedInstances リソースプロバイダーへの委任
- ルートテーブルのアタッチ
- ネットワークセキュリティグループを接続する

すべての要件が満たされると、サブネットは **もう一方** のカテゴリから **[Ready for Managed Instance]** カテゴリに移動し、マネージインスタンスに使用できます。 

新規または既存のマネージインスタンスは、既に使用されている (インスタンスの配置に使用されるインスタンスに他のリソースを含めることはできません) か、サブネットに別の DNS ゾーン (サブネット間のインスタンスの移動の制限) があるため、 **無効** とマークされたサブネットは使用できません。 

> [!div class="mx-imgBorder"]
> ![Azure SQL Managed Instance サブネットのドロップダウンのスクリーンショット](./media/vnet-subnet-move-instance/subnet-grouping-per-state.png)


サブネットの状態と指定に応じて、移行先のサブネットに対して次の調整を行うことができます。 

- **Managed Instance の準備ができています (既存の SQL Managed Instance が含まれています)** : 調整は行われません。 これらのサブネットには既にマネージインスタンスが含まれているため、サブネットを変更すると既存のインスタンスに影響を与える可能性があります。 
- **Managed Instance の準備完了 (空)** : ワークフローは、ネットワークセキュリティグループとルートテーブル内のすべての必要な規則を検証し、必要であるが不足しているすべての規則を追加します。 <sup>1</sup>

> [!Note]
> <sup>1</sup> ソースサブネットの構成に追加されたカスタム規則は、移行先のサブネットにコピーされません。 ソースサブネット構成のカスタマイズは、移行先サブネットに手動でレプリケートする必要があります。 これを実現する1つの方法は、送信元と送信先のサブネットに同じルートテーブルとネットワークセキュリティグループを使用することです。


### <a name="destination-subnet-limitations"></a>宛先サブネットの制限事項 

既存のインスタンスの宛先サブネットを選択するときは、次の制限事項を考慮してください。 

- 宛先サブネットは、ソースサブネットと同じ仮想ネットワークに存在する必要があります。 
- 移行先サブネットの dns ゾーンは、現在、マネージインスタンスの DNS ゾーンの変更がサポートされていないため、ソースサブネットの dns ゾーンと一致している必要があります。 
- Gen4 ハードウェア上で実行されているインスタンスは、Gen4 が非推奨になっているため、より新しいハードウェアの世代にアップグレードする必要があります。 ハードウェアの生成のアップグレードと別のサブネットへの移動は、1回の操作で実行できます。 


## <a name="operation-steps"></a>操作の手順

次の表は、インスタンスの移動操作中に発生する操作の手順の詳細を示しています。 

|[ステップ名]  |ステップの説明  |
|----|---------|
|要求の検証 |送信されたパラメーターを検証します。 構成の誤りが検出された場合、操作は失敗し、エラーが発生します。 |
|仮想クラスターのサイズ変更と作成 |移行先サブネットの状態に応じて、仮想クラスターが作成またはサイズ変更されます。  |
|新しいインスタンスの起動 |SQL プロセスは、デプロイ先のサブネットにデプロイされている仮想クラスターで開始されます。 |
|データベース ファイルのシード処理とデータベース ファイルのアタッチ |サービスレベルによっては、データベースがシード処理されるか、データベースファイルがアタッチされます。 |
|フェールオーバーの準備とフェールオーバー |データがシード処理されるか、データベースファイルが再アタッチされると、システムによってフェールオーバーが準備されます。 すべての準備が整うと、システムは **短時間** でフェールオーバーを実行します (通常は10秒未満)。  |
|古い SQL インスタンスのクリーンアップ |ソース仮想クラスターから古い SQL プロセスを削除します。  |
|仮想クラスターの削除 |ソースサブネット内の最後のインスタンスの場合、最後の手順では仮想クラスターを同期的に削除します。 それ以外の場合、仮想クラスターは非同期に最適化されます。  |

操作手順の詳細については、 [「Azure SQL Managed Instance 管理操作の概要](management-operations-overview.md#management-operations-steps)」を参照してください。

## <a name="move-the-instance"></a>インスタンスを移動する

クロスサブネットインスタンスの移動は、インスタンスの更新操作の一部です。 既存のインスタンス更新 API、Azure PowerShell、Azure CLI の各コマンドは、サブネット ID プロパティを使用して強化されています。 

Azure portal で、 **[ネットワーク]** ブレードの [サブネット] フィールドを使用して、インスタンスを移行先サブネットに移動します。 Azure PowerShell または Azure CLI を使用する場合は、update コマンドで別のサブネット ID を指定して、インスタンスを既存のサブネットから宛先サブネットに移動します。 

インスタンス管理コマンドの完全なリファレンスについては、「 [management API reference for Azure SQL Managed Instance](api-references-create-manage-instance.md)」を参照してください。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)

インスタンスサブネットを選択するオプションは、Azure portal の **[ネットワーク]** ブレードにあります。 インスタンスの移動操作は、サブネットを選択して変更を保存すると開始されます。 

移動操作の最初の手順は、移行先のサブネットをデプロイ用に準備することです。これには数分かかる場合があります。 サブネットの準備が整うと、インスタンスの移動管理操作が開始され、Azure portal に表示されます。 


> [!div class="mx-imgBorder"]
> ![SQL Managed Instance の [ネットワーク] ブレードでサブネットを選択する方法](./media/vnet-subnet-move-instance/how-to-select-subnet.png)


Azure portal の **概要** ブレードからインスタンスの移動操作を監視します。 通知を選択すると、追加のブレードが開き、現在のステップ、合計手順、および操作をキャンセルするボタンに関する情報が表示されます。 

> [!div class="mx-imgBorder"]
> ![インスタンスの移動操作を監視する方法](./media/vnet-subnet-move-instance/monitor-subnet-move-operation.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

宛先サブネットと同じ仮想ネットワークにサブネットを作成した後に、Azure PowerShell コマンド[AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)を使用してインスタンスを移動します。 既存のサブネットを使用する場合は、PowerShell コマンドでそのサブネット名を指定します。

このセクションの PowerShell コマンドの例では、インスタンスデプロイの宛先サブネットを準備し、マネージインスタンスを移動します。 


次の PowerShell コマンドを使用して、パラメーターを指定します。 

```powershell-interactive
### PART 1 - DEFINE PARAMETERS

#Generating basic parameters
$currentSubscriptionID = 'subscription-id'
$sqlMIResourceGroupName = 'resource-group-name-of-sql-mi'
$sqlMIName = 'sql-mi-name'
$sqlMIResourceVnetName = 'vnet-name-of-sql-mi'
$destinationSubnetName = 'name-of-the-destination-subnet-for-sql-mi'
```

サブネットにインスタンスが既にデプロイされている場合は、このコマンドをスキップします。 新しいサブネットを使用している場合は、次の Azure PowerShell コマンドを使用して、サブネットを準備します。 

```powershell-interactive
### PART 2 - PREPARE DESTINATION SUBNET

#Loading the url of script used for preparing the subnet for SQL MI deployment
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

#Generating destination subnet parameters
$parameters = @{
    subscriptionId = $currentSubscriptionID
    resourceGroupName = $sqlMIResourceGroupName
    virtualNetworkName = $sqlMIResourceVnetName
    subnetName = $destinationSubnetName
}

#Initiating subnet prepartion script
Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

> [!Note]
> サブネットを準備するスクリプトの詳細については、「 [Azure SQL Managed Instance の既存の仮想ネットワークを構成](vnet-existing-add-subnet.md)する」を参照してください。 

次のAzure PowerShellコマンドは、インスタンスをソースサブネットに移動します。 

```powershell-interactive
### PART 3 - MOVE INSTANCE TO THE NEW SUBNET

Set-AzSqlInstance -Name $sqlMIName -ResourceGroupName $sqlMIResourceGroupName `
-SubnetId "/subscriptions/$currentSubscriptionID/resourceGroups/$sqlMIResourceGroupName/providers/Microsoft.Network/virtualNetworks/$sqlMIResourceVnetName/subnets/$destinationSubnetName"
```

次の Azure PowerShell コマンドは、インスタンスを移動し、進行状況を監視する方法も提供します。 

```powershell-interactive
###PART 3 EXTENDED - MOVE INSTANCE AND MONITOR PROGRESS

# Extend the Set-AzSqlInstance command with -AsJob -Force parameters to be able to monitor the progress or proceed with script execution as moving the instance to another subnet is long running operation 
Set-AzSqlInstance -Name $sqlMIName -ResourceGroupName $sqlMIResourceGroupName `
-SubnetId "/subscriptions/$currentSubscriptionID/resourceGroups/$sqlMIResourceGroupName/providers/Microsoft.Network/virtualNetworks/$sqlMIResourceVnetName/subnets/$destinationSubnetName" -AsJob -Force

$operationProgress = Get-AzSqlInstanceOperation -ManagedInstanceName $sqlMIName -ResourceGroupName $sqlMIResourceGroupName
#checking the operation step status
Write-Host "Checking the ongoing step" -ForegroundColor Yellow
$operationProgress.OperationSteps.StepsList
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update) コマンドを使用して、インスタンスを別のサブネットに移動します。 

サブネット ID をプロパティとして指定するか、仮想ネットワーク名をプロパティとして指定するか、 `--subnet` またはプロパティとしてサブネット名を指定して、変換先を指定し `--vnet-name``--subnet` ます。 

次の例では、サブネット ID を指定して、マネージインスタンスを別のサブネットに移動します。 


```azurecli-interactive
az sql mi update -g myResourceGroup -n mySqlManagedInstance --subnet /subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVirtualNetworkName/subnets/destinationSubnetName
```

次の例では、仮想ネットワーク名とサブネット名を指定して、マネージインスタンスを別のサブネットに移動します。

```azurecli-interactive
az sql mi update -g myResourceGroup -n mySqlManagedInstance --vnet-name myVirtualNetworkName --subnet destinationSubnetName
```

次のコマンドを使用して、管理操作の進行状況を監視します。 

```azurecli-interactive
az sql mi op list -g myResourceGroup --mi mySqlManagedInstance
```
---

## <a name="next-steps"></a>次のステップ

- 最初のマネージド インスタンスを作成する方法については、[クイック スタート ガイド](instance-create-quickstart.md)を参照してください。
- 機能と比較の一覧については、[共通の SQL 機能](../database/features-comparison.md)に関するページを参照してください。
- VNet の構成の詳細については、[SQL Managed Instance VNet の構成](connectivity-architecture-overview.md)に関するページを参照してください。
- マネージド インスタンスを作成し、バックアップ ファイルからデータベースを復元するためのクイック スタートについては、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
- Azure Database Migration Service を使用して移行する方法のチュートリアルについては、[Database Migration Service を使用した SQL Managed Instance の移行](../../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。
