---
title: SAP アプリケーションの Azure 近接通信配置グループ | Microsoft Docs
description: Azure 近接通信配置グループを使用する SAP のデプロイ シナリオについて説明します
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bccbced3ce40856f858fa7e0028a27c1e1c59460
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484656"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>SAP アプリケーションで最適なネットワーク待ち時間を実現するための Azure 近接通信配置グループ

> [!IMPORTANT]
> 2021 年 11 月に、ゾーン デプロイ内で SAP ワークロードと共に近接配置グループを使用する方法を大幅に変更しました。 

SAP NetWeaver または SAP S/4HANA アーキテクチャを基盤とする SAP アプリケーションは、SAP アプリケーション層と SAP データベース層の間のネットワーク待ち時間の影響を受けます。 この影響は、アプリケーション レイヤーで実行されているほとんどのビジネス ロジックの結果です。 SAP アプリケーション レイヤーはビジネス ロジックを実行するため、データベース層に対して、1 秒あたり何千回または何万回という高い頻度でクエリを実行します。 これらのクエリの性質は、ほとんどの場合単純です。 多くの場合、データベース層で 500 マイクロ秒以内で実行できます。

そのようなクエリをアプリケーション層からデータベース層に送信し、その結果が戻ってくるまでにネットワークで費やされる時間は、ビジネス プロセスの実行にかかる時間に大きく影響します。 ネットワーク待ち時間によるこの影響が理由で、SAP デプロイ プロジェクトでは特定の最小ネットワーク待ち時間を実現しなければならないことがあります。 ネットワーク待ち時間を分類する方法のガイドラインについては、[SAP Note #1100926 - FAQ:ネットワーク パフォーマンス](https://launchpad.support.sap.com/#/notes/1100926/E) に関する記事を参照してください。

多くの Azure リージョンで、データセンターの数が増加しています。 同時に、(特にハイエンド SAP システムの) お客様は、M および Mv2 ファミリや、珍しい場合には HANA Large Instances のような、より特別な VM ファミリを使用しています。 これらの Azure 仮想マシンの種類は、ある Azure リージョンに集まっている各データセンターで必ず利用できるわけではありません。 これらの事実によって、SAP アプリケーション層と SAP DBMS 層の間のネットワーク待ち時間を最適化する機会が生じる場合があります。

ネットワーク待ち時間を最適化できるように、Azure では[近接配置グループ](../../co-location.md)が提供されています。 近接配置グループを使用すると、1 つのネットワーク スパインの配下にあるさまざまな VM の種類を強制的にグループ化し、これまでにまだ提供されていないさまざまな VM の種類の間で十分に短いネットワーク待ち時間を実現できます。 このような近接配置グループに最初の VM をデプロイするプロセスでは、その VM が特定のネットワーク スパインに関連付けられます。 同じ近接配置グループにデプロイされる他のすべての VM も同様に、同じネットワーク スパインの下にグループ化されます。 この見通しは魅力的に聞こえますが、この構成の使用には次のようないくつかの制限と落とし穴もあります。

- すべての Azure VM の種類が、すべての Azure データセンターで、または各ネットワーク スパインの配下で使用できると想定することはできません。 そのため、1 つの近接配置グループ内で異なる VM の種類を組み合わせることは厳しく制限される可能性があります。 これらの制限が発生するのは、特定の種類の VM を実行するために必要なホスト ハードウェアが、近接配置グループが割り当てられたデータセンターの内部またはネットワーク スパインの配下に存在しない可能性があるためです。
- 1 つの近接配置グループ内の VM の一部をサイズ変更する際、あらゆる場合に、近接配置グループが割り当てられているのと同じデータセンター内またはネットワーク スパインの配下で新しい VM の種類を使用できると無意識に想定することはできません。
- Azure がハードウェアを使用停止すると、近接配置グループの特定の VM が別の Azure データセンターまたはネットワーク スパインに強制移動されることがあります。 このケースの詳細については、[近接配置グループ](../../co-location.md#planned-maintenance-and-proximity-placement-groups)に関するドキュメントを参照してください。  

> [!IMPORTANT]
> 考えられる制限を受けて、近接配置グループは次の場合に限って使用する必要があります。
>
> - 特定のシナリオで必要な場合 (後述)
> - アプリケーション層と DBMS 層の間のネットワーク待ち時間が長すぎてワークロードに影響を与えている場合
> - システム全体のランドスケープまたは完全な SAP ランドスケープではなく、単一の SAP システムという粒度でのみ
> - 近接配置グループ内の各種 VM の種類と VM の数を最小限に抑えるように


これまでに近接配置グループを使用したシナリオとしては次のものがありました。

- 可用性セットを使用した SAP ワークロードのデプロイ。 SAP データベース層、SAP アプリケーション層、および ASCS または SCS の VM が、それぞれ 3 つの異なる可用性セットにグループ化されていました。 このような場合には、可用性セットがその Azure リージョン全体に分散されていないことを確認する必要がありました。その理由は、Azure リージョンによっては、このクラウドで SAP ワークロードに悪影響を与える可能性があるネットワーク待ち時間が発生するおそれがあるからです。
- SAP ワークロードの重要なリソースを異なる可用性ゾーンにデプロイしつつ、可用性セットを使用して各ゾーンのアプリケーション層の VM を異なる障害ドメインに分散する必要がありました。 この場合、このドキュメントで後述するように、近接配置グループを使用する必要があります。
- 近接配置グループを使用して VM をグループ化し、VM 内でホストされているサービスの間に最適なネットワーク待ち時間を実現しました。

デプロイ シナリオ #1 では、多くのリージョン (特に可用性ゾーンがないリージョンと可用性ゾーンのあるほとんどのリージョン) において、VM の配置場所を問わずネットワーク待ち時間が許容範囲に収まります。 もっとも、一部の Azure リージョンでは、可用性セットを使用する際に 3 つの異なる可用性セットを併置しないと、十分に優れたエクスペリエンスを実現できません。 デプロイ シナリオ #2 については、このドキュメントの以降のセクションに記載した近接配置グループを別の形で使用する方法をお勧めします。



## <a name="what-are-proximity-placement-groups"></a>近接通信配置グループとは 
Azure 近接通信配置グループとは、論理構造です。 近接配置グループは定義されると、Azure リージョンと Azure リソース グループに関連付けられます。 VM がデプロイされると、近接通信配置グループは次によって参照されます。

- 多くの Azure コンピューティング ユニットを備え、ネットワーク待ち時間の短いネットワーク スパインの配下にデプロイされた最初の Azure VM。 このようなネットワーク スパインは、多くの場合、単一の Azure データセンターに対応しています。 最初の仮想マシンは、最終的にデプロイ パラメーターと組み合わされる Azure の割り当てアルゴリズムに基づいてコンピューティング スケール ユニットにデプロイされる "スコープ VM" であると考えることができます。
- 以降にデプロイされる VM のうち、その近接配置グループを参照するものはすべて、最初の仮想マシンと同じネットワーク スパインの配下にデプロイされます。

> [!NOTE]
> 最初の VM が配置されたネットワーク スパインの配下に、特定の VM の種類を実行できるホスト ハードウェアがデプロイされていない場合、要求された VM の種類のデプロイは成功しません。 近接配置グループの境界内で VM がサポートされない旨の割り当てエラー メッセージが表示されます。

1 つの [Azure リソース グループ](../../../azure-resource-manager/management/manage-resources-portal.md)には、複数の近接通信配置グループを割り当てることができます。 しかし、1 つの近接通信配置グループは、1 つの Azure リソース グループにしか割り当てることができません。


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Azure VM のみを使用する SAP システムの近接通信配置グループ
このセクションでは、これまでに使用していたデプロイ アーキテクチャと新しい推奨事項について説明します。

### <a name="proximity-placement-groups-with-zonal-deployments"></a>ゾーン デプロイを使用した近接配置グループ
HANA Large Instances を使用しないデプロイでは、SAP アプリケーション層と DBMS 層の間のネットワーク待ち時間を合理的な範囲で短くすることが重要です。 一定のシナリオでそのような短いネットワーク待ち時間を実現するために、そのような SAP システムに Azure 近接配置グループを定義できます。

複数の SAP 運用システムまたは非運用システムを 1 つの近接配置グループにまとめることは避けてください。 1 つの近接配置グループ内にグループ化する SAP システムの数が増えるほど、次のようなことが起こる可能性が高くなるため、システムのバンドルは避けてください。

- 近接配置グループが割り当てられているネットワーク スパインの配下では使用できない VM の種類が必要になる。
- 時間の経過と共に近接配置グループ内の VM の数を増やす必要が生じた場合に、M シリーズの VM のような、メインストリームでない VM のリソースを最終的に利用できない可能性がある。

これまでに推奨してきた近接配置グループの使用方法を図示すると、次のとおりです。

![ゾーンを使用した従来の近接配置グループ](./media/sap-proximity-placement-scenarios/vm-ppg-zone-old.png)

SAP システムをデプロイした 2 つの可用性ゾーンにそれぞれ、近接配置グループ (PPG) を作成しました。 特定のゾーン内の VM はすべて、そのゾーンの個々の近接配置グループの一部です。 各ゾーンで DBMS VM をデプロイして PPG のスコープを設定してから、同じゾーンと PPG に ASCS VM をデプロイしました。 3 番目の手順では、Azure 可用性セットを作成し、スコープが設定された PPG に可用性セットを割り当て、SAP アプリケーション層をデプロイしました。 この構成の利点は、すべてのコンポーネントが同じネットワーク スパインの配下にうまく配置されていたことでした。 大きな欠点は、仮想マシンのサイズ変更の柔軟性が損なわれる可能性がある点です。


Azure 可用性ゾーン内のネットワーク待ち時間を短縮するために Microsoft が Azure リージョンにデプロイした多くの改善を基に、ゾーン デプロイの新しいデプロイのガイダンスを示すと、次のようになります。

![ゾーンを使用した新しい近接配置グループ](./media/sap-proximity-placement-scenarios/vm-ppg-zone.png)

これまでの推奨事項との違いは、2 つのゾーン内のデータベース VM が近接配置グループの一部ではなくなっている点です。 各ゾーンの近接配置グループのスコープが、SAP ASCS または SCS インスタンスを実行する VM のデプロイに応じたものに変わっています。 これは、複数のデータセンターに可用性ゾーンが集まっているリージョンでは、ASCS または SCS インスタンスとアプリケーション層が実行されるネットワーク スパインと、データベース VM が実行されるネットワーク スパインとが別のものになる可能性があることも意味しています。 ネットワークは改善されたものの、十分に優れたパフォーマンスとスループットを実現するには、依然として SAP アプリケーション層と DBMS 層の間のネットワーク待ち時間が十分な水準に収まっている必要があります。 この新しい構成の利点は、DBMS 層または SAP システムのアプリケーション層を備えた VM のサイズを変更したり、新しい VM の種類に移行したりするうえでの柔軟性が高まるという点にあります。 


### <a name="proximity-placement-groups-with-availability-set-deployments"></a>可用性セットのデプロイを使用した近接配置グループ
この場合の目的は、近接配置グループを使用して、異なる可用性セットを介してデプロイされる VM を併置することです。 この使用シナリオでは、リージョン内の異なる可用性ゾーンをまたいで制御されたデプロイを使用することはありません。 代わりに、可用性セットを使用して SAP システムをデプロイします。 その結果、DBMS VM 用、ASCS または SCS VM 用、およびアプリケーション層の VM 用に少なくとも 1 つ可用性セットがある状態になります。 VM のデプロイ時に可用性セットと可用性ゾーンの両方を指定することはできないので、それぞれの可用性セット内の VM が割り当てられる場所を制御することはできません。 その結果、一部の Azure リージョンでは、異なる VM 間のネットワーク待ち時間が依然として長すぎ、十分に優れたパフォーマンス エクスペリエンスが得られない可能性があります。 そのため、結果として得られるアーキテクチャは次のようになります。


![可用性セットを使用した近接配置グループ](./media/sap-proximity-placement-scenarios/vm-ppg-avsets.png)

この図では、1 つの近接配置グループが 1 つの SAP システムに割り当てられます。 この PPG は、3 つの可用性セットに割り当てられます。 その後、最初のデータベース層 VM を DBMS 可用性セットにデプロイすることにより、近接配置グループのスコープが設定されます。 このアーキテクチャでは、全 VM を同じネットワーク スパインの配下に併置することが推奨されます。 それにより、この記事で既に説明した制限が加わることになります。 そのため、この近接配置グループのアーキテクチャは使いすぎないようにする必要があります。


## <a name="proximity-placement-groups-and-hana-large-instances"></a>近接通信配置グループと HANA Large Instances
一部の SAP システムがアプリケーション レイヤーに関して [HANA Large Instances](./hana-overview-architecture.md) に依存している場合、[リビジョン 4 の行またはスタンプ](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)にデプロイされた HANA Large Instance ユニットを使用しているときに、HANA Large Instance ユニットと Azure VM との間のネットワーク待ち時間が大幅に改善されます。 改善点の 1 つは、HANA Large Instances ユニットはデプロイされるときに、近接通信配置グループと共にデプロイされることです。 その近接通信配置グループを使用して、アプリケーション レイヤーの VM をデプロイすることができます。 その結果、それらの VM は、HANA Large Instances ユニットをホストする同じデータセンターにデプロイされます。

HANA Large Instances ユニットがリビジョン 4 のスタンプまたは行にデプロイされているかどうかを確認するには、「[Azure portal を介した Azure HANA Large Instances の制御](./hana-li-portal.md#look-at-attributes-of-single-hli-unit)」の記事を確認してください。 HANA Large Instance ユニットの属性の概要では、近接通信配置グループの名前も確認できます。これは、HANA Large Instance ユニットがデプロイされたときに作成されたためです。 属性の概要に表示される名前は、アプリケーション レイヤーの VM のデプロイ先である近接通信配置グループの名前です。

Azure 仮想マシンのみを使用する SAP システムと比べると、HANA Large Instances を使用する場合、使用する [Azure リソース グループ](../../../azure-resource-manager/management/manage-resources-portal.md)の数を決定する際の柔軟性は低くなります。 [HANA Large Instances テナント](./hana-know-terms.md)のすべての HANA Large Instance ユニットは、1 つのリソース グループにグループ化されます ([こちらの記事](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal)を参照)。 運用システムと非運用システム、または他のシステムなどを分離するために別々のテナントにデプロイする場合を除き、すべての HANA Large Instances ユニットは 1 つの HANA Large Instances テナントにデプロイされます。 このテナントには、リソース グループとの一対一のリレーションシップがあります。 ただし、単一のユニットごとに、別個の近接通信配置グループが定義されます。

その結果、1 つのテナントの Azure リソース グループと近接通信配置グループ間のリレーションシップは次のようになります。

![近接通信配置グループと HANA Large Instances](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>近接通信配置グループを使用したデプロイの例
次に、Azure 近接通信配置グループを使用して VM をデプロイするために使用できる PowerShell コマンドをいくつか紹介します。

[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) にサインインした後の最初の手順では、デプロイに使用する Azure サブスクリプションを使用しているかどうかを確認します。

<pre><code>
Get-AzureRmContext
</code></pre>

別のサブスクリプションに変更する必要がある場合は、次のコマンドを実行します。

<pre><code>
Set-AzureRmContext -Subscription "PPG test subscription"
</code></pre>

次のコマンドを実行して、新しい Azure リソース グループを作成します。

<pre><code>
New-AzResourceGroup -Name "ppgexercise" -Location "westus2"
</code></pre>

次のコマンドを実行して、新しい近接通信配置グループを作成します。

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "ppgexercise" -Name "collocate" -Location "westus2"
</code></pre>

次のようなコマンドを使用して、最初の VM を近接通信配置グループにデプロイします。

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "ppgscopevm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "collocate" -Size "Standard_E16s_v4"
</code></pre>

上記のコマンドは、Windows ベースの VM をデプロイします。 この VM のデプロイが成功すると、近接配置グループのネットワーク スパインのスコープが Azure リージョン内で定義されます。 前のコマンドで示されているように、近接配置グループを参照する後続のすべての VM デプロイは、同じネットワーク スパインの配下にデプロイされます。これは、そのネットワーク スパインの配下に配置されているハードウェアでその VM の種類をホストでき、その VM の種類用の容量が使用可能であることが条件になります。

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>可用性セットと Availability Zones を近接通信配置グループに結合する
SAP システムのデプロイに可用性ゾーンを使用する場合の問題点の 1 つは、特定の可用性ゾーン内の可用性セットを使用して SAP アプリケーション層をデプロイできないことです。 SAP アプリケーション層を SAP ASCS または SCS VM と同じゾーンにデプロイする必要があります。 現時点では、1 つの VM をデプロイするときに可用性ゾーンと可用性セットを参照することはできません。 しかし、単に可用性ゾーンを指定して VM をデプロイするだけでは、アプリケーション層の VM をさまざまな更新ドメインと障害ドメインに分散できるとは限らなくなります。

近接通信配置グループを使用すると、この制限を回避できます。 デプロイ シーケンスを次に示します。

- 近接通信配置グループを作成します。
- 可用性ゾーンを参照することにより、アンカー VM をデプロイします。アンカー VM は ASCS または SCS VM が推奨されます。
- Azure 近接通信グループを参照する可用性セットを作成します。 (この記事で後述するコマンドを参照してください。)
- 可用性セットと近接通信配置グループを参照してアプリケーション レイヤーの VM をデプロイします。

前のセクションで説明したように、最初の VM をデプロイする代わりに、VM をデプロイするときに可用性ゾーンと近接通信配置グループを参照します。

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "centralserviceszone1" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "collocate" -Size "Standard_E8s_v4"
</code></pre>

この仮想マシンを正常にデプロイすると、SAP システムの ASCS または SCS インスタンスが 1 つの可用性ゾーンでホストされます。 近接配置グループのスコープは、定義した可用性ゾーン内のいずれかのネットワーク スパインに固定されます。

次の手順では、SAP システムのアプリケーション レイヤーに使用する可用性セットを作成する必要があります。

近接配置グループを定義して作成します。 可用性セットを作成するためのコマンドでは、別途、近接通信配置グループの ID (名前ではありません) を参照しなければなりません。 近接通信配置グループの ID を取得するには、次のコマンドを使用します。

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "ppgexercise" -Name "collocate"
</code></pre>

可用性セットを作成する際、マネージド ディスク (特に指定されていない場合は既定) と近接通信配置グループを使用する場合は追加のパラメーターを考慮する必要があります。

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "ppgexercise" -Name "ppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

理想的には、3 つの障害ドメインを使用します。 ただし、サポートされる障害ドメインの数はリージョンによって異なる場合があります。 この場合、特定のリージョンに対して可能な障害ドメインの最大数は 2 です。 アプリケーション レイヤーの VM をデプロイするには、次に示すように、可用性セット名と近接通信配置グループ名への参照を追加する必要があります。

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "appinstance1" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "collocate" -Size "Standard_E16s_v4"
</code></pre>

このデプロイの結果は次のとおりです。
- 特定の可用性ゾーン (単数または複数) に配置されている SAP システムのセントラル サービス。
- 可用性セットを通じて SAP セントラル サービス (ASCS または SCS) VM (1 つまたは複数) と同じネットワーク スパインに配置されている SAP アプリケーション層。

> [!NOTE]
> 1 つの DBMS VM と ASCS または SCS VM をあるゾーンにデプロイし、2 つ目の DBMS VM と ASCS または SCS VM を別のゾーンにデプロイして高可用性構成を作成するので、ゾーンごとに異なる近接配置グループが必要になります。 使用するどの可用性セットでも同様です。

## <a name="change-proximity-placement-group-configurations-of-an-existing-system"></a>既存のシステムの近接配置グループの構成を変更する
これまでに説明した推奨事項に従って近接配置グループを実装していた場合に、新しい構成に調整するときは、次の記事で説明している方法を使用します。

- [Azure CLI を使用して近接通信配置グループに VM をデプロイする](../../linux/proximity-placement-groups.md)
- [PowerShell を使用し、近接通信配置グループに VM をデプロイする](../../windows/proximity-placement-groups.md) 

これらのコマンドは、近接配置グループ内の既存の VM を新しい VM タイプに移行できず、割り当てエラーが発生した場合にも使用できます。

## <a name="next-steps"></a>次のステップ
次のドキュメントを参照してください。

- [Azure での SAP ワークロードの計画とデプロイに関するチェックリスト](./sap-deployment-checklist.md)
- [Azure CLI を使用して近接通信配置グループに VM をデプロイする](../../linux/proximity-placement-groups.md)
- [PowerShell を使用し、近接通信配置グループに VM をデプロイする](../../windows/proximity-placement-groups.md)
- [SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](./dbms_guide_general.md)