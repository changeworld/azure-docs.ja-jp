<properties
   pageTitle="Service Fabric クラスターのスケールアップとスケールダウン | Microsoft Azure"
   description="ノードの種類/VM スケール セットごとに自動スケール ルールを設定し、Service Fabric クラスターを需要に合わせてスケールアップまたはスケールダウンします。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="chackdan"/>


# 自動スケール ルールを使用した Service Fabric クラスターのスケールアップとスケールダウン

仮想マシン スケール セットは、セットとして仮想マシンのコレクションをデプロイおよび管理するために使用できる Azure コンピューティング リソースです。Service Fabric クラスターで定義されているすべてのノードの種類は、個別の VM スケール セットとしてセットアップされます。各ノードの種類は、個別にスケール アップまたはスケール ダウンすることができ、さまざまなセットのポートを開き、異なる容量のメトリックスを持つことができます。詳細については、[Service Fabric のノードの種類](service-fabric-cluster-nodetypes.md)に関するドキュメントを参照してください。クラスター内の Service Fabric のノードの種類はバックエンドの VM スケール セットで構成されるため、ノードの種類/VM スケール セットごとに自動スケール ルールを設定する必要があります。

>[AZURE.NOTE] サブスクリプションに、このクラスターを構成する新しい VM を追加できるだけのコア数が割り当てられている必要があります。現時点ではモデルの検証はないため、いずれかのクォータの制限に達した場合、デプロイ時のエラーが表示されます。

## スケールするノードの種類/VM スケール セットの選択

現在、ポータルを使用して VM スケール セットの自動スケール ルールを指定することはできないため、Azure PowerShell (1.0 以降) を使用して、ノードの種類を一覧表示し、それらに自動スケール ルールを追加します。

クラスターを構成する VM スケール セットの一覧を取得するには、次のコマンドレットを実行します。

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## ノードの種類/VM スケール セットの自動スケール ルールの設定

クラスターに複数のノードの種類がある場合は、スケール (アップまたはダウン) するノードの種類/VM スケール セットごとにこれを行う必要があります。自動スケールを設定する前に、必要なノードの数を考慮します。ノードの種類がプライマリである場合に必要なノードの最小数は、選択した信頼性のレベルによって決まります。信頼性のレベルの詳細については、[こちら](service-fabric-cluster-capacity.md)を参照してください。

>[AZURE.NOTE]  ノードの種類がプライマリである場合に前述の最小数未満にスケールダウンすると、クラスターが不安定になるか、停止します。これにより、アプリケーションおよびシステム サービスのデータが失われる可能性があります。

現在、自動スケール機能は、アプリケーションによって Service Fabric に報告されている場合がある負荷では駆動されません。したがって現時点では、取得する自動スケールは、各 VM スケール セット インスタンスによって出力されるパフォーマンス カウンターのみで駆動されます。

VM スケール セットごとに自動スケールを設定するには、[この手順](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)に従います。

>[AZURE.NOTE] スケールダウン シナリオでは、ノードの種類に Gold または Silver の耐久性レベルがない限り、適切なノードの名前を指定して、[Remove-ServiceFabricNodeState cmdlet](https://msdn.microsoft.com/library/azure/mt125993.aspx) を呼び出す必要があります。

## Service Fabric Explorer で確認できる動作

クラスターをスケールアップすると、Service Fabric Explorer に、クラスターの一部であるノード (VM スケール セット インスタンス) の数が反映されます。ただし、クラスターをスケールダウンすると、適切なノードの名前を指定して [Remove-ServiceFabricNodeState cmd](https://msdn.microsoft.com/library/mt125993.aspx) を呼び出さない限り、削除されたノード/VM インスタンスが異常状態を示したまま引き続き表示されます。

この動作についての説明を次に示します。

Service Fabric Explorer で示されているノードは、現在/過去におけるクラスターのノード数について、Service Fabric システム サービス (具体的には FM) が認識している内容が反映されたものです。VM スケール セットをスケールダウンした場合、VM が削除された一方で、FM システム サービスはノード (削除された VM にマップされていたもの) が復元されると認識したままとなります。そのため、(正常性状態がエラーまたは不明になる場合でも) Service Fabric Explorer はそのノードを表示し続けます。

VM が削除されたときに、ノードが削除されるようにするには、2 つのオプションを使用できます。

1) クラスターのノードの種類に Gold または Silver (近日利用開始予定) の耐久性レベルを選択します。これにより、インフラストラクチャの統合が提供されます。これによって、スケールダウンしたときに、システム サービス (FM) の状態から自動的にノードが削除されます。[耐久性レベルの詳細](service-fabric-cluster-capacity.md)に関する記事を参照してください。

2) VM インスタンスがスケールダウンされたら、[Remove-ServiceFabricNodeState コマンドレット](https://msdn.microsoft.com/library/mt125993.aspx)を呼び出す必要があります。

>[AZURE.NOTE] Service Fabric クラスターが可用性を維持し、状態を保持するには、一定数のノードが常にアップしている必要があります。これは、「維持クォーラム」と呼ばれます。そのため、先に[状態の完全バックアップ](service-fabric-reliable-services-backup-restore.md)を実行していた場合を除き、クラスター内のすべてのコンピューターをシャットダウンするのは一般に安全ではありません。

## 次のステップ
次を確認して、クラスター容量の計画、クラスターのアップグレード、およびサービスのパーティション分割についても学習してください。

- [クラスター容量の計画](service-fabric-cluster-capacity.md)
- [クラスターのアップグレード](service-fabric-cluster-upgrade.md)
- [最大のスケールに対応するためのパーティションのステートフル サービス](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

<!---HONumber=AcomDC_0608_2016-->