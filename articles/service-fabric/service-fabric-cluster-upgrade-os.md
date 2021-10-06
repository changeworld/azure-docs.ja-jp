---
title: Azure Service Fabric の Linux OS のアップグレード
description: Azure Service Fabric クラスターを別の Linux OS に移行するためのオプションについて説明します。
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 8f52481e7c457445dc842e86f7b05c3568502da4
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278013"
---
# <a name="upgrading-linux-os-for-azure-service-fabric"></a>Azure Service Fabric の Linux OS のアップグレード

このドキュメントでは、Azure Service Fabric for Linux クラスターを Ubuntu バージョン 16.04 LTS から 18.04 LTS に移行するためのガイドについて説明します。 OS (オペレーティング システム) バージョンごとに異なる SF ランタイム パッケージが必要となり、移行を円滑に行うためにはこのドキュメントに説明されている手順が欠かせません。

## <a name="overview"></a>概要

一般的なアプローチは次のとおりです。

1. Service Fabric クラスターの ARM (Azure Resource Manager) リソース "vmImage" を "Ubuntu18_04" に切り替えて、この OS バージョン用の将来のコード アップグレードをプルします。 これにより、既存のノード タイプに対して OS の組み合わせが一時的に不釣り合いとなるため、ロールオーバーの安全性を確保するために、コードの自動アップグレード ロールアウトがブロックされます。

    * OS の移行中に SF クラスター コードの手動アップグレードを実行することは避けてください。 実行すると、古いタイプのノードが、人の介入を必要とする状態に陥る可能性があります。

2. クラスター内のノード タイプごとに、基本となる仮想マシン スケール セットの Ubuntu 18.04 OS イメージをターゲットにする別のノード タイプを作成します。 新しいノード タイプはそれぞれ対応する以前の役割を担います。

    * "isPrimary": true としてマークされた以前のノード タイプを置き換える新しいプライマリ ノード タイプを作成する必要があります。
    
    * その他プライマリ以外の各ノード タイプは、同様に "isPrimary": false とマークされます。

    * 新しいターゲット OS ノード タイプの作成後、既存のワークロードが引き続き正しく動作することを確認します。 問題が確認された場合は、アプリまたはプレインストールされたマシン パッケージに必要な変更を行ってから、以前のノード タイプを削除する手順に進んでください。
3. 以前のプライマリ ノード タイプを "isPrimary": false としてマークします。 これにより、すべてのシード ノードを移行するための、時間のかかる一連のアップグレードが実行されます。
4. (Bronze 持続性ノードタイプのみ): [sfctl](service-fabric-sfctl.md) / [PowerShell](/powershell/module/ServiceFabric) / [FabricClient](/dotnet/api/system.fabric.fabricclient) でクラスターに接続し、古いノード タイプのノードをすべて無効にします。
5. 以前のノード タイプを削除します。

> [!NOTE]
> 追加されたノード タイプには、Az PowerShell によって新しい DNS 名が生成されます。外部トラフィックは、このエンドポイントにリダイレクトされることが必要です。


## <a name="ease-of-use-steps-for-non-production-clusters"></a>非運用クラスターのための簡単な手順

> [!NOTE]
> 以下の手順は、Az PowerShell コマンドレットを使用したノード タイプ移行をテスト専用クラスターで手軽に試行する方法を示しています。 実際のビジネス トラフィックにさらされる運用環境のクラスターの場合は、再現性、そして宣言型の信頼できる情報源の一貫性を維持するために、同じ手順を ARM アップグレードによって行うことになるでしょう。

1. [Update-AzServiceFabricVmImage](/powershell/module/az.servicefabric/update-azservicefabricvmimage) を使用して Service Fabric クラスター リソースの vmImage 設定を更新します。

    [Azure PowerShell](/powershell/azure/install-az-ps):
    ```powershell
    # Replace subscriptionId, resourceGroup, clusterName with ones corresponding to your cluster.
    $subscriptionId="cea219db-0593-4b27-8bfa-a703332bf433"
    Login-AzAccount; Select-AzSubscription -SubscriptionId $subscriptionId

    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    # Update cluster vmImage to target OS. This registers the SF runtime package type that is supplied for upgrades.
    Update-AzServiceFabricVmImage -ResourceGroupName $resourceGroup -ClusterName $clusterName -VmImage Ubuntu18_04
    ```

2. 既存のノード タイプごとに、対応する新しいノード タイプを追加します。

    ```powershell
    $nodeTypeName="nt1u18"
    # You can customize this to fetch a password from a secure store.
    $securePassword = ConvertTo-SecureString -String 'Yourpassword123!@#' -AsPlainText -Force

    # Ensure last upgrade is done - Ready means the next command can be issued.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Add new primary node type. Omit the IsPrimaryNodeType parameter for non-primary node types.
    Add-AzServiceFabricNodeType -ResourceGroupName $resourceGroup  -ClusterName $clusterName -NodeType $nodeTypeName -Capacity 5 -VmUserName testuser -VmPassword $securePassword -DurabilityLevel Silver -Verbose -VMImageSku 18.04-LTS -IsPrimaryNodeType $true

    # Redirect traffic to new node type dns
    # dns-Contoso01SFCluster-nt1u18.westus2.cloudapp.azure.com
    ```

