---
title: PowerShell を使用して Azure Cloud Services (延長サポート) に移行する
description: PowerShell を使用して Azure Cloud Services (クラシック) から Azure Cloud Services (延長サポート) に移行する方法
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
author: hirenshah1
ms.author: hirshah
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f7626b2af5f5d8c12a29602e62b38ebef2abfd48
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434284"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>PowerShell を使用して Azure Cloud Services (延長サポート) に移行する

次の手順では、Azure PowerShell コマンドを使用して [Cloud Services (クラシック)](../cloud-services/cloud-services-choose-me.md) から [Cloud Services (延長サポート)](overview.md) に移行する方法について説明します。

## <a name="1-plan-for-migration"></a>1) 移行を計画する
移行を成功させる上で、計画立案は最も重要な手順です。 移行の手順を開始する前に、[Cloud Services (延長サポート) の概要](overview.md)に関する記事と「[クラシックから Azure Resource Manager への IaaS リソースの移行計画](../virtual-machines/migration-classic-resource-manager-plan.md)」を確認してください。 

## <a name="2-install-the-latest-version-of-powershell"></a>2) 最新バージョンの PowerShell をインストールする
Azure PowerShell をインストールするための主なオプションは 2 つあります:[PowerShell ギャラリー](https://www.powershellgallery.com/profiles/azure-sdk/)または [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps)。 WebPI は月次の更新プログラムを受け取ります。 PowerShell ギャラリーは、継続的に更新プログラムを受け取ります。 この記事は、Azure PowerShell バージョン 2.1.0 に基づいています。

インストール指示については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/servicemanagement/install-azure-ps?preserve-view=true&view=azuresmps-4.0.0)」を参照してください。

## <a name="3-ensure-admin-permissions"></a>3) 管理者のアクセス許可を確認する
この移行を実行するには、[Azure portal](https://portal.azure.com) で自分をサブスクリプションの共同管理者として追加する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[ハブ]** メニューで、 **[サブスクリプション]** を選択します。 表示されない場合は、 **[すべてのサービス]** を選択します。
3. 適切なサブスクリプションのエントリを探し、 **[自分のロール]** フィールドを確認します。 共同管理者の場合、この値は *[アカウント管理者]* です。

共同管理者を追加できない場合は、サービス管理者またはサブスクリプションの共同管理者に連絡して、追加を依頼します。

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4) クラシック プロバイダーと CloudServices 機能を登録する
まず PowerShell プロンプトを開始します。 移行の場合、クラシックと Resource Manager の両方に合わせて環境をセットアップする必要があります。

Resource Manager モデルの自分のアカウントにサインインします。

```powershell
Connect-AzAccount
```

次のコマンドを使用して、利用可能なサブスクリプションを取得します。

```powershell
Get-AzSubscription | Sort Name | Select Name
```

現在のセッション用の Azure サブスクリプションを設定します。 この例では、既定のサブスクリプション名を **My Azure Subscription** に設定します。 例のサブスクリプション名を対象のサブスクリプションの名前に置き換えてください。

```powershell
Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

次のコマンドを使用して、移行リソース プロバイダーに登録します。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> 登録は 1 回限りの手順ですが、移行を試みる前に実行する必要があります。 登録を行わないと、次のエラー メッセージが表示されます。
>
> *BadRequest: 移行の対象サブスクリプションが登録されていません。*

サブスクリプションの CloudServices 機能を登録します。 登録が完了するまでに数分かかる場合があります。 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

登録が完了するまで 5 分間お待ちください。 

次のコマンドを使用して、クラシック プロバイダーの承認ステータスを確認します。

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

以下を使用して登録の状態を確認します。  
```powershell
Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

両方の RegistrationState が `Registered` であることを確認してから続行します。

クラシック デプロイ モデルに切り替える前に、現在のデプロイまたは仮想ネットワークの Azure リージョンに十分な Azure Resource Manager vCPU クオータがあることを確認します。 Azure Resource Manager での現在の vCPU 数は、次の PowerShell コマンドを使用して確認できます。 vCPU クォータの詳細については、「[制限と Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)」を参照してください。

