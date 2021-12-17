---
title: Azure VMware Solution に VMware HCX を構成する
description: Azure VMware Solution のプライベート クラウド用にオンプレミスの VMware HCX コネクタを構成します。
ms.topic: tutorial
ms.date: 09/07/2021
ms.openlocfilehash: 022936108246b57f27d26dfffdaa260399821740
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457508"
---
# <a name="configure-on-premises-vmware-hcx-connector"></a>オンプレミスの VMware HCX コネクタを構成する

[VMware HCX アドオンをインストール](install-vmware-hcx.md)したら、Azure VMware Solution のプライベート クラウド用にオンプレミスの VMware HCX コネクタを構成する準備ができます。  

この記事では、次のことについて説明します。

* オンプレミスの VMware HCX コネクタを Azure VMware Solution HCX Cloud Manager とペアリングする
* ネットワーク プロファイル、コンピューティング プロファイル、およびサービス メッシュを構成する
* アプライアンスの状態を確認し、移行が可能なことを検証する

これらの手順を完了すると、、仮想マシン (VM) の作成と移行のための運用準備ができた環境が得られます。 

## <a name="prerequisites"></a>[前提条件]

- [VMware HCX コネクタ](install-vmware-hcx.md)がインストールされている。

- VMware HCX Enterprise を使用する予定がある場合は、[サポート リクエスト](https://portal.azure.com/#create/Microsoft.Support)を通じて [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) アドオンを有効にしてあることを確認してください。 Azure VMware Solution の無料の 12 か月間試用版です。

- [VMware HCX MON を有効にする](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html)予定がある場合は、以下があることを確認してください。  

   - HCX ネットワーク拡張機能用のオンプレミスの NSX-T または VDS (標準スイッチなし)

   - 1 つ以上のアクティブな拡張ネットワーク セグメント

- [VMware ソフトウェア バージョンの要件](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)が満たされている。

- オンプレミスの vSphere 環境 (ソース環境) が[最小要件](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)を満たしている。

- [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) は、オンプレミスと Azure VMware Solution プライベート クラウド ExpressRoute 回線との間に構成されます。

- オンプレミスのコンポーネントと Azure VMware Solution プライベートの間の通信に[必要なすべてのポート](https://ports.vmware.com/home/VMware-HCX)を開きます。

- [VMware HCX のネットワーク セグメントを定義する](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments)。  VMware HCX の主なユース ケースは、ワークロードの移行とディザスター リカバリーです。

- HCX の使用に関する情報については、[VMware HCX のドキュメントを確認してください](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-BFD7E194-CFE5-4259-B74B-991B26A51758.html)。

## <a name="add-a-site-pairing"></a>サイトのペアリングを追加する

データセンターでは、Azure VMware Solution の VMware HCX Cloud Manager を VMware HCX Manager コネクタと接続またはペアリングできます。

> [!IMPORTANT]
> VMware Configuration Maximums ツールでは、オンプレミスの HCX Connector と HCX Cloud Manager との間におけるサイト ペアの最大数は 25 と説明されていますが、ライセンスにより、HCX Advanced Edition では 3 に、HCX Enterprise Edition では 10 に制限されます。

1. オンプレミスの vCenter にサインインし、 **[ホーム]** の下の **[HCX]** を選択します。

1. **[Infrastructure]\(インフラストラクチャ\)** で、 **[Site Pairing]\(サイトのペアリング\)** を選択し、 **[Connect To Remote Site]\(リモート サイトへの接続\)** オプション (画面の中央) を選択します。

1. 先ほどメモした Azure VMware Solution HCX Cloud Manager の URL または IP アドレス `https://x.x.x.9` と、プライベート クラウドで CloudAdmin ロールを保持しているユーザーの資格情報を入力します。 次に、 **[接続]\(Connect\)** を選択します。

   > [!NOTE]
   > サイト ペアを正常に確立するには、次のようにします。
   > * VMware HCX コネクタがポート 443 経由で HCX Cloud Manager IP にルーティングできる必要があります。
   >
   > * サイト ペアリング接続には、Active Directory などの外部 ID ソースのサービス アカウントを使用することをお勧めします。 接続済みサービスに個別のアカウントを設定する方法の詳細については、[アクセスと ID の概念](./concepts-identity.md)に関する記事を参照してください。

   Azure VMware Solution の VMware HCX Cloud Manager とオンプレミスの VMware HCX コネクタが接続 (ペアリング) されたことを示す画面が表示されます。

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Azure VMware Solution の HCX Manager と VMware HCX コネクタのペアリングを示すスクリーンショット。":::

この手順全体の概要については、動画「[Azure VMware Solution: HCX サイトのペアリング](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720)」を参照してください。

## <a name="create-network-profiles"></a>ネットワーク プロファイルを作成する

VMware HCX コネクタによって、複数の IP セグメントを必要とする (自動化) 仮想アプライアンスのサブセットがデプロイされます。 ネットワーク プロファイルを作成するときは、[計画段階](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments)で特定した IP セグメントを使用します。  4 つのネットワーク プロファイルを作成します。

   - 管理
   - vMotion
   - レプリケーション
   - アップリンク

1. **[インフラストラクチャ]** で、 **[Interconnect]\(相互接続\)**  >  **[Multi-Site Service Mesh]\(マルチサイト サービス メッシュ\)**  >  **[Network Profiles]\(ネットワーク プロファイル\)**  >  **[Create Network Profile]\(ネットワーク プロファイルの作成\)** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="vSphere クライアントでネットワーク プロファイルを作成する場所を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. ネットワーク プロファイルごとに、ネットワークとポート グループを選択し、名前を指定して、セグメントの IP プールを作成します。 **[作成]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="新しいネットワーク プロファイルの詳細のスクリーンショット。" lightbox="media/tutorial-vmware-hcx/example-configurations-network-profile.png":::

この手順全体の概要については、動画「[Azure VMware Solution: HCX ネットワーク プロファイル](https://www.youtube.com/embed/O0rU4jtXUxc)」を参照してください。

## <a name="create-a-compute-profile"></a>コンピューティング プロファイルを作成する

1. **[Infrastructure]\(インフラストラクチャ\)** で、 **[Interconnect]\(相互接続\)**  >  **[Compute Profiles]\(コンピューティング プロファイル\)**  >  **[Create Compute Profile]\(コンピューティング プロファイルの作成\)** の順に選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="コンピューティング プロファイルの作成を開始するための選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. プロファイルの名前を入力し、 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="コンピューティング プロファイル名の入力と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. 有効にするサービス (移行、ネットワーク拡張機能、ディザスター リカバリーなど) を選択した後、 **[Continue]\(続行\)** を選択します。

   > [!NOTE]
   > 通常、ここでは何も変更されません。

1. **[Select Service Resources]\(サービス リソースの選択\)** で、選択した VMware HCX サービスを有効にするためのサービス リソース (クラスター) を 1 つ以上選択します。

1. オンプレミスのデータセンター内にクラスターが表示されたら、 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="選択されたサービス リソースと [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. **[Select Datastore]\(データストアの選択\)** で、VMware HCX 相互接続アプライアンスをデプロイするためのデータストア ストレージ リソースを選択します。 その後 **[続行]** を選択します。

   複数のリソースを選択すると、VMware HCX では容量が使い果たされるまで、最初に選択されたリソースが使用されます。

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="選択されたデータ ストレージ リソースと [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::

1. **[Select Management Network Profile]\(管理ネットワーク プロファイルの選択\)** で、前の手順で作成した管理ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="管理ネットワーク プロファイルの選択と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. **[Select Uplink Network Profile]\(アップリンク ネットワーク プロファイルの選択\)** で、前の手順で作成したアップリンク ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="アップリンク ネットワーク プロファイルの選択と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. **[Select vMotion Network Profile]\(vMotion ネットワーク プロファイルの選択\)** で、前の手順で作成した vMotion ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="vMotion ネットワーク プロファイルの選択と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. **[Select vSphere Replication Network Profile]\(vSphere レプリケーション ネットワーク プロファイルの選択\)** で、前の手順で作成したレプリケーション ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="レプリケーション ネットワーク プロファイルの選択と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. **[Select Distributed Switches for Network Extensions]\(ネットワーク拡張機能の分散スイッチの選択\)** で、レイヤー 2 拡張ネットワーク上の Azure VMware Solution に移行される仮想マシンが含まれるスイッチを選択します。 その後 **[続行]** を選択します。

   > [!NOTE]
   > レイヤー 2 (L2) 拡張ネットワーク上の仮想マシンを移行しない場合は、この手順を省略できます。

   :::image type="content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="分散仮想スイッチの選択と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 接続ルールを確認し、 **[Continue]\(続行\)** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="接続ルールと [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. **[Finish]\(完了\)** を選択して、コンピューティング プロファイルを作成します。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="コンピューティング プロファイル情報を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

この手順全体の概要については、動画「[Azure VMware Solution: コンピューティング プロファイル](https://www.youtube.com/embed/e02hsChI3b8)」を参照してください。

## <a name="create-a-service-mesh"></a>サービス メッシュを作成する

>[!IMPORTANT]
>オンプレミスの VMware HCX コネクタの "アップリンク" ネットワーク プロファイル アドレスと Azure VMware Solution HCX クラウドの "アップリンク" ネットワーク プロファイル アドレスの間で、ポート UDP 500/4500 が開いていることを確認します。


1. **[インフラストラクチャ]** で、 **[相互接続]**  >  **[サービス メッシュ]**  >  **[サービス メッシュの作成]** と選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="サービス メッシュの作成を開始するための選択のスクリーンショット。" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 事前設定されたサイトを確認して、**Continue\(続行\)** を選択します。

   > [!NOTE]
   > これが最初のサービス メッシュ構成である場合は、この画面を変更する必要はありません。

1. ドロップダウン リストでソースとリモートのコンピューティング プロファイルを選択してから、 **[Continue]\(続行\)** を選択します。

   選択により、VM で VMware HCX サービスを使用できるリソースが定義されます。

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="ソース コンピューティング プロファイルの選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="リモート コンピューティング プロファイルの選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 有効にするサービスを確認したら、 **[Continue]\(続行\)** を選択します。

1. **[Advanced Configuration - Override Uplink Network profiles]\(詳細構成 - アップリンク ネットワーク プロファイルのオーバーライド\)** で、 **[Continue]\(続行\)** を選択します。

   アップリンク ネットワーク プロファイルにより、リモート サイトの相互接続アプライアンスに到達できるネットワークに接続されます。

1. **[詳細構成 - ネットワーク拡張機能アプライアンスのスケールアウト]** で、内容を見直して **[続行]** を選択します。

   アプライアンスあたり最大 8 個の VLAN を設定できますが、もう 1 つのアプライアンスをデプロイしてさらに 8 個の VLAN を追加できます。 また、追加のアプライアンスに対処するための IP 空間が必要です。アプライアンスごとに 1 つの IP アドレスが必要になります。  詳細については、「[VMware HCX Configuration Limits (VMware HCX の構成の制限)](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0)」を参照してください。

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="VLAN 数を増やす場所を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. **[Advanced Configuration - Traffic Engineering]\(詳細構成 - トラフィック エンジニアリング\)** で、内容を見直して必要と思われる変更を加えたら、 **[Continue]\(続行\)** を選択します。

1. トポロジのプレビューを確認し、 **[Continue]\(続行\)** を選択します。

1. このサービス メッシュのためにわかりやすい名前を入力し、 **[完了]** を選択して完了します。

1. **[タスクを表示]** を選択してデプロイを監視します。

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="タスクを表示するためのボタンを示すスクリーンショット。":::

   サービス メッシュのデプロイが正常に完了すると、サービスが緑色で表示されます。

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="サービスの緑色のインジケーターを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. アプライアンスの状態を調べてサービス メッシュの正常性を確認します。

1. **[Interconnect]\(相互接続\)**  >  **[Appliances]\(アプライアンス\)** の順に選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="アプライアンスの状態を確認するための選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

   >[!NOTE]
   >サービス メッシュを確立すると、プライベート クラウドに新しいデータストアと新しいホストが表示されることがあります。 これは、サービス メッシュを確立した後のまったく正常な動作です。
   >
   >:::image type="content" source="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png" alt-text="HCX サービス メッシュのデータストアとホストを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png":::

HCX インターコネクト トンネルの状態は **[UP]\(アップ\)** と、緑で示されます。 これで、VMware HCX を使用して Azure VMware Solution VM を移行および保護する準備ができました。 Azure VMware Solution では、ワークロードの移行がサポートされています (ネットワーク拡張機能を使用または不使用)。 そのため vSphere 環境のワークロードも、オンプレミスにネットワークを作成し、それらのネットワークに VM をデプロイして、移行できます。 詳細については、[VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/index.html)を参照してください。 



この手順全体の概要については、動画「[Azure VMware Solution: サービス メッシュ](https://www.youtube.com/embed/COY3oIws108)」を参照してください。


## <a name="next-steps"></a>次のステップ

HCX コネクタの構成が完了したので、以下について学習することもできます。

- [HCX ネットワーク拡張機能を作成する](configure-hcx-network-extension.md)

- [VMware HCX Mobility Optimized Networking (MON) のガイダンス](vmware-hcx-mon-guidance.md)

