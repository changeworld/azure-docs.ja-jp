---
title: リージョン間で Azure VMware Solution リソースを移動する
description: この記事では、Azure リージョン間で Azure VMware Solution リソースを移動する方法について説明します。
ms.custom: subject-moving-resources
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 116350ccf32c18e232f22788d5dd3d693d7e1e9d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323656"
---
# <a name="move-azure-vmware-solution-resources-to-another-region"></a>Azure VMware Solution のリソースを別のリージョンに移動する

>[!IMPORTANT]
>この記事の手順は、あるリージョンの Azure VMware Solution (ソース) を別のリージョンの Azure VMware Solution (ターゲット) に移動することのみを目的としています。 

いくつかの理由で Azure VMware Solution リソースを別のリージョンに移動できます。 たとえば、特定のリージョンのみで利用可能な機能やサービスのデプロイ、ポリシーとガバナンスの要件への適合、容量計画の要求への対応などです。 

この記事は、Azure リージョン A から Azure リージョン B など、ある Azure リージョンから別の Azure リージョンへの Azure VMware Solution の移行を計画し、実施する場合に役立ちます。


この図は、2 つの Azure VMware Solution 環境間の推奨される ExpressRoute 接続を示しています。  HCX サイトのペアリングとサービス メッシュは、2 つの環境間に作成されます。  HCX 移行トラフィックとレイヤー 2 拡張機能は、2 つの環境間を移動します (赤い線で示されています)。 VMware で推奨される HCX 計画については、「[HCX 移行の計画](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section1)」を参照してください。

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-2.png" alt-text="ソースとターゲットの Azure VMware Solution 環境間の ExpressRoute Global Reach 通信を示す図。" border="false":::

>[!NOTE]
>ワークフローをオンプレミスに戻す移行を行う必要はありません。トラフィックは、次のようにプライベート クラウド (ソースとターゲット) 間で流れるためです。
>
>**Azure VMware Solution プライベート クラウド (ソース) > ExpressRoute ゲートウェイ (ソース) > ExpressRoute ゲートウェイ (ターゲット) > Azure VMware Solution プライベート クラウド (ターゲット)**

この図は、両方の Azure VMware Solution 環境間の接続を示しています。 

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-connectivity-diagram.png" alt-text="ソースとターゲットの Azure VMware Solution 環境間の通信を示す図。" border="false":::


この記事では、以下を行う手順を説明します。 

> [!div class="checklist"]
> * 別の Azure リージョンへの移動を準備および計画する
> * 2 つの Azure VMware Solution プライベート クラウド間のネットワーク接続を確立する
> * Azure VMware Solution ソース環境から構成をエクスポートする
> * サポートされている構成要素を Azure VMware Solution ターゲット環境に再適用する
> * VMware HCX を使用してワークロードを移行する

## <a name="prerequisites"></a>前提条件

- 移行の問題 (ある場合) を回避するため、[VMware HCX アプライアンスが最新のパッチにアップグレードされている](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-02DB88E1-EC81-434B-9AE9-D100E427B31C.html)こと。

