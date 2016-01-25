<properties
   pageTitle="リソース バランサーのサービス記述"
   description="リソース バランサーで使用するサービス記述の構成の概要 | Microsoft Azure"
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
   ms.date="09/15/2015"
   ms.author="masnider"/>

# サービス記述の概要

## 配置の制約
サービスにおける[配置の制約](service-fabric-placement-constraint.md)とは、特定のサービス インスタンスで必要なノードのプロパティを選択するために使用されるメカニズムのことです。これらはノードのプロパティと同じように、プロパティ名と値に対するサービスの要件が記述されたキーと値のペアで構成されます。個々のステートメントは単純なブール型のロジックを使用してグループ化して、必要な制約を作成できます。Service Fabric リソース バランサーは制約を解釈します。

サービスの配置の制約は、サービス マニフェストまたはアプリケーション マニフェストを使用して定義するか、コード内で直接定義できます。

サービス マニフェストでは `ServiceTypes` の定義を指定します。

``` xml
<ServiceTypes>
    <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="false" >
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulServiceType>
</ServiceTypes>

```

アプリケーション マニフェストでは `ServiceTemplates` または `DefaultServices` の定義を指定します。

``` xml
<ServiceTemplates>
    <StatefulService ServiceTypeName="QueueReplica">
      <SingletonPartition></SingletonPartition>
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulService>
  </ServiceTemplates>

  <DefaultServices>
    <Service Name="QR">
      <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueReplica" TargetReplicaSetSize="3">
        <SingletonPartition/>
        <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
      </StatefulService>
    </Service>
  </DefaultServices>
```

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true  && SpindleCount >= 4)";
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

次の図は、クラスターに含まれるノードのうち、このサービスを配置できるノードが N5 のみであることを示しています。

![Placement constraints][Image1]

特定のノードで定義されていないプロパティに対してサービスが制約を指定した場合、指定した値に関係なくそのノードは一致と見なされません。

## サービス アフィニティ
サービス アフィニティは特殊な形式の配置の制約で、さまざまな理由から相互にローカルな依存関係を持つ別々のサービスを記述できます。つまり、これらのサービスが機能するには、両方が同じノードで実行されている必要があります。このようなアーキテクチャは Service Fabric のアプリケーションでは一般的ではありませんが、Service Fabric では従来型のアプリケーションの過渡期にあるアーキテクチャの一種と認識されているため、使用できる機能となっています。

2 つのサービス間でサービス アフィニティを確立することにより、これらのサービスのプライマリ レプリカは常に同じノードに併置されます。

アフィニティの関係は親子階層で表されます。つまり、"親" と "子" が存在します。 最初に確立されるサービスが親、次に確立されるサービスが子になります。この関係性は、"厳密な" 制約としてモデル化されます。

現在、サービス アフィニティには次のような制限があります。

- ステートレス サービスとステートフル サービスの間では使用できません。
- インスタンス数が異なるステートレス サービス間では使用できません。たとえば、2 つのステートレス サービスが作成されるとき、両方に同じ InstanceCount プロパティが必要です。
- レプリカの数が異なる可変または永続的なステートフル サービス間では使用できません。たとえば、両方のサービスの TargetReplicaSetSize と MinReplicaSetSize に同じ値が指定されている必要があります。
- パーティション分割されたサービスでは使用できません。各サービスに FABRIC\_PARTITION\_SCHEME\_SINGLETON パーティション構成が必要です。
- アフィニティの関係は、このサービス記述の他のプロパティと同じように、作成されたサービスを変更できない場合に設定されます。
- サービスのチェーンは許可されません。複数のサービスにアフィニティの関係を作成する必要がある場合は、"スター型" のモデルを使用する必要があります。

``` xml
<ServiceTemplates>
  <StatelessService ServiceTypeName="StatelessCalculatorService" InstanceCount="5">
    <SingletonPartition></SingletonPartition>
      <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
  </StatelessService>
</ServiceTemplates>
```

次のコード例は、DefaultServices の定義を代わりに使用する方法を示します。

``` xml
<DefaultServices>
  <Service Name="childService">
    <StatelessService InstanceCount="3" ServiceTypeName="calculatorService">
      <SingletonPartition/>
     <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
    </StatelessService>
  </Service>
</DefaultServices>
```

次のコード例は、上位アプリケーションを作成した後に、アフィニティの関係を作成する方法を示します。

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## リソース分散メトリック
サービスの配置の制約やノードのプロパティが厳密なルールとサービスの有効な配置を示すポリシーを表す一方で、リソース分散メトリックは一般的にクラスター内のサービスの最適な配置を示すのに役立ちます。サービスによって定義されているメトリックには、クラスター内での使用方法を示すいくつかのプロパティがあります。

