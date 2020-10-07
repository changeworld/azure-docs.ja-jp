---
title: チュートリアル - VMware HCX をデプロイして構成する
description: Azure VMware Solution プライベート クラウドのために VMware HCX ソリューションをデプロイして構成する方法について説明します。
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 69832d1537f0f1be95d3283f543ef6e54187b58d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578942"
---
# <a name="deploy-and-configure-vmware-hcx"></a>VMware HCX をデプロイして構成する

この記事では、Azure VMWare Solution プライベート クラウドのために VMware HCX をデプロイして構成する手順について説明します。 VMware HCX を使用すると、さまざまな移行の種類を通して、Azure VMware Solution やその他の接続されたサイトに VMware のワークロードを移行できます。

VMware HCX Advanced (Azure VMware Solution に事前デプロイされたもの) では、最大 3 つのサイト接続 (オンプレミスからクラウド、またはクラウド間) がサポートされます。 VMware HCX Enterprise 用に 3 つより多いサイト接続が必要な場合は、[サポート要求](https://rc.portal.azure.com/#create/Microsoft.Support)を送信することで、[VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) アドオン (現在は*プレビュー*段階です) を有効にするオプションが用意されています。 VMware HCX Enterprise Edition (EE) は、Azure VMware Solution で、*プレビュー*の機能/サービスとして使用できます。 Azure VMware Solution 向けの VMware HCX EE は*プレビュー*段階ですが、無料の機能/サービスであり、プレビュー サービスの使用条件が適用されます。 VMware HCX EE サービスが GA になると、課金が切り替わるという通知が 30 日間前に届きます。 サービスのオン/オフを切り替えるオプションも用意されます。

開始する前に、「[開始する前に](#before-you-begin)」、「[ソフトウェア バージョンの要件](#software-version-requirements)」、「[前提条件](#prerequisites)」をよく確認してください。 

では、以下のために必要なすべての手順について説明します。

> [!div class="checklist"]
> * オンプレミスの VMware HCX OVA (コネクタ) をデプロイする
> * VMware HCX をアクティブにする
> * オンプレミス環境を Azure VMware Solution 環境とペアリングする。
> * 相互接続 (コンピューティング プロファイル、ネットワーク プロファイル、およびサービス メッシュ) を構成する
> * アプライアンスの状態を確認してセットアップを完了する。

完了したら、この記事の最後に示した、推奨される次の手順に進むことができます。  

## <a name="before-you-begin"></a>開始する前に
   
* 基本的な Azure VMware Solution Software Defined Datacenter (SDDC) の[チュートリアル シリーズ](tutorial-network-checklist.md)を確認します。
* HCX ユーザー ガイドなど、[VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/index.html)を確認して参照します。
* VMware のドキュメント「[Migrating Virtual Machines with VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)」 (Vmware HCX で仮想マシンを移行する) を確認します。
* 必要に応じて、「[VMware HCX Deployment Considerations](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)」 (VMware HCX のデプロイに関する考慮事項) を確認します。
* 必要に応じて、HCX についての VMware vSphere [ブログシリーズ](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)など、HCX に関連する VMware の資料を確認します。 
* 必要に応じて、Azure VMware Solution のサポート チャネルを通じて、Azure VMware Solution の HCX Enterprise のアクティブ化を要求します。
* WAN 相互接続アプライアンスをデプロイする場合、プライベート クラウドの作成のために、ユーザーが指定した `\22` から特定の CIDR 範囲が既に割り当てられています。

コンピューティングとストレージのリソースに対するワークロードのサイズ設定は、非常に重要な計画手順です。 サイズ決定手順には、初期プライベート クラウド環境計画の一環として対処します。 

Azure Migrate ポータルで [Azure VMware Solution の評価](../migrate/how-to-create-azure-vmware-solution-assessment.md)を完了することで、ワークロードのサイズ設定をすることもできます。

## <a name="software-version-requirements"></a>ソフトウェア バージョンの要件

インフラストラクチャ コンポーネントでは、必要な最小バージョンが実行されている必要があります。 
                                                         
| コンポーネントの種類    | ソース環境の要件    | ターゲット環境の要件   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>5\.5 U1 以前を使用している場合は、HCX 操作用にスタンドアロンの HCX ユーザー インターフェイスを使用します。  | 6.0 U2 以降   |
| ESXi   | 5.0    | ESXi 6.0 以降   |
| NSX    | ソースの論理スイッチの HCX ネットワーク拡張機能の場合: NSXv 6.2 以降または NSX-T 2.4 以降   | NSXv 6.2 以降または NSX-T 2.4 以降<br/><br/>HCX 近接ルーティングの場合: NSXv 6.4 以降 (NSX-T では近接ルーティングはサポートされていません) |
| vCloud Director   | 必須ではありません - ソース サイトの vCloud Director との相互運用性はありません | ターゲット環境を vCloud Director と統合する場合、最小値は 9.1.0.2 です。  |

## <a name="prerequisites"></a>前提条件

### <a name="network-and-ports"></a>ネットワークとポート

* オンプレミスと Azure VMware Solution SDDC ExpressRoute 回線との間に構成された [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md)。

* オンプレミス コンポーネントと、オンプレミスから Azure VMware Solution SDDC への間で、通信のために必要なポートをすべて開く必要があります ([VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)を参照してください)。

* オンプレミスの HCX IX と NE アプライアンスは、vCenter と ESXi インフラストラクチャに到達できる必要があります。

### <a name="ip-addresses"></a>IP アドレス

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>VMware HCX OVA をオンプレミスにデプロイする

>[!NOTE]
>仮想アプライアンスをオンプレミスの vCenter にデプロイする前に、VMware HCX OVA をダウンロードする必要があります。 

1. **cloudadmin** ユーザー資格情報を使用して、`https://x.x.x.9` のポート443 で Azure VMware Solution HCX Manager にサインインしてから、 **[サポート]** に移動します。

   >[!TIP]
   >HCX Manager の IP アドレスを特定するには、[Azure VMware Solution] ブレードで、 **[管理]**  >  **[接続]** と移動してから、 **[HCX]** タブを選択します。 
   >
   >VCenter のパスワードは、プライベート クラウドの設定時に定義されました。

1. **[ダウンロード]** リンクを選択して、VMware HCX OVA ファイルをダウンロードします。

1. 次に、オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。これはダウンロードした OVA ファイルです。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. HCX のデプロイ先となる場所の、名前と位置情報、リソース/クラスターを選択してから、詳細情報と必要なリソースを確認します。  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. ライセンス条項を確認し、同意する場合は、必要なストレージとネットワークを選択します。次に、 **[次へ]** を選択します。

1. **[Customize template]\(テンプレートのカスタマイズ\)** に、必要なすべての情報を入力します。 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. **[次へ]** を選択し、構成を確認してから、 **[完了]** を選択して HCX OVA をデプロイします。
     
   >[!NOTE]
   >一般に、現在デプロイ中の VMware HCX Manager は、クラスターの管理ネットワークにデプロイされます。  
   
   > [!IMPORTANT]
   > デプロイが完了したら、仮想アプライアンスの電源を手動でオンにする必要が生じることがあります。
   > HCX アプライアンスの電源投入後 10 ～ 15 分待機し、次の手順に進みます。

この手順のエンド ツー エンドの概要については、[Azure VMware Solution で VMware HCX アプライアンスをデプロイする](https://www.youtube.com/embed/BwSnQeefnso)ことに関するビデオを参照してください。 


## <a name="activate-vmware-hcx"></a>VMware HCX をアクティブにする

VMware HCX OVA をオンプレミスにデプロイすると、VMware HCX をアクティブにする準備ができます。 VMware HCX をアクティブにする前に、ライセンスを取得する必要があります。

1. Azure VMware Solution で、 **[管理]**  >  **[接続]** と移動し、 **[HCX]** タブを選択してから **[追加]** を選択します。

1. `https://HCXManagerIP:9443` でオンプレミスの VMware HCX Manager にサインインし、**管理者**のユーザー資格情報でサインインします。 

   > [!IMPORTANT]
   > VMware HCX Manager の IP アドレスと共に、必ず `9443` のポート番号を含めてください。

1. **[Licensing]\(ライセンス\)** で、 **[HCX Advanced Key]\(HCX Advanced キー\)** を入力します。  
   
    > [!NOTE]
    > VMware HCX Manager では、インターネット アクセスが開かれているか、プロキシが構成されている必要があります。

1. **[データセンターの場所]** で、オンプレミスの VMware HCX Manager をインストールする最も近い場所を指定します。

1. **[システム名]** を変更するか、既定値をそのまま使用します。
   
1. 後で終了こと、または続行することが可能です。続行するには、 **[はい、続行します]** を選択します。
    
1. **[VCenter の接続]** で、お使いの vCenter サーバーの FQDN または IP アドレスと適切な資格情報を入力し、 **[続行]** を選択します。
   
1. **[SSO/PSC の構成]** で、お使いの PSC の FQDN または IP アドレスを入力してから、 **[続行]** を選択します。
   
   >[!NOTE]
   >一般に、vCenter の FQDN/IP と同じです。

1. すべての入力が正しいことを確認し、 **[再起動]** を選択します。
    
   > [!NOTE]
   > 再起動後、次の手順の操作が求められるまでに待機時間が生じます。
   >
   >サービスの再起動後は、表示される画面に vCenter が緑色で表示されてることが重要です。 vCenter と SSO のどちらにも、適切な構成パラメーターがあり、それらは前の画面と同じにする必要があります。

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

この手順の全体を網羅した概要については、[Azure VMware Solution で VMware HCX をアクティブにする](https://www.youtube.com/embed/BkAV_TNYxdE)ことに関するビデオを参照してください。


## <a name="configure-vmware-hcx"></a>VMware HCX を構成する

これで、サイトのペアリングを追加し、ネットワークとコンピューティングのプロファイルを作成し、移行、ネットワーク拡張、ディザスター リカバリーなどのサービスを有効にする準備ができました。 

### <a name="add-a-site-pairing"></a>サイトのペアリングを追加する

Azure VMware Solution で、VMware HCX Manager をデータセンターの VMware HCX Manager と接続 (ペアリング) できます。 

1. オンプレミスの vCenter にサインインし、 **[ホーム]** の下の **[HCX]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. **[インフラストラクチャ]** で、 **[サイトのペアリング]** を選択し、 **[リモート サイトへの接続]** オプション (画面の中央) を選択します。 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. **リモート HCX の URL または IP アドレス**、Azure VMware Solution の cloudadmin@vsphere.local のユーザー名と**パスワード**を入力してから、 **[接続]** を選択します。

   > [!NOTE]
   > **リモート HCX の URL** は、Azure VMware Solution プライベート クラウドの HCX Manager で、通常は管理ネットワークの ". 9" アドレスです。  たとえば、vCenter が 192.168.4.2 の場合、HCX URL は 192.168.4.9 になります。
   >
   > **パスワード**は、vCenter へのサインインに使用したのと同じパスワードになります。 このパスワードは、最初のデプロイ画面で定義したものです。

   Azure VMware Solution の HCX Manager と、オンプレミスの HCX Manager (ペアリング済み) を示す画面が表示されます。

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。":::

この手順の全体を網羅した概要については、[Azure VMware Solution で VMware HCX のサイトをペアリングする](https://www.youtube.com/embed/sKizDCRHOko)ことに関するビデオを参照してください。



### <a name="create-network-profiles"></a>ネットワーク プロファイルを作成する

VMware HCX では、いくつかの仮想アプライアンスがデプロイ (自動化) されますが、複数の IP セグメントが必要です。  ネットワーク プロファイルを作成するときには、IP セグメントを定義します。これらは、[VMware HCX ネットワーク セグメントのデプロイ前準備と計画段階](production-ready-deployment-steps.md#vmware-hcx-network-segments)の間に特定したものです。

4 つのネットワーク プロファイルを作成します。

   - 管理
   - vMotion
   - レプリケーション
   - アップリンク

1. **[インフラストラクチャ]** で、 **[Interconnect]\(相互接続\)**  >  **[Multi-Site Service Mesh]\(マルチサイト サービス メッシュ\)**  >  **[Network Profiles]\(ネットワーク プロファイル\)**  >  **[Create Network Profile]\(ネットワーク プロファイルの作成\)** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. ネットワーク プロファイルごとに、ネットワークとポート グループを選択し、名前を指定します。その特定セグメントの IP プールを作成したら、 **[作成]** を選択します。 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。":::

この手順の全体を網羅した概要については、[Azure VMware Solution で VMware HCX のネットワーク プロファイルを作成する](https://www.youtube.com/embed/NhyEcLco4JY)ことに関するビデオを参照してください。


### <a name="create-compute-profile"></a>コンピューティング プロファイルを作成する

1. **[コンピューティング プロファイル]**  >  **[コンピューティング プロファイルの作成]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. プロファイルの名前を入力し、 **[続行]** を選択します。  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. 有効にするサービス (移行、ネットワーク拡張機能、またはディザスター リカバリーなど) を選択し、 **[Continue]\(続行\)** を選択します。
  
   > [!NOTE]
   > 通常、ここでは何も変更されません。

1. **[サービス リソースの選択]** で、選択した VMware HCX サービスに対して有効にするサービス リソース (クラスター) を 1 つ以上選択します。  

1. オンプレミスのデータセンター内にクラスターが表示されたら、 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. **[データストアの選択]** から VMware HCX 相互接続アプライアンスをデプロイするためのデータストア ストレージ リソースを選択してから、 **[続行]** を選択します。

   複数のリソースを選択すると、VMware HCX では容量が使い果たされるまで、最初に選択されたリソースが使用されます。   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. **[管理ネットワーク プロファイルの選択]** で、前の手順で作成した管理ネットワーク プロファイルを選択してから、 **[続行]** を選択します。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > 管理ネットワーク プロファイルを使用すると、VMware HCX アプライアンスが vCenter と通信できるようになり、ESXi ホストに到達できます。

1. **アップリンク ネットワーク プロファイル**を選択します。これは、前の手順で作成したアップリンク ネットワーク プロファイルです。次に、 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. **vMotion ネットワーク プロファイル**を選択します。これは、前の手順で作成した vMotion ネットワーク プロファイルです。次に、 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. **vSphere レプリケーション ネットワーク プロファイル**を選択します。これは、前の手順で作成したレプリケーション ネットワーク プロファイルです。次に、 **[続行]** を選択します。

   ほとんどの場合、レプリケーション ネットワーク プロファイルは管理ネットワーク プロファイルと同じです。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. **ネットワーク拡張機能の分散スイッチ**を選択します。これには、レイヤー 2 拡張ネットワーク上の Azure VMware Solution に移行される仮想マシンが含まれる分散仮想スイッチです。次に、 **[続行]** を選択します。

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 接続ルールを確認し、 **[Continue]\(続行\)** を選択します。  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. **[Finish]\(完了\)** を選択して、コンピューティング プロファイルを作成します。

   下に示すような画面が表示されます。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

この手順の全体を網羅した概要については、[Azure VMware Solution で VMware HCX のコンピューティング プロファイルを作成する](https://www.youtube.com/embed/qASXi5xrFzM)ことに関するビデオを参照してください。




### <a name="create-service-mesh"></a>サービス メッシュを作成する

ここで、オンプレミスと Azure VMware Solution SDDC の間のサービス メッシュを構成します。

1. **[インフラストラクチャ]** で、 **[相互接続]**  >  **[サービス メッシュ]**  >  **[サービス メッシュの作成]** と選択します。    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 事前にデータが格納されたサイトを確認してから、 **[続行]** を選択します。 

   >[!NOTE]
   >これが最初のサービス メッシュ構成である場合は、この画面を変更する必要はありません。  

1. ドロップダウンでソースとリモートのコンピューティング プロファイルを選択してから、 **[続行]** を選択します。  

   選択により、VM で VMware HCX サービスを使用できるリソースが定義されます。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 有効にするサービスを確認したら、 **[続行]** を選択します。  

1. **[Advanced Configuration - Override Uplink Network profiles]\(詳細構成 - アップリンク ネットワーク プロファイルのオーバーライド\)** で、 **[Continue]\(続行\)** を選択 ます。  アップリンク ネットワーク プロファイルにより、リモート サイトの相互接続アプライアンスに到達できるネットワークに接続されます。  
  
1. **[詳細構成 - ネットワーク拡張機能アプライアンスのスケールアウト]** で、内容を見直して **[続行]** を選択します。 

1. **[詳細構成 - トラフィック エンジニアリング]** で、内容を見直して必要と思われる変更を加えたら、 **[続行]** を選択します。

1. トポロジのプレビューを確認し、 **[Continue]\(続行\)** を選択します。

1. このサービス メッシュのためにわかりやすい名前を入力し、 **[完了]** を選択して完了します。  

1. **[タスクを表示]** を選択してデプロイを監視します。 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。":::

   サービス メッシュのデプロイが正常に完了すると、サービスが緑色で表示されます。

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. アプライアンスの状態を調べてサービス メッシュの正常性を確認し、 **[相互接続]**  >  **[アプライアンス]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

この手順の全体を網羅した概要については、[Azure VMware Solution で VMware HCX のサービス メッシュを作成する](https://www.youtube.com/embed/FyZ0d3P_T24)ことに関するビデオを参照してください。



### <a name="optional-create-a-network-extension"></a>(省略可能) ネットワーク拡張機能を作成する

オンプレミス環境から Azure VMware Solution までネットワークを拡張する場合は、次の手順に従ってこれらのネットワークを拡張します。

1. **[サービス]** で **[ネットワーク拡張機能]** を選択してから、 **[ネットワーク拡張機能の作成]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Azure VMware Solution へと拡張するネットワークをそれぞれ選択したら、 **[次へ]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。":::

1. 拡張しようとしているネットワークごとに、オンプレミス ゲートウェイの IP を入力してから、 **[送信]** を選択します。 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。":::

   ネットワークの拡張が完了するまでに数分かかります。 完了すると、状態が **[拡張完了]** に変わります。

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="オンプレミスの vCenter に移動し、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。" lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

この手順の全体を網羅した概要については、[Azure VMware Solution での VMware HCX のネットワーク拡張](https://www.youtube.com/embed/cNlp0f_tTr0)に関するビデオを参照してください。


## <a name="next-steps"></a>次の手順

この段階に達して、アプライアンスの相互接続の [トンネルの状態] が **[アップ]** で緑色である場合は、VMware HCX を使用して、Azure VMware Solution VM の移行と保護を行うことができます。  Azure VMware Solution では、ワークロードの移行がサポートされています (ネットワーク拡張機能を使用)。  ネットワークを作成し、それらのネットワークに VM をデプロイすると、オンプレミスの vSphere 環境であってもワークロードを移行できます。  詳細については [VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/index.html)を、また、HCX の使用の詳細については VMware テクニカル ドキュメントの「[VMware HCX による仮想マシンの移行](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)」を参照してください。
