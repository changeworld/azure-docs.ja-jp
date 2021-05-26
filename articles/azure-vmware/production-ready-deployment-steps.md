---
title: Azure VMware Solution のデプロイを計画する
description: この記事では、Azure VMware Solution のデプロイ ワークフローの概要について説明します。  最終的な結果として、仮想マシン (VM) の作成と移行に向けて環境の準備が整います。
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 05/13/2021
ms.openlocfilehash: 0b8826582957e02ec3f18d09dec7283cc92188a1
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110097122"
---
# <a name="plan-the-azure-vmware-solution-deployment"></a>Azure VMware Solution のデプロイを計画する

このクイック スタートでは、デプロイに必要な情報を特定して収集する計画プロセスについて取り上げます。 デプロイを計画する際は、収集した情報をデプロイ中に参照しやすいよう必ず文書化してください。

ここに記載したプロセスにより、仮想マシン (VM) の作成と移行のための運用準備ができた環境が得られます。 

>[!TIP]
>収集するデータを追跡するために、[VMware HCX 計画のチェックリスト](https://www.virtualworkloads.com/2021/04/hcx-planning-checklist/)を入手してください。


## <a name="request-a-host-quota"></a>ホスト クォータをリクエストする 

Azure VMware Solution リソースの作成に備えて、ホスト クォータを早めにリクエストすることが大切です。 ホスト クォータは今すぐリクエストできるので、計画プロセスが完了したら、Azure VMware Solution のプライベートクラウドをいつでもデプロイすることができます。 ホスト クォータのリクエストをサポート チームが受け取った後、リクエストが確認され、ホストが割り当てられるまでに、最大 5 営業日かかります。 Azure VMware Solution の既存のプライベート クラウドがあり、さらに多くのホストを割り当てる必要がある場合でも、実行するプロセスは同じです。 詳細については、サブスクリプションの種類に応じて次のリンクを参照してください。
- [EA のお客様](request-host-quota-azure-vmware-solution.md#request-host-quota-for-ea-customers)
- [CSP のお客様](request-host-quota-azure-vmware-solution.md#request-host-quota-for-csp-customers)

## <a name="identify-the-subscription"></a>サブスクリプションを特定する

Azure VMware Solution のデプロイに使用する予定のサブスクリプションを特定します。  新しいサブスクリプションを作成することも、既存のものを再利用することもできます。

>[!NOTE]
>サブスクリプションは、マイクロソフト エンタープライズ契約またはクラウド ソリューション プロバイダー Azure プランに関連付けられている必要があります。 詳細については、「[Azure VMware Solution リソースを有効にする方法](deploy-azure-vmware-solution.md#step-1-register-the-microsoftavs-resource-provider)」を参照してください。

## <a name="identify-the-resource-group"></a>リソース グループを特定する

Azure VMware Solution のために使用するリソース グループを特定します。  一般に、リソース グループは Azure VMware Solution 専用に作成されますが、既存のリソース グループを使用することもできます。

## <a name="identify-the-region-or-location"></a>リージョン (場所) を特定する

Azure VMware Solution のデプロイ先リージョンを特定します。  詳細については、[リージョン別の利用可能な製品に関する Azure のガイド](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware)を参照してください。

## <a name="identify-the-resource-name"></a>リソース名を特定する

デプロイ時に使用するリソース名を定義します。  リソース名はわかりやすく説明的な名前であり、その中で Azure VMware Solution プライベート クラウドにタイトルを付けます。

>[!IMPORTANT]
>名前は 40 文字を超えてはいけません。 名前がこの制限を超えていると、プライベート クラウドで使用するパブリック IP アドレスを作成できません。 

## <a name="identify-the-size-hosts"></a>サイズ ホストを特定する

Azure VMware Solution のデプロイ時に使用するサイズ ホストを特定します。  

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="determine-the-number-of-clusters-and-hosts"></a>クラスターとホストの数を決定する

最初に実行する Azure VMware Solution のデプロイは、1 つのクラスターを含むプライベート クラウドで構成されます。 デプロイのために、最初のクラスターにデプロイするホストの数を定義する必要があります。

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]

## <a name="define-the-ip-address-segment-for-private-cloud-management"></a>プライベート クラウド管理用の IP アドレス セグメントを定義する

デプロイを計画する際の最初の手順は、IP のセグメント化を計画することです。 Azure VMware Solution には、/22 CIDR ネットワークが必要です。 このアドレス空間は、より小さなネットワーク セグメント (サブネット) に分割され、vCenter、VMware HCX、NSX-T、および vMotion 機能などの Azure VMware Solution 管理セグメントに使用されます。 以下の視覚化では、このセグメントが使用される場所を示しています。

この /22 CIDR ネットワーク アドレス ブロックは、既にオンプレミスまたは Azure にある既存のネットワーク セグメントと重複することはできません。

**例:** 10.0.0.0/22

/22 CIDR ネットワークがプライベート クラウドごとにどのように分割されるかについては、[ネットワーク計画のチェックリスト](tutorial-network-checklist.md#routing-and-subnet-considerations)のページをご覧ください。

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="特定する - IP アドレス セグメント" border="false":::  

## <a name="define-the-ip-address-segment-for-vm-workloads"></a>VM ワークロードの IP アドレス セグメントを定義する

すべての VMware 環境と同様に、VM はネットワーク セグメントに接続している必要があります。 Azure VMware Solution には、L2 拡張セグメント (後で説明) と NSX-T ネットワーク セグメントという 2 種類のセグメントがあります。 Azure VMware Solution の運用環境のデプロイが拡張されると、オンプレミスからの L2 拡張セグメントとローカルの NSX-T ネットワーク セグメントが組み合わされることがよくあります。 初期デプロイを計画するには、Azure VMware Solution で単一ネットワーク セグメント (IP ネットワーク) を特定します。 このネットワークは、オンプレミスまたはその他の Azure 内のネットワーク セグメントと重複できません。また、前に定義した/22 ネットワーク セグメント内に存在できません。

このネットワーク セグメントは、主に初期デプロイ中のテスト目的に使用されます。

>[!NOTE]
>この 1 つ以上のネットワークは、デプロイ中は必要ありません。 それらは、デプロイ後の手順として作成されます。
  
**例:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="特定する - 仮想マシンのワークロードの IP アドレス セグメント" border="false":::     

## <a name="define-the-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを定義する

>[!IMPORTANT]
>Azure Virtual WAN 内の仮想ネットワーク ゲートウェイに接続することはできますが、このクイック スタートでは取り上げません。

Azure VMware Solution プライベート クラウドには、Azure Virtual Network と ExpressRoute 回線が必要です。  

"*既存*" の ExpressRoute 仮想ネットワーク ゲートウェイを使用するか、"*新しい*" ExpressRoute 仮想ネットワーク ゲートウェイを使用するかを決めてください。  "*新しい*" 仮想ネットワーク ゲートウェイを使用する場合は、プライベート クラウドの作成後にそれを作成することになります。 既存の ExpressRoute 仮想ネットワーク ゲートウェイを使用してもかまいません。その場合は、計画の一環として、どの ExpressRoute 仮想ネットワーク ゲートウェイを使用するかをメモしておいてください。 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Azure VMware Solution にアタッチされた Azure Virtual Network を示す図" border="false":::



## <a name="define-vmware-hcx-network-segments"></a>VMware HCX のネットワーク セグメントを定義する

VMware HCX は、Azure VMware Solution にバンドルされているテクノロジです。 VMware HCX の主なユース ケースは、ワークロードの移行とディザスター リカバリーです。 いずれかの実施を計画する場合は、ここでネットワークを計画するのが最適です。 そうしない場合は、次の手順までスキップして続行できます。

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="determine-whether-to-extend-your-networks"></a>ネットワークを拡張するかどうかを判断する

ネットワーク セグメントは、必要に応じてオンプレミスから Azure VMware Solution まで拡張できます。 

ネットワーク セグメントを拡張する場合は、ここで次のガイドラインに従って、それらのネットワークを特定します。

- ネットワークはオンプレミスの VMware 環境内の [vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) に接続する必要があります。  
- [vSphere Standard Switch](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) 上のネットワークは拡張できません。

>[!IMPORTANT]
>これらのネットワークは、デプロイ時ではなく、構成の最後の手順として拡張されます。


## <a name="next-steps"></a>次の手順
これで必要な情報を収集して文書化したので、次のセクションに進み、Azure VMware Solution プライベート クラウドを作成します。

> [!div class="nextstepaction"]
> [Azure VMware Solution のデプロイ](deploy-azure-vmware-solution.md)> 