### 既定のメトリックの使用
まず、リソース メトリックの取り扱いはサービスにとって完全なオプションであることと、既定では Service Fabric リソース バランサーはサービスやクラスターの管理者の介在なしに "組み込み" のシステム メトリックの一部を使用して基本的なリソース分散を実行することを理解する必要があります。Service Fabric リソース バランサーがクラスター内で追跡して分散する既定のメトリックとは、PrimaryCount、ReplicaCount、Count です。PrimaryCount はノード上のステートフル サービスのプライマリ レプリカの数です。ReplicaCount はノード上のすべてのステートフル レプリカの数です。Count はステートレス サービスとステートフル サービスの両方を含む、ノード上のすべてのサービス オブジェクトの合計数です。一般的に、PrimaryCount メトリックが最も重要と見なされ、次のセクションで説明するように重みが最も高くなっています。PrimaryCount メトリックの後に ReplicaCount メトリック、Count メトリックと続きます。

次の図は、それぞれ 3 つのレプリカを持つ 3 つのステートフル サービスの既定の分散の例を示します。

![Default metrics][Image2]

一般的に多くのサービスにとっては既定のメトリックで十分であるため、追加のメトリックや機能について特別な要件がない限りは既定のメトリックを使用することをお勧めします。

### カスタム メトリックの定義
あるサービスにとって既定のメトリックでは十分でない場合、つまりそのサービスにディスク領域やメモリなどのリソースについて既知の要件がある場合は、カスタム メトリックを使用してください。カスタム メトリックは、サービスに競合の発生を抑えるために分散する必要があるリソースが複数存在する場合に便利です。これらのメトリックの使用は、クラスター内のレプリカ間で大きく異なる場合があります。たとえば、あるプライマリがメトリックの 100% を使用するのに対し、別のプライマリが 20% しか使用しない場合などです。また、カスタム メトリックは、メモリ、ディスク領域、接続など、コンピューター上で厳密に制限されているリソースをキャプチャする場合にも便利です。追加のカスタム メトリックを作成すると便利なこともあります。たとえば、"現在の未処理のトランザクション" メトリックは、ノード上で上限がないメトリックをキャプチャまたは示す一方で、サービスの観点から作業やリソースの消費量を示します。このメトリックは指定のノード上の "容量" を制限しない可能性がありますが、このメトリックをクラスター全体に均等に分散することでパフォーマンス向上のメリットが得られます。

サービスでカスタム メトリックを定義すると、システムの既定のメトリックは使用されなくなります。既定のメトリックを使用するには、作成時にサービスのメトリックの一覧に再度明示的に追加する必要があります。

### メトリックの名前
カスタム メトリックは、サービスを作成するときにメトリック名を定義するだけで作成できます。Service Fabric リソース バランサーはメトリックを名前で関連付けます。メトリックをノードの定義やサービス間で容量として使用する場合、Service Fabric リソース バランサーが関連付けられるようにメトリックの名前が正確に一致している必要があります。

### メトリックの重み
サービスで複数のメトリックを定義する際に、メトリックの重みも定義すると便利です。メトリックに重みを設定すると、サービスの機能にとってより重要なメトリックを Service Fabric リソース バランサーに対して指定できます。たとえば、メモリ内キューはネットワーク帯域幅の影響を受けることもありますが、ノード上の実際のメモリ使用量の影響をより強く受けます。このため、キュー内のメトリックのうち、メモリ使用量を示すメトリックに最も高い重みが設定されます。同様に、永続データベースはメモリ使用量とディスク使用量の両方の影響を受けます。限られたメモリにより複雑なクエリの処理機能が低下すると同時に、ディスク領域の不足によりストレージ操作がさらに妨害されるため、より重大な状況になる可能性があります。このため、永続ストアでは、ディスク領域が、この 2 つの重みがより高いメトリックとして選択されます。

重みが Service Fabric リソース バランサー内で使用されるのは、一連のメトリックを完全に分散できない場合のみです。このため、あるメトリックを他のメトリックと比較したときに全体的な分散が損なわれることのない解決策を見つける必要があります。この場合、重みが低いメトリックの分散は重みの高いメトリックと比較して優先度が低いと見なされるため、Service Fabric リソース バランサーは、重みがより高いメトリックを優先して分散するように提案します。

また、複数のサービスで同じ名前のメトリックが定義されると、Service Fabric リソース バランサーはそれらのメトリックを同じメトリックと見なします。同じメトリックで重みが異なる場合、リソース バランサーはそれらの重みの平均を計算して、実行時に実際に使用する重みを決定します。このため、1 つのサービスで重みをゼロに設定すると分散は不要になりますが、別のサービスで同じメトリックに高い重みを定義すると、実際の重みはその中間になります。