- ソースのローカル コンテンツ ライブラリが[公開されたコンテンツ ライブラリ](https://docs.vmware.com/en/VMware-Validated-Design/services/deployment-of-vrealize-suite-2019-on-vmware-cloud-foundation-310/GUID-59E0CBA1-2CF6-488D-AA58-C97C76FD8159.html?hWord=N4IghgNiBcIA4FcBGECWBnAFgAgMYHsA7AFwFMTs0kAnMagTxAF8g)であること。

## <a name="prepare"></a>準備

次の手順では、お使いの Azure VMware Solution プライベート クラウドを、別の Azure VMware Solution プライベート クラウドに移動するために準備する方法を示します。 

### <a name="export-the-source-configuration"></a>ソース構成をエクスポートする

1. ソースから、[拡張セグメント、ファイアウォール規則、ポートの詳細、およびルート テーブルをエクスポートします](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-FCE6567E-1174-49CC-90F1-BA7B695B28F0.html)。

1. [インベントリ リスト ビューの内容を CSV ファイル にエクスポートします](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.vcenterhost.doc/GUID-C0E8DD52-677E-464F-A3EA-044EE20B7B92.html)。

1. [ワークロードを移行グループ (移行ウェーブ) に分類します](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1)。


### <a name="deploy-the-target-environment"></a>ターゲット環境をデプロイする

ソース構成を移動する前に、[ターゲット環境をデプロイする](plan-private-cloud-deployment.md)必要があります。


### <a name="back-up-the-source-configuration"></a>ソースの構成をバックアップする

VC、NSX-T、ファイアウォール ポリシーおよび規則を含む Azure VMware Solution (ソース) 構成をバックアップします。 

- **コンピューティング:** 既存のインベントリ構成をエクスポートします。 インベントリのバックアップには、RVtool (オープンソース アプリ) を使用できます。

- **ネットワークとファイアウォール ポリシーおよび規則:** Azure VMware Solution ターゲットで、ソース環境と同じネットワーク セグメントを作成します。

Azure VMware Solution では、すべてのバックアップ ソリューションがサポートされています。 インストール、データのバックアップ、バックアップの復元を行うには、CloudAdmin 特権が必要です。 詳細については、[Azure VMware Solution VM のバックアップ ソリューション](ecosystem-back-up-vms.md)に関する記事を参照してください。

- Commvault ソリューションを使用した VM ワークロードのバックアップ:

   - Azure VMware Solution vCenter の Command Center から [VMware クライアントを作成します](https://documentation.commvault.com/commvault/v11_sp20/article?p=119380.htm)。

   - バックアップに必要な VM を含む [VM グループを作成します](https://documentation.commvault.com/commvault/v11_sp20/article?p=121182.htm)。

   - [VM グループに対してバックアップを実行します](https://documentation.commvault.com/commvault/v11_sp20/article?p=121657.htm)。

   - [VM を復元します](https://documentation.commvault.com/commvault/v11_sp20/article?p=87275.htm)。

- [Veritas NetBackup ソリューション](https://vrt.as/nb4avs)を使用した VM ワークロードのバックアップ。 

>[!TIP]
>[Azure Resource Mover](../resource-mover/move-region-within-resource-group.md?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) を使用して、リージョン間移動がサポートされているリソース (Azure VMware Solution に依存します) の一覧を確認して移行できます。

### <a name="locate-the-source-expressroute-circuit-id"></a>ソース ExpressRoute 回線 ID を見つける

1. ソースから [Azure portal](https://portal.azure.com/) にサインインします。

2. **[管理]**  >  **[接続]**  >  **[ExpressRoute]** の順に選択します。

3. ソースの **ExpressRoute ID** をコピーします。  これは、プライベート クラウド間でピアリングするために必要です。


### <a name="create-the-targets-authorization-key"></a>ターゲットの承認キーを作成する

1. ターゲットから [Azure portal](https://portal.azure.com/) にサインインします。

1. **[管理]**  >  **[接続]**  >  **[ExpressRoute]** の順に選択し、 **[+ Request an authorization key]\(+ 承認キーを要求する\)** を選択します。

   :::image type="content" source="media/expressroute-global-reach/start-request-authorization-key.png" alt-text="ExpressRoute の承認キーを要求する方法を示すスクリーンショット。" border="true" lightbox="media/expressroute-global-reach/start-request-authorization-key.png":::

1. これに名前を指定し、 **[作成]** を選択します。

   キーの作成には約 30 秒かかることがあります。 作成された新しいキーが、プライベート クラウドの承認キーの一覧に表示されます。

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="ExpressRoute Global Reach の承認キーを示すスクリーンショット。":::
  
1. 承認キーと ExpressRoute ID をコピーします。 これらは、ピアリングを行う際に必要になります。 承認キーはしばらくすると消えるため、表示されたらすぐにコピーしてください。

### <a name="peer-between-private-clouds"></a>プライベート クラウド間のピアリング

両方の環境の ExpressRoute 回線 ID と承認キーを用意したので、ソースをターゲットにピアリングできます。 プライベート クラウドの ExpressRoute 回線のリソース ID と承認キーを使用して、ピアリングを完了します。
 
1. ソースの ExpressRoute 回線と同じサブスクリプションを使用して、ターゲットから [Azure portal](https://portal.azure.com) にサインインします。

1. [管理] で、 **[接続]**  >  **[ExpressRoute Global Reach]**  >  **[追加]** の順に選択します。

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Azure VMware Solution プライベート クラウドの [ExpressRoute Global Reach] タブを示すスクリーンショット。":::

1. 前の手順で作成した ExpressRoute 回線 ID とターゲットの承認キーを貼り付けます。  次に、 **[作成]** を選択します。

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="接続情報を入力するためのダイアログを示すスクリーンショット。":::   

### <a name="create-a-site-pairing-between-private-clouds"></a>プライベート クラウド間のサイトのペアリングを作成する

接続を確立したら、VM の移行を容易にするために、プライベート クラウド間の VMware HCX サイトのペアリングを作成します。 Azure VMware Solution で、VMware HCX Cloud Manager をデータセンターの VMware HCX Manager コネクタと接続またはペアリングできます。 

1. ソースの vCenter にサインインし、 **[ホーム]** の下の **[HCX]** を選択します。

1. **[Infrastructure]\(インフラストラクチャ\)** で、 **[Site Pairing]\(サイトのペアリング\)** を選択し、 **[Connect To Remote Site]\(リモート サイトへの接続\)** オプション (画面の中央) を選択します。 

1. 前にメモしておいた Azure VMware Solution HCX Cloud Manager の URL または IP アドレス (`https://x.x.x.9`)、Azure VMware Solution のユーザー名 (cloudadmin\@vsphere.local)、およびパスワードを入力します。 次に、 **[接続]\(Connect\)** を選択します。

   > [!NOTE]
   > サイト ペアを正常に確立するには、次のようにします。
   > * VMware HCX コネクタがポート 443 経由で HCX Cloud Manager IP にルーティングできる必要があります。
   >
   > * vCenter へのサインインに使用したのと同じパスワードを使用します。 このパスワードは、最初のデプロイ画面で定義したものです。

   Azure VMware Solution の VMware HCX Cloud Manager とオンプレミスの VMware HCX コネクタが接続 (ペアリング) されたことを示す画面が表示されます。

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Azure VMware Solution の HCX Manager と VMware HCX コネクタのペアリングを示すスクリーンショット。":::

### <a name="create-a-service-mesh-between-private-clouds"></a>プライベート クラウド間にサービス メッシュを作成する

> [!NOTE]
> Azure VMware Solution を使用してサービス メッシュを正常に確立するには:
>
> * オンプレミスの VMware HCX コネクタの "アップリンク" ネットワーク プロファイル アドレスと Azure VMware Solution HCX クラウドの "アップリンク" ネットワーク プロファイル アドレスの間で、ポート UDP 500/4500 を開きます。
>
> * [VMware HCX の必須ポート](https://ports.vmware.com/home/VMware-HCX)を確認してください。

1. **[インフラストラクチャ]** で、 **[相互接続]**  >  **[サービス メッシュ]**  >  **[サービス メッシュの作成]** と選択します。    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="サービス メッシュの作成を開始するための選択のスクリーンショット。" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 事前設定されたサイトを確認して、**Continue\(続行\)** を選択します。 

   > [!NOTE]
   > これが最初のサービス メッシュ構成である場合は、この画面を変更する必要はありません。  

1. ドロップダウン リストでソースとリモートのコンピューティング プロファイルを選択してから、 **[Continue]\(続行\)** を選択します。  

   選択により、VM で VMware HCX サービスを使用できるリソースが定義されます。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="ソース コンピューティング プロファイルの選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="リモート コンピューティング プロファイルの選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 有効にするサービスを確認して、 **[Continue]\(続行\)** を選択します。  

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

## <a name="move"></a>詳細ビュー

次の手順では、お使いの Azure VMware Solution プライベート クラウドのリソースを、異なるリージョンの別の Azure VMware Solution プライベート クラウドに移動する方法を示します。 

このセクションでは、以下の移行を行います。

- リソース プールの構成とフォルダーの作成

- VM テンプレートと関連付けられたタグ

- ソースのポート グループおよび関連付けられている VLAN に基づく論理セグメントの配置 

- ネットワーク セキュリティのサービスとグループ

- ソースのファイアウォール ポリシーに基づく、ゲートウェイ ファイアウォール ポリシーおよび規則

### <a name="migrate-the-source-vsphere-configuration"></a>ソースの vSphere 構成を移行する

この手順では、ソースの vSphere 構成をコピーし、ターゲット環境に移動します。 

1. ソースの vCenter から、同じリソース プール構成を使用し、ターゲットの vCenter で [同じリソース プール構成を作成します](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html#example-creating-resource-pools-4)。

2. ソースの vCenter から、同じ VM フォルダー名を使用し、ターゲットの vCenter の **[フォルダー]** の下に[同じ VM フォルダーを作成します](https://docs.vmware.com/en/VMware-Validated-Design/6.1/sddc-deployment-of-cloud-operations-and-automation-in-the-first-region/GUID-9D935BBC-1228-4F9D-A61D-B86C504E469C.html)。

3. VMware HCX を使用して、ソースの vCenter からターゲットの vCenter にすべての VM テンプレートを移行します。

   1. ソースから、既存のテンプレートを VM に変換し、それらをターゲットに移行します。

   2. ターゲットから、VM を VM テンプレートに変換します。

4. ソース環境から、同じ VM タグ名を使用して、[ターゲットの vCenter にタグを作成します](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vcenterhost.doc/GUID-05323758-1EBF-406F-99B6-B1A33E893453.html)。 

5. ソースの vCenter コンテンツ ライブラリから、サブスクライブ済みライブラリのオプションを使用して、ISO、OVF、OVA、および VM のテンプレートをターゲットのコンテンツ ライブラリにコピーします。

   1. コンテンツ ライブラリがまだ公開されていない場合は、 **[Enable publishing]\(公開の有効化\)** オプションを選択します。

   2. ソースのコンテンツ ライブラリから、公開されたライブラリの URL をコピーします。

   3. ターゲットから、ソースのライブラリの URL を使用して、[サブスクライブ済みコンテンツ ライブラリを作成します](deploy-vm-content-library.md)。

   4. **[Sync Now]\(今すぐ同期\)** を選択します。


### <a name="configure-the-target-nsx-t-environment"></a>ターゲットの NSX-T 環境を構成する

この手順では、ソースの NSX-T 構成を使用して、ターゲットの NSX-T 環境を構成します。

>[!NOTE]
>ソースの NSX-T には複数の機能が構成されているはずです。そのため、ソースの NSX-T からコピーまたは読み取りを行い、ターゲットのプライベート クラウドにそれを再作成する必要があります。 L2 拡張機能を使用して、ターゲットの AVS プライベート クラウドにソースを移行する間は VM の IP アドレスと Mac アドレスを同じままにして、IP の変更や関連構成によるダウンタイムを回避してください。

1. 既定の Tier-1 ゲートウェイの下で、ターゲット環境で必要な[NSX ネットワーク コンポーネントを構成します](tutorial-nsx-t-network-segment.md)。

1. [セキュリティ グループの構成を作成します](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html)。

1. [分散ファイアウォールのポリシーおよび規則を作成します](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html)。

1. [ゲートウェイ ファイアウォールのポリシーおよび規則を作成します](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-DE6FE8CB-017E-41C8-85FC-D71CF27F85C2.html)。

1. [DHCP サーバーまたは DHCP リレー サービスを作成します](configure-dhcp-azure-vmware-solution.md)。 

1. [ポートのミラーリングを構成します](configure-port-mirroring-azure-vmware-solution.md)。

1. [DNS フォワーダーを構成します](configure-dns-azure-vmware-solution.md)。

1. [新しい Tier-1 ゲートウェイを構成します (既定値以外)](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-A6042263-374F-4292-892E-BC86876325A4.html)。  この構成は、ソースに構成されている NSX-T に基づいています。 

### <a name="migrate-the-vms-from-the-source"></a>ソースから VM を移行する 

この手順では、VMware HCX を使用して、ソースからターゲットに VM を移行します。 ソースからレイヤー 2 拡張機能を実行し、HCX を使用して vMotion を実行することで、中断を最小限に抑えながら VM をソースからターゲットに移動するオプションを利用できます。 

vMotion 以外に、Bulk や Cold vMotion などの他の方法もお勧めします。  詳細については、下記のリンクをクリックしてください。

- [HCX 移行を計画する](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1)

- [HCX を使用して仮想マシンを移行する](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-14D48C15-3D75-485B-850F-C5FCB96B5637.html)

### <a name="cutover-extended-networks"></a>拡張ネットワークを切り替える 

この手順では、最後のゲートウェイの切り替えを実行して、拡張ネットワークを終了します。 また、ソースの Azure VMware Solution 環境からターゲット環境にゲートウェイを移動 (移行) します。

>[!IMPORTANT]
>ターゲットの Azure VMware Solution 環境への VLAN ワークロードの移行後に、ゲートウェイの切り替えを行う必要があります。 また、ソースとターゲットの環境に対する VM の依存関係は一切存在してはなりません。

ゲートウェイの切り替え前に、移行されたすべてのワークロードサービスとパフォーマンスを確認します。 アプリケーションと Web サービスの所有者が (待機時間の問題以外の) パフォーマンスに納得したら、ゲートウェイの切り替えを進めることができます。  切り替えが完了したら、パブリック DNS の A と PTR のレコードを変更する必要があります。 

VMware の推奨事項については、「[拡張ネットワークの切り換え](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section9)」を参照してください。


### <a name="public-ip-dnat-for-migrated-dmz-vms"></a>移行された DMZ VM のパブリック IP DNAT

この時点では、ワークロードがターゲット環境に移行されています。 これらのアプリケーション ワークロードは、パブリック インターネットから到達できる必要があります。 ターゲット環境には、任意のアプリケーションをホスティングする 2 つの方法が用意されています。 アプリケーションは、以下が可能です。

- アプリケーション ゲートウェイのロード バランサーでホスティングおよび発行される。

- vWAN のパブリック IP 機能を使用して発行される。

パブリック IP は通常、Azure ファイアウォールに変換された宛先 NAT です。 DNAT 規則に従い、ファイアウォール ポリシーによって、パブリック IP アドレス要求がポートを持つプライベート アドレス (Web サーバー) に変換されます。 詳細については、[Azure Virtual WAN でパブリック IP 機能を使用する方法](./enable-public-internet-access.md)に関する記事を参照してください。

>[!NOTE]
>SNAT は Azure VMware Solution で既定で構成されているため、[管理] タブにある Azure VMware Solution のプライベート クラウド接続設定から SNAT を有効にする必要があります。

## <a name="decommission"></a>Decommission

この最後の手順では、ネットワーク構成を含め、すべての VM ワークロードが正常に移行されたことを確認します。 依存関係がない場合は、HCX サービス メッシュ、サイトのペアリング、およびネットワーク接続をソース環境から切断できます。 

>[!NOTE]
>プライベート クラウドの使用を停止すると、構成とデータが失われるため、元に戻すことはできません。


## <a name="next-steps"></a>次のステップ

各項目の詳細情報

- [Microsoft.AVS の移動操作のサポート](../azure-resource-manager/management/move-support-resources.md#microsoftavs)
- [ネットワーク リソースの移動ガイダンス](../azure-resource-manager/management/move-limitations/networking-move-limitations.md)
- [仮想マシンの移動に関するガイダンス](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)
- [App Service リソースの移動に関するガイダンス](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)
