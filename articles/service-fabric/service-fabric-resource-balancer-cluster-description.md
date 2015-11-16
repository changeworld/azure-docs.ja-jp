<properties
   pageTitle="リソース バランサー クラスターの記述 | Microsoft Azure"
   description="障害ドメイン、アップグレード ドメイン、ノードのプロパティ、ノード容量をリソース バランサーに指定し、Service Fabric クラスターを記述します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Service Fabric クラスターの記述

Service Fabric のリソース バランサーは、クラスターを記述する複数のメカニズムを提供します。実行時に、リソース バランサーは、この情報を使用して、クラスター リソースの最大使用率を維持しながら、クラスターで実行中のサービスの高可用性を保証する方法でサービスを確実に配置します。クラスターを記述するリソース バランサー機能には、障害ドメイン、アップグレード ドメイン、ノードのプロパティ、およびノード容量があります。さらに、リソース バランサーには、パフォーマンスを調整できるいくつかの構成オプションがあります。

## 主要な概念

### 障害ドメイン:

障害ドメインによって、クラスター管理者は、電源やネットワーク ソースなどの共有される物理的な依存関係のために、同時に障害が発生する可能性のある物理ノードを定義できます。一般に、障害ドメインは、障害ドメイン ツリーのより高い観点から共に障害が発生する可能性が高い追加のノードと共に、共有される依存関係に関連する階層を表します。次の図は、データ センター、ラック、ブレードの順で、階層障害ドメインを使用して編成された複数のノードを示しています。

![障害ドメイン][Image1]

 実行時に、Service Fabric リソース マネージャーは、クラスター内の障害ドメインを考慮に入れ、すべてが異なる障害ドメインに属するように、特定のサービスに対するレプリカを分散させようとします。このプロセスは、1 つのフォールト ドメインでの障害発生時に、そのサービスの可用性と状態が損なわれないようにするために役立ちます。次の図では、1 つまたは 2 つだけのドメインに集中させる十分な領域があるにもかかわらず、複数の障害ドメインに分散されるサービスのレプリカを示します。

![障害ドメイン][Image2]

障害ドメインは、クラスター マニフェスト内で構成されます。各ノードは、特定のフォールト ドメイン内に定義されます。実行時に、リソース マネージャーは、すべてのノードからレポートを結合して、システム内のすべての障害ドメインの正確な概要を作成します。

### アップグレード ドメイン

アップグレード ドメインは、リソース マネージャーによって使用される別の情報です。障害ドメインのように、アップグレード ドメインは、ほぼ同時に、アップグレードのためにシャットダウンされる一連のノードを記述します。アップグレード ドメインは、階層ではなく、タグと見なすことができます。

障害ドメインは、クラスター内のノードの物理的なレイアウトで定義されます。一方、アップグレード ドメインは、ポリシーに基づき、クラスターの管理者によって決定されます。ポリシーは、クラスター内でのアップグレードに関係します。アップグレード ドメインが多いほど、実行中のクラスターとサービスへの影響を制限し、障害が多数のサービスに影響を与えることを防止することにより、アップグレードの粒度が小さくなります。Service Fabric がドメインをアップグレードした後、次のドメインに移行するまでに待機する時間など、他のポリシーによっては、アップグレード ドメインが多いほど、クラスター内のアップグレードの完了にかかる時間が長くなることもあります。

これらの理由から、リソース マネージャーは、障害ドメインと同じように、アップグレード ドメインの情報を収集し、クラスター内のアップグレード ドメイン間でレプリカを分散させます。アップグレード ドメインは、障害ドメインと一対一で対応している場合も、対応していない場合もあります。一般に、一対一で対応していないと想定されます。次の図は、定義済みの障害ドメイン上に、複数のアップグレード ドメインのレイヤーを示しています。リソース マネージャーは、クラスター内でアップグレードまたは障害が発生してもサービスの高可用性を維持するために、いかなるフォールト ドメインまたはアップグレード ドメインにもレプリカが集中しないように、複数のドメインにわたってレプリカを分散させます。

![アップグレード ドメイン][Image3]

アップグレード ドメインと障害ドメインはどちらも、次に示すように、クラスター マニフェスト内のノード定義の一部として構成されます。

``` xml
<Infrastructure>
    <WindowsServer>
      <NodeList>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node1" FaultDomain="fd:/RACK1/BLADE1" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node2" FaultDomain="fd:/RACK2/BLADE1" UpgradeDomain="ud:/UD2"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node3" FaultDomain="fd:/RACK3/BLADE2" UpgradeDomain="ud:/UD3"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node4" FaultDomain="fd:/RACK2/BLADE2" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node5" FaultDomain="fd:/RACK1/BLADE2" UpgradeDomain="ud:/UD2"/>
        </NodeList>
    </WindowsServer>
</Infrastructure>
```
- Azure のデプロイメントで、障害ドメインとアップグレード ドメインは、Azure によって割り当てられます。したがって、Azure のインフラストラクチャ オプション内のノードとロールの定義には、障害ドメインまたはアップグレード ドメインの情報は含まれません。

