---
title: SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) をデプロイする方法のアーキテクチャの概要。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3918096a977cfd48e2128646d7c552e842ab8834
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063682"
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ

## <a name="what-is-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) とは

SAP HANA on Azure (L インスタンス) は、Azure 独自のソリューションです。 Azure では、SAP HANA をデプロイおよび実行するための仮想マシンを提供するだけでなく、お客様専用のベア メタル サーバー上でも SAP HANA を実行およびデプロイすることができます。 SAP HANA on Azure (L インスタンス) ソリューションは、お客様に割り当てられた非共有ホスト/サーバーのベア メタル ハードウェア上に構築されます。 サーバー ハードウェアは、コンピューティング/サーバー、ネットワーク、ストレージのインフラストラクチャを含むより大きなスタンプに埋め込まれます。 この全体が HANA テーラード データ センター統合 (TDI) 認定の対象となります。 SAP HANA on Azure (L インスタンス) には、さまざまなサーバー SKU やサイズが用意されています。 ユニットには、36 個の Intel CPU コアと 768 GB のメモリを搭載できます。また、最大 480 個の Intel CPU コアと最大 24 TB のメモリを搭載するユニットに拡張できます。

インフラストラクチャ スタンプ内のお客様の分離はテナントで行われ、次のようになります。

- **ネットワーク**: お客様に割り当てられているテナントごとに、仮想ネットワークを通じてインフラストラクチャ スタック内のお客様を分離します。 テナントは 1 件のお客様に割り当てられます。 お客様は複数のテナントを持つことができます。 テナントのネットワーク分離では、各テナントが同じお客様に属している場合でも、インフラストラクチャ スタンプ レベルでのテナント間のネットワーク通信が禁止されます。
- **ストレージ コンポーネント**: ストレージ ボリュームが割り当てられているストレージ仮想マシンを通じて分離します。 ストレージ ボリュームは 1 つのストレージ仮想マシンにのみ割り当てることができます。 ストレージ仮想マシンは、SAP HANA TDI 認定のインフラストラクチャ スタック内の 1 つのテナントに排他的に割り当てられます。 そのため、ストレージ仮想マシンに割り当てられているストレージ ボリュームには、関連付けられている特定のテナント内でのみアクセスできます。 デプロイされている別のテナントには表示されません。
- **サーバーまたはホスト**: サーバー ユニットまたはホスト ユニットがお客様間やテナント間で共有されることはありません。 お客様にデプロイされたサーバーまたはホストは、1 つのテナントに割り当てられたアトミック ベア メタル コンピューティング ユニットです。 別のお客様とホストまたはサーバーを共有することになるハードウェア パーティション分割やソフト パーティション分割が使用されることは*ありません*。 特定のテナントのストレージ仮想マシンに割り当てられたストレージ ボリュームは、このようなサーバーにマウントされます。 テナントは、排他的に割り当てられている別の SKU のサーバー ユニットを 1 つから複数持つことができます。
- SAP HANA on Azure (L インスタンス) インフラストラクチャ スタンプ内では多数のさまざまなテナントがデプロイされており、テナントの概念により、ネットワーク、ストレージ、およびコンピューティング レベルで相互に分離されています。 


これらのベア メタル サーバー ユニットでは、SAP HANA の実行のみがサポートされます。 SAP アプリケーション層またはワークロードのミドルウェア層は仮想マシン内で実行されます。 SAP HANA on Azure (L インスタンス) のユニットを実行しているインフラストラクチャ スタンプは、Azure ネットワーク サービスのバックボーンに接続されています。 これにより、SAP HANA on Azure (L インスタンス) のユニットと仮想マシン間の低遅延の接続が実現されます。

このドキュメントは、SAP HANA on Azure (L インスタンス) について説明する複数のドキュメントの 1 つです。 このドキュメントでは、基本的なアーキテクチャ、責任、ソリューションで提供されるサービスについて説明します。 また、ソリューションの機能の概要についても説明します。 ネットワークや接続など、他のほとんどの領域については、他の 4 つのドキュメントで詳しく説明しています。 SAP HANA on Azure (L インスタンス) のドキュメントでは、SAP NetWeaver のインストールや VM へのデプロイについては取り上げていません。 Azure における SAP NetWeaver については、同じ Azure ドキュメント コンテナーにある別のドキュメントで取り上げています。 


HANA L インスタンス ガイダンスの別のドキュメントでは、次の分野について説明します。

