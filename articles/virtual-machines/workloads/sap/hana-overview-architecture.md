---
title: "SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ | Microsoft Docs"
description: "SAP HANA on Azure (L インスタンス) をデプロイする方法のアーキテクチャの概要。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 18501fc0fdb3174b3ec74e3dde3422b27898bf10
ms.contentlocale: ja-jp
ms.lasthandoff: 04/28/2017


---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ 
これは 5 部構成のアーキテクチャ/テクニカル デプロイ ガイドであり、Azure の新しい SAP HANA on Azure (L インスタンス) に SAP をデプロイする場合に役立つ情報を提供します。 包括的なガイドではなく、SAP ソリューションのセットアップに関連する詳細については取り上げません。 その代わりに、初期のデプロイおよび実行中の運用を支援する貴重な情報を提供します。 SAP HANA のインストールに関連する SAP ドキュメント (またはこのトピックについて取り上げた多くの SAP サポート ノート) の代わりとしてこのガイドを使用しないでください。 また、SAP HANA on Azure (L インスタンス) のインストールの詳細についても説明します。


このガイドの 5 つのパートで取り上げるトピックは次のとおりです。

- [SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure (L インスタンス) のインストールと構成の方法](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure (L インスタンス) のトラブルシューティングと監視の方法](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>定義

このアーキテクチャ/テクニカル デプロイ ガイドでは、いくつかの一般的な定義が広く使用されています。 次の用語とその意味に注意してください。

- **IaaS:** サービスとしてのインフラストラクチャ
- **PaaS:** サービスとしてのプラットフォーム
- **SaaS:** サービスとしてのソフトウェア
- **SAP コンポーネント:** 個別の SAP アプリケーション (ECC、BW、Solution Manager EP など)。 SAP コンポーネントは、従来の ABAP または Java テクノロジか、ビジネス オブジェクトなどの非 NetWeaver ベース アプリケーションに基づいて作成できます。
- **SAP 環境:** 開発、QAS、トレーニング、DR または実稼働などのビジネス機能を実行するために論理的にグループ化された、1 つまたは複数の SAP コンポーネント。
- **SAP ランドスケープ:** これは、IT 環境内にある SAP 資産全体を指します。 SAP ランドスケープには、運用環境と非運用環境のすべてが含まれます。
- **SAP システム:** DBMS 層とアプリケーション層を組み合わせたシステム (SAP ERP 開発システム、SAP BW テスト システム、SAP CRM 運用システムなど)。Azure デプロイメントでは、オンプレミスと Azure 間でこれら 2 つの層を分割することはできません。 つまり、SAP システムはオンプレミスか Azure のいずれかにデプロイされます。 ただし、SAP ランドス ケープの異なるシステムを Azure かオンプレミスのいずれかにデプロイすることはできます。 たとえば、SAP CRM の開発システムとテスト システムを Azure にデプロイし、SAP CRM 運用システムをオンプレミスにデプロイすることは可能です。 SAP HANA on Azure (L インスタンス) の場合は、Azure VM での SAP システムの SAP アプリケーション層の実行および L インスタンス スタンプのユニットでの HANA インスタンスの実行がサポートされています。
- **L インスタンス スタンプ:** SAP HANA TDI 認定のハードウェア インフラストラクチャ スタック。Azure 内で SAP HANA インスタンスを実行するための専用のスタックです。
- **SAP HANA on Azure (L インスタンス):** 異なる Azure リージョンの L インスタンス スタンプにデプロイされる SAP HANA TDI 認定ハードウェアで HANA インスタンスを実行する、Azure におけるサービスの正式名称。 関連用語の **HANA L インスタンス**は、SAP HANA on Azure (L インスタンス) の省略形であり、このテクニカル デプロイ ガイドで広く使用されています。
- **クロスプレミス:** オンプレミスのデータ センターと Azure との間に、サイト間、マルチサイトまたは ExpressRoute 接続を使用した Azure サブスクリプションに、VM をデプロイするシナリオのことを指します。 この種のデプロイメントは、共通の Azure ドキュメントでもクロスプレミス シナリオとして説明されています。 この接続の目的は、オンプレミスのドメイン、オンプレミスの Active Directory/OpenLDAP、およびオンプレミスの DNS を、Azure 内に拡張することです。 オンプレミスのランドスケープが、サブスクリプションの Azure 資産に拡張されます。 この拡張により。VM はオンプレミス ドメインの一部になることができます。 オンプレミス ドメインのドメイン ユーザーは、サーバーにアクセスし、それらの VM (DBMS サービスなど) 上でサービスを実行できます。 オンプレミスにデプロイした VM と Azure にデプロイした VM の間では、通信と名前解決が可能です。 これが、大部分の SAP 資産をデプロイする場合の一般的なシナリオです。 詳細については、「[VPN ゲートウェイの計画と設計](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」および「[Azure Portal を使用してサイト間接続を持つ VNet を作成する](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

Microsoft Azure パブリック クラウドでの SAP ワークロードのデプロイに関するトピックでは、その他のさまざまなリソースが紹介されています。 Azure における SAP HANA のデプロイを計画する場合は、Azure IaaS のプリンシパルおよび Azure IaaS での SAP ワークロードのデプロイに詳しい担当者を起用することを強くお勧めします。 次のリソースで詳細を示します。これらのリソースは作業を続行する前に参照してください。


- [Microsoft Azure 仮想マシンにおける SAP ソリューションの使用](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>認定

SAP では、Azure IaaS などの特定のインフラストラクチャ上の SAP HANA をサポートするための要件として、NetWeaver 認定資格のほか、SAP HANA の特別な認定資格を要求しています。

NetWeaver (および一部の SAP HANA 認定資格) に関する主要な SAP ノートは、「[SAP Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533)」(SAP ノート #1928533 – Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類) です。

この「[SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E)」(SAP ノート #2316233 - SAP HANA on Microsoft Azure (L インスタンス)) も重要です。 この SAP ノートでは、このガイドで説明するソリューションについて取り上げています。 また、GS5 VM の種類の Azure での SAP HANA の実行がサポートされています。 [この場合の情報は、SAP の Web サイトで公開されています](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)。

SAP ノート #2316233 で取り上げられている SAP HANA on Azure (L インスタンス) ソリューションを使用すると、Microsoft と SAP のお客様は大規模な SAP Business Suite、SAP Business Warehouse (BW)、S/4 HANA、BW/4HANA、またはその他の SAP HANA ワークロードを Azure にデプロイできます。 これは、SAP-HANA 認定の専用のハードウェア スタンプ ([SAP HANA Tailored Datacenter Integration – TDI](https://scn.sap.com/docs/DOC-63140)) に基づいています。 SAP TDI で構成されたソリューションとして実行すると、SAP HANA ベースのすべてのアプリケーション (SAP Business Suite on SAP HANA、SAP Business Warehouse (BW) on SAP HANA、S4/HANA および BW4/HANA を含む) を確実に機能させることができます。

Azure Virtual Machines で SAP HANA を実行する場合と比較すると、このソリューションにはメモリ ボリュームが大幅に増加するという利点があります。 このソリューションを有効にする場合は、いくつかの重要な点を理解しておく必要があります。

- SAP アプリケーション層および SAP 以外のアプリケーションは、通常の Azure ハードウェア スタンプでホストされる Azure Virtual Machines (VM) で実行されます。
- お客様のオンプレミスのインフラストラクチャ、データ センター、およびアプリケーションのデプロイは、Azure ExpressRoute (推奨) または仮想プライベート ネットワーク (VPN) 経由で Microsoft Azure クラウド プラットフォームに接続されます。 Active Directory (AD) と DNS も Azure に拡張されます。
- HANA ワークロード用の SAP HANA データベース インスタンスは SAP HANA on Azure (L インスタンス) で実行されます。 L インスタンス スタンプは Azure ネットワークに接続されるため、Azure VM で実行されるソフトウェアは HANA L インスタンスで実行される HANA インスタンスとやり取りできます。
- SAP HANA on Azure (L インスタンス) のハードウェアは、SUSE Linux Enterprise Server または Red Hat Enterprise Linux がプレインストールされたサービスとしてのインフラストラクチャ (IaaS) で提供される専用のハードウェアです。 Azure Virtual Machines と同様に、オペレーティング システムに対する更新とメンテナンスはユーザーが実行します。
- HANA のインストールまたは HANA L インスタンスのユニットでの SAP HANA の実行に必要な追加コンポーネントのインストールはユーザーが実行し、SAP HANA on Azure の実行中のすべての運用および管理は現状のままです。
- ここで説明するソリューション以外にも、SAP HANA on Azure (L インスタンス) に接続する Azure サブスクリプションに他のコンポーネントをインストールできます。  たとえば、SAP HANA データベースとの (直接の) 通信を有効にするコンポーネント (ジャンプ サーバー、RDP サーバー、SAP HANA Studio、SAP BI シナリオ向けの SAP Data Services、ネットワーク監視ソリューション) などです。
- Azure と同様に、HANA L インスタンスは高可用性と障害復旧のサポートを有効にする機能を提供します。

## <a name="architecture"></a>アーキテクチャ

SAP HANA on Azure (L インスタンス) ソリューションでは、大まかには、Azure VM に SAP アプリケーション層が配置され、Azure IaaS に接続される同じ Azure リージョン内の L インスタンス スタンプにある SAP TDI で構成されたハードウェアにデータベース層が配置されます。

> [!NOTE]
> SAP アプリケーション層は、SAP DBMS 層を持つ同じ Azure リージョンにデプロイする必要があります。 これは、Azure 上の SAP ワークロードに関して公開されている情報で明確に文書化されています。

SAP HANA on Azure (L インスタンス) の全体的なアーキテクチャでは、SAP TDI 認定のハードウェア構成 (SAP HANA データベース用の仮想化されていない、ベア メタルの高パフォーマンス サーバー)、およびニーズに合わせて SAP アプリケーション層用のリソースをスケールするための Azure の機能と柔軟性が提供されます。

![SAP HANA on Azure (L インスタンス) のアーキテクチャの概要](./media/hana-overview-architecture/image1-architecture.png)

上記のアーキテクチャは、次の 3 つのセクションに分かれています。

- **右:** エンド ユーザーがアクセスする LOB アプリケーション (SAP など) を使用してデータ センター内のさまざまなアプリケーションを実行するオンプレミスのインフラストラクチャ。 可能であれば、Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) を使用してこのオンプレミスのインフラストラクチャを Azure に接続するのが理想的です。

- **中央:** Azure IaaS を示します。ここでは、Azure VM を使用して、SAP または SAP HANA を DBMS システムとして利用するその他のアプリケーションをホストします。 Azure VM が提供するメモリを使用して機能する小規模な HANA インスタンスは、アプリケーション層と共に Azure VM にデプロイされます。 Virtual Machines の詳細については、[こちら](https://azure.microsoft.com/services/virtual-machines/)を参照してください。
<br />SAP システムとその他のアプリケーションをグループ化して Azure Virtual Network (VNet) を作成するために、Azure ネットワークが使用されます。 これらの VNet は、オンプレミスのシステムおよび SAP HANA on Azure (L インスタンス) に接続します。
<br />Microsoft Azure での実行用にサポートされている SAP NetWeaver アプリケーションおよびデータベースについては、「[SAP Support Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533)」(SAP サポート ノート #1928533 – Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類) を参照してください。 Azure での SAP ソリューションのデプロイに関するドキュメントについては、次を参照してください。

  -  [Windows Virtual Machines (VM) における SAP の使用](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Microsoft Azure 仮想マシンにおける SAP ソリューションの使用](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **左:** Azure L インスタンス スタンプの SAP HANA TDI 認定ハードウェアを示します。 HANA L インスタンス ユニットは、オンプレミスから Azure への接続と同じテクノロジを使用してサブスクリプションの Azure VNet に接続されます。

Azure L インスタンス スタンプ自体は次のコンポーネントを結合します。

- **コンピューティング:** 必要とされるコンピューティング能力を提供し、SAP HANA 認定を獲得している Intel Xeon E7-8890v3 プロセッサをベースとしたサーバー。
- **ネットワーク:** コンピューティング、ストレージ、および LAN コンポーネントを相互接続する統合された高速ネットワーク ファブリック。
- **ストレージ:** 統合されたネットワーク ファブリック経由でアクセスするストレージ インフラストラクチャ。 デプロイする SAP HANA on Azure (L インスタンス) 構成に応じて、特定のストレージ容量が提供されます (追加の月額をお支払いいただくと、ストレージ容量を増やすことができます)。

L インスタンス スタンプのマルチテナント インフラストラクチャ内では、分離されたテナントとしてお客様の環境がデプロイされます。 これらのテナントには Azure サブスクリプションとの 1 対 1 の関係があります。 つまり、2 つの異なる Azure サブスクリプション以外の Azure L インスタンス スタンプで実行される SAP HANA on Azure (L インスタンス) の同じインスタンスにアクセスすることはできません。

Azure VM と同様に、SAP HANA on Azure (L インスタンス) は複数の Azure リージョンで提供されます。 障害復旧機能を提供するためにオプトインを選択できます。 さまざまな Azure リージョンの異なる L インスタンス スタンプが相互に接続されます。

Azure Virtual Machines で異なる VM の種類から選択できるのと同様に、ワークロードの種類が異なる SAP HANA に対応した HANA L インスタンスのさまざまな SKU から選択できます。 SAP では、プロセッサのソケットの比率に対してメモリを適用し、Intel プロセッサの世代に基づいてワークロードを変更します。4 種類の SKU が提供されています。

2016年 12 月の時点で、SAP HANA on Azure (L インスタンス) は、米国東部と米国西部の Azure リージョンで、次の 6 つの構成を使用できます。

| SAP ソリューション | CPU | RAM | Storage |
| --- | --- | --- | --- |
| OLAP 用に最適化: SAP BW、BW/4HANA、<br /> または SAP HANA (一般的な OLAP ワークロード向け) | SAP HANA on Azure S72<br /> – 2 x Intel® Xeon® プロセッサ E7-8890 v3 |  768 GB |  3 TB |
| --- | SAP HANA on Azure S144<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v3 |  1.5 TB |  6 TB |
| --- | SAP HANA on Azure S192<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v4 |  2.0 TB |  8 TB |
| OLTP 用に最適化: SAP Business Suite<br /> on SAP HANA または S/4HANA (OLTP)、<br /> 一般的な OLTP | SAP HANA on Azure S72m<br /> – 2 x Intel® Xeon® プロセッサ E7-8890 v3 |  1.5 TB |  6 TB |
|---| SAP HANA on Azure S144m<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v3 |  3.0 TB |  12 TB |
|---| SAP HANA on Azure S192m<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v4 |  4.0 TB |  16 TB |

上記のさまざまな構成の説明については、「[SAP Support Note #2316233 – SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E)」(SAP サポート ノート #2316233 – SAP HANA on Microsoft Azure (L インスタンス)) を参照してください。

選択する構成は、ワークロード、CPU リソース、および必要なメモリによって異なります。 OLTP ワークロードでは、OLAP ワークロード用に最適化された SKU を活用できます。 同様に、OLAP HANA ワークロード用の OLTP SKU を活用できます。 ただし、アプライアンスの種類が "_HANA での BW のスケールアップ_" の場合は、[SAP の Web サイト](http://global12.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/appliances.html)に記載されているように、HANA で活用するメモリを Intel E7 プロセッサ世代で認定されたメモリに制限しなければならない場合があります。

L インスタンス スタンプの完全なインフラストラクチャは 1 件のお客様が使用するためだけに割り当てられるものではないことに注意してください。 これは、Azure にデプロイされたネットワーク ファブリック経由で接続されるコンピューティング リソースとストレージ リソースのラックを使用する場合と同様に、SAP HANA on Azure (L インスタンス) に適用されます。 Azure などの HANA L インスタンスのインフラストラクチャは、ネットワークの分離によって別のテナントから分離されたお客様の &quot;テナント&quot; をデプロイします。 そのため、HANA L インスタンスのこれらのデプロイは相互に干渉せず、相互に表示されることもありません。 L インスタンス スタンプにデプロイされたテナントは、1 つの Azure サブスクリプションに割り当てられます。 HANA L インスタンスも必要とする 2 つ目の Azure サブスクリプションがある場合は、これらのインスタンス間の直接の通信を防ぐネットワーク内のすべての分離を使用して L インスタンス スタンプの別のテナントにデプロイされます。

ただし、HANA L インスタンスで実行する SAP HANA と Azure にデプロイされた Azure VM で実行する SAP HANA には大きな違いがあります。

- SAP HANA on Azure (L インスタンス) の仮想化層はありません。 基になるベア メタル ハードウェアのパフォーマンスを確保します。
- Azure とは異なり、SAP HANA on Azure (L インスタンス) サーバーは特定のお客様に専用のサーバーです。 サーバーを再起動またはシャットダウンしても、オペレーティング システムと SAP HANA が別のサーバーにデプロイされることはありません  (唯一の例外は、サーバーで問題が発生している可能性があり、別のブレードで再デプロイを実行する必要がある場合です)。
- Azure とは異なり、価格/パフォーマンス比が最も高くなるようにホスト プロセッサの種類を選択する場合は、Intel E7v3 プロセッサ ラインの最もパフォーマンスが高いプロセッサが SAP HANA on Azure (L インスタンス) 用に選択されます。

複数のお客様が SAP HANA on Azure (L インスタンス) ハードウェアでのデプロイを実行し、独自の VLAN にデプロイすることでハードウェアが相互に保護されます。 HANA L インスタンスを Azure Virtual Network (VNet) に接続するために、しかるべきネットワーク コンポーネントにより、テナントの Azure サブスクリプションの Azure VNet に対し、隔離された方法でその HANA L インスタンス ユニットが接続されます。 

## <a name="operations-model-and-responsibilities"></a>運用モデルと責任

SAP HANA on Azure (L インスタンス) が提供するサービスは Azure IaaS サービスと連携しています。 HANA L インスタンスには、SAP HANA 用に最適化されたオペレーティング システムがインストールされています。 Azure IaaS VM と同様に、OS の強化、必要な追加ソフトウェアのインストール、HANA のインストール、OS と HANA の運用、OS と HANA の更新などの大部分のタスクはユーザーが担当します。 Microsoft が OS の更新や HANA の更新を強制することはありません。

![SAP HANA on Azure (L インスタンス) の責任](./media/hana-overview-architecture/image2-responsibilities.png)

上の図に示すように、SAP HANA on Azure (L インスタンス) はマルチ テナントのサービスとしてのインフラストラクチャです。 そのため、ほとんどの部分で OS とインフラストラクチャの境界に基づいて責任が分担されています。 Microsoft はオペレーティング システムのラインの下側のサービスのすべての面を担当し、お客様はラインの上側 (オペレーティング システムを含む) を担当します。 そのため、コンプライアンス、セキュリティ、アプリケーション管理、基盤と OS の管理に採用する現在の大部分のオンプレミスの手法を引き続き使用できます。 システムはネットワーク内に存在するかのように見えます。

ただし、このサービスは SAP HANA 用に最適化されているため、最良の結果を得るためにお客様と Microsoft が協力して、基になるインフラストラクチャの機能を使用しなければならない領域が存在します。

それぞれの層とお客様の責任の詳細を次に示します。

**ネットワーク:** SAP HANA を実行する L インスタンス スタンプ、ストレージへのアクセス、インスタンス間の接続 (スケールアウトとその他の機能用)、ランドスケープへの接続、SAP アプリケーション層が Azure Virtual Machines でホストされる Azure への接続のためのすべての内部ネットワーク。 また、障害復旧時のレプリケーション用の Azure データ センター間の WAN 接続も含まれます。 すべてのネットワークはテナントによってパーティション分割され、QoS が適用されます。

**ストレージ:** SAP HANA サーバーで必要なすべてのボリュームおよびスナップショットに対応した、仮想化およびパーティション分割されたストレージ。

**サーバー:** テナントに割り当てられている SAP HANA DB を実行する専用の物理サーバー。 このサーバーはハードウェア仮想化されています。

**SDDC:** データ センターをソフトウェアによるエンティティとして管理するために使用する管理ソフトウェア。 これにより、Microsoft ではスケール、可用性、およびパフォーマンスのためのリソースをプールできます。

**O/S:** サーバーで実行するために選択する OS (SUSE Linux または Red Hat Linux)。 お客様が提供する OS イメージは、個々の Linux ベンダーが SAP HANA を実行するために Microsoft に提供するイメージになります。 特定の SAP HANA 最適化イメージでは、Linux ベンダーのサブスクリプションが必要です。 お客様の責任には、OS ベンダーへのイメージの登録が含まれます。 さらに、Microsoft による引き渡しの時点から、Linux オペレーティング システムの修正プログラムも、お客様の責任になります。 これは、SAP HANA を正常にインストールするために必要であるが、特定の Linux ベンダーが SAP HANA 最適化 OS イメージに含めていない追加パッケージに関係します (HANA のインストールに関する SAP のドキュメントと SAP Notes を参照してください)。 お客様の責任には、OS の動作不良/最適化に関連する OS の修正プログラムと特定のサーバー ハードウェアに関連するドライバー、 または OS のセキュリティや機能の修正プログラムも含まれます。 以下の項目の監視とキャパシティ プランニングも、お客様の責任になります。

- CPU リソース使用量
- メモリ使用量
- 空き領域、IOPS、および待機時間に関連するディスク ボリューム
- HANA L インスタンスと SAP アプリケーション層の間のネットワーク ボリュームのトラフィック

HANA L インスタンスの基になるインフラストラクチャは、OS ボリュームのバックアップと復元の機能を提供します。 この機能の活用もユーザーの責任において行います。

**ミドルウェア:** 主に SAP HANA インスタンスです。 管理、運用、および監視はユーザーが担当します。 ストレージのスナップショットを使用してバックアップ/復元および障害復旧を実行するための機能が提供されます。 これらの機能はインフラストラクチャから提供されます。 ただし、これらの機能を使用した高可用性と障害復旧の構築、高可用性と障害復旧の活用、およびストレージのスナップショットが正常に実行されたかどうかの監視もお客様の責任に含まれます。

**データ:** SAP HANA で管理されるデータおよびその他のデータ (ボリュームやファイル共有上のバックアップ ファイルなど)。 お客様の責任には、ディスクの空き領域の監視、ボリューム上のコンテンツの管理、およびディスク ボリュームとストレージのスナップショットのバックアップが正常に実行されたかどうかの監視が含まれます。 ただし、データを DR サイトに正常にレプリケーションする処理は Microsoft が担当します。

**アプリケーション:** SAP アプリケーション インスタンス。または SAP 以外のアプリケーションの場合は、それらのアプリケーションのアプリケーション層。 お客様の責任には、Azure VNet 内および Azure VNet から SAP HANA on Azure (L インスタンス) への CPU リソース使用量、メモリ使用量、Azure Storage 使用量、ネットワーク帯域幅の使用量のキャパシティ プランニングに関連するアプリケーションのデプロイ、管理、運用、および監視が含まれます。

**WAN:** ワークロード用にプレミスから Azure デプロイメントに確立する接続。 ワークロードがミッション クリティカルである場合は、Azure ExpressRoute を使用します。 この接続は SAP HANA on Azure (L インスタンス) ソリューションの一部ではないため、この接続のセットアップを担当するのはユーザーです。

**アーカイブ:** ストレージ アカウントで独自の手法を使用してデータのアーカイブ コピーを作成できます。 このアーカイブについては、管理、コンプライアンス、コスト、および運用が必要です。 ユーザーが担当するのは、Azure でアーカイブ コピーとバックアップを生成し、対応する方法でそれらを保存する処理です。

「[Azure 上の SAP HANA の L インスタンスの SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/)」を参照してください。

## <a name="sizing"></a>サイズ変更

通常、HANA L インスタンスのサイズ変更は HANA のサイズ変更とまったく同じです。 デプロイ済みの既存のシステムでは、他の RDBMS から HANA への移動が必要な場合があり、既存の SAP システムで実行する複数のレポートが SAP から提供されています。 これらはデータを確認し、HANA にデータベースを移動する場合のテーブルのメモリ要件を計算します。 これらのレポートを実行する方法、およびレポートの最新の修正プログラム/バージョンを入手する方法の詳細については、次の SAP ノートを参照してください。

- [SAP Note #1793345 - Sizing for SAP Suite on HANA (SAP ノート #1793345 - HANA での SAP Suite のサイズ変更)](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report (SAP ノート #1872170 - Suite on HANA および S/4 HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - FAQ: SAP BW on HANA Sizing Report (SAP ノート #2121330 - FAQ: SAP BW on HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - Sizing Report for BW on HANA (SAP ノート #1736976 - BW on HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - New Sizing Report for BW on HANA (SAP ノート #2296290 - BW on HANA の新しいサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/2296290)

緑色のフィールドの実装では、SAP Quick Sizer を使用して、HANA 上への SAP ソフトウェアの実装に必要なメモリを計算できます。

HANA に必要なメモリはデータ ボリュームの拡張に伴って増加するため、現時点でのメモリ使用量を把握しておくと、将来的に必要になるメモリを予測できます。 必要なメモリに基づいて、いずれかの HANA L インスタンス SKU に要求をマップできます。

## <a name="requirements"></a>必要条件

SAP HANA on Azure (L インスタンス) を実行する場合の要件があります。

**Microsoft Azure:**

- SAP HANA on Azure (L インスタンス) にリンク可能な Azure サブスクリプション。
- Microsoft プレミア サポートの連絡先。 Azure での SAP の実行に関連する情報については、「[SAP Support Note #2015553 – SAP on Microsoft Azure: Support Prerequisites](https://launchpad.support.sap.com/#/notes/2015553)」(SAP サポート ノート #2015553 – Microsoft Azure 上のSAP: サポートの前提条件) を参照してください。
- SAP を使用したサイズ変更のエクササイズの実行後に必要な HANA L インスタンス SKU の認識。

**ネットワーク接続:**

- Azure ExpressRoute を使用してオンプレミスを Azure に接続: オンプレミスのデータ センターを Azure に接続するには、1 Gbps 以上の接続を ISP と契約してください。

**オペレーティング システム:**

- SAP アプリケーション用の SUSE Linux Enterprise Server 12 のライセンス。

> [!NOTE] 
> Microsoft が提供するオペレーティング システムは SUSE に登録されず、SMT インスタンスにも接続されません。

- Azure VM 上の Azure にデプロイされる SUSE Linux Subscription Management Tool (SMT)。 これにより、SAP HANA on Azure (L インスタンス) を SUSE で登録し、個々に更新できるようになります (HANA L インスタンス データ センター内ではインターネットにアクセスできないため)。 
- SAP HANA 用の Red Hat Enterprise Linux 6.7 または 7.2 のライセンス。

> [!NOTE]
> Microsoft が提供するオペレーティング システムは Red Hat に登録されません。また、Red Hat Subscription Manager インスタンスにも接続されません。

- Azure VM 上の Azure にデプロイされる Red Hat Subscription Manager。 これにより、SAP HANA on Azure (L インスタンス) を Red Hat で登録し、個々に更新できるようになります (Azure L インスタンス スタンプにデプロイされたテナント内から直接インターネットにアクセスできないため)。
- SAP が定める要件上、Linux プロバイダーとのサポート契約も必要です。 HANA L インスタンスのソリューションであるということ、または Linux を Azure 内で実行するという事実によって、この要件がなくなるわけではありません。 いくつかの Linux Azure ギャラリー イメージとは異なり、HANA L インスタンスのソリューション プランにサービス使用料は含まれません。 Linux ディストリビューターとのサポート契約に関して、お客様ご自身で SAP の要件を満たす必要があります。   
   - SUSE Linux については、「[SAP Note #1984787 - SUSE LINUX Enterprise Server 12: Installation notes (SAP ノート #1984787 - SUSE LINUX Enterprise Server 12: インストールに関する注意事項)](https://launchpad.support.sap.com/#/notes/1984787)」と「[SAP Note #1056161 - SUSE Priority Support for SAP applications (SAP ノート #1056161 - SAP アプリケーションの SUSE 優先サポート)](https://launchpad.support.sap.com/#/notes/1056161)」でサポート契約の要件を確認してください。
   - Red Hat Linux の場合、サポートとサービス (HANA L インスタンスのオペレーティング システムに対する更新) を含んだ適切なサブスクリプション レベルを満たしている必要があります。 サポートとサービスに関して、Red Hat は "RHEL for SAP Business Applications" サブスクリプションの取得を推奨しています。 詳しくは、「[SAP Note #2002167 - Red Hat Enterprise Linux 7.x: Installation and Upgrade (SAP ノート #2002167 - Red Hat Enterprise Linux 7.x: インストールとアップグレード)](https://launchpad.support.sap.com/#/notes/2002167)」と「[SAP ノート #1496410 - Red Hat Enterprise Linux 6.x: インストールとアップグレード](https://launchpad.support.sap.com/#/notes/1496410)」をご覧ください。

**データベース:**

- SAP HANA (Platform Edition または Enterprise Edition) 用のライセンスとソフトウェア インストール コンポーネント。

**アプリケーション:**

- SAP HANA に接続する SAP アプリケーション用のライセンスとソフトウェア インストール コンポーネント、および関連する SAP サポート契約。
- SAP HANA on Azure (L インスタンス) 環境に関連して使用される SAP 以外のアプリケーション用のライセンスとソフトウェア インストール コンポーネント、および関連する SAP サポート契約。

**スキル:**

- Azure IaaS とそのコンポーネントの使用経験と知識。
- Azure への SAP ワークロードのデプロイの経験と知識。
- "SAP HANA Installation" の資格を持つ担当者。
- SAP HANA に関連する高可用性と障害復旧を設計するための SAP アーキテクトのスキル。


## <a name="storage"></a>Storage

SAP HANA on Azure (L インスタンス) のストレージ レイアウトは、SAP により推奨されたベスト プラクティスを使用し、SAP HANA on Azure サービス管理によって構成されています。詳細はホワイト ペーパー『[SAP HANA Storage Requirements (SAP HANA ストレージ要件)](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)』を参照してください。

HANA L インスタンスには通常、ストレージ容量の 4 倍のメモリが搭載されます。 このユニットには、HANA ログ バックアップを保存するためのボリュームが用意されています。 詳しくは、「[SAP HANA on Azure (L インスタンス) のインストールと構成の方法](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

バックアップ/復元やディザスター リカバリーを目的としてストレージ スナップショットを利用するかどうかは、お客様が選択できます。 この点について詳しくは、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

### <a name="encryption-of-data-at-rest"></a>保存データの暗号化
HANA L インスタンス用のストレージでは、ディスクに保存されるデータを透過的に暗号化することができます。 HANA L インスタンス ユニットのデプロイ時に、このタイプの暗号化を有効にするかどうかをお客様が選択できます。 また、暗号化の対象ボリュームをデプロイ後に変更することもできます。 非暗号化ボリュームから暗号化ボリュームへの移行は透過的に行われ、ダウンタイムは発生しません。 


## <a name="networking"></a>ネットワーク

Azure ネットワークのアーキテクチャは、SAP アプリケーションのデプロイを成功させるための重要な要素です。 通常、SAP HANA on Azure (L インスタンス) デプロイメントには、サイズとデータベース、CPU リソース使用量、メモリ使用量が異なる複数の SAP ソリューションを含む大規模な SAP ランドスケープが存在します。 SAP HANA に基づくこのような SAP システムは 1 つまたは 2 つだけである可能性が高いため、SAP ランドスケープは次のコンポーネントを活用するハイブリッド構成になる場合がほとんどです。

- デプロイされた SAP システム (オンプレミス)。 サイズが原因で、これらは現在 Azure でホストできません。典型的な例としては、Microsoft SQL Server でデータベースとして実行される SAP ERP 実稼働システム (CPU が 100 個以上、メモリが 1 TB) が挙げられます。
- デプロイされた SAP HANA ベースの SAP システム (オンプレミス)。たとえば、SAP HANA データベース用に 6 TB 以上のメモリを必要とする SAP ERP システムなどです。
- デプロイされた SAP システム (Azure VM 内)。 VM 上の Azure に正常にデプロイ可能な SAP NetWeaver ベースのアプリケーション用の (リソース使用量とメモリの要求に基づく) 開発、テスト、サンドボックス、実稼働インスタンスなどです。 また、これらのシステムは SQL Server (「[SAP Support Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E)」(SAP サポート ノート #1928533 – Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類) を参照) や SAP HANA (「[SAP HANA Certified IaaS Platforms](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)」(SAP HANA 認定の IaaS プラットフォーム) を参照) などのデータベースに基づく場合もあります。
- Azure L インスタンス スタンプの SAP HANA on Azure (L インスタンス) を活用する、デプロイされた SAP アプリケーション サーバー (VM 上の Azure 内)。

4 つ以上の異なるデプロイ シナリオを持つハイブリッド SAP ランドスケープが典型的ですが、Azure には完全な SAP ランドスケープのインスタンスがあり、Microsoft Azure VM がこれまで以上に強化されているため、Azure VM にデプロイする SAP ソリューションの数は増えていくでしょう。

Azure にデプロイされた SAP システムにおける Azure ネットワークは複雑なものではありません。 このネットワークは次の原則に基づいています。

- Azure Virtual Network (VNet) は、オンプレミスのネットワークに接続する Azure ExpressRoute 回線に接続する必要があります。
- 通常、ExpressRoute 回線には 1 Gbps 以上の帯域幅が必要です。 これにより、オンプレミスのシステムと Azure VM で実行するシステム間のデータ転送 (およびオンプレミスのエンド ユーザーから Azure システムへの接続) に十分な帯域幅を確保できます。
- Azure 内のすべての SAP システムを Azure VNet で設定して、相互通信を可能にする必要があります。
- オンプレミスでホストされる Active Directory と DNS は、ExpressRoute 経由でオンプレミスから Azure に拡張されます。


> [!NOTE] 
> 1 つの Azure サブスクリプションは、特定の Azure リージョン内の L インスタンス スタンプの 1 つのテナントにのみリンクできます。逆に言うと、1 つの L インスタンス スタンプのテナントを 1 つの Azure サブスクリプションにのみリンクできます。

SAP HANA on Azure (L インスタンス) を 2 つの異なる Azure リージョンにデプロイすると、別のテナントが L インスタンス スタンプにデプロイされます。 ただし、2 つのインスタンスが同じ SAP ランドスケープに含まれている限り、それらは同じ Azure サブスクリプションで実行することができます。 

> [!IMPORTANT] 
> Azure Resource Management デプロイメントは SAP HANA on Azure (L インスタンス) でサポートされます。

### <a name="internet-connectivity-of-hana-large-instances"></a>HANA L インスタンスのインターネット接続
HANA L インスタンスには、インターネットとの間に直接的な接続がありません。 そのため機能上の制約が生じます (OS イメージを直接 OS ベンダーに登録できないなど)。 ローカル SLES SMT サーバーや RHEL サブスクリプション マネージャーに対する作業が必要となる場合があります。

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Azure VM と HANA L インスタンス間のデータの暗号化
HANA L インスタンスと Azure VM 間で転送されるデータは暗号化されません。 ただし HANA DBMS 側と JDBC/ODBC ベースのアプリケーションとの間で純粋にデータを交換する目的であれば、トラフィックを暗号化することが可能です。 [SAP から提供されているこちらのドキュメント](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)をご覧ください。  

### <a name="additional-azure-vnet-information"></a>Azure VNet の追加情報

Azure VNet を ExpressRoute に接続するには、Azure ゲートウェイを作成する必要があります (「[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照)。 Azure ゲートウェイを使用すると、Azure の外部にあるインフラストラクチャ (または Azure L インスタンス スタンプ) への ExpressRoute 接続を確立したり、Azure VNet 間を接続したりできます (「[PowerShell を使用した Resource Manager の VNet 間接続の構成](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照)。 Azure ゲートウェイは最大 4 つの異なる ExpressRoute に接続できます (ExpressRoute の接続元が異なる MS Enterprise Edges (MSEE) である場合のみ)。  詳しくは、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

> [!NOTE] 
> Azure ゲートウェイが提供するスループットは 2 つのユース ケースで異なります (「[VPN Gateway について](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照)。 ExpressRoute 接続を使用して VNet ゲートウェイで実現する最大スループットは 10 Gbps です。 Azure VNet 内の Azure VM とオンプレミスのシステム間で (単一のコピー ストリームとして) ファイルをコピーすると、複数のゲートウェイ SKU の最大限のスループットを確保できません。 VNet ゲートウェイのすべての帯域幅を活用するには、複数のストリームを使用するか、1 つのファイルのパラレル ストリームで複数のファイルをコピーする必要があります。

前述の記事を参照する場合は、異なる Azure リージョンにおける UltraPerformance ゲートウェイ SKU の使用の可否に関する情報に注意してください。

### <a name="networking-for-sap-hana-on-azure"></a>SAP HANA on Azure のネットワーク

SAP HANA on Azure (L インスタンス) に接続するには、ExpressRoute を使用して、VNet ゲートウェイ経由で Azure VNet をお客様のオンプレミスのネットワークに接続する必要があります。 また、2 つ目の ExpressRoute 回線を経由して、Azure L インスタンス スタンプにある HANA L インスタンスに Azure VNet を接続する必要があります。 VNet ゲートウェイには、少なくとも 2 つの ExpressRoute 接続があり、その両方が VNet ゲートウェイの最大帯域幅を共有します。

> [!IMPORTANT] 
> SAP アプリケーションとデータベース層の間のすべてのネットワーク トラフィックについて考えた場合、SAP HANA on Azure (L インスタンス) への接続の際には、VNet 用の HighPerformance または UltraPerformance ゲートウェイ SKU のみサポートされます。

![SAP HANA on Azure (L インスタンス) およびオンプレミスに接続された Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

### <a name="single-sap-system"></a>単一の SAP システム

上に示したオンプレミスのインフラストラクチャは ExpressRoute 経由で Azure に接続され、ExpressRoute 回線は Microsoft Enterprise Edge Router (MSEE) に接続されます (「[ExpressRoute の技術概要](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照)。 接続が確立されると、そのルートは Microsoft Azure バックボーンに接続され、すべての Azure リージョンにアクセスできるようになります。

> [!NOTE] 
> Azure で SAP ランドスケープを実行する場合は、SAP ランドスケープの Azure リージョンに最も近い MSEE に接続してください。 Azure IaaS 内の Azure VM と L インスタンス スタンプ間のネットワーク待ち時間を最小限に抑えるため、Azure L インスタンス スタンプは専用の MSEE デバイスを経由して接続されます。

SAP アプリケーション インスタンスをホストする Azure VM 用の VNet ゲートウェイはその ExpressRoute 回線に接続され、L インスタンス スタンプに接続するための専用の別の MSEE ルーターに同じ VNet が接続されます。

これは、SAP アプリケーション層が Azure でホストされ、SAP HANA データベースが SAP HANA on Azure (L インスタンス) で実行される 1 つの SAP システムのわかりやすい例です。 VNet ゲートウェイの帯域幅 (2 Gbps または 10 Gbps) がボトルネックにならないことが前提です。

### <a name="multiple-sap-systems-or-large-sap-systems"></a>複数の SAP システムまたは大規模な SAP システム

SAP HANA on Azure (L インスタンス) に接続する複数の SAP システムまたは大規模な SAP システムがデプロイされる場合は、HighPerformance VNet ゲートウェイ SKU のスループットがボトルネックになる可能性があると想定することが妥当です。 その場合は、UltraPerformance SKU を選択してください (使用可能な場合)。 ただし、HighPerformance SKU (最大 2 Gbps) しか使用できない場合や、UltraPerformance SKU (最大 10 Gbps) では不十分な可能性がある場合は、アプリケーション層を複数の Azure VNet に分割する必要があります。 また次のようなケースでは、HANA L インスタンスに接続する特別な VNet を作成することをお勧めします。

- NFS 共有のホストとなる Azure 内の VM に対して HANA L インスタンス内の HANA インスタンスから直接バックアップを実行する。
- HANA L インスタンス ユニットから、バックアップ ファイルなど大きなファイルを Azure 内の管理されたディスク領域にコピーする。

ストレージを管理する VM を別個の VNet でホストすれば、HANA L インスタンスから Azure に大きなファイルやデータが転送されることによる影響が、VNet ゲートウェイ (SAP アプリケーション レイヤーを実行する VM に対してサービスを提供する) に及ぶことはありません。 

スケーラブルなネットワーク アーキテクチャでは次のように対応します。

- 1 つの大規模な SAP アプリケーション層に対して複数の Azure VNet を活用します。
- 同じ VNet に属する個々のサブネット内の SAP システムの組み合わせに対して、デプロイされた SAP システムごとに個別の Azure VNet をデプロイします。

 SAP HANA on Azure (L インスタンス) 用のスケーラブルなネットワーク アーキテクチャを次に示します。

![複数の Azure VNet にわたる SAP アプリケーション層のデプロイ](./media/hana-overview-architecture/image4-networking-architecture.png)

上の図に示すように、SAP アプリケーション層 (コンポーネント) を複数の Azure VNet にわたってデプロイした場合、Azure VNet でホストされるアプリケーション間の通信時に発生する待機時間のオーバーヘッドを回避できませんでした。 既定では、異なる VNet にある Azure VM 間のネットワーク トラフィックは、この構成では MSEE ルーター経由で発生します。 ただし、2016 年 9 月以降、これを回避して最適化できます。 2 つの VNet 間の通信を最適化し、遅延時間を削減する方法は、同じリージョン内の Azure VNet をピアリングすることです。 これは、サブスクリプションが異なっていても行われます。 Azure VNet のピアリングを使用することで、2 つの異なる Azure VNet 内の VM 間の通信は、Azure ネットワークのバックボーンを使用して直接的に相互に通信できます。 その結果、VM が同じ VNet 内に存在する場合と同様の遅延時間が示されます。 これに対し、Azure VNet ゲートウェイ経由で接続される IP アドレス範囲のトラフィックは、VNet の個別の VNet ゲートウェイ経由でルーティングされます。 Azure VNet のピアリングの詳細については、「[VNet ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)」を参照してください。


### <a name="routing-in-azure"></a>Azure でのルーティング

SAP HANA on Azure (L インスタンス) のネットワーク ルーティングに関する重要な考慮事項が 2 つあります。

1. SAP HANA on Azure (L インスタンス) には、専用の ExpressRoute 接続の Azure VM からのみアクセスできます。オンプレミスから直接アクセスすることはできません。 そのため、直接アクセスを必要とする管理クライアントやアプリケーション (オンプレミスで実行する SAP Solution Manager など) は SAP HANA データベースに接続できません。

2. SAP HANA on Azure (L インスタンス) ユニットには、お客様が提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます (詳しくは「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください)。  この IP アドレスには、Azure VNet を HANA on Azure (L インスタンス) に接続する ExpressRoute と Azure サブスクリプションを介してアクセスできます。 サーバー IP プールのアドレス範囲から割り当てられる IP アドレスは、このソリューションの最初のデプロイにおけるケースであるため、直接ハードウェア ユニットに割り当てられ、NAT 変換されません。 

> [!NOTE] 
> "_データ ウェアハウス_" のシナリオ (直接実行されている SAP HANA データベースにアプリケーションまたはエンド ユーザーが接続する必要がある) で SAP HANA on Azure (L インスタンス) に接続する必要がある場合は、別のネットワーク コンポーネントを使用してください (データを相互にルーティングするためのリバース プロキシ)。 たとえば、仮想ファイアウォール/トラフィック ルーティング ソリューションとして Azure にデプロイする F5 BIG-IP や NGINX (および Traffic Manager) などです。

### <a name="leveraging-in-multiple-regions"></a>複数のリージョンでの活用

障害復旧を除くその他の理由で SAP HANA on Azure (L インスタンス) を複数の Azure リージョンにデプロイしなければならない場合があります。 このような場合はおそらく、リージョン内の異なる VNet にデプロイされた各 VM から HANA L インスタンスにアクセスする必要があります。 複数の HANA L インスタンス ユニットに割り当てられた IP アドレスは (ゲートウェイ経由でインスタンスに直接接続されている) Azure VNet を超えて反映されないため、前述の VNet の設計はわずかに変更されます。Azure VNet ゲートウェイは、複数の MSEE の 4 つの異なる ExpressRoute 回線を処理できます。いずれかの L インスタンス スタンプに接続される各 VNet は別の Azure リージョン内の L インスタンス スタンプに接続できます。

![複数の Azure リージョン内の Azure L インスタンス スタンプに接続される Azure VNet](./media/hana-overview-architecture/image8-multiple-regions.png)

上の図は、2 つのリージョン内の異なる Azure VNet が 2 つの Azure リージョン内の SAP HANA on Azure (L インスタンス) への接続に使用する 2 つの異なる ExpressRoute 回線にどのように接続されるかを示しています。 新しく導入される接続を直交する赤い線で示しています。 Azure VNet からのこのような接続により、いずれかの VNet で実行される VM は 2 つのリージョンにデプロイされた各 HANA L インスタンスのユニットにアクセスできます (同じサブスクリプションを使用することを前提とします)。 上の図に示すように、オンプレミスから 2 つの Azure リージョンへの 2 つの ExpressRoute 接続があることが前提となります。この設計は、障害復旧の用途に推奨されます。

> [!IMPORTANT] 
> 複数の ExpressRoute 回線を使用する場合は、トラフィックを適切にルーティングするために、AS パス プリペンドとローカル プリファレンス BGP の設定を使用してください。



