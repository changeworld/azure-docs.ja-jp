---
title: チュートリアル - VMware HCX をデプロイして構成する
description: Azure VMware Solution プライベート クラウドのために VMware HCX ソリューションをデプロイして構成する方法について説明します。
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: c78eae11497702054bb54b5980228fd0a3962577
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367773"
---
# <a name="deploy-and-configure-vmware-hcx"></a>VMware HCX をデプロイして構成する

この記事では、Azure VMware Solution プライベート クラウドのためにオンプレミスの VMware HCX コネクタをデプロイして構成する手順について説明します。 VMware HCX を使用すると、さまざまな移行の種類を通じて、Azure VMware Solution やその他の接続されたサイトに VMware のワークロードを移行できます。 Azure VMware Solution では HCX Cloud Manager をデプロイして構成するため、オンプレミスの VMware データセンターで HCX コネクタをダウンロードし、アクティブ化して構成する必要があります。

VMware HCX Advanced コネクタは、Azure VMware Solution にあらかじめデプロイされています。 最大 3 つのサイト接続 (オンプレミスからクラウド、またはクラウドからクラウド) がサポートされます。 3 つを超えるサイト接続が必要な場合は、[VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) アドオンを有効にするために[サポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)を送信してください。 このアドオンは現在、プレビュー段階です。 

>[!NOTE]
>VMware HCX Enterprise Edition (EE) は、Azure VMware Solution でプレビュー サービスとして使用できます。 これは無料で、プレビュー サービスの使用条件が適用されます。 VMware HCX EE サービスが一般提供されると、課金に切り替わるという通知が 30 日前に届きます。 サービスを無効にするかオプトアウトするオプションも用意されます。

