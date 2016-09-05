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
   ms.date="08/19/2016"
   ms.author="masnider"/>

# Service Fabric サービスの配置ポリシー
Service Fabric のクラスターが複数のデータセンターや Azure のリージョンなど、地理的に離れた場所に広がっている場合や、環境が地政学的な管理の異なる領域に広がっている場合 (もしくは法的、政策的な境界について考慮しなければならない場合や、こうした距離がパフォーマンス/待機時間に実際に影響を与える場合) には、他にも最終的に考慮する必要のあるさまざまなルールがあります。こうしたルールの多くはノード プロパティおよび配置の制約によって構成できますが、なかには非常に複雑なものもあります。物事をシンプルにするために、次の追加のコマンドを用意しました。他の配置の制約と同様に、配置ポリシーを名前付きサービス インスタンスごとに構成できます。

## 無効なドメインを指定する
invalidDomain 配置ポリシーでは、特定の障害ドメインがこのワークロードに対して無効になるように指定できます。地政学的または企業のポリシー上の理由などにより、特定の地域で特定のサービスが実行されないようにできます。無効なドメインは、別々のポリシーで複数指定できます。

![無効なドメインの例][Image1]

コード:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## 必要なドメインを指定する
required domain 配置ポリシーでは、指定したドメインにサービスのすべてのステートフル レプリカまたはステートレス サービス インスタンスが存在している必要があります。必要なドメインは、別々のポリシーで複数指定できます。

![必要なドメインの例][Image2]

コード:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## プライマリ レプリカの優先ドメインを指定する
優先プライマリ ドメインは変わったコントロールであり、プライマリが存在する必要がある障害ドメインの選択が許可されます (選択が可能な場合)。すべてが正常な場合、プライマリは最終的にこのドメインに配置されます。何らかの理由によりドメインまたはプライマリ レプリカで障害が発生した場合、またはシャットダウンした場合、プライマリは別の場所に移行します。この場所が優先ドメインではない場合、可能であれば、クラスター リソース マネージャーにより優先ドメインに戻されます。元来、この設定は、ステートフル サービスでのみ有効です。このポリシーは、Azure のリージョンまたは複数のデータセンターに分散しているクラスターに最適です。このような状況では、冗長性のためにすべての場所を使用するが、プライマリになる操作の待機時間を減らす目的で (プライマリは書き込みと、既定ではすべての読み込みにサービスを提供します) プライマリ レプリカを特定の場所に配置することが優先されます。

![優先プライマリ ドメインとフェールオーバー][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## すべてのドメイン間で配信することとパッキングを許可しないことをレプリカに要求する
指定できるもう 1 つのポリシーでは、利用可能な障害ドメイン間で常に配信されるようにレプリカに要求します。これは、クラスターが正常であるほとんどのケースで既定で実行されますが、特定のパーティションのレプリカが 1 つの障害またはアップグレード ドメインに一時的にパッキングされるという結果になる場合があります。たとえば、3 つの障害ドメイン (0、1、および 2) でクラスターに 9 個のノードが、サービスに 3 つのレプリカがあり、障害ドメイン 1 および 2 のこれらのレプリカに使用したノードが停止し、容量の問題でこれらのドメイン内の他のいずれのノードも有効にならなかったと仮定します。Service Fabric がこれらのレプリカの交換を構築した場合、クラスター リソース マネージャーはこれらを障害ドメイン 0 に配置する必要がありますが、これによって、障害ドメインの制約が違反される状況が発生します。また、レプリカ セット全体が失われる可能性も高まります (FD 0 が完全に失われた場合)。(制約と制約の優先順位の概要については、[このトピック](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)をご覧ください)

"Load Balancer がこのレプリカに対して制約違反を検出しました:fabric:/<サービス名> セカンダリ パーティション <パーティション ID> が制約: FaultDomain に違反しています" のような正常性の警告を受け取ったことがある場合は、この状況、または似た状況が既に発生しています。通常、このような状況は一過性ですが (ノードが長時間停止しない、または停止した場合は有効な正しい障害ドメインに他のノードがある交換を構築する必要がある)、可用性と引き換えにこれらすべてのレプリカを失った方が良いワークロードもあります。これを行うには RequireDomainDistribution ポリシーを指定します。これにより、同じパーティションからの 2 つのレプリカを同じ障害またはアップグレード ドメインで許可しないことが保証されます。

常に目標とする数のレプリカ (状態のコピー) を持つ必要のあるワークロードもあれば (ドメインのエラー合計数を信じて、通常、ローカルの状態を回復できるとわかっている)、正確性やデータ損失のリスク以前にダウンタイムを選んだ方が良いワークロードもあります。ほとんどの実稼働ワークロードは 3 つ以上のレプリカを使って実行されるため、既定ではドメイン配信が不要です。これにより一時的にドメインに複数のレプリカがパッキングされていても、負荷分散とフェールオーバーによってケースが対応されます。

コード:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

では、これらの構成を地理的に分散していないクラスターのサービスに構成することはできるでしょうか。 もちろん、できます。 しかし、あまり利点はありません。特に、必要な、無効な、および優先ドメインの構成は、実際に地理的に分散しているクラスターを実行していない場合は、避ける必要があります。特定のワークロードを単一のラックで実行したり、ローカル クラスターのセグメントを他のものより優先することは、異なる種類のハードウェアまたはワークロード セグメンテーションを使用しているのでない限り意味がありません。また、これらのケースは通常の配置の制約が対処します。

## 次のステップ
- サービスの構成に利用できるその他のオプションの詳細については、「[サービスの構成について学習する](service-fabric-cluster-resource-manager-configure-services.md)」にあるその他のクラスター リソース マネージャーに関するトピックを参照してください。

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0824_2016-->