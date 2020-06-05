---
title: Hybrid Cloud Extension (HCX) をインストールする
description: Azure VMware Solution (AVS) プライベート クラウド用の VMware Hybrid Cloud Extension (HCX) ソリューションを設定します
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: dc5f7f82b83c82538b2d5a7b4c87131afb3fcc20
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873645"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Azure VMware Solution 用の HCX をインストールする

この記事では、Azure VMware Solution (AVS) プライベート クラウド用の VMware Hybrid Cloud Extension (HCX) ソリューションを設定する手順を説明します。 HCX Advanced (既定のインストール) では最大 3 つの外部サイトがサポートされており、各外部サイトでは、HCX Enterprise マネージャーまたはコネクタをインストールしてアクティブ化する必要があります。
HCX を使用すると、さまざまな組み込みの HCX でサポートされた移行の種類により、クラウドやその他の接続されたサイトに VMware のワークロードを移行できます。 3 つより多くのサイトが必要な場合は、サポートを通じて HCX Enterprise アドオンを有効にすることができます。 HCX Enterprise では、一般提供 (GA) 後にはお客様に対して追加料金が発生しますが、[追加の機能](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)が提供されます。

「[開始する前に](#before-you-begin)」、「[ソフトウェア バージョンの要件](#software-version-requirements)」、「[前提条件](#prerequisites)」を最初によく確認してください。 

その後、以下のことを行うのに必要なすべての手順について説明します。

> [!div class="checklist"]
> * オンプレミスの HCX OVA をデプロイする
> * HCX をアクティブ化して構成する
> * ネットワーク アップリンクとサービス メッシュを構成する
> * アプライアンスの状態を確認してセットアップを完了する

セットアップが完了した後、推奨される次の手順を示します。

## <a name="before-you-begin"></a>開始する前に
    
* 基本的な AVS Software Defined Datacenter (SDDC) の[チュートリアル シリーズ](tutorial-network-checklist.md)を確認します
* HCX ユーザー ガイドなど、[VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/index.html)を確認して参照します
* VMware のドキュメント「[Vmware HCX で仮想マシンを移行する](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)」を確認します
* 必要に応じて、「[VMware HCX のデプロイに関する考慮事項](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)」を確認します
* 必要に応じて、HCX についての VMware vSphere [ブログシリーズ](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)など、HCX に関連する VMware の資料を確認します。 
* AVS サポート チャネルを通じて、AVS HCX Enterprise のアクティブ化を注文します。

コンピューティング リソースとストレージ リソースに対するワークロードのサイズの決定は、AVS Private Cloud HCX ソリューションの使用を準備する際に不可欠な計画手順です。 このサイズ決定手順は、最初のプライベート クラウド環境計画の一環として行う必要があります。 

## <a name="software-version-requirements"></a>ソフトウェア バージョンの要件
インフラストラクチャ コンポーネントでは、必要な最小バージョンが実行されている必要があります。 
                                                         
| コンポーネントの種類                                                          | ソース環境の要件                                                                   | ターゲット環境の要件                                                                      |
| --- | --- | --- |
| vCenter Server                                                          | 5.1<br/><br/>5\.5 U1 以前を使用している場合は、HCX 操作用にスタンドアロンの HCX ユーザー インターフェイスを使用します。         | 6.0 U2 以降                                                                                          |
| ESXi                                                                    | 5.0                                                                                               | ESXi 6.0 以降                                                                                        |
| NSX                                                                     | ソースの論理スイッチの HCX ネットワーク拡張機能の場合:NSXv 6.2 以降または NSX-T 2.4 以降              | NSXv 6.2 以降または NSX-T 2.4 以降<br/>HCX 近接ルーティングの場合:NSXv 6.4 以降 (NSX-T では近接ルーティングはサポートされていません) |
| vCloud Director                                                         | 必須ではありません - ソース サイトの vCloud Director との相互運用性はありません | ターゲット環境が vCloud Director と統合されている場合、最小値は 9.1.0.2 です。              |

## <a name="prerequisites"></a>前提条件

* オンプレミスと AVS SDDC ER 回線の間に、グローバル リーチを構成する必要があります。

* オンプレミスと AVS SDDC の間で、すべての必要なポートを開く必要があります ([VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)を参照)。

* オンプレミスの HCX Manager 用に 1 つの IP アドレス、相互接続 (IX) とネットワーク拡張機能 (NE) アプライアンス用に 2 つ以上の IP アドレス。

* オンプレミスの HCX IX と NE アプライアンスは、vCenter と ESXi インフラストラクチャに接続できる必要があります。

* WAN 相互接続アプライアンスをデプロイするには、Azure portal で SDDC デプロイに使用される /22 CIDR ネットワーク アドレス ブロックに加えて、HCX に /29 ブロックが必要です。 ネットワークの計画でこれを考慮する必要があります。

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>VMware HCX OVA をオンプレミスにデプロイする

1. AV SDDC vCenter にサインインし、 **[HCX]** を選択します。

    ![AV vCenter で HCX を選択する](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. VMware HCX OVA ファイルをダウンロードするには、 **[Administration]\(管理\)**  >  **[System Updates]\(システム更新\)** を選択します。

    ![システムの更新プログラムを取得する](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。  

    ![OVF テンプレートを選択する](./media/hybrid-cloud-extension-installation/select-template.png)

1. 名前と場所を選択し、HCX をデプロイする必要があるリソースまたはクラスターを選択します。 次に、詳細情報と必要なリソースを確認します。  

    ![テンプレートの詳細を確認する](./media/hybrid-cloud-extension-installation/configure-template.png)

1. ライセンス条項を確認し、同意する場合は、必要なストレージとネットワークを選択します。 **[次へ]** を選択します。

1. **[Customize template]\(テンプレートのカスタマイズ\)** に、必要なすべての情報を入力します。 

    ![テンプレートのカスタマイズ](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. **[Next]\(次へ\)** を選択し、構成を確認し、 **[Finish]\(完了\)** を選択して、HCX OVA をデプロイします。

## <a name="activate-hcx"></a>HCX をアクティブ化する

インストールが完了したら、次の手順を実行します。

1. `https://HCXManagerIP:9443` で HCX Manager を開き、ユーザー名とパスワードを使用してサインインします。 

1. **[Licensing]\(ライセンス\)** で、 **[HCX Advanced Key]\(HCX Advanced キー\)** を入力します。  

    ![HCX キーを入力する](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX Manager では、インターネット アクセスが開かれているか、またはプロキシが構成されている必要があります。

1. vCenter を構成します。

    ![vCenter を構成する](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. **[Datacenter Location]\(データセンターの場所\)** で、必要に応じて、データセンターの場所を編集します。

    ![データベースの場所](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>HCX を構成する 

1. オンプレミスの vCenter にサインインし、 **[Home]\(ホーム\)**  >  **[HCX]** を選択します。

    ![VCenter の HCX](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. **[Infrastructure]\(インフラストラクチャ\)**  >  **[Site Pairing]\(サイトのペアリング\)**  >  **[Add a site pairing]\(サイトのペアリングの追加\)** を選択します。

    ![サイトのペアリングを追加する](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. **[Remote HCX URL]\(リモート HCX URL\)** 、 **[Username]\(ユーザー名\)** 、 **[Password]\(パスワード\)** を入力します。 次に、 **[接続]\(Connect\)** を選択します。

   システムに接続されたサイトが表示されます。
   
    ![サイトの接続](./media/hybrid-cloud-extension-installation/site-connection.png)

1. **[Interconnect]\(相互接続\)**  >  **[Multi-Site Service Mesh]\(複数サイト サービス メッシュ\)**  >  **[Network Profiles]\(ネットワーク プロファイル\)**  >  **[Create Network Profile]\(ネットワーク プロファイルの作成\)** を選択します。

    ![ネットワーク プロファイルを作成する](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. HCX IX および NE IP アドレス範囲を入力します (IX および NE アプライアンスには、少なくとも 2 つの IP アドレスが必要です)。
    
   ![IP アドレス範囲を入力する](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > ネットワーク拡張機能アプライアンス (HCX-NE) には、分散仮想スイッチ (DVS) との間に一対一の関係があります。  

1. 次に、 **[Compute profile]\(コンピューティング プロファイル\)**  >  **[Create compute profile]\(コンピューティング プロファイルの作成\)** を選択します。

1. コンピューティング プロファイル名を入力し、 **[Continue]\(続行\)** を選択します。  

    ![コンピューティング プロファイルを作成する](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. 移行、ネットワーク拡張機能、pr ディザスター リカバリーなど、有効にするサービスを選択します。 **[続行]** をクリックします。

    ![サービスを選択する](./media/hybrid-cloud-extension-installation/select-services.png)

1. **[Select Service Resources]\(サービス リソースの選択\)** で、選択した HCX サービスを有効にする必要がある 1 つまたは複数のサービス リソースを選択します。 **[続行]** をクリックします。
    
   ![サービス リソースを選択する](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > ソース VM が HCX を使用した移行の対象となる特定のクラスターを選択します。

1. **[Datastore]\(データストア\)** を選択し、 **[Continue]\(続行\)** を選択します。 
      
    HCX 相互接続アプライアンスをデプロイするための各コンピューティング リソースとストレージ リソースを選択します。 複数のリソースを選択すると、HCX では容量が使い果たされるまで、最初に選択されたリソースが使用されます。  
    
    ![デプロイ リソースを選択する](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. **[Network Profiles]\(ネットワーク プロファイル\)** で作成した管理ネットワーク プロファイルを選択し、 **[Continue]\(続行\)** を選択します。  
      
    vCenter ホストと ESXi ホストの管理インターフェイスに到達できるネットワーク プロファイルを選択します。 そのようなネットワーク プロファイルをまだ定義していない場合は、ここで作成できます。  
    
    ![管理ネットワーク プロファイルを選択する](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. **[Network Uplink]\(ネットワーク アップリンク\)** を選択し、 **[Continue]\(続行\)** を選択します。
      
    次のいずれかが満たされるように、1 つまたは複数のネットワーク プロファイルを選択します。  
    * リモート サイトの相互接続アプライアンスに、このネットワーク経由で到達できる。  
    * リモート側アプライアンスが、このネットワーク経由で、ローカル相互接続アプライアンスに到達できる。  
    
    複数のサイトで共有されない直接接続のようなポイント ツー ポイント ネットワークを使用している場合は、コンピューティング プロファイルが複数のサイトで共有されるため、このステップを省略できます。 そのような場合は、相互接続サービス メッシュの作成時に、アップリンク ネットワーク プロファイルを上書きして指定することができます。  
    
    ![アップリンク ネットワーク プロファイルを選択する](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. **[vMotion Network Profile]\(vMotion ネットワーク プロファイル\)** を選択し、 **[Continue]\(続行\)** を選択します。
      
    ESXi ホストの vMotion インターフェイスに到達できるネットワーク プロファイルを選択します。 そのようなネットワーク プロファイルをまだ定義していない場合は、ここで作成できます。 vMotion ネットワークがない場合は、 **[Management Network Profile]\(管理ネットワーク プロファイル\)** を選択します。  
    
    ![vMotion ネットワーク プロファイルを選択する](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. **[vSphere Replication Network Profile]\(vSphere レプリケーション ネットワーク プロファイル\)** を選択し、 **[Continue]\(続行\)** を選択します。
      
    ESXi ホストの vSphere レプリケーション インターフェイスに到達できるネットワーク プロファイルを選択します。 ほとんどの場合、このプロファイルは管理ネットワーク プロファイルと同じです。  
    
    ![vSphere レプリケーション ネットワーク プロファイルを選択する](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. **[Distributed Switches for Network Extensions]\(ネットワーク拡張機能用分散スイッチ\)** を選択し、 **[Continue]\(続行\)** を選択します。  
      
    移行する仮想マシンが接続されるネットワークが存在する分散仮想スイッチを選択します。

    ![分散仮想スイッチを選択する](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. 接続ルールを確認し、 **[Continue]\(続行\)** を選択します。 **[Finish]\(完了\)** を選択して、コンピューティング プロファイルを作成します。  

    ![コンピューティング プロファイルを作成する](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

## <a name="configure-network-uplink"></a>ネットワーク アップリンクを構成する

次に、ネットワーク アップリンク用の AVS SDDC でネットワーク プロファイルの変更を構成します。

1. SDDC NSX-T にサインインして新しい論理スイッチを作成するか、オンプレミスと AVS SDDC の間のネットワーク アップリンクに使用できる既存の論理スイッチを使用します。

1. オンプレミスと AVS SDDC の通信に使用できる、AVS SDDC 内の HCX アップリンク用のネットワーク プロファイルを作成します。  
    
   ![アップリンク用のネットワーク プロファイルを作成する](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. 必要な L2 ネットワーク拡張機能に基づいて、ネットワーク プロファイルの名前と、少なくとも 4 または 5 つの空き IP アドレスを入力します。  
    
   ![アップリンク用のネットワーク プロファイルを構成する](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. **[Create]\(作成\)** を選択して、AVS SDDC の構成を完了します

## <a name="configure-service-mesh"></a>サービス メッシュを構成する

次に、オンプレミスと AVS SDDC の間のサービス メッシュを構成します。

1. AV SDDC vCenter にサインインし、 **[HCX]** を選択します。

1. **[Infrastructure]\(インフラストラクチャ\)**  >  **[Interconnect]\(相互接続\)**  >  **[Service Mesh]\(サービス メッシュ\)**  >  **[Create Service Mesh]\(サービス メッシュの作成\)** を選択します。  前の手順で作成したネットワーク プロファイルとコンピューティング プロファイルを構成します。    
      
    ![サービス メッシュを構成する](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

1. **[Create Service Mesh]\(サービス メッシュの作成\)** を選択し、 **[Continue]\(続行\)** を選択します。  
      
    ハイブリッド モビリティを有効にするペアのサイトを選択します。  
    
    ![ペアになっているサイトを選択する](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

1. **[Compute profile]\(コンピューティング プロファイル\)** を選択し、 **[Continue]\(続行\)** を選択します。
      
    ハイブリッド サービスを有効にするソース サイトとリモート サイトのそれぞれで 1 つのコンピューティング プロファイルを選択します。 選択すると、仮想マシンが HCX サービスを使用できるリソースが定義されます。  
      
    ![ハイブリッド サービスを有効にする](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

1. HCX に対して有効にするサービスを選択し、 **[Continue]\(続行\)** をします。  
      
    ![HCX サービスを選択する](./media/hybrid-cloud-extension-installation/hcx-services.png)

1. **[Advanced Configuration - Override Uplink Network profiles]\(詳細構成 - アップリンク ネットワーク プロファイルのオーバーライド\)** で、 **[Continue]\(続行\)** を選択 ます。  
      
    アップリンク ネットワーク プロファイルは、リモート サイトの相互接続アプライアンスに到達できるネットワークに接続するために使用されます。  
      
    ![アップリンク プロファイルをオーバーライドする](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

1. **[Advanced Configuration – Network Extension Appliance Scale Out]\(詳細構成 – ネットワーク拡張機能アプライアンスのスケールアウト\)** で、 **[Configure the Network Extension Appliance Scale Out]\(ネットワーク拡張機能アプライアンスのスケールアウトの構成\)** を選択します。 
      
    ![ネットワーク拡張機能のスケールアウト](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

1. DVS スイッチの数に対応するアプライアンスの数を入力します。  
      
    ![アプライアンスの数を構成する](./media/hybrid-cloud-extension-installation/appliance-scale.png)

1. **[Advanced Configuration - Traffic Engineering]\(詳細構成 - トラフィック エンジニアリング\)** で、 **[Continue]\(続行\)** を選択します。  
      
    ![トラフィック エンジニアリングを構成する](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

1. トポロジのプレビューを確認し、 **[Continue]\(続行\)** を選択します。 次に、このサービス メッシュのわかりやすい名前を入力し、 **[Finish]\(完了\)** を選択して完了します。  
      
    ![サービス メッシュを完了する](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    サービス メッシュがデプロイされて構成されます。  
      
    ![デプロイされたサービス メッシュ](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>アプライアンスの状態を確認する
アプライアンスの状態を確認するには、 **[Interconnect]\(相互接続\)**  >  **[Appliances]\(アプライアンス\)** を選択します。 
      
![アプライアンスの状態](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>次のステップ

アプライアンスの相互接続の **[Tunnel Status]\(トンネルの状態\)** が **[UP]\(アップ\)** で緑色になっている場合は、HCX を使用して AVS VM の移行と保護を行うことができます。 VMware のテクニカル ドキュメントの「[VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/index.html)」と「[Vmware HCX で仮想マシンを移行する](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)」を参照してください。