まず、この記事の「[開始する前に](#before-you-begin)」、「[ソフトウェア バージョンの要件](#software-version-requirements)」、および「[前提条件](#prerequisites)」のセクションをよく確認してください。 

では、以下のために必要なすべての手順について説明します。

> [!div class="checklist"]
> * オンプレミスの VMware HCX OVA (HCX コネクタ) をデプロイする。
> * VMware HCX コネクタをアクティブにする。
> * オンプレミスの HCX コネクタを Azure VMware Solution HCX Cloud Manager とペアリングする。
> * 相互接続 (ネットワーク プロファイル、コンピューティング プロファイル、およびサービス メッシュ) を構成する。
> * アプライアンスの状態を確認し、移行が可能なことを検証して、セットアップを完了する。

完了したら、この記事の最後に示されている、推奨される次の手順に進むことができます。  

## <a name="before-you-begin"></a>開始する前に
   
* 基本的な Azure VMware Solution Software-Defined Datacenter (SDDC) の[チュートリアル シリーズ](tutorial-network-checklist.md)を確認します。
* HCX ユーザー ガイドなど、[VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/index.html)を確認して参照します。
* VMware のドキュメントで「[VMware HCX で仮想マシンを移行する](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)」を確認します。
* 必要に応じて、「[VMware HCX Deployment Considerations](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)」 (VMware HCX のデプロイに関する考慮事項) を確認します。
* 必要に応じて、VMware vSphere [ブログ シリーズ](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)など、HCX に関連する VMware の資料を確認します。 
* 必要に応じて、Azure VMware Solution のサポート チャネルを通じて、Azure VMware Solution の HCX Enterprise のアクティブ化を要求します。
* 必要に応じて、[HCX に必要なネットワーク ポートを確認](https://ports.vmware.com/home/VMware-HCX)します。
* Azure VMware Solution HCX Cloud Manager は Azure VMware Solution プライベート クラウド用に提供されている /22 ネットワークから事前に構成されていますが、オンプレミスの HCX コネクタではお客様がオンプレミス ネットワークからネットワーク範囲を割り当てる必要があります。 これらのネットワークと範囲については、この記事の後の方で説明します。

コンピューティングとストレージのリソースに対するワークロードのサイズ設定は、非常に重要な計画手順です。 サイズ設定手順は、プライベート クラウド環境の初期計画の一環として行います。 

Azure Migrate ポータルで [Azure VMware Solution の評価](../migrate/how-to-create-azure-vmware-solution-assessment.md)を完了することで、ワークロードのサイズ設定を行えます。

## <a name="software-version-requirements"></a>ソフトウェア バージョンの要件

インフラストラクチャ コンポーネントでは、必要な最小バージョンが実行されている必要があります。 
                                                         
| コンポーネントの種類    | ソース環境の要件    | ターゲット環境の要件   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>5\.5 U1 以前を使用している場合は、HCX 操作用にスタンドアロンの HCX ユーザー インターフェイスを使用します。  | 6.0 U2 以降   |
| ESXi   | 5.0    | ESXi 6.0 以降   |
| NSX    | ソースの論理スイッチの HCX ネットワーク拡張機能の場合: NSXv 6.2 以降または NSX-T 2.4 以降。   | NSXv 6.2 以降または NSX-T 2.4 以降<br/><br/>HCX 近接ルーティングの場合: NSXv 6.4 以降。 (NSX-T では近接ルーティングはサポートされていません。) |
| vCloud Director   | 必要なし。 ソース サイトの vCloud Director との相互運用性はありません。 | ターゲット環境を vCloud Director と統合する場合、最小値は 9.1.0.2 です。  |

## <a name="prerequisites"></a>前提条件

### <a name="network-and-ports"></a>ネットワークとポート

* オンプレミスと Azure VMware Solution SDDC ExpressRoute 回線との間に [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) を構成します。

* オンプレミスのコンポーネントと Azure VMware Solution SDDC の間の通信に[必要なすべてのポート](https://ports.vmware.com/home/VMware-HCX)を開く必要があります。

詳細については、[VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)を参照してください。


### <a name="ip-addresses"></a>IP アドレス

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>VMware HCX コネクタ OVA をオンプレミスにデプロイする

> [!NOTE]
> 仮想アプライアンスをオンプレミスの vCenter にデプロイする前に、VMware HCX コネクタ OVA をダウンロードする必要があります。 

1. ブラウザー ウィンドウを開き、 **cloudadmin** ユーザー資格情報を使用して、`https://x.x.x.9` のポート 443 で Azure VMware Solution HCX Manager にサインインしてから、 **[サポート]** に移動します。

   > [!TIP]
   > Azure VMware Solution 内の HCX Cloud Manager の IP アドレスをメモしておきます。 IP アドレスを特定するには、[Azure VMware Solution] ペインで、 **[管理]**  >  **[接続]** の順に移動してから、 **[HCX]** タブを選択します。 
   >
   >VCenter のパスワードは、プライベート クラウドの設定時に定義されました。

1. **[ダウンロード]** リンクを選択して、VMware HCX コネクタ OVA ファイルをダウンロードします。

1. オンプレミスの vCenter に移動します。 OVF テンプレート (ダウンロードした OVA ファイル) を選択して、HCX コネクタをオンプレミスの vCenter にデプロイします。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. 名前と場所を選択し、HCX コネクタをデプロイするリソースまたはクラスターを選択します。 次に、詳細情報と必要なリソースを確認します。  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. ライセンス条項を確認します。 同意する場合は、必要なストレージとネットワークを選択します。次に、 **[次へ]** を選択します。

1. **[Customize template]\(テンプレートのカスタマイズ\)** に、必要なすべての情報を入力します。 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. **[次へ]** を選択し、構成を確認してから **[完了]** を選択して、HCX コネクタ OVA をデプロイします。
     
   > [!NOTE]
   > 一般に、現在デプロイ中の VMware HCX コネクタは、クラスターの管理ネットワークにデプロイされます。  
   
   > [!IMPORTANT]
   > 場合によっては、仮想アプライアンスを手動で有効にする必要があります。  その場合は、10 分から 15 分待ってから、次の手順に進みます。

この手順全体の概要については、動画「[Azure VMware Solution: HCX アプライアンスのデプロイ](https://www.youtube.com/embed/BwSnQeefnso)」を参照してください。 


## <a name="activate-vmware-hcx"></a>VMware HCX をアクティブにする

VMware HCX コネクタ OVA をオンプレミスにデプロイし、アプライアンスを起動したら、アクティブにすることができます。 まず、ライセンス キーを Azure VMware Solution ポータルから取得する必要があります。

1. Azure VMware Solution ポータルで、 **[管理]**  >  **[接続]** の順に移動し、 **[HCX]** タブを選択してから **[追加]** を選択します。

1. **管理者** の資格情報を使用して、`https://HCXManagerIP:9443` でオンプレミスの VMware HCX Manager にサインインし、サインインします。 

   > [!IMPORTANT]
   > VMware HCX Manager の IP アドレスと共に、必ず `9443` のポート番号を含めてください。

1. **[Licensing]\(ライセンス\)** で、 **[HCX Advanced Key]\(HCX Advanced キー\)** にキーを入力します。  
   
    > [!NOTE]
    > VMware HCX Manager では、オープンなインターネット アクセスがあるか、プロキシが構成されている必要があります。

1. **[Datacenter Location]\(データセンターの場所\)** で、VMware HCX Manager をオンプレミスにインストールする最も近い場所を指定します。

1. **[System Name]\(システム名\)** で、名前を変更するか、既定値をそのまま使用します。
   
1. **[Yes, Continue]\(はい、続行します\)** を選択します。
    
1. **[VCenter の接続]** で、お使いの vCenter サーバーの FQDN または IP アドレスと適切な資格情報を入力し、 **[続行]** を選択します。
   
1. **[Configure SSO/PSC]\(SSO/PSC の構成\)** で、Platform Services Controller の FQDN または IP アドレスを入力してから、 **[Continue]\(続行\)** を選択します。
   
   >[!NOTE]
   >一般に、このエントリは vCenter の FQDN または IP アドレスと同じです。

1. すべての入力が正しいことを確認し、 **[Restart]\(再起動\)** を選択します。
    
   > [!NOTE]
   > 再起動後、次の手順の操作が求められるまでに待機時間が生じます。

サービスの再起動後は、表示される画面に vCenter が緑色で表示されてることが重要です。 vCenter と SSO のどちらにも、適切な構成パラメーターを指定する必要があります。それらは、前の画面と同じにする必要があります。

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

この手順全体の概要については、動画「[Azure VMware Solution: HCX をアクティブにする](https://www.youtube.com/embed/BkAV_TNYxdE)」を参照してください。


## <a name="configure-the-vmware-hcx-connector"></a>VMware HCX コネクタを構成する

これで、サイトのペアリングを追加し、ネットワークとコンピューティングのプロファイルを作成し、移行、ネットワーク拡張、ディザスター リカバリーなどのサービスを有効にする準備ができました。 

### <a name="add-a-site-pairing"></a>サイトのペアリングを追加する

Azure VMware Solution で、VMware HCX Cloud Manager をデータセンターの VMware HCX Manager コネクタと接続 (ペアリング) できます。 

1. オンプレミスの vCenter にサインインし、 **[ホーム]** の下の **[HCX]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. **[Infrastructure]\(インフラストラクチャ\)** で、 **[Site Pairing]\(サイトのペアリング\)** を選択し、 **[Connect To Remote Site]\(リモート サイトへの接続\)** オプション (画面の中央) を選択します。 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. 前にメモしておいたリモート HCX URL または IP アドレスと、Azure VMware Solution の cloudadmin@vsphere.local のユーザー名、およびパスワードを入力します。 次に、 **[接続]\(Connect\)** を選択します。

   > [!NOTE]
   > サイト ペアを正常に確立するには、HCX コネクタがポート 443 経由で HCX Cloud Manager IP にルーティングできる必要があります。
   >
   > パスワードは、vCenter へのサインインに使用したのと同じパスワードです。 このパスワードは、最初のデプロイ画面で定義したものです。

   Azure VMware Solution の HCX Cloud Manager とオンプレミスの HCX コネクタが接続 (ペアリング) されたことを示す画面が表示されます。

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="OVF テンプレートを参照しているスクリーンショット。":::

この手順全体の概要については、動画「[Azure VMware Solution: HCX サイトのペアリング](https://www.youtube.com/embed/sKizDCRHOko)」を参照してください。



### <a name="create-network-profiles"></a>ネットワーク プロファイルを作成する

VMware HCX によって、複数の IP セグメントを必要とする仮想アプライアンスのサブセットがデプロイ (自動化) されます。 ネットワーク プロファイルを作成するときには、IP セグメントを定義します。これらは、[VMware HCX ネットワーク セグメントのデプロイ前準備と計画段階](production-ready-deployment-steps.md#vmware-hcx-network-segments)の間に特定したものです。

4 つのネットワーク プロファイルを作成します。

   - 管理
   - vMotion
   - レプリケーション
   - アップリンク

1. **[インフラストラクチャ]** で、 **[Interconnect]\(相互接続\)**  >  **[Multi-Site Service Mesh]\(マルチサイト サービス メッシュ\)**  >  **[Network Profiles]\(ネットワーク プロファイル\)**  >  **[Create Network Profile]\(ネットワーク プロファイルの作成\)** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. ネットワーク プロファイルごとに、ネットワークとポート グループを選択し、名前を指定して、そのセグメントの IP プールを作成します。 **[作成]** を選択します。 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="OVF テンプレートを参照しているスクリーンショット。":::

この手順全体の概要については、動画「[Azure VMware Solution: HCX ネットワーク プロファイル](https://www.youtube.com/embed/NhyEcLco4JY)」を参照してください。


### <a name="create-a-compute-profile"></a>コンピューティング プロファイルを作成する

1. **[コンピューティング プロファイル]**  >  **[コンピューティング プロファイルの作成]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. プロファイルの名前を入力し、 **[続行]** を選択します。  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. 有効にするサービス (移行、ネットワーク拡張機能、ディザスター リカバリーなど) を選択した後、 **[Continue]\(続行\)** を選択します。
  
   > [!NOTE]
   > 通常、ここでは何も変更されません。

1. **[Select Service Resources]\(サービス リソースの選択\)** で、選択した VMware HCX サービスを有効にするためのサービス リソース (クラスター) を 1 つ以上選択します。  

1. オンプレミスのデータセンター内にクラスターが表示されたら、 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. **[Select Datastore]\(データストアの選択\)** で、VMware HCX 相互接続アプライアンスをデプロイするためのデータストア ストレージ リソースを選択します。 その後 **[続行]** を選択します。

   複数のリソースを選択すると、VMware HCX では容量が使い果たされるまで、最初に選択されたリソースが使用されます。   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. **[Select Management Network Profile]\(管理ネットワーク プロファイルの選択\)** で、前の手順で作成した管理ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > 管理ネットワーク プロファイルを使用すると、VMware HCX アプライアンスが vCenter と通信できるようになります。 このプロファイルを通じて、ESXi ホストに到達できます。

1. **[Select Uplink Network Profile]\(アップリンク ネットワーク プロファイルの選択\)** で、前の手順で作成したアップリンク ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. **[Select vMotion Network Profile]\(vMotion ネットワーク プロファイルの選択\)** で、前の手順で作成した vMotion ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. **[Select vSphere Replication Network Profile]\(vSphere レプリケーション ネットワーク プロファイルの選択\)** で、前の手順で作成したレプリケーション ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。

   ほとんどの場合、レプリケーション ネットワーク プロファイルは管理ネットワーク プロファイルと同じです。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. **[Select Distributed Switches for Network Extensions]\(ネットワーク拡張機能の分散スイッチの選択\)** で、レイヤー 2 拡張ネットワーク上の Azure VMware Solution に移行される仮想マシンが含まれる分散仮想スイッチを選択します。 その後 **[続行]** を選択します。

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 接続ルールを確認し、 **[Continue]\(続行\)** を選択します。  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. **[Finish]\(完了\)** を選択して、コンピューティング プロファイルを作成します。


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

この手順全体の概要については、動画「[Azure VMware Solution: コンピューティング プロファイル](https://www.youtube.com/embed/qASXi5xrFzM)」を参照してください。

### <a name="create-a-service-mesh"></a>サービス メッシュを作成する

ここで、オンプレミスと Azure VMware Solution SDDC の間のサービス メッシュを構成します。

   > [!NOTE]
   > Azure VMware Solution を使用してサービス メッシュを正常に確立するには:
   >
   > オンプレミスの HCX コネクタで定義された "アップリンク" ネットワーク プロファイル アドレスと Azure VMware Solution HCX クラウドの "アップリンク" ネットワーク プロファイル アドレスの間で、ポート UDP 500/4500 を開きます。
   >
   > [HCX の必須ポート](https://ports.vmware.com/home/VMware-HCX)を確認してください。

1. **[インフラストラクチャ]** で、 **[相互接続]**  >  **[サービス メッシュ]**  >  **[サービス メッシュの作成]** と選択します。    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 事前にデータが格納されたサイトを確認してから、 **[続行]** を選択します。 

   >[!NOTE]
   >これが最初のサービス メッシュ構成である場合は、この画面を変更する必要はありません。  

1. ドロップダウン リストでソースとリモートのコンピューティング プロファイルを選択してから、 **[Continue]\(続行\)** を選択します。  

   選択により、VM で VMware HCX サービスを使用できるリソースが定義されます。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 有効にするサービスを確認したら、 **[Continue]\(続行\)** を選択します。  

1. **[Advanced Configuration - Override Uplink Network profiles]\(詳細構成 - アップリンク ネットワーク プロファイルのオーバーライド\)** で、 **[Continue]\(続行\)** を選択します。  

   アップリンク ネットワーク プロファイルにより、リモート サイトの相互接続アプライアンスに到達できるネットワークに接続されます。  
  
1. **[詳細構成 - ネットワーク拡張機能アプライアンスのスケールアウト]** で、内容を見直して **[続行]** を選択します。 

1. **[Advanced Configuration - Traffic Engineering]\(詳細構成 - トラフィック エンジニアリング\)** で、内容を見直して必要と思われる変更を加えたら、 **[Continue]\(続行\)** を選択します。

1. トポロジのプレビューを確認し、 **[Continue]\(続行\)** を選択します。

1. このサービス メッシュのためにわかりやすい名前を入力し、 **[完了]** を選択して完了します。  

1. **[タスクを表示]** を選択してデプロイを監視します。 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="OVF テンプレートを参照しているスクリーンショット。":::

   サービス メッシュのデプロイが正常に完了すると、サービスが緑色で表示されます。

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. アプライアンスの状態を調べてサービス メッシュの正常性を確認します。 
1. **[Interconnect]\(相互接続\)**  >  **[Appliances]\(アプライアンス\)** の順に選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

この手順全体の概要については、動画「[Azure VMware Solution: サービス メッシュ](https://www.youtube.com/embed/FyZ0d3P_T24)」を参照してください。



### <a name="optional-create-a-network-extension"></a>(省略可能) ネットワーク拡張機能を作成する

オンプレミス環境から Azure VMware Solution までネットワークを拡張する場合は、これらの手順に従います。

1. **[サービス]** で **[ネットワーク拡張機能]** を選択してから、 **[ネットワーク拡張機能の作成]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Azure VMware Solution へと拡張する各ネットワークを選択し、 **[Next]\(次へ\)** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="OVF テンプレートを参照しているスクリーンショット。":::

1. 拡張しようとしているネットワークごとに、オンプレミス ゲートウェイの IP を入力してから、 **[送信]** を選択します。 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="OVF テンプレートを参照しているスクリーンショット。":::

   ネットワークの拡張が完了するまでに数分かかります。 完了すると、状態が **[Extension complete]\(拡張完了\)** に変わります。

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

この手順全体の概要については、動画「[Azure VMware Solution: ネットワーク拡張機能](https://www.youtube.com/embed/cNlp0f_tTr0)」を参照してください。


## <a name="next-steps"></a>次のステップ

この段階に達して、アプライアンスの相互接続のトンネルの状態が **[UP]\(アップ\)** で緑色である場合は、VMware HCX を使用して、Azure VMware Solution VM の移行と保護を行うことができます。 Azure VMware Solution では、ワークロードの移行がサポートされています (ネットワーク拡張機能を使用または不使用)。 vSphere 環境のワークロードも、オンプレミスにネットワークを作成し、それらのネットワークに VM をデプロイして、移行できます。  

HCX の使用の詳細については、VMware のテクニカル ドキュメントを参照してください。

* [VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/index.html)
* [VMware HCX で仮想マシンを移行する](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [HCX の必須ポート](https://ports.vmware.com/home/VMware-HCX)