3. シード ノードとシステム サービスを新しいノード タイプにロールオーバーするために、以前のプライマリ ノード タイプを非プライマリに更新します。

    ```powershell
    # Query to ensure background upgrades are done.
    (Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup).ClusterState

    # Update old nodetype to isPrimary: false
    $oldNodeTypeName="nt1"
    Update-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -IsPrimaryNodeType $false -NodeType $oldNodeTypeName -Verbose

    # Ensure node type is showing isPrimary: False before proceeding.
    Get-AzServiceFabricCluster -ResourceGroupName $resourceGroup
    ```

    出力例:
    ```
    NodeTypes :
              NodeTypeDescription :
                  Name : nt1
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Bronze
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : False
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
              NodeTypeDescription :
                  Name : nt1u18
                  PlacementProperties :
                  Capacities :
                  ClientConnectionEndpointPort : 19000
                  HttpGatewayEndpointPort : 19080
                  DurabilityLevel : Silver
                  ApplicationPorts :
                      StartPort : 20000
                      EndPort : 30000
                  EphemeralPorts :
                      StartPort : 49152
                      EndPort : 65534
                  IsPrimary : True
                  VmInstanceCount : 5
                  ReverseProxyEndpointPort :
    ```

4. 以前のノード タイプを削除する手順に進む前に、Bronze 持続性ノード タイプを削除するために、まずノードを無効にします。 *ssh* でクラスター ノードに接続し、次のコマンドを実行します。

    ```bash
    # as root user:

    # install jq tool to automatically parse JSON responses
    apt-get install jq -fy

    # retrieve the thumbprint to be used for establishing a fabric client
    dataroot=$(cat /etc/servicefabric/FabricDataRoot)
    nodename=_nt1_0
    cat $dataroot/$nodename/Fabric/ClusterManifest.current.xml | grep ClientCertThumbprints
    # 0777FE1A43E306F332D96DA339EF6834D0E4A453

    # verify node count
    sfctl cluster select --endpoint https://Contoso01SFCluster.westus2.cloudapp.azure.com:19080 --pem /var/lib/waagent/0777FE1A43E306F332D96DA339EF6834D0E4A453.pem --no-verify

    # sample command to list all nodes
    sfctl node list

    # for each node part of the node type to be removed, disable the node:
    nodeTypeBeingDisabled=nt1
    nodes=$(sfctl node list | jq --arg nodeTypeBeingDisabled "$nodeTypeBeingDisabled" '.items[] | select(.type==$nodeTypeBeingDisabled) | .name' | sed s/\"//g)
    echo $nodes
    for n in $nodes; do echo "Disabling $n"; sfctl node disable --node-name $n --deactivation-intent RemoveNode --timeout 300; done
    ```

5. SF クラスター リソースのノード タイプ属性を削除し、関連する仮想マシン スケール セットとネットワーク リソースの使用を停止して、以前のノード タイプを削除します。

    ```powershell
    $resourceGroup="Group1"
    $clusterName="Contoso01SFCluster"
    $oldNodeTypeName="nt1"

    # Remove the Service Fabric node type, associated virtual machine scale set resource, and any trailing networking resources that are no longer used. 
    Remove-AzServiceFabricNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -NodeType $oldNodeTypeName
    ```

    > [!NOTE]
    > 一部のケースで、以下のエラーが発生する場合があります。 このような場合、Service Fabric Explorer (SFX) で見ると、削除されたノード タイプの InfrastructureService がエラー状態になっている可能性があります。 これを解決するには、削除操作を再試行してください。
    ```
    Remove-AzServiceFabricNodeType : Code: ClusterUpgradeFailed, Message: Long running operation failed with status 'Failed'
    ```

ワークロードが新しいノード タイプに正しく移行され、以前のノード タイプが消去されたことが確認されたら、クラスターは、後続の Service Fabric ランタイム コードのバージョンおよび構成のアップグレードに進むことができます。

## <a name="next-steps"></a>次のステップ

* [Service Fabric のアップグレードの管理](service-fabric-cluster-upgrade-version-azure.md)
* [Service Fabric クラスター設定](service-fabric-cluster-fabric-settings.md)のカスタマイズ
* クラスターの[耐久性の特性](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)についてさらに学習します。
* [ノード タイプと Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md) についてさらに学習します。
* [Service Fabric クラスターのスケーリング](service-fabric-cluster-scaling.md)についてさらに学習します。
* [クラスターのスケールイン/アウト](service-fabric-cluster-scale-in-out.md)
* [Azure Service Fabric でノード タイプを削除する](service-fabric-how-to-remove-node-type.md)

