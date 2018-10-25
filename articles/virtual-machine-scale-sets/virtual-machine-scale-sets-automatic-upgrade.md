---
title: Azure 仮想マシン スケール セットを使用した OS イメージの自動アップグレード | Microsoft Docs
description: スケール セット内の VM インスタンス上の OS イメージを自動的にアップグレードする方法について説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: rajraj
ms.openlocfilehash: cf25d08fc9a0e1ae458d350be93af31447928ecb
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069456"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure 仮想マシン スケール セットによる OS イメージの自動アップグレード

OS イメージの自動アップグレードは、すべての VM を最新の OS イメージに自動的にアップグレードする Azure 仮想マシン スケール セットの機能です。

OS の自動アップグレードには、次の特徴があります。

- 構成すると、イメージ発行元によって公開された最新の OS イメージが、ユーザーの介入なしでスケール セットに自動的に適用されます。
- 新しいプラットフォーム イメージが発行元によって発行されるたびに、ローリング方法でバッチごとにインスタンスをアップグレードします。
- アプリケーション正常性プローブと統合します。
- すべての VM サイズ、および Windows と Linux の両方のプラットフォーム イメージで動作します。
- 自動アップグレードはいつでも停止できます (OS のアップグレードは、手動でも開始できます)。
- VM の OS ディスクが、最新バージョンのイメージで作成された新しい OS ディスクに置き換えられます。 永続化されているデータ ディスクは保持されたまま、構成済みの拡張機能とカスタム データ スクリプトが実行されます。
- 現在、Azure Disk Encryption (プレビュー) はサポートされていません。  

## <a name="how-does-automatic-os-image-upgrade-work"></a>OS イメージの自動アップグレードのしくみ

アップグレードは、VM の OS ディスクを、最新のイメージ バージョンを使用して作成された新しいものに置き換えるによって機能します。 永続化されているデータ ディスクは保持されたまま、すべての構成済みの拡張機能とカスタム データ スクリプトが実行されます。 アプリケーションのダウンタイムを最小限に抑えるため、アップグレードはマシンのバッチで行われ、いつでもスケール セットの 20% を超えてアップグレードされることはありません。 さらに、Azure Load Balancer アプリケーションの正常性プローブを統合するオプションもあります。 アプリケーションのハートビートを組み込み、アップグレード プロセスでのバッチごとのアップグレードの成功を検証することを強くお勧めします。 実行手順は次のとおりです。 

1. アップグレード プロセスを開始する前に、オーケストレーターは異常なインスタンスが 20% を超えていないことを確認します。 
2. アップグレードする VM インスタンスのバッチ (インスタンスの合計数の最大 20% を含むバッチ) を識別します。
3. VM インスタンスのこのバッチの OS イメージをアップグレードします。
4. アプリケーション正常性プローブが構成されている場合、アップグレードはプローブが正常になるまで 5 分間待機した後、次のバッチのアップグレードに進みます。 
5. アップグレードするインスタンスが残っている場合は、次のバッチで手順 1 を実行します。それ以外の場合、アップグレードは完了します。

スケール セットの OS アップグレード オーケストレーターは、各バッチをアップグレードする前に、VM インスタンスの全体の正常性を確認します。 バッチのアップグレード中に、Azure データ センターで他の計画済みまたは計画外のメンテナンスが同時に発生されることがあり、これによって VM の可用性に影響が出る場合があります。 そのため、一時的に 20% を超えるインスタンスがダウンする可能性があります。 このような場合は、現在のバッチの最後に、スケール セットのアップグレードは停止します。

## <a name="supported-os-images"></a>サポート対象の OS イメージ
現時点では、特定の OS プラットフォーム イメージのみがサポートされています。 現時点では、自分で作成したカスタム イメージを使用することはできません。 

現時点では、次の SKU がサポートされています (将来さらに追加される予定です)。
    
| 発行元               | OS 製品      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04 LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS *        | 
| Rogue Wave (OpenLogic)  | CentOS        | 7.5 *              | 
| CoreOS                  | CoreOS        | 安定版             | 
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |

* これらのイメージのサポートは、現在展開中であり、まもなくすべての Azure リージョンで利用できるようになります。 

## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>OS イメージの自動アップグレードを構成するための要件

- プラットフォーム イメージの *version* プロパティには *latest* を設定する必要があります。
- Service Fabric 以外のスケール セットにアプリケーション正常性プローブを使用します。
- スケール セット モデルが参照しているリソースが使用でき、最新状態に保たれていることを確認します。 
  たとえば、VM 拡張プロパティ内のブートストラップ ペイロード、ストレージ アカウント内のペイロードの SAS URI は、モデル内のシークレットを参照しています。 

