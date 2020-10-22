---
title: Azure VMware Solution 上に Horizon をデプロイする
description: Azure VMware Solution 上に VMware Horizon をデプロイする方法について説明します。
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 9f8951c1c346eb15ac981b99a4dbf1541f3e3eed
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078886"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Azure VMware Solution 上に Horizon をデプロイする 

>[!NOTE]
>このドキュメントでは、VMware Horizon 製品に焦点を当てています。 これは以前は Horizon 7 と呼ばれており、製品名が Horizon 7 から Horizon に変わっています。 Horizon は Horizon Cloud on Azure とは異なるソリューションですが、共有コンポーネントがいくつかあります。 Azure VMware Solution の主な利点は、単純なサイズ変更方法と、VMware Cloud Foundation 管理が Azure portal に統合されていることです。

[VMware Horizon](https://www.vmware.com/products/horizon.html)® は、データ センターで実行され、シンプルで集中管理された仮想デスクトップおよびアプリケーション プラットフォームです。 どこからでも、あらゆるデバイスでエンド ユーザーに仮想デスクトップとアプリケーションを提供します。 Horizon を使用すると、Windows 仮想デスクトップ、Linux 仮想デスクトップ、リモート デスクトップ サーバー (RDS) でホストされているアプリケーション、デスクトップ、および物理マシンへの接続を作成して仲介することができます。

ここでは、Azure VMware Solution への Horizon のデプロイに焦点を当てます。 VMware Horizon に関する一般的な情報については、以下の Horizon の製品ドキュメントを参照してください。

* [VMware Horizon とは](https://www.vmware.com/products/horizon.html)

* [VMware Horizon の詳細情報](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Horizon の参照アーキテクチャ](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Azure VMware Solution に Horizon を導入する場合、Azure プラットフォームには 2 つの仮想デスクトップ インフラストラクチャ (VDI) ソリューションが用意されています。 次の図は、主な相違点の概要を示しています。

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Azure VMware Solution 上の Horizon と Horizon Cloud on Azure の違い" border="false":::

Horizon 8 リリース ラインの Horizon 2006 以降のバージョンでは、オンプレミス デプロイと Azure VMware Solution デプロイの両方がサポートされています。 オンプレミスでサポートされているものの、Azure VMware Solution ではサポートされていない Horizon の機能がいくつかあります。 また、Horizon エコシステムのその他の製品もサポートされています。 詳細については、[機能パリティと相互運用性](https://kb.vmware.com/s/article/80850)に関するページを参照してください。

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>ハイブリッド クラウドでの Horizon のデプロイ

Horizon Cloud Pod アーキテクチャ (CPA) を使用してオンプレミスのデータ センターと Azure データ センターを相互接続する場合は、ハイブリッド クラウド環境に Horizon をデプロイできます。 通常、CPA は、デプロイをスケールアップし、ハイブリッド クラウドを構築し、事業継続とディザスター リカバリーのための冗長性を提供するために使用されます。 VMware Horizon のビジネス継続性に関するガイダンスについて詳しくは、「[既存の Horizon 7 環境の拡張](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874)」をご覧ください。

>[!IMPORTANT]
>CPA は拡張されたデプロイではなく、各 Horizon ポッドは独立しています。個々のポッドに属する接続サーバーは、すべて 1 つの場所に配置し、ネットワークの観点から同じブロードキャスト ドメインで実行する必要があります。

オンプレミスまたはプライベート データ センターと同様に、Azure VMware Solution のプライベート クラウドに Horizon をデプロイできます。 以下のセクションでは、オンプレミスと Azure VMware Solution への Horizon のデプロイの主な違いについて説明します。

Azure プライベート クラウドは、概念的には、Horizon ドキュメントで使用される用語である VMware SDDC と同じです。 このドキュメントでは、これ以降、Azure プライベート クラウドと VMware SDDC を交換可能な用語として使用します。

Azure VMware Solution 上の Horizon でサブスクリプション ライセンスを管理するには、Horizon クラウド コネクタが必要です。 クラウド コネクタは、Horizon 接続サーバーと共に Azure Virtual Network にデプロイできます。

>[!IMPORTANT]
>Azure VMware Solution 上の Horizon に対する Horizon コントロール プレーンのサポートはまだご利用いただけません。 必ず VHD バージョンの Horizon クラウド コネクタをダウンロードしてください。

## <a name="vcenter-cloud-admin-role"></a>vCenter クラウド管理者ロール

Azure VMware Solution は SDDC サービスであり、Azure VMware Solution 上の SDDC のライフサイクルは Azure によって管理されるため、Azure VMware Solution 上の vCenter 権限モデルは設計によって制限されます。

お客様は、vCenter 権限の制限付きセットを持つクラウド管理者ロールを使用する必要があります。 Horizon 製品は、Azure VMware Solution でクラウド管理者ロールを使用するように変更されています。具体的には次のとおりです。

* インスタント クローンのプロビジョニングは、Azure VMware Solution で実行するように変更されました。

* 特定の vSAN ポリシー (VMware_Horizon) が、Horizon で動作するように Azure VMware Solution で作成されています。これは、Horizon 用にデプロイされた SDDC で利用可能であり、使用されている必要があります。

* vSphere コンテンツ ベースの読み取りキャッシュ (CBRC) は、ビュー ストレージ アクセラレータとも呼ばれ、Azure VMware Solution で実行する場合は無効になります。

>[!IMPORTANT]
>CBRC を再び有効にすることはできません。

>[!NOTE]
>Azure VMware Solution では、Horizon 2006 (Horizon 8) 以上が Horizon 8 ブランチにデプロイされ、Horizon 接続サーバーのインストーラーで **[Azure]** オプションが選択されている限り、特定の Horizon 設定が自動的に構成されます。

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Azure VMware Solution 上の Horizon のデプロイ アーキテクチャ

一般的な Horizon アーキテクチャの設計では、ポッドとブロック戦略が使用されます。 ブロックは単一の vCenter ですが、複数のブロックを結合することでポッドが作成されます。 Horizon ポッドは、Horizon のスケーラビリティの制限によって決定される組織の単位です。 各 Horizon ポッドには個別の管理ポータルがあるため、標準的な設計手法はポッドの数を最小限に抑えることです。

すべてのクラウドには独自のネットワーク接続方式があります。 Azure VMware Solution のネットワーク接続は、VMware の SDDC ネットワーク/NSX エッジと組み合わせることで、オンプレミスとは異なる Horizon をデプロイするための独自の要件を提示します。

各 Azure プライベート クラウド/SDDC は、次のことを前提として、4,000 のデスクトップ セッションまたはアプリ セッションを処理できます。

* ワークロードのトラフィックは、LoginVSI タスク ワーカー プロファイルのトラフィックと一致する。

* プロトコル トラフィックだけが考慮され、ユーザー データは考慮されない。

* NSX Edge が大きなサイズに構成されている。

>[!NOTE]
>ワークロードのプロファイルとニーズは異なる場合があるため、結果はユース ケースによって異なる場合があります。 ユーザー データ ボリュームでは、ワークロードのコンテキストにおけるスケールの制限が低くなることがあります。 デプロイのサイズを変更し、それに応じて計画します。 詳細については、「[Horizon のデプロイのために Azure VMware Solution ホストのサイズを変更する](#size-azure-vmware-solution-hosts-for-horizon-deployments)」セクションのサイズ変更のガイドラインを参照してください。

Azure プライベート クラウド/SDDC の上限を考慮して、Azure Virtual Network 内部で Horizon 接続サーバーと VMware Unified Access Gateway (UAG) が実行されるデプロイ アーキテクチャをお勧めします。 これにより、各 Azure プライベート クラウド/SDDC が実質的にブロックになります。 その結果として、Azure VMware Solution で実行される Horizon のスケーラビリティが最大になります。

Azure Virtual Network から Azure プライベート クラウド/SDDC への接続は、ExpressRoute FastPath で構成する必要があります。 次の図は、基本的な Horizon ポッドのデプロイを示しています。

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Azure VMware Solution 上の Horizon と Horizon Cloud on Azure の違い" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Azure VMware Solution 上の Horizon をスケーリングするためのネットワーク接続

このセクションでは、いくつかの一般的なデプロイ例を使用して、Azure VMware Solution 上の Horizon をスケーリングするためのネットワーク アーキテクチャの概要について説明します。 ここでの焦点は、特に重要なネットワーク要素です。

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Azure VMware Solution 上の単一の Horizon ポッド

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Azure VMware Solution 上の Horizon と Horizon Cloud on Azure の違い" border="false":::

単一の Horizon ポッドは最も簡単なデプロイ シナリオです。 この例では、米国東部リージョンに 1 つだけ Horizon ポッドをデプロイすることにしました。 各プライベート クラウド/SDDC は、約 4,000 のデスクトップ セッションのトラフィックを処理すると推定されるため、最大限の Horizon ポッドをデプロイするには、最大 3 つのプライベート クラウド/SDDC のデプロイを計画できます。

そのため、この例では、Azure Virtual Network にデプロイされた Horizon インフラストラクチャの仮想マシン (VM) と組み合わせて、ワークロードとデータのニーズに基づいて、Horizon ポッド 1 つあたり 12,000 のセッションに接続できます。 Azure Virtual Network に対する各プライベート クラウドと SDDC との間の接続は ExpressRoute Fast Path であり、プライベート クラウド間の東西トラフィックは必要ありません。

この基本的なデプロイの例には、次のような重要な前提があります。

* Cloud Pod アーキテクチャ (CPA) を使用してこの新しいポッドに接続するオンプレミスの Horizon ポッドはありません。

* エンド ユーザーは、インターネット経由で仮想デスクトップに接続します (オンプレミスのデータ センター経由で接続することはできません)。

この基本的な例では、Azure Virtual Network の AD ドメイン コントローラーを、VPN または ExpressRoute 回線経由でオンプレミスの Active Directory に接続できます。

ここで説明する基本的な例のバリエーションは、オンプレミスのリソースへの接続をサポートすることになります。 これは、デスクトップにアクセスしたり、仮想デスクトップ アプリケーション トラフィックを生成したり、CPA を使用してオンプレミスの Horizon ポッドに接続したりするユーザーである可能性があります。

次の図は、その方法を示しています。  企業ネットワークを Azure Virtual Network に接続するには、ExpressRoute が必要です。  また、Global Reach を使用して、企業ネットワークを各プライベート クラウド/SDDC に接続する必要があります。これにより、SDDC から ExpressRoute およびオンプレミスのリソースへの接続が可能になります。

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Azure VMware Solution 上の Horizon と Horizon Cloud on Azure の違い" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>複数のリージョンにわたる Azure VMware Solution 上の複数の Horizon ポッド

別の Horizon ポッドの例として、複数のポッド間での Horizon のスケーリングを示す例を見てみましょう。  この例では、2 つの異なるリージョンに 2 つの Horizon ポッドをデプロイし、CPA を使用してそれらをフェデレーションします。  ネットワーク構成は前の例と似ていますが、リージョン間リンクがいくつかあります。 

各リージョンの Azure Virtual Network を別のリージョンのプライベート クラウド/SDDC に接続する必要があります。これにより、CPA フェデレーションの一部である Horizon 接続サーバーで、管理下にあるすべてのデスクトップに接続できるようになります。  この構成にプライベート クラウド/SDDC を追加すると、全体で 24,000 のセッションに拡張できるようになります。 

この例では複数のリージョンを示していますが、同じリージョンに 2 つの Horizon ポッドをデプロイする場合も同じ原則が適用されます。 2 つ目の Horizon ポッドが "*別の Azure Virtual Network*" にデプロイされていることを確認する必要があることに注意してください。  最後に、前の単一ポッドの例と同じように、お客様の ExpressRoute と Global Reach を使用して、企業ネットワークとオンプレミスのポッドをこのマルチポッド/リージョンの例に接続できます。

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Azure VMware Solution 上の Horizon と Horizon Cloud on Azure の違い" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Horizon のデプロイのために Azure VMware Solution ホストのサイズを変更する 

Azure VMware Solution のホスト インスタンスは標準化されているため、Azure VMware Solution で実行されているホスト上の Horizon のサイズ設定方法は、オンプレミスの Horizon よりも簡単です。 ホストのサイズを正確に設定すると、VDI の要件をサポートするために必要なホストの数が決まり、デスクトップごとのコストを判断するのに役立ちます。

### <a name="azure-vmware-solution-host-instance"></a>Azure VMware Solution のホスト インスタンス

* PowerEdge R640 サーバー - DSS 制限付き

* 36 コア \@2.3 GHz

* 576 GB RAM

* HBA330 12 Gbps SAS HBA コントローラー (非 RAID)

* 1.92 TB SSD SATA ミックス使用 6 Gbps 512 2.5 インチ ホット プラグ AG ドライブ、3 DWPD、10512 TBW

* Intel 1.6 TB、NVMe、ミックス使用 Express Flash、2.5 SFF ドライブ、U.2、P4600 キャリア付き

* 2 vSAN ディスク グループ:1.6 x 4 (1.92 TB)

### <a name="horizon-sizing-inputs"></a>Horizon のサイズ設定の入力

計画したワークロードについて、次のことを確認してください。

* 同時実行されるデスクトップの数

* デスクトップごとに必要な仮想 CPU

* デスクトップごとに必要な仮想 RAM

* デスクトップごとに必要なストレージ

一般に、VDI のデプロイは CPU または RAM の制約を受けます。これらの要因により、ホストのサイズが決まります。 パフォーマンス テストで検証された LoginVSI ナレッジ ワーカー タイプのワークロードについて、次の例を見てみましょう。

* 同時実行される 2,000 のデスクトップ デプロイ

* デスクトップごとに 2 つの仮想 CPU

* デスクトップごとに 4 GB の仮想 RAM

* デスクトップごとに 50 GB のストレージ

この例では、ホストの合計数が 18 になり、ホストごとの VM の密度は 111 になります。

>[!IMPORTANT]
>お客様のワークロードは、LoginVSI ナレッジ ワーカーのこの例とは異なります。 デプロイの計画の一環として、特定のサイズとパフォーマンスのニーズに応じて VMware EUC SE を操作します。 ホストのサイズを確認し、それに応じて調整する前に、実際の計画されたワークロードを使用して、独自のパフォーマンス テストを実行してください。

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Azure VMware Solution 上の Horizon のライセンス 

Azure VMware Solution 上の Horizon の実行コストには、4 つのコンポーネントがあります。 

### <a name="azure-vmware-solution-capacity-cost"></a>Azure VMware Solution の容量コスト

価格の詳細については、[Azure VMware Solution の価格](https://azure.microsoft.com/pricing/details/azure-vmware/)に関するページを参照してください。

### <a name="horizon-licensing-cost"></a>Horizon ライセンス コスト

Azure VMware Solution では、次の 2 つのライセンスを使用できます。これは、同時ユーザー (CCU) または名前付きユーザー (NU) のどちらでもかまいません。

* Horizon サブスクリプション ライセンス

* Horizon ユニバーサル サブスクリプション ライセンス

近い将来、Azure VMware Solution にのみ Horizon をデプロイする場合は、Horizon サブスクリプション ライセンスを使用してください。その方が低コストです。

Azure VMware Solution とオンプレミスの両方に Horizon をデプロイする場合は、ディザスター リカバリーのユース ケースと同様に、Horizon ユニバーサル サブスクリプション ライセンスを選択してください。 ユニバーサル ライセンスにはオンプレミス デプロイ用の vSphere ライセンスが含まれているため、コストが高くなります。

VMware EUC 営業チームと協力して、ニーズに応じた Horizon ライセンス コストを判断します。

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Azure Virtual Network 上の Horizon インフラストラクチャ VM のコスト

Horizon インフラストラクチャ VM は、標準のデプロイ アーキテクチャに基づいて、接続サーバー、UAG、App Volume Manager で構成され、お客様の Azure Virtual Network にデプロイされます。 Azure で高可用性 (HA)、Microsoft SQL、または Microsoft Active Directory (AD) サービスをサポートするには、追加の Azure ネイティブ インスタンスが必要です。 次に示すのは、2,000 デスクトップ デプロイの例に基づく Azure インスタンスの一覧です。 

>[!NOTE]
>エラーを処理できるようにするには、接続数に必要な数よりも 1 つ多いサーバーをデプロイしてください (n+1)。 接続サーバー、UAG、および App Volumes Manager の最小推奨インスタンス数は 2 であり、必要な数は環境でサポートされるユーザーの量に基づいて増加します。  1 つの接続サーバーでサポートされるセッションの最大数は 4,000 ですが、ベスト プラクティスとして 2,000 をお勧めします。 ポッドあたり最大 7 つの接続サーバーがサポートされており、推奨されるポッドあたりの合計アクティブ セッション数は 12,000 です。 最新の数値については、[VMware Horizon 7 のサイズ制限と推奨事項に関する VMware サポート技術情報の記事](https://kb.vmware.com/s/article/2150348)を参照してください。

| Horizon インフラストラクチャ コンポーネント | Azure インスタンス | 必要なインスタンスの数 (2,000 デスクトップの場合)    | 解説  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| 接続サーバー                | D4sv3          | 2       | *上記の「注」を参照*                         |    
| UAG                              | F2sv2          | 2       | *上記の「注」を参照*                         |
| App Volume Manager              | D4sv3          | 2       | *上記の「注」を参照*                         |
| クラウド コネクタ                  | D4sv3          | 1       |                                          |
| AD コントローラー                    | D4sv3          | 2       | *Azure で MSFT AD サービスを使用するオプション* |
| MS-SQL Database                  | D4sv3          | 2       | *Azure で SQL サービスを使用するオプション*     |
| Windows ファイル共有               | D4sv3          |         | *省略可能*                               |

インフラストラクチャ VM のコストは、上記の例の 2,000 デスクトップ デプロイでは、ユーザー単位で 1 か月ごとに \$0.36 になります。 この例では、2020 年 6 月現在の米国東部での Azure インスタンスの価格が使用されていることに注意してください。 価格は、リージョン、選択したオプション、およびタイミングによって異なる場合があります。
