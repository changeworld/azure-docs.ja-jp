---
title: Azure Service Fabric ノード タイプのスケールアップ
description: 仮想マシン スケール セットを追加することで Service Fabric クラスターをスケーリングする方法を説明します。
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: a42e33fa87b6cf7966368481ef6d3920511919e3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260451"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Service Fabric クラスターのプライマリ ノード タイプをスケールアップする
この記事では、仮想マシンのリソースを増やして、Service Fabric クラスターのプライマリ ノード タイプをスケールアップする方法について説明します。 Service Fabric クラスターは、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターに属しているコンピューターまたは VM を "ノード" と呼びます。 仮想マシン スケール セットは、セットとして仮想マシンのコレクションをデプロイおよび管理するために使用する Azure コンピューティング リソースです。 Azure クラスターで定義されているすべてのノードの種類は、[異なるスケール セットとしてセットアップされます](service-fabric-cluster-nodetypes.md)。 その後は、ノードの種類ごとに個別に管理できます。 Service Fabric クラスターを作成した後は、クラスターのノード タイプを垂直方向にスケーリング (ノードのリソースを変更) するか、そのノード タイプの VM のオペレーティング システムをアップグレードすることができます。  クラスターは、クラスターでワークロードを実行中であっても、いつでもスケーリングできます。  クラスターをスケーリングすると、アプリケーションも自動的にスケーリングされます。

> [!WARNING]
> クラスターの状態が異常な場合は、プライマリ ノード タイプのスケールアップ手順を試行しないでください。クラスターがさらに不安定になります。
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>プライマリ ノード タイプの VM のサイズとオペレーティング システムをアップグレードするプロセス
ここでは、プライマリ ノード タイプの VM の VM サイズとオペレーティング システムを更新するプロセスについて説明します。  アップグレード後、プライマリ ノード タイプの VM は、サイズが Standard D4_V2 で、コンテナー搭載 Windows Server 2016 Datacenter を実行します。

> [!WARNING]
> 運用クラスターでこの手順を実行する前に、サンプル テンプレートを調べて、テスト クラスターに対してプロセスを検証することをお勧めします。 クラスターも一時的に使用できなくなります。 同じ NodeType として宣言されている複数の VMSS を並列に変更することはできません。各 NodeType の VMSS を変更するには、個別にデプロイ操作を実行する必要があります。

1. これらのサンプル [テンプレート](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json)および[パラメーター](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) ファイルを使用して、2 つのノード タイプと 2 つのスケール セット (ノード タイプごとに 1 つのスケール セット) を持つ初期クラスターを展開します。  どちらのスケール セットも、サイズは Standard D2_V2 で、Windows Server 2012 R2 Datacenter を実行しています。  クラスターがベースラインのアップグレードを完了するまで待ちます。   
2. (省略可能) ステートフル サンプルをクラスターに展開します。
3. プライマリ ノード タイプの VM をアップグレードすることを決定したら、これらのサンプル [テンプレート](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json)および[パラメーター](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) ファイルを使用してプライマリ ノード タイプに新しいスケール セットを追加して、プライマリ ノード タイプが 2 つのスケール セットを持つようにします。  システム サービスとユーザー アプリケーションは、2 つの異なるスケール セットの VM 間で移行できます。  新しいスケール セットの VM は、サイズが Standard D4_V2 で、コンテナー搭載 Windows Server 2016 Datacenter を実行します。  新しいスケール セットと共に新しいロードバランサーとパブリック IP アドレスも追加されます。  
    テンプレートで新しいスケール セットを見つけるには、*vmNodeType2Name* パラメーターに指定されている "Microsoft.Compute/virtualMachineScaleSets" リソースを探します。  新しいスケール セットは、properties->virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef 設定を使用してプライマリ ノード タイプに追加されます。