### ノードのプロパティ
ノードのプロパティは、特定のノードの追加のメタデータを提供するユーザー定義のキーと値のペアです。ノードのプロパティの例には、ノードにハード ドライブやビデオ カードがあるか否か、ハード ディスク ドライブ内のスピンドル数、コア数、およびその他の物理プロパティが含まれます。

ノードのプロパティを使用して、ポリシーの決定を支援するために、より抽象的なプロパティを指定することもできます。たとえば、クラスター内の複数のノードには、さまざまなセクションにクラスターをセグメント化するための手段として、「色」を割り当てることができます。コード例では、ノードの種類の定義の一部として、クラスター マニフェストを使用してノードのプロパティが定義され、クラスター内の複数のノードにこのプロパティを適用できることを示します。

NodeName、NodeType、FaultDomain、および UpgradeDomain の配置プロパティには、既定値があります。Service Fabric は、既定値を自動的に提供して、サービスを作成するときに使用できるようにします。これらと同じ名前で、独自の配置プロパティを指定しないでください。

``` xml
<NodeTypes>
  <NodeType Name="NodeType1">
    <PlacementProperties>
      <Property Name="HasDisk" Value="true"/>
      <Property Name="SpindleCount" Value="4"/>
      <Property Name="HasGPU" Value="true"/>
      <Property Name="NodeColor" Value="blue"/>
      <Property Name="NodeName" Value="Node1"/>
      <Property Name="NodeType" Value="NodeType1"/>
      <Property Name="FaultDomain" Value="fd:/RACK1/BLADE1"/>
      <Property Name="UpgradeDomain" Value="ud:/UD1"/>
    </PlacementProperties>
  </NodeType>
    <NodeType Name="NodeType2">
    <PlacementProperties>
      <Property Name="HasDisk" Value="false"/>
      <Property Name="SpindleCount" Value="-1"/>
      <Property Name="HasGPU" Value="false"/>
      <Property Name="NodeColor" Value="green"/>
    </PlacementProperties>
  </NodeType>
</NodeTypes>
```

実行時に、リソース バランサーは、ノードのプロパティ情報を使用して、特定の機能を必要とするサービスが、確実に適切なノードに配置されるようにします。

### ノード容量
ノード容量とは、特定のノードで使用できる名前と特定のリソースの量を定義するキーと値ペアです。コード例は、"MemoryInMb" と呼ばれるメトリック用の容量があり、既定で 2048 MB のメモリを使用できることを示しています。 容量は、ノードのプロパティと同じように、クラスター マニフェストを使用して定義されます。

``` xml
<NodeType Name="NodeType03">
  <Capacities>
    <Capacity Name="MemoryInMB" Value="2048"/>
    <Capacity Name="DiskSpaceInGB" Value="1024"/>
  </Capacities>
</NodeType>
```
![ノード容量][Image4]

ノードで実行されるサービスは、レポーティング ロードを使用して、容量の要件を更新できるため、リソース バランサーは、いずれかのメトリックで、容量を超過しているかどうかも定期的に確認します。容量を超過している場合、リソース バランサーは、負荷の低いノードにサービスを移行して、リソースの競合を減らし、全体的なパフォーマンスと使用率を向上させることができます。

特定のメトリックは、ノードの種類のプロパティ セクションにも表示されることがありますが、実行時に使用できるノードのプロパティである場合は、容量として表示する方が適切であることに注意してください。プロパティとしても表示することにより、「最小ハードウェア要件」に依存するサービスは、配置に関する制約を使用してノードのクエリを実行できます。これは、実行時に他のサービスによってリソースが消費される場合に、必要になる可能性があります。リソース バランサーが追加の処理を実行できるように、容量として表示することもお勧めします。

新しいサービスを作成するときに、Service Fabric クラスター リソース バランサーは、既存のノードの容量と既存の使用量に関する情報を使用して、新しいサービス全体を配置するために十分な容量を利用できるかどうかを判断します。容量が不足している場合、残りのクラスター容量が不十分であることを示すエラー メッセージと共に、サービスの作成要求が拒否されます。


### リソース バランサーの構成

クラスター マニフェスト内で、次の複数のさまざまな構成値は、リソース バランサーの全体的な動作を定義します。

- 分散しきい値は、特定のメトリックに関して、リソース バランサーによる対応が必要になるクラスターのバランス悪化の程度を指定します。分散しきい値は、リソース バランサーによってクラスターのバランスが再調整するまでに許可される、使用量が最大のノードと使用量が最小のノード間の最大比率です。

次の図は、特定のメトリックの分散のしきい値が 10 である 2 つの例を示しています。

![分散しきい値][Image5]

この例で、ノードの「使用率」では、ノードの容量によって決定されるノードのサイズを考慮に入れず、指定したメトリックのノードに関して現在レポートされている絶対的な使用量のみを考慮に入れることに注意してください。

コード例は、メトリックの分散しきい値が、クラスター マニフェスト内の FabricSettings 要素を使用して、メトリックごとに構成されていることを示しています。

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

