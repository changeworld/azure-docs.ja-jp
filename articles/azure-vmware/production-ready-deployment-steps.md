---
title: Azure VMware Solution のデプロイの計画
description: この記事では、Azure VMware Solution のデプロイ ワークフローの概要について説明します。  最終的な結果として、仮想マシン (VM) の作成と移行に向けて環境の準備が整います。
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 60e0a4083c0253d322b2e10472d0df7496722c10
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107246"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Azure VMware Solution のデプロイの計画

この記事では、デプロイ中に使用される情報を特定して収集するための計画プロセスについて説明します。 デプロイを計画する際は、収集した情報をデプロイ中に参照しやすいよう必ず文書化してください。

このクイック スタートで説明されているプロセスにより、仮想マシン (VM) の作成と移行のための運用準備ができた環境が得られます。 

収集するデータを追跡するために、「[HCX 計画のチェックリスト](https://www.virtualworkloads.com/2021/04/hcx-planning-checklist/)」を入手してください。

> [!IMPORTANT]
> Azure VMware Solution リソースの作成に備えて、ホスト クォータを早めにリクエストすることが大切です。 ホスト クォータは今すぐリクエストできるので、計画プロセスが完了したら、Azure VMware Solution のプライベートクラウドをいつでもデプロイすることができます。 ホスト クォータのリクエストをサポート チームが受け取った後、リクエストが確認され、ホストが割り当てられるまでに、最大 5 営業日かかります。 Azure VMware Solution の既存のプライベート クラウドがあり、さらに多くのホストを割り当てる必要がある場合でも、実行するプロセスは同じです。 詳細については、サブスクリプションの種類に応じて次のリンクを参照してください。
> - [EA のお客様](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-ea-customers)
> - [CSP のお客様](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-csp-customers)

## <a name="subscription"></a>サブスクリプション

Azure VMware Solution のデプロイに使用する予定のサブスクリプションを特定します。  新しいサブスクリプションを作成することも、既存のものを再利用することもできます。

>[!NOTE]
>サブスクリプションは、マイクロソフト エンタープライズ契約またはクラウド ソリューション プロバイダー Azure プランに関連付けられている必要があります。 詳細については、「[Azure VMware Solution リソースを有効にする方法](enable-azure-vmware-solution.md)」を参照してください。

## <a name="resource-group"></a>Resource group

Azure VMware Solution のために使用するリソース グループを特定します。  一般に、リソース グループは Azure VMware Solution 専用に作成されますが、既存のリソース グループを使用することもできます。

## <a name="region"></a>リージョン

Azure VMware Solution のデプロイ先リージョンを特定します。  詳細については、[リージョン別の利用可能な製品に関する Azure のガイド](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware)を参照してください。

## <a name="resource-name"></a>リソース名

デプロイ時に使用するリソース名を定義します。  リソース名はわかりやすく説明的な名前であり、その中で Azure VMware Solution プライベート クラウドにタイトルを付けます。

>[!IMPORTANT]
>名前は 40 文字を超えてはいけません。 名前がこの制限を超えていると、プライベート クラウドで使用するパブリック IP アドレスを作成できません。 

## <a name="size-hosts"></a>サイズ ホスト

Azure VMware Solution のデプロイ時に使用するサイズ ホストを特定します。  完全な一覧については、[Azure VMware Solution プライベート クラウドとクラスター](concepts-private-clouds-clusters.md#hosts)に関するドキュメントを参照してください。

## <a name="number-of-clusters-and-hosts"></a>クラスターとホストの数

最初に実行する Azure VMware Solution のデプロイは、1 つのクラスターを含むプライベート クラウドで構成されます。 デプロイのために、最初のクラスターにデプロイするホストの数を定義する必要があります。

>[!NOTE]
>クラスターあたりのホストの最小数は 3 で、最大数は 16 です。 プライベート クラウドあたりのクラスターの最大数は 4 です。 

詳細については、[Azure VMware Solution プライベート クラウドとクラスター](concepts-private-clouds-clusters.md#clusters)に関するドキュメントを参照してください。

>[!TIP]
>最初のデプロイ数を超える数が必要な場合は、後でいつでもクラスターを拡張したり、クラスターを追加したりできます。

## <a name="ip-address-segment-for-private-cloud-management"></a>プライベート クラウド管理用の IP アドレス セグメント

デプロイを計画する際の最初の手順は、IP のセグメント化を計画することです。 Azure VMware Solution には、/22 CIDR ネットワークが必要です。 このアドレス空間は、より小さなネットワーク セグメント (サブネット) に分割され、vCenter、VMware HCX、NSX-T、および vMotion 機能などの Azure VMware Solution 管理セグメントに使用されます。 以下の視覚化では、このセグメントが使用される場所を示しています。

この /22 CIDR ネットワーク アドレス ブロックは、既にオンプレミスまたは Azure にある既存のネットワーク セグメントと重複することはできません。

**例:** 10.0.0.0/22

/22 CIDR ネットワークがプライベート クラウドごとにどのように分割されるかについては、[ネットワーク計画のチェックリスト](tutorial-network-checklist.md#routing-and-subnet-considerations)のページをご覧ください。

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="特定する - IP アドレス セグメント" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>仮想マシンのワークロードの IP アドレス セグメント

すべての VMware 環境と同様に、仮想マシンはネットワーク セグメントに接続している必要があります。 Azure VMware Solution には、L2 拡張セグメント (後で説明) と NSX-T ネットワーク セグメントという 2 種類のセグメントがあります。 Azure VMware Solution の運用環境のデプロイが拡張されると、オンプレミスからの L2 拡張セグメントとローカルの NSX-T ネットワーク セグメントが組み合わされることがよくあります。 初期デプロイを計画するには、Azure VMware Solution で単一ネットワーク セグメント (IP ネットワーク) を特定します。 このネットワークは、オンプレミスまたはその他の Azure 内のネットワーク セグメントと重複できません。また、前に定義した/22 ネットワーク セグメント内に存在できません。

このネットワーク セグメントは、主に初期デプロイ中のテスト目的に使用されます。

>[!NOTE]
>この 1 つ以上のネットワークは、デプロイ中は必要ありません。 それらは、デプロイ後の手順として作成されます。
  
**例:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="特定する - 仮想マシンのワークロードの IP アドレス セグメント" border="false":::     

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Azure Virtual Network を Azure VMware Solution に接続する

Azure VMware Solution への接続を提供するために、ExpressRoute は Azure VMware Solution のプライベート クラウドから ExpressRoute 仮想ネットワーク ゲートウェイへと構築されます。

使用する ExpressRoute 仮想ネットワーク ゲートウェイは、"*既存の*" ものでも "*新しい*" ものでもかまいません。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="特定する - Azure VMware Solution を接続するための Azure Virtual Network" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>既存の ExpressRoute 仮想ネットワーク ゲートウェイを使用する

"*既存の*" ExpressRoute 仮想ネットワーク ゲートウェイを使用することを計画している場合は、デプロイ後の手順として Azure VMware Solution の ExpressRoute 回線が確立されます。 この場合、 **[仮想ネットワーク]** フィールドは空白のままにしておきます。

一般的な推奨事項として、既存の ExpressRoute 仮想ネットワーク ゲートウェイを使用しても差し支えありません。 計画の目的で、使用する ExpressRoute 仮想ネットワーク ゲートウェイをメモし、[次の手順](#vmware-hcx-network-segments)に進みます。

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>新しい ExpressRoute 仮想ネットワーク ゲートウェイを作成する

"*新しい*" ExpressRoute 仮想ネットワーク ゲートウェイを作成するときは、既存の Azure 仮想ネットワークを使用することも、新しく作成することもできます。  

- 既存の Azure 仮想ネットワークの場合:
   1. 既存の ExpressRoute 仮想ネットワーク ゲートウェイがない Azure 仮想ネットワークを特定します。
   2. デプロイの前に、Azure 仮想ネットワークに [GatewaySubnet](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet) を作成します。

- 新しい Azure 仮想ネットワークと仮想ネットワーク ゲートウェイの場合は、 **[仮想ネットワーク]** リストの下にある **[新規作成]** リンクを選択して、デプロイ中にそれを作成することになります。  アドレス空間とサブネットはデプロイの前に定義し、デプロイ手順の実行時にその情報を入力できるようにしておくことが大切です。

次の画像は、 **[仮想ネットワーク]** フィールドが強調表示された **[プライベート クラウドを作成する]** デプロイ画面を示しています。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="[仮想ネットワーク] フィールドが強調表示された Azure VMware Solution のデプロイ画面のスクリーンショット。":::

> [!NOTE]
> 使用または作成される仮想ネットワークは、オンプレミス環境と Azure VMware Solution から認識される可能性があるため、この仮想ネットワークで使用する IP セグメントとサブネットが重複しないようにしてください。

## <a name="vmware-hcx-network-segments"></a>VMware HCX のネットワーク セグメント

VMware HCX は、Azure VMware Solution にバンドルされているテクノロジです。 VMware HCX の主なユース ケースは、ワークロードの移行とディザスター リカバリーです。 いずれかの実施を計画する場合は、ここでネットワークを計画するのが最適です。 そうしない場合は、次の手順までスキップして続行できます。

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="optional-extend-your-networks"></a>(省略可能) ネットワークを拡張する

ネットワーク セグメントは、オンプレミスから Azure VMware Solution まで拡張できます。 ネットワーク セグメントを拡張する場合は、ここでそれらのネットワークを特定します。  

次に、考慮すべき点をいくつか示します。

- オンプレミスからネットワークを拡張する予定の場合、これらのネットワークはオンプレミスの VMware 環境内の [vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) に接続する必要があります。  
- [vSphere Standard Switch](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) 上のネットワークは拡張できません。

>[!NOTE]
>これらのネットワークは、デプロイ時ではなく、構成の最後の手順として拡張されます。
>
## <a name="next-steps"></a>次の手順
これで必要な情報を収集して文書化したので、次のセクションに進み、Azure VMware Solution プライベート クラウドを作成します。

> [!div class="nextstepaction"]
> [Azure VMware Solution のデプロイ](deploy-azure-vmware-solution.md)
> 