4. クラスターの正常性を確認し、すべてのノードが正常であることを確認します。
5. プライマリ ノード タイプの以前のスケール セットに含まれるノードは、削除するために無効にします。 一度にすべてを無効にすることができます。この操作はキューに格納されます。 すべてのノードが無効になるまで待ちます。この操作には時間がかかることがあります。  ノード タイプの以前のノードが無効になると、システム サービスとシード ノードによって、プライマリ ノード タイプに含まれる新しいスケール セットの VM は移行されます。
6. プライマリ ノード タイプから以前のスケール セットを削除します。 (手順 5 のようにノードを無効にした後、Azure portal の [仮想マシン スケール セット] ブレードで、種類が古いノードを 1 つずつ割り当て解除します)。
7. 以前のスケール セットに関連付けられているロード バランサーを削除します。 新しいパブリック IP アドレスとロード バランサーが新しいスケール セット用に構成されている間、クラスターは使用できません。  
8. 以前のプライマリ ノード タイプのスケール セットに関連付けられたパブリック IP アドレスの DNS 設定を変数に格納し、そのパブリック IP アドレスを削除します。
9. 新しいプライマリ ノード タイプのスケール セットに関連付けられたパブリック IP アドレスの DNS 設定を、削除されたパブリック IP アドレスの DNS 設定に置き換えます。  これで、クラスターに再びアクセスできるようになります。
10. クラスターからノードのノード状態を削除します。  以前のスケール セットの持続性レベルがシルバーまたはゴールドの場合、この手順はシステムによって自動的に実行されます。
11. 前の手順でステートフル アプリケーションをデプロイした場合は、そのアプリケーションが機能していることを確認します。

## <a name="set-up-the-test-cluster"></a>テスト クラスターをセットアップする

最初に、このチュートリアルに必要な 2 つのファイル セット (事前[テンプレート]()と[パラメーター]()、および事後[テンプレート]()と[パラメーター]()) をダウンロードします。

次に、Azure アカウントにサインインします。

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

