---
title: Azure VM 上で SAP の使用を開始する | Microsoft Docs
description: Microsoft Azure において仮想マシン (VM) 上で実行される SAP ソリューションについて説明します
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c609c659d99ce6eca0326a5a14cbbb67099e686
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840470"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure を使用して SAP ワークロード シナリオをホストして実行する

Microsoft Azure を使用した場合、スケーラブルかつ SAP に準拠した、エンタープライズで実証済みのプラットフォーム上で、ミッション クリティカルな SAP ワークロードとシナリオを確実に実行できます。 Azure によって、スケーラビリティ、柔軟性、およびコスト削減が実現します。 Microsoft と SAP のパートナーシップの拡大により、Azure での開発およびテストから運用までのシナリオを通して SAP アプリケーションを実行でき、そのすべてがサポートされます。 SAP NetWeaver から SAP S/4HANA、Linux から Windows 上の SAP BI、そして SAP HANA から SQL まで対応しています。

SAP NetWeaver シナリオをさまざまな DBMS を使用して Azure 上でホストすることに加え、その他の SAP ワークロード シナリオ (SAP BI など) も Azure 上でホストできます。 

Azure for SAP HANA の独自性は、Azure を一線を画すものにしているオファーにあります。 SAP HANA を利用したより多くのメモリと CPU リソースを要する SAP シナリオのホストを可能にするために、Azure では顧客専用のベアメタル ハードウェアの使用を提供しています。 このソリューションを使用して、S/4HANA またはその他の SAP HANA ワークロードに対して最大 24 TB (120 TB スケールアウト) のメモリを必要とする SAP HANA のデプロイを実行します。 

また、Azure で SAP ワークロード シナリオをホストすると、ID の統合およびシングル サインオンの要件を作成できます。 この状況は、さまざまな SAP コンポーネントおよび SAP のサービスとしてのソフトウェア (SaaS) またはサービスとしてのプラットフォーム (PaaS) のオファーに接続するために Azure Active Directory (Azure AD) を使用した場合に、発生する可能性があります。 Azure AD と SAP のエンティティを使用したこのような統合とシングル サインオンのシナリオについては、「AAD SAP ID 統合およびシングル サインオン」セクションに一覧と説明があります。

## <a name="changes-to-the-sap-workload-section"></a>SAP ワークロード セクションの変更
この記事の最後には、SAP on Azure ワークロード セクションでのドキュメントへの変更が記載されています。


## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス)

一連のドキュメントでは、SAP HANA on Azure (Large Instances)、略して HANA Large Instances について説明します。 HANA Large Instances の次の分野の情報については、以下をご覧ください。