この例は、**米国西部** リージョンでの可用性をチェックします。 例のリージョン名を対象のリージョン名に置き換えてください。

```powershell
Get-AzVMUsage -Location "West US"
```

ここで、クラシック デプロイ モデルの自分のアカウントにサインインします。

```powershell
Add-AzureAccount
```

次のコマンドを使用して、利用可能なサブスクリプションを取得します。

```powershell
Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

現在のセッション用の Azure サブスクリプションを設定します。 この例では、既定のサブスクリプションを **My Azure Subscription** に設定します。 例のサブスクリプション名を対象のサブスクリプションの名前に置き換えてください。

```powershell
Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="5-migrate-your-cloud-services"></a>5) クラウド サービスを移行する 
移行を開始する前に、 [移行手順](./in-place-migration-overview.md#migration-steps)のしくみと各手順について理解しておいてください。 

* [仮想ネットワークにはないクラウド サービスを移行する](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [仮想ネットワークにあるクラウド サービスを移行する](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> ここで説明するすべての操作がべき等です。 サポートされていない機能や構成エラー以外の問題が発生した場合は、準備、中止、またはコミット操作を再試行することをお勧めします。 これによりプラットフォームでアクションが再試行されます。


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5.1) オプション 1 - 仮想ネットワークにはないクラウド サービスを移行する
次のコマンドを使用して、クラウド サービスの一覧を取得します。 移行するクラウド サービスを選択します。

```powershell
Get-AzureService | ft Servicename
```

クラウド サービスのデプロイ名を取得します。 この例では、**My Service** というサービス名を使用しています。 例のサービス名を対象のサービスの名前に置き換えてください。

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

まず、次のコマンドを使用して、クラウド サービスを移行できることを検証します。 コマンドを実行すると、移行をブロックするエラーが表示されます。 

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

検証が成功した場合、または警告のみが表示される場合は、準備の手順に進むことができます。 

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

Azure PowerShell または Azure portal のいずれかを使用して、準備したクラウド サービス (延長サポート) の構成を確認します。 移行の準備ができていない場合に以前の状態に戻すには、移行を中止します。
```powershell
Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```
移行を完了する準備ができたら、移行をコミットします。

```powershell
Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5.1) オプション 2 - 仮想ネットワークにあるクラウド サービスを移行する

仮想ネットワークにあるクラウド サービスを移行する場合、その仮想ネットワークを移行します。 クラウド サービスは、対象の仮想ネットワークと共に自動的に移行されます。

> [!NOTE]
> 仮想ネットワーク名は、新しいポータルに表示される名前と異なる可能性があります。 新しい Azure portal には、`[vnet-name]` という名前が表示されますが、実際の仮想ネットワーク名は `Group [resource-group-name] [vnet-name]` 型の名前です。 移行を開始する前に、コマンド `Get-AzureVnetSite | Select -Property Name` を使用して実際の仮想ネットワーク名を確認するか、以前の Azure portal で確認します。 

この例では、仮想ネットワーク名を **myVnet** に設定します。 例の仮想ネットワーク名を対象の仮想ネットワークの名前に置き換えてください。

```powershell
$vnetName = "myVnet"
```

最初に、次のコマンドを使用して、仮想ネットワークを移行できるかどうかを検証します。

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

次のコマンドによって、移行をブロックするすべての警告とエラーが表示されます。 検証が成功した場合は、次の Prepare の手順に進むことができます。

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Azure PowerShell または Azure portal のいずれかを使用して、準備したクラウド サービス (延長サポート) の構成を確認します。 移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

準備した構成が正しい場合は、次に進み、以下のコマンドを使用してリソースをコミットできます。

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>次のステップ

[移行後の変更](post-migration-changes.md)に関するセクションを参照し、新しい Cloud Services (延長サポート) のデプロイのデプロイ ファイル、自動化、その他の属性での変更を確認します。
