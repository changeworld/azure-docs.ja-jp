<properties
   pageTitle="Service Fabric クラスター リソース マネージャー - 配置ポリシー"
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
   ms.date="03/03/2016"
   ms.author="masnider"/>

# 配置ポリシー
Service Fabric のクラスターが複数のデータセンターや Azure のリージョンなど、地理的に離れた場所に広がっていたり、環境が地政学的な管理の異なる領域に広がっていたり (もしくは法的、政策的な境界について考慮しなければならない) する場合、最終的に考慮する可能性があるルールが多数あります。これらの多くはノード プロパティおよび (先に述べた) 配置の制約によって構成できますが、なかには非常に複雑なものもあります。どの場合でも、配置の制約と同様に次のようなショートカットが提供されており、配置ポリシーをサービスごとに構成できます。

-	InvalidDomain – 特定の障害ドメインがこのワークロードに対して無効になるように指定できます。地政学的または企業のポリシー上の理由などにより、特定の地域で特定のサービスが実行されないようにする場合に適しています。

![無効なドメインの例][Image1]

コード:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```

-	RequiredDomain – 読んで字のごとくで、指定したドメインにすべてのレプリカが存在している必要があります。

![必要なドメインの例][Image2]

コード:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

-	PreferPrimaryDomain – 優先プライマリ ドメインは興味深いコントロールで、プライマリが存在する必要がある障害ドメインの選択が許可されます (選択が可能な場合)。すべてが正常な場合、プライマリは最終的にこのドメインに配置されます。何らかの理由によりドメインまたはプライマリ レプリカで障害が発生した場合、またはシャットダウンした場合、プライマリは別の場所に移行します。戻れるようになったら、優先ドメインに戻ります。元来、この設定は、ステートフル サービスでのみ有効です。

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

-	RequireDomainDistribution – ドメイン要求配信は、いくつかの状況を回避するために使用できるもう 1 つのオプションです。上の例では、一時的に複数になっているレプリカ (つまり、クォーラム) を同じデータセンターにまとめることができます。この場合、プライマリがほとんどのレプリカとは異なるデータセンターにあるため、実際にはおそらく問題ありません。ただし、(本題からは逸れますが) 一時的に望ましくない構成になってしまう可能性があります。これはその時のクラスターの状態によっても変わりますが、レプリカを常に別のドメインに置くようにすることで、完全にこの状況を回避することもできます。RequireDomainDistribution フラグによって、これが可能になります。ただし、パッキングが発生する場合に、レプリカがまったく配置されない場合もあります。つまり、実行可能なドメインが復帰するまで、実行されるレプリカの数が一時的に目標よりも少なくなります。我々は常に目標とする数のレプリカ (状態のコピー) を希望する顧客も見てきましたが、その一方で可用性をリスクにさらしたくないと考える顧客もいました。レプリカを障害ドメインにパッキングし、そのドメインで障害が発生したら、そのすべてのレプリカが同時に使えなくなってしまいます。レプリカの数が少ない場合は、これによってクォーラムの損失が生じる場合があります。多くの人は、4 つ以上のレプリカを使って実行するため、既定ではドメイン配信が不要です。これにより一時的にドメインに複数のレプリカがパッキングされていても、負荷分散とフェールオーバーによってケースが対応されます。

コード:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

無効な、または必要なドメインは 1 つのサービスに複数構成できますが、それ以外の 2 つ (優先ドメインとドメイン配信) は、サービスごとに 1 回しか構成できません。

では、これらの構成を地理的に分散していないクラスターのサービスに構成することはできるでしょうか。 もちろん、できます。 しかし、あまり利点はありません。特に、必要な、無効な、および優先ドメインの構成は、実際に地理的に分散しているクラスターを実行していない場合は、避ける必要があります。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
- [Learn about configuring Services (サービスの構成について)](service-fabric-cluster-resource-manager-configure-services.md)



[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0309_2016-->