このチュートリアルでは、自己署名証明書を作成するシナリオについて説明します。 Azure Key Vault の既存の証明書を使用するには、次の手順をスキップし、代わりに[既存の証明書を使用したクラスターのデプロイ](./upgrade-managed-disks.md#use-an-existing-certificate-to-deploy-the-cluster)に関する記事の手順を反映させます。

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>自己署名証明書を生成してクラスターをデプロイする

まず、Service Fabric クラスターのデプロイに必要な変数を割り当てます。 `resourceGroupName`、`certSubjectName`、`parameterFilePath`、および `templateFilePath` の値を、特定のアカウントと環境に合わせて調整します。

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> コマンドを実行して、新しい Service Fabric クラスターをデプロイする前に、`certOutputFolder` の場所がローカル コンピューターに存在することを確認してください。

次に、*Deploy-2NodeTypes-2ScaleSets.parameters.json* ファイルを開き、PowerShell で設定した動的な値に対応するように `clusterName` と `dnsName` の値を調整し、変更を保存します。

次に、Service Fabric テスト クラスターをデプロイします。

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

デプロイが完了したら、ローカル コンピューターで *.pfx* ファイル (`$certPfx`) を見つけて、証明書ストアにインポートします。

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

この操作によって、証明書の拇印が返されます。これを使用して、新しいクラスターに接続し、その正常性状態を確認します。

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>新しいクラスターに接続して正常性状態を確認する

クラスターに接続し、そのすべてのノードが正常であることを確認します (`clusterName` と `thumb` の変数をお使いのクラスターに置き換えます)。

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

これで、アップグレード手順を開始する準備ができました。

## <a name="upgrade-the-primary-node-type-vms"></a>プライマリ ノード タイプの VM をアップグレードする

プライマリ ノード タイプの VM をアップグレードすることを決定した後、プライマリ ノード タイプに新しいスケール セットを追加して、プライマリ ノード タイプに 2 つのスケール セットが含まれるようにします。 必要な変更を示すために、サンプルの[テンプレート](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) ファイルと[パラメーター](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) ファイルが提供されています。 新しいスケール セットの VM は、サイズが Standard D4_V2 で、コンテナー搭載 Windows Server 2016 Datacenter を実行します。 新しいスケール セットと共に新しいロードバランサーとパブリック IP アドレスも追加されます。 

テンプレートで新しいスケール セットを見つけるには、vmNodeType2Name パラメーターに指定されている "Microsoft.Compute/virtualMachineScaleSets" リソースを探します。 新しいスケール セットは、properties->virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef 設定を使用してプライマリ ノード タイプに追加されます。

### <a name="deploy-the-updated-template"></a>更新したテンプレートをデプロイする

必要に応じて `parameterFilePath` と `templateFilePath` を調整し、次のコマンドを実行します。

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

デプロイが完了したら、クラスターの正常性を再度確認し、(元のスケール セットと新しいスケール セットの) すべてのノードが正常であることを確認します。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>ノードを新しいスケール セットに移行する

これで、元のスケール セットのノードの無効化を開始する準備ができました。 これらのノードが無効になると、システム サービスとシード ノードが、プライマリ ノードの種類としてもマークされているため、新しいスケール セットの VM に移行されます。

非プライマリ ノード タイプをスケールアップする場合、この手順では、サービス配置制約を新しい仮想マシン スケール セット/ノード タイプを含めるように変更してから、古い仮想マシン スケール セット インスタンスの数を、一度に 1 ノードずつ、0 まで減らします (ノードの削除がクラスターの信頼性に影響しないようにするため)。

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Service Fabric Explorer を使用して、新しいスケール セットへのシード ノードの移行と、元のスケール セットのノードの*無効にしています*から*無効*状態への進行状況を監視します。

> [!NOTE]
> 元のスケール セットのすべてのノードで無効化操作が完了するまでにいくらか時間がかかる場合があります。 データの整合性を確保するため、一度に変更できるシード ノードは 1 つだけです。 各シード ノードの変更には、クラスターの更新が必要であるため、シード ノードの交換には、2 回のクラスターのアップグレード (ノードの追加と削除のために 1 回ずつ) が必要です。 このサンプル シナリオで 5 つのシード ノードをアップグレードすると、10 回のクラスターのアップグレードが発生します。

## <a name="remove-the-original-scale-set"></a>元のスケール セットを削除する

無効化操作が完了したら、スケール セットを削除します。

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

Service Fabric Explorer では、削除されたノード (つまり、*クラスターの正常性状態*) が *エラー*状態で表示されるようになります。

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>古いロード バランサーを削除して DNS 設定を更新する

古いプライマリ ノード タイプに関連するリソースを削除できるようになりました。最初にロード バランサーと古いパブリック IP を削除します。 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

次に、新しいパブリック IP の DNS 設定を更新して、古いプライマリ ノード タイプのパブリック IP の設定を反映させます。

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

もう一度、クラスターの正常性を確認します。

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

最後に、関連する各ノードのノード状態を削除します。 古いスケール セットの持続性レベルがシルバーまたはゴールドの場合、これは自動的に実行されます。

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

これで、クラスターのプライマリ ノード タイプがアップグレードされました。 デプロイされたアプリケーションが正常に機能すること、クラスターの正常性に問題がないことを確認します。

## <a name="next-steps"></a>次のステップ
* [クラスターにノード タイプを追加する](virtual-machine-scale-set-scale-node-type-scale-out.md)方法について学習します。
* [アプリケーションのスケーラビリティ](service-fabric-concepts-scalability.md)について学習します。
* [Azure クラスターをスケールインまたはスケールアウト](service-fabric-tutorial-scale-cluster.md)します。
* fluent Azure コンピューティング SDK を使用して [Azure クラスターをプログラムでスケーリングします](service-fabric-cluster-programmatic-scaling.md)。
* [スタンドアロン クラスターのスケールインまたはスケールアウトします](service-fabric-cluster-windows-server-add-remove-nodes.md)。