![Metric weights][Image3]

この例では、メトリックの重みが 2 種類の場合に、優先度が高いメトリックが全体に分散されて、異なる分散結果につながるシナリオを示します。上の図の左側の例では B がより適切に分散されていますが、右側の例では A がより適切に分散されています。この例では両方のメトリックが分散される結果となる解決策がないため、Service Fabric リソース バランサーはメトリックの重みを使用してより重要なメトリック、つまりどちらのメトリックが優先的に分散されるかを決定します。

メトリックの重みは、サービスでメトリックを追跡するもののそのメトリックに基づく分散が不要な場合にゼロに設定されます。たとえば、複数のノード上で制限が明示されているメトリックの場合、(通常のリソース分散が保証する) "ノード間での使用量を均等にする" 機能は重要ではありません。このようなメトリックでは、リソースの分散を必要としませんが、ノード上に設定された制限を適用する必要があるため、分散の重みをゼロに定義できます。実行時に、Service Fabric リソース バランサーでは設定された制限を適用し、メトリックの使用がノード間で著しく偏っている場合でもそのメトリックの分散を積極的には行いません。

### プライマリまたはセカンダリ ロールのメトリックの既定の読み込み
サービスがプライマリまたはセカンダリのどちらかのロールにある場合、そのサービスのメトリックを定義する際に、予期される消費量を指定する必要があります。この情報はリソース バランサーによるサービスの初期配置を効率化するだけでなく、サービスの有効期間中にそのサービスが関連付けられているメトリックの実際の使用量を予測するのに役立ちます。Service Fabric リソース バランサーは、サービスを配置するときだけでなく、クラスター内での分散やその他の変更のためにレプリカを移動する必要がある場合はいつでも、このメトリックの消費量を自動的に考慮します。サービスの負荷が比較的予測可能で安定している場合、これらの値を設定するとサービスの実行中にレポートの読み込みを無効にできます。

次のコード例は、メモリ使用量とディスク領域に 1 つずつカスタム メトリックを完全に定義したサービスを示します。

``` cpp
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.Weight = ServiceLoadMetricWeight.High;
memoryMetric.PrimaryDefaultLoad = 100;
memoryMetric.SecondaryDefaultLoad = 50;

ServiceLoadMetricDescription diskMetric = new ServiceLoadMetricDescription();
diskMetric.Name = "DiskInMb";
diskMetric.Weight = ServiceLoadMetricWeight.Medium;
diskMetric.PrimaryDefaultLoad = 1024;
diskMetric.SecondaryDefaultLoad = 750;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(diskMetric);

Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

次のコード例は、サービス マニフェストによる ServiceTypes の定義を示します。

``` xml
<ServiceTypes>
  <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="true">
    <LoadMetrics>
      <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
      <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
    </LoadMetrics>
  </StatefulServiceType>
</ServiceTypes>
```

次のコード例は、アプリケーション マニフェストによる ServiceTemplates の定義を示します。

``` xml
<ServiceTemplates>
   <StatefulService ServiceTypeName="QueueReplica">
     <SingletonPartition></SingletonPartition>
     <LoadMetrics>
       <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
       <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
     </LoadMetrics>
   </StatefulService>
 </ServiceTemplates>
```
次のコード例は、アプリケーション マニフェストによる DefaultServices の定義を示します。

``` xml
<DefaultServices>
  <Service Name="QueueServiceInstance">
    <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueService" TargetReplicaSetSize="3">
      <SingletonPartition/>
      <LoadMetrics>
        <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
        <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
      </LoadMetrics>
    </StatefulService>
  </Service>
</DefaultServices>
```

既定の読み込み値は、コードを使用してサービス実行中のレポートの読み込みを具体的に有効にしない限り更新されないため、"容量を予約する" リソース分散モデルの確立にも使用できます。たとえば、ワークロードが一般的にいくつかのサイズのバケットに分類され、既知の数の作業単位をノードにいつでも配置できる場合は、カスタム メトリック "単位" を作成し、単位に対してノード容量とサービスの既定の負荷を定義できます。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

詳細については、「[リソース バランサー アーキテクチャの概要](service-fabric-resource-balancer-architecture.md)」と「[配置の制約の概要](service-fabric-placement-constraint.md)」を参照してください。

[Image1]: media/service-fabric-resource-balancer-service-description/PC.png
[Image2]: media/service-fabric-resource-balancer-service-description/DM.png
[Image3]: media/service-fabric-resource-balancer-service-description/MW.png

<!---HONumber=AcomDC_0114_2016-->