## <a name="configure-automatic-os-image-upgrade"></a>OS イメージの自動アップグレードの構成
OS イメージの自動アップグレードを構成するには、スケール セットのモデル定義で *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* プロパティが *true* に設定されていることを確認します。 

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{ 
  "properties": { 
    "upgradePolicy": { 
      "automaticOSUpgradePolicy": { 
        "enableAutomaticOSUpgrade":  true 
      } 
    } 
  } 
} 
```

次の例では、Azure CLI (2.0.47 以降) を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットの自動アップグレードを構成します。

```azurecli
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```
Azure PowerShell を使用してこのプロパティを構成するサポートは、まもなく展開されます。

## <a name="using-application-health-probes"></a>アプリケーションの正常性プローブの使用 

OS のアップグレード中は、スケール セット内の VM インスタンスが、一度に 1 つのバッチでアップグレードされます。 アップグレードは、アップグレード済みの VM インスタンス上でユーザーのアプリケーションが正常である場合のみ続行されます。 アプリケーションがスケール セットの OS アップグレード エンジンに正常性通知を提供することをお勧めします。 既定では、OS のアップグレード中、プラットフォームは、VM の電源状態と拡張機能のプロビジョニング状態を考慮して、アップグレード後に VM インスタンスが正常であるかどうかを判断します。 VM インスタンスの OS のアップグレード中、VM インスタンス上の OS ディスクは、最新バージョンのイメージに基づく新しいディスクに置き換えられます。 OS のアップグレードが完了した後、構成済みの拡張機能がこれらの VM 上で実行されます。 VM 上のすべての拡張機能が正常にプロビジョニングされた場合にのみ、アプリケーションは正常であるとみなされます。 

スケール セットにアプリケーション正常性プローブをオプションで構成して、アプリケーションの進行中の状態に関する正確な情報をプラットフォームに提供できます。 アプリケーション正常性プローブは、正常性シグナルとして使用されるカスタム ロード バランサー プローブです。 スケール セットの VM インスタンスで実行されているアプリケーションは、外部 HTTP または TCP 要求に応答して、正常かどうかを示すことができます。 カスタム ロード バランサー プローブの動作方法の詳細については、「[Load Balancer プローブを理解する](../load-balancer/load-balancer-custom-probe-overview.md)」を参照してください。 アプリケーション正常性プローブは、OS の自動アップグレードでは必須ではありませんが、使用することを強くお勧めします。

スケール セットが複数の配置グループを使用するように構成されている場合は、[Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) を使用するプローブを使用する必要があります。

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>スケール セットに関するアプリケーション正常性プローブとしてのカスタム ロード バランサー プローブの構成
ベスト プラクティスとして、スケール セットの正常性のためのロード バランサー プローブを明示的に作成します。 既存の HTTP プローブまたは TCP プローブと同じエンドポイントを使用できますが、この正常性プローブでは、従来のロード バランサー プローブとは異なる動作が必要になる可能性があります。 たとえば、従来のロード バランサー プローブは、インスタンスの負荷が高すぎる場合に異常を返すことがありますが、OS の自動アップグレード中にインスタンスの正常性を決定するには、その動作は適切ではない可能性があります。 2 分未満のプローブ率が高いプローブを構成します。

ロード バランサー プローブは、スケール セットの *networkProfile* 内で参照でき、次のように、内部または公開されているロード バランサーに関連付けることができます。

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```
> [!NOTE]
> Service Fabric と OS 自動アップグレードを使用している場合、Service Fabric で実行されているサービスの高可用性を維持するために、新しい OS イメージは更新ドメインごとにロールアウトされます。 Service Fabric クラスターの持続性の特徴の詳細については、[こちらのドキュメント](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)を参照してください。

### <a name="keep-credentials-up-to-date"></a>資格情報を最新に保つ
スケール セットが外部のリソースにアクセスするときに資格情報を使用する場合、たとえば、VM 拡張機能がストレージ アカウントの SAS トークンを使用するように構成されている場合などは、資格情報が最新に保たれているか確認する必要があります。 証明書やトークンなどの資格情報の期限が切れている場合は、アップグレードは失敗し、VM の最初のバッチは障害が発生した状態になります。

リソースの認証エラーが発生した場合に、VM を復旧し、OS の自動アップグレードを再度有効にするための推奨手順は次のとおりです。

* 拡張機能に渡されたトークン (またはその他の資格情報) を再生成します。
* 外部エンティティと対話する VM 内から使用されるすべての資格情報が最新であることを確認します。
* 新しいトークンで、スケール セット モデルの拡張機能を更新します。
* 障害が発生したものを含むすべての VM インスタンスを更新する、更新されたスケール セットをデプロイします。 

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>OS イメージの自動アップグレードの履歴を取得する 
Azure PowerShell、Azure CLI 2.0、または REST API を使用して、スケール セットに対して実行された最新の OS のアップグレードの履歴を確認できます。 過去 2 か月間に試行された最後の 5 回の OS アップグレードの履歴を取得できます。

### <a name="azure-powershell"></a>Azure PowerShell
次の例では、Azure PowerShell を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットの状態をチェックします。

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
次の例では、Azure CLI (2.0.47 以降) を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットの状態をチェックします。

```azurecli
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
次の例では、REST API を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットの状態をチェックします。

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```
この API のドキュメント (https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getosupgradehistory) を参照してください。

GET 呼び出しは、次の例の出力に似たプロパティを返します。

```json
{
  "value": [
    {
      "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
} 
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>プラットフォーム OS イメージの最新バージョンを取得する方法 

以下の例を使用して、OS の自動アップグレードがサポートされている SKU のイメージ バージョンを取得できます。 

```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

```powershell
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

```azurecli
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>テンプレートを使用したデプロイ

次のテンプレートを使用して、自動アップグレードを使用するスケール セットをデプロイできます。<a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>自動ローリング アップグレード - Ubuntu 16.04 LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>次の手順
スケール セットを使用して OS の自動アップグレードを使用する方法の例については、[プレビュー機能の GitHub リポジトリ](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)を参照してください。