- [SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure (L インスタンス) のインストールと構成](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure (L インスタンス) のトラブルシューティングと監視](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [STONITH を使用した SUSE での高可用性のセットアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Type II SKU の OS のバックアップと復元](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>定義

このアーキテクチャ/テクニカル デプロイ ガイドでは、いくつかの一般的な定義が広く使用されています。 次の用語とその意味に注意してください。

- **IaaS**: サービスとしてのインフラストラクチャ。
- **PaaS**: サービスとしてのプラットフォーム。
- **SaaS**: サービスとしてのソフトウェア。
- **SAP コンポーネント**: 個々の SAP アプリケーション (ERP Central Component (ECC)、Business Warehouse (BW)、Solution Manager、Enterprise Portal (EP) など)。 SAP コンポーネントは、従来の ABAP または Java テクノロジか、ビジネス オブジェクトなどの非 NetWeaver ベース アプリケーションに基づいて作成できます。
- **SAP 環境**: 開発、品質保証、トレーニング、ディザスター リカバリー、運用など、ビジネス機能を実行するために論理的にグループ化された、1 つまたは複数の SAP コンポーネント。
- **SAP ランドスケープ**: IT 環境内にある SAP 資産全体を指します。 SAP ランドス ケープには、運用環境と非運用環境のすべてが含まれます。
- **SAP システム**: SAP ERP 開発システム、SAP BW テスト システム、SAP CRM 運用システムなどの DBMS 層とアプリケーション層の組み合わせ。 Azure のデプロイでは、この 2 つの層をオンプレミスと Azure に分けることはできません。 SAP システムは、オンプレミスまたは Azure のいずれかにデプロイされます。 SAP ランドスケープの各種システムは、Azure とオンプレミスのどちらにデプロイしても構いません。 たとえば、SAP CRM の開発システムとテスト システムを Azure にデプロイし、SAP CRM 運用システムをオンプレミスにデプロイできます。 SAP HANA on Azure (L インスタンス) の場合、VM で SAP システムの SAP アプリケーション層をホストし、SAP HANA on Azure (L インスタンス) スタンプのユニットで関連する SAP HANA インスタンスをホストすることを目的としています。
- **L インスタンス スタンプ**: SAP HANA TDI 認定のハードウェア インフラストラクチャ スタック。Azure 内で SAP HANA インスタンスを実行するための専用のスタックです。
- **SAP HANA on Azure (L インスタンス)**: さまざまな Azure リージョンの L インスタンス スタンプにデプロイされた SAP HANA TDI 認定ハードウェアで HANA インスタンスを実行する、Azure におけるサービスの正式名称。 関連用語の *HANA L インスタンス*は、*SAP HANA on Azure (L インスタンス)* の省略形であり、このテクニカル デプロイ ガイドで広く使用されています。
- **クロスプレミス**: オンプレミスのデータ センターと Azure の間で、サイト間接続、マルチサイト接続、または Azure ExpressRoute 接続を使用する Azure サブスクリプションに VM をデプロイするシナリオを指します。 この種のデプロイは、共通の Azure ドキュメントでもクロスプレミス シナリオとして説明されています。 この接続の目的は、オンプレミスのドメイン、オンプレミスの Azure Active Directory/OpenLDAP、およびオンプレミスの DNS を Azure に拡張することです。 オンプレミスのランドスケープが、Azure サブスクリプションの Azure 資産に拡張されます。 この拡張により、VM をオンプレミス ドメインに含めることができます。 

   オンプレミス ドメインのドメイン ユーザーは、サーバーにアクセスし、それらの VM 上でサービス (DBMS サービスなど) を実行できます。 オンプレミスにデプロイした VM と Azure にデプロイした VM 間での通信と名前解決が可能です。 このシナリオが、ほとんどの SAP 資産をデプロイする際の一般的な方法です。 詳細については、[Azure VPN ゲートウェイの計画と設計](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事、および[Azure Portal を使用したサイト間接続の仮想ネットワークの作成](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。
- **テナント**: HANA L インスタンス スタンプにデプロイされたお客様は、"*テナント*" に分離されます。 テナントは、ネットワーク、ストレージ、およびコンピューティング レイヤーで他のテナントから分離されます。 別のテナントに割り当てられているストレージ ユニットやコンピューティング ユニットは、HANA L インスタンス スタンプ レベルで相互に認識したり通信したりすることはできません。 同じお客様が別々のテナントにデプロイを置くこともありますが、 その場合でも HANA L インスタンス スタンプ レベルでテナント間が通信することはありません。
- **SKU カテゴリ**: HANA L インスタンスの場合、次の 2 つの SKU のカテゴリが提供されます。
    - **Type I クラス**: S72、S72m、S144、S144m、S192、S192m、S192xm
    - **Type II クラス**: S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m


クラウドに SAP ワークロードをデプロイする方法については、他のさまざまなリソースが用意されています。 Azure への SAP HANA のデプロイを計画する場合、Azure IaaS の原則と Azure IaaS での SAP ワークロードのデプロイに関する知識と経験が必要です。 作業を続行する前に、[Azure 仮想マシンでの SAP ソリューションの使用](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事で詳細を確認してください。 

## <a name="certification"></a>認定

SAP では、Azure IaaS などの特定のインフラストラクチャ上の SAP HANA をサポートするための要件として、NetWeaver 認定資格のほか、SAP HANA の特別な認定資格を要求しています。

NetWeaver (および一部の SAP HANA 認定資格) に関する主要な SAP ノートは、「[SAP Note #1928533 - SAP applications on Azure: Supported products and Azure VM types (SAP ノート #1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)](https://launchpad.support.sap.com/#/notes/1928533)」です。

SAP HANA on Azure (L インスタンス) ユニットの SAP HANA の認定記録については、[SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)のサイトを参照してください。 

SAP HANA 認定 IaaS プラットフォームのサイトで取り上げられている SAP HANA on Azure (L インスタンス) タイプを使用すると、Microsoft と SAP のお客様は大規模な SAP Business Suite、SAP BW、S/4 HANA、BW/4HANA、または他の SAP HANA ワークロードを Azure にデプロイできます。 このソリューションは、SAP-HANA 認定の専用のハードウェア スタンプ ([SAP HANA テーラード データ センター統合 - TDI](https://scn.sap.com/docs/DOC-63140)) に基づいています。 SAP HANA TDI で構成されたソリューションを実行すると、SAP HANA ベースのすべてのアプリケーション (SAP Business Suite on SAP HANA、SAP BW on SAP HANA、S4/HANA、BW4/HANA など) がハードウェア インフラストラクチャ上で動作します。

VM で SAP HANA を実行する場合に比べ、このソリューションには利点があります。 このソリューションでは、メモリ ボリュームが大幅に増加します。 このソリューションを有効にする場合は、次の重要な側面を理解しておく必要があります。

- SAP アプリケーション層と SAP 以外のアプリケーションは、通常の Azure ハードウェア スタンプでホストされる VM で実行されます。
- お客様のオンプレミスのインフラストラクチャ、データ センター、アプリケーションのデプロイは、ExpressRoute (推奨) または仮想プライベート ネットワーク (VPN) 経由でクラウド プラットフォームに接続されます。 Active Directory と DNS も Azure に拡張されます。
- HANA ワークロード用の SAP HANA データベース インスタンスは SAP HANA on Azure (L インスタンス) で実行されます。 L インスタンス スタンプは Azure ネットワークに接続されるので、VM で実行されるソフトウェアは HANA L インスタンスで実行される HANA インスタンスとやり取りできます。
- SAP HANA on Azure (L インスタンス) のハードウェアは、SUSE Linux Enterprise Server または Red Hat Enterprise Linux がプレインストールされた IaaS で提供される専用のハードウェアです。 仮想マシンと同様に、オペレーティング システムの更新とメンテナンスはお客様が行います。
- HANA のインストール、または HANA L インスタンスのユニットで SAP HANA を実行するために必要な追加コンポーネントのインストールはお客様が行います。 SAP HANA on Azure の個々の継続的な運用と管理もお客様が行います。
- ここで説明するソリューション以外にも、SAP HANA on Azure (L インスタンス) に接続する Azure サブスクリプションに他のコンポーネントをインストールできます。 例として、SAP HANA データベースとの (直接) 通信を可能にするコンポーネント (ジャンプ サーバー、RDP サーバー、SAP HANA Studio、SAP BI シナリオ向け SAP Data Services、ネットワーク監視ソリューションなど) が挙げられます。
- Azure 内と同様に、HANA L インスタンスは高可用性とディザスター リカバリー機能をサポートします。

## <a name="architecture"></a>アーキテクチャ

大まかに言うと、SAP HANA on Azure (L インスタンス) ソリューションでは、VM 内に SAP アプリケーション層があります。 データベース層は、Azure IaaS に接続される同じ Azure リージョン内の L インスタンス スタンプにある SAP TDI で構成されたハードウェア上にあります。

> [!NOTE]
> SAP アプリケーション層は、SAP DBMS 層と同じ Azure リージョンにデプロイします。 この規則は、Azure 上の SAP ワークロードに関して公開されている情報で明確に文書化されています。 

SAP HANA on Azure (L インスタンス) の全体的なアーキテクチャでは、SAP TDI 認定のハードウェア構成 (SAP HANA データベース用の仮想化されていない、ベア メタルの高パフォーマンス サーバー) が提供されます。 また、SAP アプリケーション層のリソースをニーズに合わせてスケーリングするための Azure の機能と柔軟性も提供されます。

![SAP HANA on Azure (L インスタンス) のアーキテクチャの概要](./media/hana-overview-architecture/image1-architecture.png)

上記のアーキテクチャは、次の 3 つのセクションに分かれています。

- **右**: エンド ユーザーが LOB アプリケーション (SAP など) にアクセスできるように、データ センターでさまざまなアプリケーションを実行するオンプレミスのインフラストラクチャを示します。 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) を使用して、このオンプレミスのインフラストラクチャを Azure に接続するのが理想的です。

- **中央**: Azure IaaS を示します。この例では、VM を使用して、SAP アプリケーションや、SAP HANA を DBMS システムとして使用する他のアプリケーションをホストします。 VM が提供するメモリを使用して機能する小規模な HANA インスタンスは、アプリケーション層と共に VM にデプロイされます。 仮想マシンの詳細については、「[Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)」をご覧ください。

   Azure ネットワーク サービスは、SAP システムと他のアプリケーションをグループ化して仮想ネットワークを作成する際に使用されます。 これらの仮想ネットワークは、オンプレミスのシステムおよび SAP HANA on Azure (L インスタンス) に接続します。

   Azure で実行するためにサポートされている SAP NetWeaver アプリケーションおよびデータベースについては、「[SAP Support Note #1928533 - SAP applications on Azure: Supported products and Azure VM types (SAP サポート ノート #1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)](https://launchpad.support.sap.com/#/notes/1928533)」をご覧ください。 Azure で SAP ソリューションをデプロイする方法については、次のドキュメントをご覧ください。

  -  [Windows 仮想マシンにおける SAP の使用](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Azure 仮想マシンにおける SAP ソリューションの使用](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **左**: Azure L インスタンス スタンプの SAP HANA TDI 認定ハードウェアを示します。 HANA L インスタンス ユニットは、オンプレミスから Azure への接続と同じテクノロジを使用してサブスクリプションの仮想ネットワークに接続されます。

Azure L インスタンス スタンプ自体は次のコンポーネントを結合します。

- **コンピューティング**: 必要なコンピューティング能力を提供し、SAP HANA 認定を受けている Intel Xeon E7-8890v3 プロセッサまたは Intel Xeon E7-8890v4 プロセッサを搭載したサーバー。
- **ネットワーク**: コンピューティング、ストレージ、および LAN コンポーネントを相互接続する統合された高速ネットワーク ファブリック。
- **ストレージ**: 統合されたネットワーク ファブリック経由でアクセスするストレージ インフラストラクチャ。 デプロイする SAP HANA on Azure (L インスタンス) 構成に応じて、特定のストレージ容量が提供されます。 追加の月額料金をお支払いいただくと、ストレージ容量を増やすことができます。

L インスタンス スタンプのマルチテナント インフラストラクチャ内では、分離されたテナントとしてお客様の環境がデプロイされます。 テナントのデプロイ時に、Azure 加入契約内で Azure サブスクリプションに名前を付けます。 これが、HANA L インスタンスの課金対象となる Azure サブスクリプションになります。 これらのテナントには Azure サブスクリプションとの 1 対 1 の関係があります。 ネットワークについては、異なる Azure サブスクリプションに属する異なる仮想ネットワークから、1 つの Azure リージョン内の 1 つのテナントにデプロイされた HANA L インスタンス ユニットにアクセスできます。 それらの Azure サブスクリプションが同じ Azure 加入契約に属している必要があります。 

VM と同様に、SAP HANA on Azure (L インスタンス) は複数の Azure リージョンで提供されます。 ディザスター リカバリー機能を提供するために、オプトインを選択できます。 1 つの地政学的リージョン内の異なる L インスタンス スタンプが相互に接続されます。 たとえば、米国西部と米国東部の HANA L インスタンス スタンプは、ディザスター リカバリー レプリケーションのために、専用のネットワーク リンクを介して接続されます。 

Azure Virtual Machines でさまざまな VM の種類の中から選択できるのと同様に、SAP HANA のさまざまなワークロードの種類に対応した HANA L インスタンスの各種 SKU の中から選択できます。 SAP では、Intel プロセッサの世代に基づいて、さまざまなワークロードにメモリ対プロセッサ ソケット比を適用します。 提供される SKU の種類を次の表に示します。

SAP HANA on Azure (L インスタンス) サービスは、米国西部、米国東部、オーストラリア東部、オーストラリア南東部、西ヨーロッパ、北ヨーロッパ、東日本、西日本の各 Azure リージョンで、次のような複数の構成で提供されています。

[HANA L インスタンスの SAP HANA 認定 SKU](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 一覧は次のような内容です。

| SAP ソリューション | CPU | メモリ | Storage | 可用性 |
| --- | --- | --- | --- | --- |
| OLAP 用に最適化: SAP BW、BW/4HANA、<br /> または SAP HANA (一般的な OLAP ワークロード向け) | SAP HANA on Azure S72<br /> – 2 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 36 CPU コアと 72 CPU スレッド |  768 GB |  3 TB | 使用可能 |
| --- | SAP HANA on Azure S144<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 72 CPU コアと 144 CPU スレッド |  1.5 TB |  6 TB | 提供終了 |
| --- | SAP HANA on Azure S192<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 96 CPU コアと 192 CPU スレッド |  2.0 TB |  8 TB | 使用可能 |
| --- | SAP HANA on Azure S384<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  4.0 TB |  16 TB | 使用可能 |
| OLTP 用に最適化: SAP Business Suite<br /> on SAP HANA または S/4HANA (OLTP)、<br /> 一般的な OLTP | SAP HANA on Azure S72m<br /> – 2 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 36 CPU コアと 72 CPU スレッド |  1.5 TB |  6 TB | 使用可能 |
|---| SAP HANA on Azure S144m<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v3<br /> 72 CPU コアと 144 CPU スレッド |  3.0 TB |  12 TB | 提供終了 |
|---| SAP HANA on Azure S192m<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 96 CPU コアと 192 CPU スレッド  |  4.0 TB |  16 TB | 使用可能 |
|---| SAP HANA on Azure S384m<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  6.0 TB |  18 TB | 使用可能 |
|---| SAP HANA on Azure S384xm<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  8.0 TB |  22 TB |  使用可能 |
|---| SAP HANA on Azure S576m<br /> – 12 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 288 CPU コアと 576 CPU スレッド |  12.0 TB |  28 TB | 使用可能 |
|---| SAP HANA on Azure S768m<br /> – 16 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 384 CPU コアと 768 CPU スレッド |  16.0 TB |  36 TB | 使用可能 |
|---| SAP HANA on Azure S960m<br /> – 20 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 480 CPU コアと 960 CPU スレッド |  20.0 TB |  46 TB | 使用可能 |


SAP HANA TDIv5 の場合、SAP を使用してユーザー固有のサイズとユーザー固有のプロジェクトを構成できるので、認定と見なされないサーバー構成になる可能性があります。

- [SAP HANA 認定アプライアンス](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

多くの場合、このようなユーザー固有のサーバー構成は、SAP で認定されているサーバー ユニットよりも多くのメモリを搭載しています。 ユーザーは SAP と連携して、ユーザー固有のサイズのサーバー構成について SAP のサポートを受け、認定される可能性があります。 Azure では、次の HANA L インスタンス Standard SKU を利用できます。また、このような TDIv5 のユーザー固有のサイズ設定プロジェクトについては、Microsoft の価格表を参照してください。


| メモリ内の拡張可能な <br /> 元の SKU | CPU | メモリ | Storage | 可用性 |
| --- | --- | --- | --- | --- |
| S192m を拡張可能 | SAP HANA on Azure S192xm<br /> – 4 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 96 CPU コアと 192 CPU スレッド |  6.0 TB |  16 TB | 使用可能 |
| S384xm を拡張可能 | SAP HANA on Azure S384xxm<br /> – 8 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 192 CPU コアと 384 CPU スレッド |  12.0 TB |  28 TB | 使用可能 |
| S576m を拡張可能 | SAP HANA on Azure S576xm<br /> – 12 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 288 CPU コアと 576 CPU スレッド |  18.0 TB |  41 TB | 使用可能 |
| S768m を拡張可能 | SAP HANA on Azure S768xm<br /> – 16 x Intel® Xeon® プロセッサ E7-8890 v4<br /> 384 CPU コアと 768 CPU スレッド |  24.0 TB |  56 TB | 使用可能 |

- CPU コア = サーバー ユニットのプロセッサの合計の、非ハイパースレッド CPU コアの合計。
- CPU スレッド = サーバー ユニットのプロセッサの合計の、ハイパースレッド CPU コアによって提供されるコンピューティング スレッドの合計。 ほとんどのユニットは、ハイパースレッディング テクノロジを使用するように既定で構成されます。
- サプライヤーの推奨事項に基づいて、S768m、S768xm、および S960m は、SAP HANA の実行にハイパースレッディングを使用するように構成されていません。


選択する構成は、ワークロード、CPU リソース、および必要なメモリによって異なります。 OLTP ワークロードでは、OLAP ワークロードに最適化された SKU をご利用いただけます。 

ユーザー固有のサイズ設定プロジェクトのユニットを除き、オファーのハードウェア ベースは SAP HANA TDI 認定です。 ハードウェアの 2 種類のクラスでは、SKU を次のように分けています。

- S72、S72m、S144、S144m、S192、S192m、S192xm は、"Type I クラス" の SKU と呼ばれます。
- S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m は、"Type II クラス" の SKU と呼ばれます。

完全な HANA L インスタンス スタンプは、1 件のお客様専用に割り当てられるわけではありません。 これは、Azure にデプロイされたネットワーク ファブリック経由で接続されるコンピューティング リソースとストレージ リソースのラックにも当てはまります。 Azure などの HANA L インスタンス インフラストラクチャでは、次の 3 つのレベルで相互に分離されたお客様のさまざまな &quot;テナント&quot; をデプロイします。

- **ネットワーク**: HANA L インスタンス スタンプ内の仮想ネットワークによって分離します。
- **ストレージ**: ストレージ ボリュームが割り当てられており、テナント間でストレージ ボリュームを分離するストレージ仮想マシンによって分離します。
- **コンピューティング**: サーバー ユニットを 1 つのテナント専用に割り当てます。 サーバー ユニットがハード パーティション分割やソフト パーティション分割されることはありません。 1 つのサーバー ユニットやホスト ユニットがテナント間で共有されることもありません。 

異なるテナント間で HANA L インスタンス ユニットのデプロイが相互に表示されることはありません。 異なるテナントにデプロイされた HANA L インスタンス ユニットは、HANA L インスタンス スタンプ レベルで相互に直接通信することはできません。 HANA L インスタンス スタンプ レベルでは、同じテナント内の HANA L インスタンス ユニットだけが相互に通信できます。

L インスタンス スタンプにデプロイされたテナントは、課金のために 1 つの Azure サブスクリプションに割り当てられます。 ネットワークについては、同じ Azure 加入契約内の他の Azure サブスクリプションの仮想ネットワークからアクセスできます。 同じ Azure リージョンの別の Azure サブスクリプションを使用してデプロイする場合は、分離された HANA L インスタンス テナントを要求することもできます。

HANA L インスタンスで実行される SAP HANA と、Azure にデプロイされた VM で実行される SAP HANA には大きな違いがあります。

- SAP HANA on Azure (L インスタンス) の仮想化層はありません。 基になるベア メタル ハードウェアのパフォーマンスを確保します。
- Azure とは異なり、SAP HANA on Azure (L インスタンス) サーバーは特定のお客様に専用のサーバーです。 サーバー ユニットまたはホスト ユニットがハード パーティション分割やソフト パーティション分割されることはありません。 そのため、HANA L インスタンス ユニットは、その全体がテナントに割り当てられ、テナントがお客様に割り当てられて使用されます。 サーバーを再起動またはシャットダウンしても、オペレーティング システムと SAP HANA が別のサーバーに自動的にデプロイされることはありません  (Type I クラスの SKU の場合、唯一の例外は、サーバーで問題が発生し、別のサーバーで再デプロイを実行する必要がある場合です)。
- Azure とは異なり、価格/パフォーマンス比が最も高くなるようにホスト プロセッサの種類を選択する場合は、Intel E7v3 および E7v4 プロセッサ ラインの最もパフォーマンスが高いプロセッサが SAP HANA on Azure (L インスタンス) 用に選択されます。


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>1 つの HANA L インスタンス ユニットでの複数の SAP HANA インスタンスの実行
HANA L インスタンス ユニットで複数のアクティブな SAP HANA インスタンスをホストできます。 ストレージ スナップショットとディザスター リカバリーの機能を提供するために、このような構成ではインスタンスごとにボリューム セットが必要になります。 現時点では、HANA L インスタンス ユニットを次のように分けることができます。

- **S72、S72m、S144、S192**: 256 GB の最小ユニットから 256 GB ずつ増加。 256 GB、512 GB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。
- **S144m、S192m**: 512 GB の最小ユニットから 256 GB ずつ増加。 512 GB、768 GB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。
- **Type II クラス**: 2 TB の最小ユニットから 512 GB ずつ増加。 512 GB、1 TB、1.5 TB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。

複数の SAP HANA インスタンスの実行例は次のようになります。

| SKU | メモリ サイズ | ストレージ サイズ | 複数のデータベースとサイズ |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 768 GB HANA インスタンス x 1<br /> または 512 GB インスタンス x 1 + 256 GB インスタンス x 1<br /> または 256 GB インスタンス x 3 | 
| S72m | 1.5 TB | 6 TB | 512 GB HANA インスタンス x 3<br />または 512 GB インスタンス x 1 + 1 TB インスタンス x 1<br />または 256 GB インスタンス x 6<br />または 1.5 TB インスタンス x 1 | 
| S192m | 4 TB | 16 TB | 512 GB インスタンス x 8<br />または 1 TB インスタンス x 4<br />または 512 GB インスタンス x 4 + 1 TB インスタンス x 2<br />または 768 GB インスタンス x 4 + 512 GB インスタンス x 2<br />または 4 TB インスタンス x 1 |
| S384xm | 8 TB | 22 TB | 2 TB インスタンス x 4<br />または 4 TB インスタンス x 2<br />または 3 TB インスタンス x 2 + 2 TB インスタンス x 1<br />または 2.5 TB インスタンス x 2 + 3 TB インスタンス x 1<br />または 8 TB インスタンス x 1 |


他のバリエーションもあります。 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA のデータ階層化と拡張ノードの使用
SAP では、さまざまな SAP NetWeaver リリースの SAP BW および SAP BW/4HANA のデータ階層化モデルをサポートしています。 データ階層化モデルの詳細については、SAP ドキュメントの「[SAP BW/4HANA and SAP BW on HANA with SAP HANA extension nodes (SAP HANA 拡張ノードを持つ SAP BW/4HANA および SAP BW-on-HANA)](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)」をご覧ください。
HANA L インスタンスを使用すると、FAQ および SAP ブログ ドキュメントで説明する SAP HANA 拡張ノードのオプション 1 の構成をご利用いただけます。 オプション 2 の構成は次の HANA L インスタンス SKU を使用して設定できます: S72m、S192、S192m、S384、S384m。 

ドキュメントを参照しても、すぐには利点を確認できないかもしれません。 しかし、SAP のサイズ変更ガイドラインを見ると、オプション 1 およびオプション 2 の SAP HANA 拡張ノードを使用することによる利点を確認できます。 次に例を示します。

- SAP HANA のサイズ変更ガイドラインでは通常、メモリの 2 倍のデータ ボリュームが必要です。 ホット データで SAP HANA インスタンスを実行した場合、メモリの 50% 以下しかデータが入っていません。 残りのメモリは、SAP HANA が作業を実行するために残しておくのが理想です。
- つまり、2 TB のメモリを持つ HANA L インスタンス S192 ユニットで SAP BW データベースを実行すると、データ ボリュームとしては 1 TB しかありません。
- オプション 1 の追加の SAP HANA 拡張ノードと S192 HANA L インスタンス SKU を使用した場合、データ ボリューム用に 2 TB の容量が追加されます。 オプション 2 の構成では、ウォーム データ ボリューム用に 4 TB が追加されます。 ホット ノードと比較した場合、オプション 1 ではデータの格納に "ウォーム" 拡張ノードの全メモリ容量をご利用いただけます。 オプション 2 の SAP HANA 拡張ノード構成では、データ ボリュームに 2 倍のメモリをご利用いただけます。
- その結果、オプション 1 ではデータ容量が 3 TB、ホットとウォームの割合が 1:2 になります。 オプション 2 の拡張ノード構成では、データ容量が 5 TB、ホットとウォームの割合が 1:4 になります。

メモリに対してデータ ボリュームが大きいほど、求めるウォーム データがディスク ストレージに保存されている可能性が高くなります。


## <a name="operations-model-and-responsibilities"></a>運用モデルと責任

SAP HANA on Azure (L インスタンス) が提供するサービスは Azure IaaS サービスと連携しています。 HANA L インスタンスでは、SAP HANA に最適化されたオペレーティング システムがインストールされたインスタンスが提供されます。 Azure IaaS VM と同様に、OS の強化、追加ソフトウェアのインストール、HANA のインストール、OS と HANA の運用、OS と HANA の更新などのほとんどのタスクはお客様が行う必要があります。 Microsoft が OS の更新や HANA の更新を強制することはありません。

![SAP HANA on Azure (L インスタンス) の責任](./media/hana-overview-architecture/image2-responsibilities.png)

図に示すように、SAP HANA on Azure (L インスタンス) はマルチテナント IaaS です。 大部分が、OS とインフラストラクチャの境界に基づいて責任が分担されます。 Microsoft は、オペレーティング システムのラインの下側にあるサービスのあらゆる面に対して責任を負います。 お客様はラインの上側にあるサービスのあらゆる面に対して責任を負います。 OS はお客様の責任となります。 お客様は、コンプライアンス、セキュリティ、アプリケーション管理、基盤、OS 管理に現在使用しているほとんどのオンプレミスの手法を引き続きご利用いただけます。 システムはネットワーク内に存在するかのように見えます。

このサービスは SAP HANA に最適化されているため、最良の結果を得るために、お客様が Microsoft と協力して基になるインフラストラクチャの機能を使用する必要がある領域が存在します。

それぞれの層とお客様の責任の詳細を次に示します。

**ネットワーク**: SAP HANA を実行する L インスタンス スタンプのすべての内部ネットワーク。 お客様の責任には、ストレージへのアクセス、インスタンス間の接続 (スケールアウトと他の機能用)、ランドスケープへの接続、仮想マシンで SAP アプリケーション層がホストされる Azure への接続が含まれます。 また、ディザスター リカバリーのためのレプリケーションに使用する、Azure データ センター間の WAN 接続も含まれます。 すべてのネットワークはテナントによってパーティション分割され、サービス品質が適用されます。

**ストレージ**: SAP HANA サーバーで必要なすべてのボリュームおよびスナップショットに対応した、仮想化およびパーティション分割されたストレージ。 

**サーバー**: テナントに割り当てられている SAP HANA DB を実行する専用の物理サーバー。 Type I クラスの SKU のサーバーは、抽象化されたハードウェアです。 この種のサーバーでは、サーバー構成が収集され、物理ハードウェア間で移動できるプロファイルに保持されます。 操作によるプロファイルのこのような (手動の) 移動は、Azure のサービス復旧に少し似ています。 Type II クラスの SKU のサーバーには、このような機能はありません。

**SDDC**: データ センターをソフトウェアによるエンティティとして管理するために使用する管理ソフトウェア。 SDDC により、Microsoft はスケール、可用性、パフォーマンスのためのリソースをプールできます。

**O/S**: サーバーで実行するために選択する OS (SUSE Linux または Red Hat Linux)。 お客様に提供される OS イメージは、SAP HANA を実行するために各 Linux ベンダーから Microsoft に提供されたイメージです。 特定の SAP HANA 最適化イメージでは、Linux ベンダーのサブスクリプションが必要です。 OS ベンダーへのイメージの登録はお客様が行う必要があります。 

Microsoft による引き渡しの時点から、Linux オペレーティング システムの修正プログラムの適用もお客様の責任になります。 この修正プログラムには、SAP HANA を正常にインストールするために必要であり、特定の Linux ベンダーが SAP HANA に最適化された OS イメージに含めていない追加パッケージが含まれます  (詳細については、SAP の HANA インストール ドキュメントと SAP ノートをご覧ください)。 

お客様は、OS の動作不良または最適化に関連する OS 修正プログラムと特定のサーバー ハードウェアに関連するドライバーに対して責任を負います。 また、OS のセキュリティまたは機能の修正プログラムの適用もお客様の責任となります。 

お客様の責任には、次の項目の監視と容量計画も含まれます。

- CPU リソース使用量
- メモリ使用量
- 空き領域、IOPS、待機時間に関連するディスク ボリューム
- HANA L インスタンスと SAP アプリケーション層の間のネットワーク ボリュームのトラフィック

HANA L インスタンスの基になるインフラストラクチャは、OS ボリュームのバックアップと復元の機能を提供します。 この機能もお客様の責任で使用します。

**ミドルウェア**: 主に SAP HANA インスタンスです。 管理、運用、監視はお客様が担当します。 お客様は、ストレージ スナップショットを使用してバックアップ/復元とディザスター リカバリーを実行するために提供される機能をご利用いただけます。 これらの機能はインフラストラクチャから提供されます。 お客様の責任には、これらの機能を使用した高可用性またはディザスター リカバリーの設計、それらの活用、ストレージ スナップショットが正常に実行されたかどうかを確認するための監視も含まれます。

**データ**: SAP HANA で管理されるデータおよびその他のデータ (ボリュームやファイル共有上のバックアップ ファイルなど)。 お客様の責任には、ディスクの空き領域の監視とボリューム上のコンテンツの管理が含まれます。 また、ディスク ボリュームとストレージ スナップショットのバックアップが正常に実行されたかどうかの監視もお客様が行う必要があります。 ディザスター リカバリー サイトへのデータ レプリケーションの正常な実行は Microsoft の責任となります。

**アプリケーション**: SAP アプリケーション インスタンス。SAP 以外のアプリケーションの場合は、それらのアプリケーションのアプリケーション層。 お客様の責任には、それらのアプリケーションのデプロイ、管理、運用、監視が含まれます。 仮想ネットワーク内でのCPU リソース使用量、メモリ使用量、Azure Storage 使用量、ネットワーク帯域幅の使用量の容量計画はお客様の責任になります。 仮想ネットワークから SAP HANA on Azure (L インスタンス) へのリソース使用量の容量計画もお客様の責任になります。

**WAN**: ワークロード用に確立するオンプレミスから Azure デプロイへの接続。 HANA L インスタンスを使用するすべてのお客様が、接続に Azure ExpressRoute を使用します。 この接続は、SAP HANA on Azure (L インスタンス) ソリューションには含まれていません。 この接続のセットアップはお客様が行う必要があります。

**アーカイブ**: ストレージ アカウントで独自の手法を使用してデータのアーカイブ コピーを作成できます。 アーカイブには、管理、コンプライアンス、コスト、運用が必要です。 お客様は、Azure でアーカイブ コピーとバックアップを生成し、コンプライアンスに準拠した方法でそれらを保存する責任を負います。

「[Azure 上の SAP HANA の L インスタンスの SLA](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/)」を参照してください。

## <a name="sizing"></a>サイズ変更

通常、HANA L インスタンスのサイズ変更は HANA のサイズ変更とまったく同じです。 デプロイ済みの既存のシステムを他の RDBMS から HANA に移行する場合、既存の SAP システムで実行される複数のレポートが SAP から提供されています。 データベースを HANA に移行する場合、これらのレポートによってデータが確認され、HANA インスタンスのメモリ要件が計算されます。 これらのレポートを実行する方法と、レポートの最新の修正プログラムまたはバージョンを入手する方法の詳細については、次の SAP ノートをご覧ください。

- [SAP Note #1793345 - Sizing for SAP Suite on HANA (SAP ノート #1793345 - HANA での SAP Suite のサイズ変更)](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report (SAP ノート #1872170 - Suite on HANA および S/4 HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - FAQ: SAP BW on HANA Sizing Report (SAP ノート #2121330 - FAQ: SAP BW on HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - Sizing Report for BW on HANA (SAP ノート #1736976 - BW on HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - New Sizing Report for BW on HANA (SAP ノート #2296290 - BW on HANA の新しいサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/2296290)

緑色のフィールドの実装では、SAP Quick Sizer を使用して、HANA 上への SAP ソフトウェアの実装に必要なメモリを計算できます。

データ量の増加に伴って、HANA のメモリ要件が増加します。 将来的に必要になるメモリ容量を予測できるように、現在のメモリ使用量に注意してください。 メモリ要件に基づいて、いずれかの HANA L インスタンス SKU に要求をマップできます。

## <a name="requirements"></a>必要条件

SAP HANA on Azure (L インスタンス) を実行するための要件を以下に示します。

**Microsoft Azure**

- SAP HANA on Azure (L インスタンス) にリンク可能な Azure サブスクリプション。
- Microsoft Premier サポート契約。 Azure での SAP の実行に関する具体的な情報については、「[SAP Support Note #2015553 - SAP on Microsoft Azure: Support Prerequisites (SAP サポート ノート #2015553 - Microsoft Azure 上のSAP: サポートの前提条件)](https://launchpad.support.sap.com/#/notes/2015553)」をご覧ください。 384 個以上の CPU で HANA L インスタンス ユニットを使用する場合は、Premier サポート契約を拡張して Azure Rapid Response を含める必要があります。
- SAP でのサイズ変更の実行後に必要な HANA L インスタンス SKU の認識。

**ネットワーク接続**

- オンプレミスと Azure 間の ExpressRoute: オンプレミスのデータ センターを Azure に接続するには、1 Gbps 以上の接続を ISP と契約してください。 

**オペレーティング システム**

- SAP アプリケーション用の SUSE Linux Enterprise Server 12 のライセンス。

   > [!NOTE] 
   > Microsoft が提供するオペレーティング システムは SUSE に登録されません。 また、Subscription Management Tool インスタンスにも接続されません。

- Azure の VM にデプロイされた SUSE Linux Subscription Management Tool。 このツールにより、SAP HANA on Azure (L インスタンス) を SUSE で登録し、個々に更新できるようになります  (HANA L インスタンス データ センター内ではインターネットにアクセスできません)。 
- SAP HANA 用の Red Hat Enterprise Linux 6.7 または 7.x のライセンス。

   > [!NOTE]
   > Microsoft が提供するオペレーティング システムは Red Hat に登録されません。 また、Red Hat Subscription Manager インスタンスにも接続されません。

- Azure の VM にデプロイされた Red Hat Subscription Manager。 Red Hat Subscription Manager により、SAP HANA on Azure (L インスタンス) を Red Hat で登録し、個々に更新できるようになります  (Azure L インスタンス スタンプにデプロイされたテナント内からインターネットに直接アクセスすることはできません)。
- SAP が定める要件上、Linux プロバイダーとのサポート契約も必要です。 HANA L インスタンスのソリューションであること、または Azure で Linux を実行するという事実によって、この要件がなくなるわけではありません。 一部の Linux Azure ギャラリー イメージとは異なり、HANA L インスタンスのソリューション プランにサービス料金は*含まれません*。 Linux ディストリビューターとのサポート契約に関する SAP の要件を満たすのは、お客様の責任になります。 
   - SUSE Linux については、「[SAP Note #1984787 - SUSE Linux Enterprise Server 12: Installation notes (SAP ノート #1984787 - SUSE Linux Enterprise Server 12: インストールに関する注意事項)](https://launchpad.support.sap.com/#/notes/1984787)」および「[SAP Note #1056161 - SUSE priority support for SAP applications (SAP ノート #1056161 - SAP アプリケーションの SUSE 優先サポート)](https://launchpad.support.sap.com/#/notes/1056161)」でサポート契約の要件を確認してください。
   - Red Hat Linux の場合、HANA L インスタンスのオペレーティング システムに対するサポートとサービスの更新を含む適切なサブスクリプション レベルが必要になります。 Red Hat では、Red Hat Enterprise Linux for [SAP Solutions](https://access.redhat.com/solutions/3082481 サブスクリプションを推奨しています。 

各種 Linux バージョンと各種 SAP HANA バージョンのサポート マトリックスについては、[SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581) を確認してください。

オペレーティング システムと HLI ファームウェア/ドライバーのバージョンの互換性対応表については、[HLI の OS のアップグレード](os-upgrade-hana-large-instance.md)に関するページを参照してください。


**データベース**

- SAP HANA (Platform Edition または Enterprise Edition) 用のライセンスとソフトウェア インストール コンポーネント。

**アプリケーション**

- SAP HANA に接続する SAP アプリケーションのライセンスとソフトウェア インストール コンポーネント、および関連する SAP サポート契約。
- SAP HANA on Azure (L インスタンス) 環境に関連して使用される SAP 以外のアプリケーションのライセンスとソフトウェア インストール コンポーネント、および関連する SAP サポート契約。

**スキル**

- Azure IaaS とそのコンポーネントの使用経験と知識。
- Azure への SAP ワークロードのデプロイの経験と知識。
- SAP HANA インストール認定資格者。
- SAP HANA に関連する高可用性とディザスター リカバリーを設計するための SAP アーキテクトのスキル。

**SAP**

- SAP の顧客であり、SAP とサポート契約を結んでいることが望まれます。
- 特に、Type II クラスの HANA L インスタンス SKU の実装では、SAP HANA のバージョンと、サイズの大きいスケールアップ ハードウェア上での最終的な構成について、SAP に相談してください。


## <a name="storage"></a>Storage

SAP HANA on Azure (L インスタンス) の記憶域のレイアウトは、SAP が推奨するガイドラインを使用して、クラシック デプロイ モデルで SAP HANA によって構成されます。 これらのガイドラインは、ホワイト ペーパー「[SAP HANA Storage Requirements (SAP HANA ストレージ要件)](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)」に記載されています。

Type I クラスの HANA L インスタンスは、ストレージ ボリュームの 4 倍のメモリ ボリュームを備えています。 Type II クラスの HANA L インスタンス ユニットの場合、ストレージが 4 倍以上になることはありません。 これらのユニットは、HANA トランザクション ログ バックアップを保存するためのボリュームを備えています。 詳細については、[SAP HANA on Azure (L インスタンス) のインストールと構成](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

記憶域割り当てに関しては、次の表を参照してください。 この表は、各種 HANA L インスタンス ユニットで提供されるさまざまなボリュームのおおよその容量を示しています。

| HANA L インスタンス SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S192 | 4,608 GB | 1,024 GB | 1,536 GB | 1,024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xm | 16,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xxm |  20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576m | 20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4,096 GB | 2,048 GB | 4,096 GB |
| S768m | 28,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S768xm | 40,960 GB | 6,144 GB | 4,096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


実際にデプロイされるボリュームは、デプロイと、ボリューム サイズの表示に使用するツールによって異なる場合があります。

HANA L インスタンス SKU を分割する場合、考えられる各分割部分の例は次のようになります。

| メモリ パーティション (GB) | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1,024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


これらのサイズはおおよそのボリューム数であり、デプロイと、ボリュームの確認に使用するツールによって若干異なる場合があります。 他のパーティション サイズ (2.5 TB など) もあります。 これらのストレージ サイズは、上記のパーティションに使用したものと同様の数式で計算されます。 "パーティション" という用語は、オペレーティング システム、メモリ、または CPU リソースがパーティション分割されていることを意味するわけではありません。 これは、1 つの HANA L インスタンス ユニットにデプロイできるさまざまな HANA インスタンスのストレージ パーティションを示しています。 

ストレージを増やすことが必要な場合があります。 ストレージを追加するには、1 TB 単位で追加のストレージを購入します。 この追加のストレージは、ボリュームとして追加できます。 また、1 つ以上の既存のボリュームを拡張するために使用することもできます。 もともとデプロイされているボリュームのサイズ (上記の表に記載) を減らすことはできません。 また、ボリュームの名前やマウント名を変更することもできません。 上記のストレージ ボリュームは、NFS4 ボリュームとして HANA L インスタンス ユニットに接続されます。

バックアップ/復元とディザスター リカバリーのために、ストレージ スナップショットをご利用いただけます。 詳細については、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

ご使用のシナリオの記憶域レイアウトの詳細については、[HLI でサポートされるシナリオ](hana-supported-scenario.md)に関する記事を参照してください。

### <a name="encryption-of-data-at-rest"></a>保存データの暗号化
HANA L インスタンスに使用されるストレージでは、ディスクに保存されているデータを透過的に暗号化することができます。 HANA L インスタンス ユニットのデプロイ時に、この種の暗号化を有効にすることができます。 また、デプロイの実行後に暗号化されたボリュームを変更することもできます。 暗号化されていないボリュームから暗号化されたボリュームへの移行は透過的に行われ、ダウンタイムは発生しません。 

Type I クラスの SKU では、ブート LUN が格納されているボリュームが暗号化されます。 HANA L インスタンスの Type II クラスの SKU の場合、OS 方式でブート LUN を暗号化する必要があります。 詳細については、Microsoft のサービス管理チームに問い合わせてください。


## <a name="networking"></a>ネットワーク

Azure ネットワーク サービスのアーキテクチャは、HANA L インスタンスへの SAP アプリケーションのデプロイを成功させるための重要な要素です。 通常、SAP HANA on Azure (L インスタンス) デプロイメントには、サイズとデータベース、CPU リソース使用量、メモリ使用量が異なる複数の SAP ソリューションを含む大規模な SAP ランドスケープが存在します。 このような SAP システムのすべてが SAP HANA に基づくわけではないと考えられます。 ほとんどの場合、SAP ランドスケープは、次のコンポーネントを使用するハイブリッド構成になります。

- デプロイされた SAP システム (オンプレミス)。 サイズに起因して、現在、これらのシステムは Azure でホストできません。 例として、SQL Server (データベースとして使用) で実行され、VM が提供できるものよりも多くの CPU またはメモリ リソースを必要とする、SAP ERP 実稼働システムが挙げられます。
- デプロイされたSAP HANA ベースの SAP システム (オンプレミス)。
- VM にデプロイされた SAP システム。 これらのシステムは、Azure (VM) に正常にデプロイできる SAP NetWeaver ベースのアプリケーション用の (リソース使用量とメモリの需要に基づく) 開発、テスト、サンドボックス、または実稼働インスタンスです。 これらのシステムは、SQL Server などのデータベースに基づく場合もあります。 詳細については、「[SAP Support Note #1928533 - SAP applications on Azure: Supported products and Azure VM types (SAP サポート ノート #1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)](https://launchpad.support.sap.com/#/notes/1928533/E)」をご覧ください。 これらのシステムは、SAP HANA などのデータベースに基づく場合もあります。 詳細については、[SAP HANA 認定 IaaS プラットフォーム](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)に関するページをご覧ください。
- Azure L インスタンス スタンプの SAP HANA on Azure (L インスタンス) を活用する、Azure (VM) にデプロイされた SAP アプリケーション サーバー。

4 種類以上のデプロイ シナリオがあるハイブリッド SAP ランドスケープが一般的です。 Azure で実行されている完全な SAP ランドスケープの顧客事例も多数あります。 VM の強化に伴って、すべての SAP ソリューションを Azure に移行するお客様が増えています。

Azure にデプロイされた SAP システムのコンテキストでの Azure ネットワークは複雑ではありません。 このネットワークは次の原則に基づいています。

- Azure 仮想ネットワークは、オンプレミスのネットワークに接続する ExpressRoute 回線に接続する必要があります。
- オンプレミスを Azure に接続する ExpressRoute 回線には、通常、1 Gbps 以上の帯域幅が必要です。 この最小帯域幅により、オンプレミスのシステムと VM で実行されているシステム間のデータ転送用の十分な帯域幅を確保できます。 また、オンプレミスのユーザーから Azure システムへの接続用の十分な帯域幅も確保できます。
- Azure 内のすべての SAP システムを仮想ネットワークで設定して、相互通信を可能にする必要があります。
- オンプレミスでホストされる Active Directory と DNS は、ExpressRoute 経由でオンプレミスから Azure に拡張されます。


> [!NOTE] 
> 課金の観点から、1 つの Azure サブスクリプションは、特定の Azure リージョン内の L インスタンス スタンプの 1 つのテナントにのみリンクできます。 逆に言うと、1 つの L インスタンス スタンプのテナントは 1 つの Azure サブスクリプションにのみリンクできます。 この要件は、Azure の他の課金対象オブジェクトと変わりません。

SAP HANA on Azure (L インスタンス) を複数の異なる Azure リージョンにデプロイすると、別のテナントが L インスタンス スタンプにデプロイされます。 これらのインスタンスが同じ SAP ランドスケープに含まれていれば、それらを同じ Azure サブスクリプションで実行できます。 

> [!IMPORTANT] 
> SAP HANA on Azure (L インスタンス) でサポートされるのは、Azure Resource Manager デプロイメントだけです。

 

### <a name="additional-virtual-network-information"></a>仮想ネットワークの追加情報

仮想ネットワークを ExpressRoute に接続するには、Azure ゲートウェイを作成する必要があります。 詳細については、「[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

Azure ゲートウェイは、Azure の外部のインフラストラクチャまたは Azure インスタンス スタンプへの ExpressRoute で使用できます。 また、仮想ネットワーク間の接続に使用することもできます。 詳細については、[PowerShell を使用した Resource Manager のネットワーク間接続の構成](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。 ExpressRoute の接続元が、それぞれ異なる Microsoft エンタープライズ エッジ ルーターであれば、Azure ゲートウェイを最大 4 つの異なる ExpressRoute に接続できます。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

> [!NOTE] 
> Azure ゲートウェイが提供するスループットは 2 つのユース ケースで異なります。 詳細については、「[VPN Gateway について](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 ExpressRoute 接続を使用して仮想ネットワーク ゲートウェイで実現できる最大スループットは 10 Gbps です。 仮想ネットワーク内の VM とオンプレミスのシステム間で (単一のコピー ストリームとして) ファイルをコピーすると、さまざまなゲートウェイ SKU の最大限のスループットを実現できません。 仮想ネットワーク ゲートウェイのすべての帯域幅を活用するには、複数のストリームを使用します。 または、1 つのファイルのパラレル ストリームで複数のファイルをコピーする必要があります。


### <a name="networking-architecture-for-hana-large-instance"></a>HANA L インスタンスのネットワーク アーキテクチャ
HANA L インスタンスのネットワーク アーキテクチャは、次の 4 つの部分に分けることができます。

- オンプレミス ネットワークと Azure への ExpressRoute 接続。 この部分はお客様の領域であり、ExpressRoute 経由で Azure に接続されます。 次の図の右下の部分を参照してください。
- 仮想ネットワークを使用する前述の Azure ネットワーク サービス (ここでもゲートウェイを使用)。 この部分は、アプリケーションの要件、セキュリティ、コンプライアンス要件に適した設計を見つける必要がある領域です。 仮想ネットワークの数と選択する Azure ゲートウェイ SKU の観点で、HANA L インスタンスを使用するかどうかがもう 1 つの検討事項となります。 図の右上の部分を参照してください。
- ExpressRoute テクノロジを使用した Azure への HANA L インスタンスの接続。 この部分は Microsoft によってデプロイされ、処理されます。 お客様が行う必要があるのは、HANA L インスタンスへの資産のデプロイ後に、ExpressRoute 回線を仮想ネットワークに接続する IP アドレス範囲を指定するだけです。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 
- HANA L インスタンスでのネットワーキング。ほとんどの場合、これはお客様に対して透過的です。

![SAP HANA on Azure (L インスタンス) およびオンプレミスに接続された仮想ネットワーク](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

HANA L インスタンスを使用するため、オンプレミスの資産を ExpressRoute 経由で Azure に接続するという要件は変わりません。 また、HANA L インスタンス ユニットでホストされている HANA インスタンスに接続するアプリケーション層をホストする VM を、1 つまたは複数の仮想ネットワークで実行するための要件も変わりません。 

Azure での SAP デプロイとの違いは次のとおりです。

- 顧客テナントの HANA L インスタンス ユニットは、別の ExpressRoute 回線を介して仮想ネットワークに接続されます。 負荷条件を分離するために、オンプレミスと仮想ネットワーク間の ExpressRoute リンクと、仮想ネットワークと HANA L インスタンス間のリンクは、同じルーターを共有しません。
- SAP アプリケーション層と HANA L インスタンス間のワークロード プロファイルは、SAP HANA からアプリケーション層への多数の小さな要求とデータ転送 (結果セット) などのバーストという異なる特性を示します。
- SAP アプリケーション アーキテクチャは、オンプレミスと Azure 間でデータが交換される標準的なシナリオよりも、ネットワーク待機時間の影響を受けやすくなります。
- 仮想ネットワーク ゲートウェイには少なくとも 2 つの ExpressRoute 接続があります。 これらの接続は、仮想ネットワーク ゲートウェイの受信データの最大帯域幅を共有します。

VM と HANA L インスタンス ユニット間で発生するネットワーク待機時間は、VM 間のネットワーク ラウンド トリップの標準的な待機時間よりも長くなる可能性があります。 Azure リージョンによっては、測定値が、「[SAP Note #1100926 - FAQ: Network performance (SAP ノート #1100926 - FAQ: ネットワーク パフォーマンス)](https://launchpad.support.sap.com/#/notes/1100926/E)」で平均以下に分類されているラウンド トリップの待機時間 (0.7 ms) を超える可能性があります。 しかし、お客様は SAP HANA ベースの実稼働 SAP アプリケーションを SAP HANA L インスタンスに問題なくデプロイしています。 デプロイを実行したお客様から、HANA L インスタンス ユニットを使用して SAP HANA で SAP アプリケーションを実行することによる大幅な改善が報告されています。 Azure HANA L インスタンスでビジネス プロセスを十分にテストしてください。
 
VM と HANA L インスタンス間の決定論的なネットワーク待機時間を実現するには、仮想ネットワーク ゲートウェイ SKU の選択が不可欠となります。 オンプレミスと VM 間のトラフィック パターンとは異なり、VM と HANA L インスタンス間のトラフィック パターンでは、送信される要求やデータ量は小さくても、大きなバーストが発生する可能性があります。 このようなバーストを適切に処理するために、UltraPerformance ゲートウェイ SKU を使用することを強くお勧めします。 Type II クラスの HANA L インスタンス SKU の場合、仮想ネットワーク ゲートウェイとして UltraPerformance ゲートウェイ SKU を使用することが必須となります。

> [!IMPORTANT] 
> SAP アプリケーション層とデータベース層の間の全体的なネットワーク トラフィックを考慮し、SAP HANA on Azure (L インスタンス) への接続では、仮想ネットワーク用の HighPerformance または UltraPerformance ゲートウェイ SKU だけがサポートされます。 HANA L インスタンスの Type II SKU の場合、仮想ネットワーク ゲートウェイとしてサポートされるのは UltraPerformance ゲートウェイ SKU だけです。



### <a name="single-sap-system"></a>単一の SAP システム

上記のオンプレミスのインフラストラクチャは ExpressRoute 経由で Azure に接続されます。 ExpressRoute 回線は、エンタープライズ エッジ ルーターに接続されます。 詳細については、[ExpressRoute の技術概要](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。 ルートが確立されると、そのルートは Azure バックボーンに接続され、すべての Azure リージョンにアクセスできるようになります。

> [!NOTE] 
> Azure で SAP ランドスケープを実行するには、SAP ランドスケープの Azure リージョンに最も近いエンタープライズ エッジ ルーターに接続します。 Azure IaaS 内の VM と L インスタンス スタンプ間のネットワーク待ち時間を最小限に抑えるために、Azure L インスタンス スタンプは専用のエンタープライズ エッジ ルーター デバイスを介して接続されます。

SAP アプリケーション インスタンスをホストする VM の仮想ネットワーク ゲートウェイは ExpressRoute 回線に接続されます。 同じ仮想ネットワークが、L インスタンス スタンプへの接続専用の別のエンタープライズ エッジ ルーターに接続されます。

このシステムは、単一の SAP システムのわかりやすい例です。 SAP アプリケーション層は Azure でホストされます。 SAP HANA データベースは SAP HANA on Azure (L インスタンス) で実行されます。 仮想ネットワーク ゲートウェイの帯域幅 (2 Gbps または 10 Gbps のスループット) がボトルネックにならないことが前提となります。

### <a name="multiple-sap-systems-or-large-sap-systems"></a>複数の SAP システムまたは大規模な SAP システム

SAP HANA on Azure (L インスタンス) に接続する複数の SAP システムまたは大規模な SAP システムをデプロイする場合、仮想ネットワーク ゲートウェイのスループットがボトルネックになることがあります。 このような場合は、アプリケーション層を複数の仮想ネットワークに分割します。 次のような場合には、HANA L インスタンスに接続する特別な仮想ネットワークを作成することもできます。

- HANA L インスタンス内の HANA インスタンスから、NFS 共有をホストする Azure の VM に直接バックアップを実行する。
- HANA L インスタンス ユニットから、バックアップ ファイルなど大きなファイルを Azure 内の管理されたディスク領域にコピーする。

別の仮想ネットワークを使用して、ストレージを管理する VM をホストします。 この配置では、HANA L インスタンスから Azure に大きなファイルやデータを転送することによる影響が、SAP アプリケーション層を実行する VM に対応する仮想ネットワーク ゲートウェイに及ぶことはありません。 

スケーラブルなネットワーク アーキテクチャでは次のように対応します。

- 1 つの大規模な SAP アプリケーション層に対して複数の仮想ネットワークを活用します。
- 同じ仮想ネットワークの個々のサブネットに SAP システムの組み合わせを配置するのではなく、デプロイされた SAP システムごとに個別の仮想ネットワークをデプロイします。

 SAP HANA on Azure (L インスタンス) 用のスケーラブルなネットワーク アーキテクチャを次に示します。

![複数の仮想ネットワークにわたる SAP アプリケーション層のデプロイ](./media/hana-overview-architecture/image4-networking-architecture.png)

この図は、複数の仮想ネットワークにわたってデプロイされた SAP アプリケーション層 (コンポーネント) を示しています。 この構成では、これらの仮想ネットワークでホストされるアプリケーション間の通信時に発生する待機時間のオーバーヘッドを回避できませんでした。 既定では、異なる仮想ネットワークにある VM 間のネットワーク トラフィックは、この構成では エンタープライズ エッジ ルーター経由でルーティングされます。 2 つの仮想ネットワーク間の通信を最適化し、待機時間を短縮するには、同じリージョン内の仮想ネットワークをピアリングします。 この方法は、それらの仮想ネットワークが異なるサブスクリプションに存在する場合でも機能します。 仮想ネットワークのピアリングを使用すると、2 つの異なる仮想ネットワーク内の VM 間の通信は、Azure ネットワーク バックボーンを使用して相互に直接通信できます。 VM が同じ仮想ネットワークに存在する場合と同様の待機時間が示されます。 Azure 仮想ネットワーク ゲートウェイ経由で接続される IP アドレス範囲のトラフィックは、仮想ネットワークの個々の仮想ネットワーク ゲートウェイ経由でルーティングされます。 

仮想ネットワーク ピアリングの詳細については、「[仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)」をご覧ください。


### <a name="routing-in-azure"></a>Azure でのルーティング

SAP HANA on Azure (L インスタンス) のネットワーク ルーティングに関する 3 つの重要な考慮事項を次に示します。

* SAP HANA on Azure (L インスタンス) には、VM および専用の ExpressRoute 接続経由でのみアクセスできます。オンプレミスから直接アクセスすることはできません。 オンプレミスから HANA L インスタンス ユニットへの直接アクセスは、Microsoft から提供されるため、すぐには実現できません。 この一時的なルーティングの制限は、SAP HANA L インスタンスに使用されている現在の Azure ネットワーク アーキテクチャに起因します。 直接アクセスを必要とする管理クライアントやアプリケーション (オンプレミスで実行されている SAP Solution Manager など) は、SAP HANA データベースに接続できません。

* ディザスター リカバリーのために 2 つの異なる Azure リージョンにデプロイされている HANA L インスタンス ユニットがある場合、同じ一時的なルーティングの制限が適用されます。 つまり、あるリージョン (例: 米国西部) の HANA L インスタンス ユニットの IP アドレスは、別のリージョン (例: 米国東部) で展開されている HANA L インスタンス ユニットにルーティングされません。 この制限は、リージョン間の Azure ネットワーク ピアリング、または HANA L インスタンス ユニットを仮想ネットワークに接続する ExpressRoute 回線の交差接続の使用とは無関係です。 図については、「複数のリージョンでの HANA L インスタンス ユニットの使用」の図を参照してください。 デプロイ済みのアーキテクチャに伴うこの制限があるため、HANA システム レプリケーションをディザスター リカバリー機能としてすぐに使用することはできません。

* SAP HANA on Azure (L インスタンス) ユニットには、お客様が提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 この IP アドレスには、仮想ネットワークを HANA on Azure (L インスタンス) に接続する ExpressRoute と Azure サブスクリプションを使用してアクセスできます。 サーバー IP プールのアドレス範囲から割り当てられる IP アドレスは、ハードウェア ユニットに直接割り当てられます。 このソリューションの最初のデプロイでこの割り当てが行われるため、NAT を使用した割り当ては*行われません*。 

> [!NOTE] 
> 上記の最初の 2 つのリスト項目で説明した一時的なルーティングの制限を克服するには、ルーティングに追加のコンポーネントを使用します。 制限を克服するために使用できるコンポーネントは次のとおりです。

> * データをルーティングするリバース プロキシ。 たとえば、仮想ファイアウォール/トラフィック ルーティング ソリューションとして Azure にデプロイする F5 BIG-IP や NGINX (および Traffic Manager) などです。
> * Linux VM で [IPTables ルール](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)を使用して、オンプレミスの場所と HANA L インスタンス ユニット間、または別のリージョンにある HANA L インスタンス ユニット間のルーティングを有効にします。

> サード パーティのネットワーク アプライアンスまたは IPTables を使用するカスタム ソリューションの実装とサポートは、Microsoft では提供されていないことに注意してください。 使用するコンポーネントのベンダーまたはインテグレーターがサポートを提供する必要があります。 

### <a name="internet-connectivity-of-hana-large-instance"></a>HANA L インスタンスのインターネット接続
HANA L インスタンスは、インターネットに直接接続することは*できません*。 たとえば、この制限によって、OS イメージを OS ベンダーに直接登録することができない可能性があります。 ローカルの SUSE Linux Enterprise Server Subscription Management Tool サーバーや Red Hat Enterprise Linux Subscription Manager を使用することが必要になる場合があります。

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM と HANA L インスタンス間でのデータの暗号化
HANA L インスタンスと VM 間で転送されるデータは暗号化されません。 ただし、HANA DBMS 側と JDBC/ODBC ベースのアプリケーションとの間でのデータ交換のみを目的としている場合は、トラフィックの暗号化を有効にすることができます。 詳細については、[SAP から提供されているこちらのドキュメント](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)をご覧ください。

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>複数のリージョンでの HANA L インスタンス ユニットの使用

ディザスター リカバリー以外の理由で、SAP HANA on Azure (L インスタンス) を複数の Azure リージョンにデプロイする場合があります。 たとえば、リージョン内の異なる仮想ネットワークにデプロイされた各 VM から HANA L インスタンスにアクセスする必要がある場合です。 異なる HANA L インスタンス ユニットに割り当てられた IP アドレスは、ゲートウェイ経由でインスタンスに直接接続されている仮想ネットワークを超えて伝達されることはありません。 そのため、仮想ネットワーク設計が若干変更されます。 仮想ネットワーク ゲートウェイは、エンタープライズ エッジ ルーターごとに 4 つの異なる ExpressRoute 回線を処理できます。 いずれかの L インスタンス スタンプに接続された各仮想ネットワークは、別の Azure リージョン内の L インスタンス スタンプに接続できます。

![異なる Azure リージョン内の Azure L インスタンス スタンプに接続された仮想ネットワーク](./media/hana-overview-architecture/image8-multiple-regions.png)

この図は、2 つのリージョン内の異なる仮想ネットワークが、2 つの Azure リージョン内の SAP HANA on Azure (L インスタンス) への接続に使用する 2 つの異なる ExpressRoute 回線に接続されるしくみを示しています。 新しく導入される接続を直交する赤い線で示しています。 仮想ネットワークからのこれらの接続により、いずれかの仮想ネットワークで実行される VM は、2 つのリージョンにデプロイされた各 HANA L インスタンス ユニットにアクセスできます。 図に示すように、オンプレミスから 2 つの Azure リージョンへの 2 つの ExpressRoute 接続があることが前提となります。 この配置は、ディザスター リカバリーに推奨されます。

> [!IMPORTANT] 
> 複数の ExpressRoute 回線を使用する場合は、トラフィックを適切にルーティングできるように、AS パス プリペンドとローカル プリファレンス BGP の設定を使用してください。