- [SAP HANA on Azure (Large Instances) の概要](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA on Azure (Large Instances) のアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [SAP HANA on Azure (Large Instances) のインフラストラクチャと接続](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [SAP HANA on Azure (Large Instances) をインストールする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [SAP HANA on Azure (Large Instances) の高可用性とディザスター リカバリー](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [SAP HANA on Azure (Large Instances) のトラブルシューティングと監視](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

次のステップ:

- [SAP HANA on Azure (Large Instances) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)を確認してください



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 仮想マシン上の SAP HANA
ドキュメントのこのセクションでは、SAP HANA のさまざまな側面について説明します。 前提条件として、Azure IaaS の基本的なサービスを提供する Azure のプリンシパル サービスをよく理解している必要があります。 そのため、Azure の計算、ストレージ、およびネットワークに関する知識が必要になります。 これらの主題の多くは、SAP NetWeaver に関連する [Azure 計画ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)で取り扱っています。 

Azure 上の HANA については、以下の記事とその関連記事をご覧ください。

- [クイック スタート: Azure VM への単一インスタンスの SAP HANA の手動インストール](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Azure に SAP S/4HANA または BW/4HANA をデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Azure における SAP HANA インフラストラクチャの構成と運用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 仮想マシンの SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [1 つの Azure リージョン内での SAP HANA の可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Azure リージョンの枠を越えた SAP HANA の可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 仮想マシン上の SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 仮想マシン上の SAP HANA のバックアップ ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [ファイル レベルの SAP HANA Azure バックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [ストレージ スナップショットに基づいた SAP HANA のバックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 仮想マシン上にデプロイされた SAP NetWeaver
このセクションでは、SAP NetWeaver および Business One on Azure の計画とデプロイに関するドキュメントを紹介します。 ドキュメントでは、Azure 上で SAP ワークロードを利用する非 HANA データベースの基礎と使用に注目しています。 また、高可用性に関するドキュメントや記事が、次に示すような Azure における HANA の高可用性の基礎になります。

- [Azure 仮想マシン上の SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Azure に SAP ERP 6.0 向け SAP IDES EHP7 SP3 をデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Microsoft Azure SUSE Linux VM での SAP NetWeaver の実行](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver のための Azure Virtual Machines の計画と実装](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver のための Azure Virtual Machines のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Site Recovery を使用して多層 SAP NetWeaver アプリケーションのデプロイを保護する](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Azure 用の SAP LaMa コネクタ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Azure における SAP ワークロード下の非 HANA データベースについては、以下をご覧ください。

- [SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver のための SQL Server Azure Virtual Machines DBMS のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [SAP ワークロードのための Oracle Azure Virtual Machines DBMS のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [SAP ワークロードのための IBM DB2 Azure Virtual Machines DBMS のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure VM での SAP MaxDB、Live Cache、Content Server のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Azure での SAP HANA データベースについては、「Azure 仮想マシン上の SAP HANA」セクションをご覧ください。

Azure 上での SAP ワークロードの高可用性については、以下をご覧ください。

- [SAP NetWeaver のための Azure Virtual Machines 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

このドキュメントでは、アーキテクチャとシナリオに関するその他のさまざまなドキュメントが参照されています。 シナリオに関する以降のドキュメントでは、高可用性のさまざまな方法のデプロイと構成を説明する詳細な技術ドキュメントへのリンクが提供されています。 SAP NetWeaver ワークロードに対する高可用性を確立して構成する方法を示した各種ドキュメントでは、Linux および Windows のオペレーティング システムについて取り扱っています。


Azure Active Directory (AAD) と SAP サービス間の統合とシングル サインオンについては、以下をご覧ください。

- [チュートリアル:Azure Active Directory と SAP Cloud for Customer の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [チュートリアル:Azure Active Directory と SAP Cloud Platform Identity Authentication の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [チュートリアル:Azure Active Directory と SAP Cloud Platform の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [チュートリアル:Azure Active Directory と SAP NetWeaver の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [チュートリアル:Azure Active Directory と SAP Business ByDesign の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [チュートリアル:Azure Active Directory と SAP HANA の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [お使いの S/4HANA 環境:Azure AD を使用した Fiori Launchpad SAML シングル サインオン](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

SAP コンポーネントへの Azure サービスの統合については、以下をご覧ください。

- [Power BI Desktop での SAP HANA の使用](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery と SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Power BI Desktop での SAP BW コネクタの使用](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory による SAP HANA と Business Warehouse データの統合](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>変更履歴

- 2020 年 1 月 28 日:SAP HANA クラスター リソースのタイムアウトを Red Hat のタイムアウトの推奨事項に合わせるために、[RHEL 上の Azure VM での SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)に関する記事が変更されました
- 2020 年 1 月 17 日:「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)」が変更され、既存の VM を近接通信配置グループに移動するセクションが変更されました
- 2020 年 1 月 17 日:「[Azure Availability Zones での SAP ワークロードの構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)」が変更され、Availability Zones 間の待機時間の測定を自動化する手順が示されるようになりました
- 2020 年 1 月 16 日:「[SAP HANA on Azure (L インスタンス) のインストールと構成の方法](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)」が変更され、OS リリースが HANA IaaS ハードウェア ディレクトリに適合するようになりました
- 2020 年 1 月 16 日:[SLES マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)に関する記事が変更され、エンキュー サーバー 2 のアーキテクチャ (ENSA2) を使用した SAP システム用の手順が追加されました
- 01/10/2020:[SLES 上の Azure NetApp Files を使用した Azure VM のスタンバイ ノードによる SAP HANA のスケールアウト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)に関する記事と [RHEL 上の Azure NetApp Files を使用した Azure VM のスタンバイ ノードによる SAP HANA のスケールアウト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)に関する記事が変更され、`nfs4_disable_idmapping` の変更を永続的にする方法の手順が追加されました。
- 01/10/2020:[SAP アプリケーション用の Azure NetApp Files を使用した SLES 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)に関する記事と [SAP アプリケーション用の Azure NetApp Files を使用した RHEL 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)に関する記事が変更され、Azure NetApp Files NFSv4 ボリュームをマウントする手順が追加されました。
- 12/23/2019:「[SLES マルチ SID 上の Azure VM の SAP NetWeaver に対する高可用性に関するガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)」のリリース
- 12/18/2019:「[RHEL 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)」のリリース
- 2019/11/21: [SUSE Linux Enterprise Server 上の Azure NetApp Files を使用した Azure VM 上のスタンバイ ノードでの SAP HANA スケールアウト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)が変更され、NFS ID マッピングの構成を簡素化されました。また、ルーティングを簡素化するために推奨プライマリ ネットワーク インターフェイスが変更されました。
- 2019/11/15: [SAP アプリケーション向け Azure NetAppファイルを使用した SUSE Linux Enterprise Server 上の SAP NetWeaver の高可用性](high-availability-guide-suse-netapp-files.md)、および [SAP アプリケーション向け Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver の高可用性](high-availability-guide-rhel-netapp-files.md)の軽微な変更。これは、容量プールのサイズ制限を明確にし、NFSv3 バージョンのみがサポートされているというステートメントを削除するためのものです。
- 11/12/2019:[Azure NetApp Files (SMB) を使用した Windows での SAP NetWeaver の高可用性](high-availability-guide-windows-netapp-files-smb.md)のリリース
- 11/08/2019:「[SUSE Linux Enterprise Server 上の Azure VM での SAP HANA の高可用性](sap-hana-high-availability.md)」、「[Azure 仮想マシン (VM) 上で SAP HANA システム レプリケーションを設定する](sap-hana-high-availability-rhel.md)」、「[SUSE Linux Enterprise Server for SAP Applications 上の SAP NetWeaver の Azure Virtual Machines 高可用性](high-availability-guide-suse.md)」、「[Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の SAP NetWeaver の Azure Virtual Machines 高可用性](high-availability-guide-suse-netapp-files.md)」、「[Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性](high-availability-guide-rhel.md)」、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](high-availability-guide-rhel-netapp-files.md)」、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](high-availability-guide-suse-nfs.md)」、「[Red Hat Enterprise Linux for SAP NetWeaver における Azure VM での GlusterFS](high-availability-guide-rhel-glusterfs.md)」での Azure Standard Load Balancer の使用を勧めるための変更  
- 11/08/2019:「[SAP ワークロードの計画とデプロイ チェックリスト](sap-deployment-checklist.md)」での暗号化の推奨事項を明確にするための変更  
- 2019/11/04: 「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](high-availability-guide-suse-pacemaker.md)」で、ユニキャスト構成を使用してクラスターを直接作成するように変更  
- 2019/10/29: [SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](high-availability-guide-standard-load-balancer-outbound-connections.md)に関するページのリリース
- 2019/10/25: 「[SAP HANA Azure 仮想マシンのストレージ構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)」および「[SUSE Linux Enterprise Server 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)」で、/hana/shared ボリューム用の NFS プロトコルを明確にするための変更
- 2019/10/22: 「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)」、「[SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)」、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」、「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)」、「[Pacemaker による SUSE Linux Enterprise Server 上の Azure VM での IBM Db2 LUW の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)」、および「[SUSE Linux Enterprise Server 上の Azure VM での SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)」での、Azure Load-Balancer Detection Hardening に関する変更
- 「[SAP HANA Azure 仮想マシンのストレージ構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)」で、ANF およびヘッダーに関するセクションを変更
- 2019/10/21: 「[SLES 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)」のリリース
- 2019/10/16: 「[バックアップと復元](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)」の壊れたリンクの修正
- 2019/10/16: 「[Pacemaker による SUSE Linux Enterprise Server 上の Azure VM での IBM Db2 LUW の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)」で、最低限推奨される OS を SLES 12 SP3 から SLES 12 SP4 に変更
- 2019/10/11: 「[SAP HANA Azure 仮想マシンのストレージ構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)」で、Ultra Disk Storage の構成と ANF の概要を変更
- 2019/10/01:「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)」のグラフィックをわかりやすく変更
- 2019/10/01:「[Azure における SAP HANA インフラストラクチャの構成と運用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)」の、/hana/shared に対する高可用性 NFS 共有に関する説明を修正。 
- 2019/09/28:「[Azure の Red Hat Enterprise Linux に Pacemaker をセットアップする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)」で、RHEL クラスターではフェンス メカニズムがサポートされていないことがはっきりわかるように、SBD の説明を変更  
- 2019/09/17:SAP の VM 拡張機能に関する用語を統合するための NetWeaver 計画およびデプロイ ガイドの変更  
- 2019/08/22:カスタム ロールの作成用の URL を更新する「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)」の変更  
- 2019/08/16:Azure Fence Agent の新しいバージョンに更新する場合に、カスタム ロールのアクションを更新するようにお客様に通知するための「[Azure の Red Hat Enterprise Linux に Pacemaker をセットアップする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)」の変更  
- 2019/08/15:Ultra ディスクの一般提供 (旧称 Ultra SSD) を反映するための「[SAP HANA Azure 仮想マシンのストレージ構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)」の変更
- 2019/08/01:「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)」を変更して、SLES 15 固有の変更を統合しました 
- 2019/07/23:[Azure のファイル共有を使用した Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスのクラスター化](sap-high-availability-guide-wsfc-file-share.md)での、Azure Site Recovery による記憶域スペース ダイレクトのサポートを反映するための変更
- 2019/07/14:[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)のリリース
- 2019/07/11:HANA Large Instances のリビジョン 4 に対応して、HANA Large Instances に関するさまざまなドキュメントを変更しました
- 2019/07/09:[Red Hat Enterprise Server の IBM Db2 HADR](high-availability-guide-rhel-ibm-db2-luw.md) に関する新しいガイドのリリース
- 2019/06/13:[SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines](high-availability-guide-rhel-netapp-files.md) のリリース


