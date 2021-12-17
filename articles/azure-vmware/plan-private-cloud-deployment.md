---
title: Azure VMware Solution のデプロイを計画する
description: Azure VMware Solution のデプロイを計画する方法について説明します。
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 09/27/2021
ms.openlocfilehash: 6528727fda867f5f07d7d83b09df1bbf70df6b92
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079468"
---
# <a name="plan-the-azure-vmware-solution-deployment"></a>Azure VMware Solution のデプロイを計画する

仮想マシン (VM) の作成と移行のための運用準備ができた環境を構築するには、Azure VMware Solution のデプロイを計画することが重要です。 計画プロセスでは、デプロイに必要なものを特定して収集します。 計画する際は、収集した情報をデプロイ中に参照しやすいよう必ず文書化してください。 適切なデプロイにより、仮想マシン (VM) の作成と移行のための運用準備ができた環境を構築できます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Azure サブスクリプション、リソース グループ、リージョン、リソース名を識別する
> * サイズ ホストを識別し、クラスターとホストの数を決定する
> * 対象となる Azure プランのホスト クォータを要求する
> * プライベート クラウド管理用の /22 CIDR IP セグメントを識別する
> * 単一のネットワーク セグメントを識別する
> * 仮想ネットワーク ゲートウェイを定義する
> * VMware HCX のネットワーク セグメントを定義する

完了したら、最後に示されている推奨される次の手順に従って、このファースト ステップ ガイドを続行します。


## <a name="identify-the-subscription"></a>サブスクリプションを特定する

Azure VMware Solution のデプロイに使用する予定のサブスクリプションを特定します。  新しいサブスクリプションを作成することも、既存のサブスクリプションを使用することもできます。

>[!NOTE]
>サブスクリプションは、Microsoft Enterprise Agreement (EA)、クラウド ソリューション プロバイダー (CSP) Azure プラン、またはMicrosoft 顧客契約 (MCA) に関連付けられている必要があります。 詳しくは、「[対象となる条件](request-host-quota-azure-vmware-solution.md#eligibility-criteria)」をご覧ください。

## <a name="identify-the-resource-group"></a>リソース グループを特定する

Azure VMware Solution のために使用するリソース グループを特定します。  一般に、リソース グループは Azure VMware Solution 専用に作成されますが、既存のリソース グループを使用することもできます。

## <a name="identify-the-region-or-location"></a>リージョン (場所) を特定する

Azure VMware Solution のデプロイ先[リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)を特定します。 

## <a name="define-the-resource-name"></a>リソース名を定義する

リソース名はわかりやすく説明的な名前とし、その中で Azure VMware Solution プライベート クラウドにタイトルを付けます (例: **MyPrivateCloud**)。

>[!IMPORTANT]
>名前は 40 文字を超えてはいけません。 名前がこの制限を超えていると、プライベート クラウドで使用するパブリック IP アドレスを作成できません。 

## <a name="identify-the-size-hosts"></a>サイズ ホストを特定する

Azure VMware Solution のデプロイ時に使用するサイズ ホストを特定します。  

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="determine-the-number-of-clusters-and-hosts"></a>クラスターとホストの数を決定する

最初に実行する Azure VMware Solution のデプロイは、1 つのクラスターを含むプライベート クラウドで構成されます。 デプロイの最初のクラスターに、デプロイするホストの数を定義する必要があります。

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]


>[!NOTE]
>クラスターあたりのホスト数、プライベート クラウドあたりのクラスター数、プライベート クラウドあたりのホスト数に関する制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)」を参照してください。

## <a name="request-a-host-quota"></a>ホスト クォータをリクエストする 

計画プロセスの完了後に、Azure VMware Solution のプライベート クラウドをいつでもデプロイすることができるように、早い段階でホスト クォータをリクエストすることが重要です。
ホスト クォータをリクエストする前に、Azure サブスクリプション、リソース グループ、リージョンを特定していることを確認してください。 また、サイズ ホストがわかっていることを確認し、必要なクラスターとホストの数を決定します。