- アクティビティしきい値は、負荷の絶対量が多い場合にリソース バランサーが応答するケースを制限することにより、リソース バランサーの実行頻度のゲートとして機能します。この方法により、クラスターが特定のメトリックで非常にビジーな状態でない場合、リソース バランサーは、少量のメトリックのバランスがクラスター内で非常に悪くても実行されません。この方法は、わずかな利益のために、クラスターを再分散することによるリソースの浪費を防止します。次の図は、メトリックの分散しきい値が 4 であり、アクティビティしきい値が 1536 であることを示しています。

![アクティビティしきい値][Image6]リソース バランサーを実行させるには、同じメトリックでアクティビティしきい値と分散しきい値の両方を超過する必要があることにも注意してください。2 つ内いずれかのメトリックをトリガーするだけでは、リソース バランサーは実行されません。

コード例は、分散しきい値と同じように、アクティビティしきい値が、クラスター マニフェスト内の FabricSettings 要素を使用してメトリックごとに構成されることを示しています。

``` xml
<FabricSettings>
  <Section Name="MetricActivityThresholds">
    <Parameter Name="MetricName" Value="1536"/>
  </Section>
</FabricSettings>
```

- PLBRefreshInterval – リソース バランサーが、制約違反を確認する必要がある情報をスキャンする頻度を制御します。制約違反には、満たされていない配置に関する制約、インスタンスまたはレプリカが必要な数よりも少ないサービス、いくつかのメトリックに対して容量を超過しているノード、過負荷なフォールト ドメインやアップグレード ドメイン、またはクラスター内のノードの負荷に関する分散しきい値とアクティビティしきい値およびその現在のビューの使用によるクラスターの不均衡が含まれます。更新間隔は、秒単位で定義され、既定値は 1 です。制約チェックと配置の頻度は、2 つの新しい間隔 (MinConstraintCheckInterval および MinPlacementInterval) で制御することもできます。新しいパラメーターを定義する場合、PLBRefreshInterval は使用されず、定義することはできません。

- MinConstraintCheckInterval – リソース バランサーが、制約違反を確認する必要がある情報をスキャンする頻度を制御します。制約違反には、満たされていない配置に関する制約、インスタンスまたはレプリカが必要な数よりも少ないサービス、いくつかのメトリックに対して容量を超過しているノード、過負荷なフォールト ドメインやアップグレード ドメイン、またはクラスター内のノードの負荷に関する分散しきい値とアクティビティしきい値およびその現在のビューの使用によるクラスターの不均衡が含まれます。制約チェックの間隔は、秒単位で定義されます。制約チェックの間隔が定義されていない場合、その既定値は、PLBRefreshInterval の値と等しくなります (同時に両方の値を指定することはできません)。

- MinPlacementInterval – リソース バランサーが、配置する必要がある新しいインスタンスまたはレプリカがあるかどうかを確認し、配置を試行する頻度を制御します。配置の間隔は、秒単位で定義されます。配置の間隔が定義されていない場合、その既定値は、PLBRefreshInterval の値と等しくなります (同時に両方の値を指定することはできません)。

- MinLoadBalancingInterval – リソース分散ラウンド間の最小時間を決定します。リソース バランサーが、最後の PLBRefreshInterval 間隔中に実行したスキャンの情報に基づいて処理を実行した場合、少なくともこれと同じ時間が経過するまで、リソース バランサーは再び実行されません。これは、秒単位で定義され、既定値は 5 です。

これらの値は小さいですが、特定のメトリックの分散しきい値とアクティビティしきい値が満たされている場合にのみ、負荷分散全体がクラスター内で発生することに注意してください。コード例は、正確かつアクティブな負荷分散が特定のクラスターで不要な場合、FabricSettings 要素内の次の構成を使用して、これらの値を大きくすることができます。この構成例で、負荷分散は 5 分ごとに実行され、2 つの制約チェック間の最小間隔は 10 秒、配置の最小間隔は 5 秒です。PLBRefreshInterval は、この例で定義されていないことに注意してください。

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="MinPlacementInterval" Value="5" />
  <Parameter Name="MinConstraintChecknterval" Value="10" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

次の 2 つ目の構成例では、PLBRefreshInterval と MinLoadBalancingInterval を定義しました。PLBRefreshInterval は 2 秒に設定されているため、MinPlacementInterval と MinConstraintCheckInterval の両方の値も 2 秒に設定されます。

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="PLBRefreshInterval" Value="2" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

詳細: [リソース バランサーのアーキテクチャ](service-fabric-resource-balancer-architecture.md)


[Image1]: media/service-fabric-resource-balancer-cluster-description/FD1.png
[Image2]: media/service-fabric-resource-balancer-cluster-description/FD2.png
[Image3]: media/service-fabric-resource-balancer-cluster-description/UD.png
[Image4]: media/service-fabric-resource-balancer-cluster-description/NC.png
[Image5]: media/service-fabric-resource-balancer-cluster-description/Config.png
[Image6]: media/service-fabric-resource-balancer-cluster-description/Thresholds.png
 

<!---HONumber=Nov15_HO2-->