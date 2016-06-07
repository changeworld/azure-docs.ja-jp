<properties
   pageTitle="Service Fabric クラスター リソース マネージャー - 配置ポリシー | Microsoft Azure"
   description="Service Fabric サービスの追加の配置ポリシーとルールの概要"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/20/2016"
   ms.author="masnider"/>

# Service Fabric サービスの配置ポリシー
Service Fabric のクラスターが複数のデータセンターや Azure のリージョンなど、地理的に離れた場所に広がっている場合や、環境が地政学的な管理の異なる領域に広がっている場合 (もしくは法的、政策的な境界について考慮しなければならない場合や、こうした距離がパフォーマンス/待機時間に実際に影響を与える場合) には、ほかにも最終的に考慮する必要のあるさまざまなルールがあります。こうしたルールの多くはノード プロパティおよび配置の制約によって構成できますが、なかには非常に複雑なものもあります。どのような場合でも、配置の制約と同様に次のようなショートカットが提供されており、配置ポリシーを名前付きサービス インスタンスごとに構成できます。

## 無効なドメインを指定する
invalidDomain 配置ポリシーでは、特定の障害ドメインがこのワークロードに対して無効になるように指定できます。地政学的または企業のポリシー上の理由などにより、特定の地域で特定のサービスが実行されないようにできます。無効なドメインは複数指定できます。

![無効なドメインの例][Image1]

コード:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```
## 必要なドメインを指定する
required domain 配置ポリシーでは、指定したドメインにすべてのレプリカが存在している必要があります。複数の必要なドメインを指定できます。

![必要なドメインの例][Image2]

コード:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## プライマリ レプリカの優先ドメインを指定する
優先 Primary domain は変わったコントロールであり、プライマリが存在する必要がある障害ドメインの選択が許可されます (選択が可能な場合)。すべてが正常な場合、プライマリは最終的にこのドメインに配置されます。何らかの理由によりドメインまたはプライマリ レプリカで障害が発生した場合、またはシャットダウンした場合、プライマリは別の場所に移行します。戻れるようになったら、優先ドメインに戻ります。元来、この設定は、ステートフル サービスでのみ有効です。このポリシーは、冗長性のために他の場所を使用するが、プライマリになる操作の待機時間を減らす目的で (プライマリは書き込みと、既定ではすべての読み込みにサービスを提供します) プライマリ レプリカを特定の場所に配置する場合に特に役立ちます。

![優先プライマリ ドメインとフェールオーバー][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUs/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUs")
```

## すべてのドメイン間で配信することとパッキングを許可しないことをレプリカに要求する
指定できるもう 1 つのポリシーでは、利用可能なドメイン間で常に配信されるようにレプリカに要求します。上の例では、一時的に複数になっているレプリカ (つまり、クォーラム) を同じデータセンターにまとめることができます。この場合、プライマリがほとんどのレプリカとは異なるデータセンターにあるため、実際にはおそらく問題ありません。ただし、一時的に望ましくない構成になってしまう可能性があります。これはその時のクラスターの状態によっても変わりますが、レプリカを常に別のドメインに置くようにすることで、完全にこの状況を回避することもできます。これを選択した場合、残りのノードで引き続き実行されることをサービスに許可するより、ダウンタイム/障害を選択することになります。RequireDomainDistribution ポリシーによってこの動作が可能になりますが、パッキングが発生する場合に、レプリカがまったく配置されない場合もあります。つまり、実行可能なドメインが復帰するまで、実行されるレプリカの数が一時的に目標よりも少なくなります。我々は常に目標とする数のレプリカ (状態のコピー) を希望する顧客も見てきましたが、その一方で可用性をリスクにさらしたくないと考える顧客もいました。レプリカを障害ドメインにパッキングし、そのドメインで障害が発生したら、そのすべてのレプリカが同時に使えなくなってしまいます。レプリカの数が少ない場合は、これによってクォーラムの損失が生じる場合があります。ドメインが戻ってこない事態が発生した場合、データが失われる可能性があります。多くの人は、3 つ以上のレプリカを使って実行するため、既定ではドメイン配信が不要です。これにより一時的にドメインに複数のレプリカがパッキングされていても、負荷分散とフェールオーバーによってケースが対応されます。

コード:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

では、これらの構成を地理的に分散していないクラスターのサービスに構成することはできるでしょうか。 もちろん、できます。 しかし、あまり利点はありません。特に、必要な、無効な、および優先ドメインの構成は、実際に地理的に分散しているクラスターを実行していない場合は、避ける必要があります。

## 次のステップ
- サービスの公正で利用できるその他のオプションに関する詳細については、「[サービスの構成について学習する](service-fabric-cluster-resource-manager-configure-services.md)」にあるその他のクラスター リソース マネージャーに関するトピックを参照してください。

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0525_2016-->