ホスト クォータのリクエストをサポート チームが受け取った後、リクエストが確認され、ホストが割り当てられるまでに、最大 5 営業日かかります。

- [EA のお客様](request-host-quota-azure-vmware-solution.md#request-host-quota-for-ea-customers)
- [CSP のお客様](request-host-quota-azure-vmware-solution.md#request-host-quota-for-csp-customers)


## <a name="define-the-ip-address-segment-for-private-cloud-management"></a>プライベート クラウド管理用の IP アドレス セグメントを定義する

Azure VMware Solution には、/22 CIDR ネットワークが必要です (例: `10.0.0.0/22`)。 このアドレス空間は、より小さなネットワーク セグメント (サブネット) に分割され、vCenter、VMware HCX、NSX-T、および vMotion 機能などの Azure VMware Solution 管理セグメントに使用されます。 この図は、Azure VMware Solution の管理 IP アドレス セグメントを示しています。

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Azure VMware Solution の管理 IP アドレス セグメントを示す図。" border="false":::  

>[!IMPORTANT]
>/22 CIDR ネットワーク アドレス ブロックは、既にオンプレミスまたは Azure にある既存のネットワーク セグメントと重複しないようにする必要があります。 /22 CIDR ネットワークがプライベート クラウドごとにどのように分割されるかの詳細については、「[ルーティングとサブネットに関する考慮事項](tutorial-network-checklist.md#routing-and-subnet-considerations)」を参照してください。



## <a name="define-the-ip-address-segment-for-vm-workloads"></a>VM ワークロードの IP アドレス セグメントを定義する

すべての VMware 環境と同様に、VM はネットワーク セグメントに接続している必要があります。  Azure VMware Solution の運用環境のデプロイが拡張されると、オンプレミスからの L2 拡張セグメントとローカルの NSX-T ネットワーク セグメントが組み合わされることがよくあります。 

最初のデプロイ用に、1 つのネットワーク セグメント (IP ネットワーク) を特定します (例: `10.0.4.0/24`)。 このネットワーク セグメントは、主に初期デプロイ中のテスト目的に使用されます。  アドレス ブロックは、オンプレミスまたは Azure 内のネットワーク セグメントと重複しないようにする必要があります。また、既に定義されている /22 ネットワーク セグメント内に設定することはできません。 
  
:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="特定する - 仮想マシンのワークロードの IP アドレス セグメント" border="false":::     


## <a name="define-the-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを定義する

Azure VMware Solution には、Azure Virtual Network と ExpressRoute 回線が必要です。 "*既存*" の ExpressRoute 仮想ネットワーク ゲートウェイを使用するか、"*新しい*" ExpressRoute 仮想ネットワーク ゲートウェイを使用するかを決めてください。 "*新しい*" 仮想ネットワーク ゲートウェイを使用する場合は、プライベート クラウドの作成後にそれを作成します。 既存の ExpressRoute 仮想ネットワーク ゲートウェイを使用してもかまいません。その場合は、計画の一環として、どの ExpressRoute 仮想ネットワーク ゲートウェイを使用するかをメモしておいてください。 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Azure VMware Solution にアタッチされた Azure Virtual Network を示す図" border="false":::

>[!IMPORTANT]
>Azure Virtual WAN 内の仮想ネットワーク ゲートウェイに接続することはできますが、このクイック スタートでは取り上げません。

## <a name="define-vmware-hcx-network-segments"></a>VMware HCX のネットワーク セグメントを定義する

VMware HCX は、データ センターやクラウド間でのアプリケーションの移行、ワークロードの再調整、ビジネス継続性を簡略化する、アプリケーション モビリティ プラットフォームです。 さまざまな移行の種類を使用して、Azure VMware Solution やその他の接続サイトに VMware ワークロードを移行できます。 

VMware HCX コネクタによって、複数の IP セグメントを必要とする (自動化) 仮想アプライアンスのサブセットがデプロイされます。 ネットワーク プロファイルを作成するときに、IP セグメントを使用します。 パイロットまたは小規模製品のユース ケースをサポートする VMware HCX のデプロイでは、次のことを確認します。  移行のニーズに合わせて、必要に応じて変更します。 

- **管理ネットワーク:** VMware HCX をオンプレミスでデプロイする場合、VMware HCX の管理ネットワークを把握する必要があります。  通常は、オンプレミスの VMware クラスターで使用される管理ネットワークと同じです。  少なくとも、このネットワーク セグメントでは VMware HCX 用に **2 つ** の IP を識別します。 パイロットまたは小規模なユース ケースを超えるデプロイの規模によっては、さらに多くの数が必要になる場合があります。

  >[!NOTE]
  >大規模な環境に対応するために、オンプレミスの VMware クラスターに使用される管理ネットワークを使用するのではなく、新しい /26 ネットワークを作成し、そのネットワークをポート グループとしてオンプレミスの VMware クラスターに見せるようにしてください。  その後、最大 10 個のサービス メッシュと 60 個のネットワーク エクステンダー (サービス メッシュごとに -1) を作成できます。 Azure VMware Solution プライベート クラウドを使用すると、ネットワーク エクステンダーごとに **8 個** のネットワークを拡張できます。

- **アップリンク ネットワーク:** VMware HCX をオンプレミスでデプロイする場合、VMware HCX のアップリンク ネットワークを把握する必要があります。 管理ネットワークに使用するのと同じネットワークを使用します。 

- **vMotion ネットワーク:** VMware HCX をオンプレミスでデプロイする場合、VMware HCX の vMotion ネットワークを把握する必要があります。  通常は、オンプレミスの VMware クラスターで vMotion 用に使用されるネットワークと同じです。  少なくとも、このネットワーク セグメントでは VMware HCX 用に **2 つ** の IP を識別します。 パイロットまたは小規模なユース ケースを超えるデプロイの規模によっては、さらに多くの数が必要になる場合があります。

  vMotion ネットワークを、分散仮想スイッチまたは vSwitch0 で公開する必要があります。 そうでない場合は、対応するように環境を変更します。

  >[!NOTE]
  >多くの VMware 環境では、vMotion に対して非ルーティングのネットワーク セグメントが使用され、問題はありません。
  
- **レプリケーション ネットワーク:** VMware HCX をオンプレミスでデプロイする場合、レプリケーション ネットワークを定義する必要があります。 管理ネットワークとアップリンク ネットワークに使用しているのと同じネットワークを使用します。  オンプレミスのクラスター ホストが専用のレプリケーション VMkernel ネットワークを使用する場合、このネットワーク セグメントに **2 つ** の IP アドレスを予約し、レプリケーション ネットワーク用にレプリケーション VMkernel ネットワークを使用します。


## <a name="determine-whether-to-extend-your-networks"></a>ネットワークを拡張するかどうかを判断する

ネットワーク セグメントは、必要に応じてオンプレミスから Azure VMware Solution まで拡張できます。 ネットワーク セグメントを拡張する場合は、ここで次のガイドラインに従って、それらのネットワークを特定します。

- ネットワークはオンプレミスの VMware 環境内の [vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) に接続する必要があります。  
- [vSphere Standard Switch](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) 上のネットワークは拡張できません。

>[!IMPORTANT]
>これらのネットワークは、デプロイ時ではなく、構成の最後の手順として拡張されます。


## <a name="next-steps"></a>次の手順
これで必要な情報を収集して文書化したので、次のチュートリアルに進み、Azure VMware Solution プライベート クラウドを作成します。

> [!div class="nextstepaction"]
> [Azure VMware Solution のデプロイ](deploy-azure-vmware-solution.md)
