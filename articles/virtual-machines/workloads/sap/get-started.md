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
ms.date: 05/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b95112146c0003f3fc5ea563e5561dc26ea064e8
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800725"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure を使用して SAP ワークロード シナリオをホストして実行する

Microsoft Azure を使用した場合、スケーラブルかつ SAP に準拠した、エンタープライズで実証済みのプラットフォーム上で、ミッション クリティカルな SAP ワークロードとシナリオを確実に実行できます。 Azure によって、スケーラビリティ、柔軟性、およびコスト削減が実現します。 Microsoft と SAP のパートナーシップの拡大により、Azure での開発およびテストから運用までのシナリオを通して SAP アプリケーションを実行でき、そのすべてがサポートされます。 SAP NetWeaver から SAP S/4HANA、Linux から Windows 上の SAP BI、そして SAP HANA から SQL まで対応しています。

SAP NetWeaver シナリオをさまざまな DBMS を使用して Azure 上でホストすることに加え、その他の SAP ワークロード シナリオ (SAP BI など) も Azure 上でホストできます。 

Azure for SAP HANA の独自性は、Azure を一線を画すものにしているオファーにあります。 SAP HANA を利用したより多くのメモリと CPU リソースを要する SAP シナリオのホストを可能にするために、Azure では顧客専用のベアメタル ハードウェアの使用を提供しています。 このソリューションを使用して、S/4HANA またはその他の SAP HANA ワークロードに対して最大 24 TB (120 TB スケールアウト) のメモリを必要とする SAP HANA のデプロイを実行します。 

また、Azure で SAP ワークロード シナリオをホストすると、ID の統合およびシングル サインオンの要件を作成できます。 この状況は、さまざまな SAP コンポーネントおよび SAP のサービスとしてのソフトウェア (SaaS) またはサービスとしてのプラットフォーム (PaaS) のオファーに接続するために Azure Active Directory (Azure AD) を使用した場合に、発生する可能性があります。 Azure AD と SAP のエンティティを使用したこのような統合とシングル サインオンのシナリオについては、「AAD SAP ID 統合およびシングル サインオン」セクションに一覧と説明があります。

## <a name="changes-to-the-sap-workload-section"></a>SAP ワークロード セクションの変更
この記事の最後には、SAP on Azure ワークロード セクションでのドキュメントへの変更が記載されています。 変更ログのエントリは、約 180 日間保持されます。

## <a name="you-want-to-know"></a>お知りになりたい情報について
ご不明な点がございましたら、スタート ページのこのセクションに示されている特定のドキュメントまたはフローを参照してください。 以下についてお知りになりたい場合は、次のようにしてください。

- SAP ソフトウェアの各リリースとオペレーティング システムの各バージョンでサポートされる、Azure VM および HANA Large Instances ユニット。 回答と情報を検索するプロセスについては、「[Azure デプロイでサポートされている SAP ソフトウェア](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)」というドキュメントをお読みください
- Azure VM および HANA Large Instances でサポートされる SAP デプロイ シナリオ。 サポートされるシナリオについては、次のドキュメントを参照してください。
    - [Azure 仮想マシンの SAP ワークロードでサポートされるシナリオ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [HANA L インスタンスのサポートされるシナリオ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- さまざまな Azure リージョンで使用できる Azure サービス、Azure VM の種類、Azure Storage サービスについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」のサイトを参照してください。 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス)

一連のドキュメントでは、SAP HANA on Azure (Large Instances)、略して HANA Large Instances について説明します。 HANA Large Instances については、まず、[SAP HANA on Azure (Large Instances) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)に関するドキュメントを参照してから、HANA Large Instance セクションの関連ドキュメントを参照してください



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 仮想マシン上の SAP HANA
ドキュメントのこのセクションでは、SAP HANA のさまざまな側面について説明します。 前提条件として、Azure IaaS の基本的なサービスを提供する Azure のプリンシパル サービスをよく理解している必要があります。 そのため、Azure の計算、ストレージ、およびネットワークに関する知識が必要になります。 これらの主題の多くは、SAP NetWeaver に関連する [Azure 計画ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)で取り扱っています。 

Azure 上の HANA については、以下の記事とその関連記事をご覧ください。

- [Azure における SAP HANA インフラストラクチャの構成と運用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 仮想マシンの SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Azure 仮想マシン上の SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 仮想マシン上の SAP HANA のバックアップ ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 仮想マシン上にデプロイされた SAP NetWeaver
このセクションでは、SAP NetWeaver および Business One on Azure の計画とデプロイに関するドキュメントを紹介します。 ドキュメントでは、Azure 上で SAP ワークロードを利用する非 HANA データベースの基礎と使用に注目しています。 また、高可用性に関するドキュメントや記事が、次に示すような Azure における HANA の高可用性の基礎になります。

- [Azure 計画ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。 
- [Azure 仮想マシン上の SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
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

- 2020 年 5 月 21 日:[SAP の HA シナリオにおける Azure 標準 ILB を使用した VM のパブリック エンドポイント接続](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)へのリンクを追加するために、[Azure での SLES に対する Pacemaker の設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)および [Azure での RHEL に対する Pacemaker の設定](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)に関するページを変更  
- 2020 年 5 月 19 日:HANA 関連ボリュームに LVM を使用する場合はルート ボリューム グループを使用できないという重要なメッセージを「[SAP HANA Azure 仮想マシンのストレージ構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)」に追加
- 2020 年 5 月 19 日:HANA L インスタンス タイプ II でサポートされる新しい OS を「HANA L インスタンスと互換性があるオペレーティング システム」に追加 (https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance)
- 2020 年 5 月 12 日:リンクを更新するため、およびサード パーティ製ファイアウォールの構成に関する情報を追加するために、[SAP の HA シナリオにおける Azure 標準 ILB を使用した VM のパブリック エンドポイント接続](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)に関するページを変更
- 2020 年 5 月 11 日:[SLES 上の Azure VM での SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)に関するページを変更して、netcat リソースのリソースの持続性を 0 に設定。これにより、フェールオーバーが効率化されます 
- 2020 年 5 月 5 日:Mv1 VM ファミリで Gen2 デプロイを使用できることを示すために、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)」を変更
- 2020 年 4 月 24 日:ANF ボリュームが自動的に割り当てられることをより明確に示す、「[SUSE Linux Enterprise Server 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)」、「[Red Hat Enterprise Linux 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)」、「[SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)」および「[SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)」への変更
- 2020 年 4 月 22 日:クラスターのメンテナンス モードの切り替えと競合するため、属性 `is-managed` を手順から削除して「[SUSE Linux Enterprise Server 上の Azure VM での SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)」を変更
- 2020 年 4 月 21 日:「[Azure デプロイでサポートされている SAP ソフトウェア](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)」および「[Microsoft Azure で実行されているSAP の認定と構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)」の記事に、SAP (Hybris) Commerce Platform 1811 以降用のサポートされている DBMS として、SQL Azure DB を追加
- 2020 年 4 月 16 日:「[Azure デプロイでサポートされている SAP ソフトウェア](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)」および「[Microsoft Azure で実行されているSAP の認定と構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)」の記事に、SAP (Hybris) Commerce Platform 用のサポートされている DBMS として、SAP HANA を追加
- 2020 年 4 月 13 日:「[SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)」での正確な SAP ASE リリース番号への修正
- 2020 年 4 月 7 日:「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)」での、cloud-netconfig-azure の手順を明確にするための変更
- 2020 年 4 月 6 日「[SUSE Linux Enterprise Server 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)」および「[Red Hat Enterprise Linux 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)」での、NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) への参照を削除するための変更 ([TR-4746](https://www.netapp.com/us/media/tr-4746.pdf) に置き換えられました)
- 2020 年 3 月 31 日:「[SUSE Linux Enterprise Server 上の Azure VM での SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)」および「[Red Hat Enterprise Linux 上の Azure VM での SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)」での、ストライプ ボリュームを作成するときにストライプ サイズを指定する方法の手順を追加するための変更
- 2020 年 3 月 27 日:「[SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)」での、ファイル システム マウント オプションを NetApp TR-4746 (同期マウント オプションを削除する) に合わせるための変更
- 2020 年 3 月 26 日:「[SUSE Linux Enterprise Server for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)」での、NetApp TR-4746 に参照を追加するための変更
- 2020 年 3 月 26 日:「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)」、「[SAP アプリケーション用の Azure NetApp Files を使用した SLES 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)」、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」、「[SUSE Linux Enterprise Server for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)」、「[Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)」、および「[SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)」での、図を更新し、Azure Load Balancer バックエンド プールの作成手順を明確にするための変更
- 2020 年 3 月 19 日:ドキュメント「[クイックスタート: Azure Virtual Machines への単一インスタンスの SAP HANA の手動インストール](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)」から「[Azure Virtual Machines への SAP HANA のインストール](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)」へのメジャー リビジョン
- 2020 年 3 月 17 日:「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)」での、必要なくなった SBD 構成設定を削除するための変更
- 2020 年 3 月 16 日:「[Azure デプロイでサポートされている SAP ソフトウェア](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)」での、SAP HANA IaaS 認定プラットフォームの列の認定シナリオの明確化
- 2020 年 3 月 11 日:「[Azure 仮想マシンの SAP ワークロードでサポートされるシナリオ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)」での、DBMS インスタンスあたり複数のデータベースのサポートを明確にするための変更
- 2020 年 3 月 11 日:「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)」での、第 1 世代および第 2 世代 VM を説明するための変更
- 2020 年 3 月 10 日:ANF の実際の既存スループット制限を明確にするための、「[SAP HANA Azure 仮想マシンのストレージ構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)」の変更
- 2020 年 3 月 9 日:「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)」、「[SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)」、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」、「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)」、「[Pacemaker による SUSE Linux Enterprise Server 上の Azure VM での IBM Db2 LUW の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)」、「[SUSE Linux Enterprise Server 上の Azure VM での SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)」、および「[SUSE Linux Enterprise Server for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)」での、リソース エージェント azure-lb を使用してクラスター リソースを更新するための変更 
- 2020 年 3 月 5 日:「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)」での、Azure リージョンと Azure Virtual Machines のための構造の変更とコンテンツの変更
- 2020 年 3 月 3 日:[SAP アプリケーション用の ANF を使用した SLES 上の Azure VM 上の SAP NW の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)に関する記事で、より効率的な ANF ボリューム レイアウトに変更します。
- 2020 年 3 月 1 日:「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)」を修正し、Azure Backup サービスを含めました。 「[ファイル レベルの SAP HANA Azure バックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)」コンテンツを削減して凝縮し、ディスク スナップショットを介したバックアップに関する 3 番目のドキュメントを削除しました。 コンテンツは、Azure Virtual Machines 上の SAP HANA のバックアップ ガイドで扱われます。 
- 2020 年 2 月 27 日:「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)」、「[SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)」、「[SUSE Linux Enterprise Server for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)」を変更し、"失敗時" のクラスター パラメーターを調整しました
- 2020 年 2 月 26 日:「[SAP HANA Azure 仮想マシンのストレージ構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)」を変更し、Azure での HANA のファイル システムの選択を明確にします
- 2020 年 2 月 26 日:「[SAP NetWeaver のための高可用性のアーキテクチャとシナリオ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)」を変更し、RHEL マルチ SID 上の Azure VM 上の SAP NetWeaver での高可用性のガイドへのリンクを含めます
- 2020 年 2 月 26 日:「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)」、「[SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)」、「[Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)」、および「[SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)」を変更し、マルチ SID ASCS/ERS クラスターがサポートされないという記述を削除します
- 2020 年 2 月 26 日:SUSE マルチ SID クラスター ガイドへのリンクを追加するための、[Red Hat Enterprise Linux for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)をリリースします
- 2020 年 2 月 25 日:新しい HA 記事へのリンクを追加するための、[SAP のための高可用性のアーキテクチャとシナリオ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)に関する記事での変更
- 2020 年 2 月 25 日:Standard Azure Load Balancer を使用するパブリック エンドポイントへのアクセスについて説明されているドキュメントを示すための、「[Pacemaker による SUSE Linux Enterprise Server 上の Azure VM での IBM Db2 LUW の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)」での変更
- 2020 年 2 月 21 日:「[SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)」記事の全面改訂
- 2020 年 2 月 21 日:/hana/data のストライプ サイズと、I/O スケジューラの設定の追加に関する新しい推奨事項を示すための、「[SAP HANA Azure 仮想マシンのストレージ構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)」での変更
- 2020 年 2 月 21 日:S224 と S224m の新たに認定された SKU を示すための、HANA Large Instance に関するドキュメントでの変更
- 2020 年 2 月 21 日:エンキュー サーバー レプリケーション 2 アーキテクチャ (ENSA2) に合わせてクラスター制約を調整するための、[RHEL 上の SAP NetWeaver のための Azure VM 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)および [Azure NetApp Files を使用した RHEL 上の SAP NetWeaver のための Azure VM 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)に関する記事での変更
- 2020 年 2 月 20 日:SUSE マルチ SID クラスター ガイドへのリンクを追加するための、[SLES マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)に関する記事での変更
- 2020 年 2 月 13 日:新しいドキュメントへのリンクを実装するための、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)」での変更
- 2020 年 2 月 13 日:「[Azure 仮想マシンの SAP ワークロードでサポートされるシナリオ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)」という新しいドキュメントの追加
- 2020 年 2 月 13 日:「[Azure デプロイでサポートされている SAP ソフトウェア](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)」という新しいドキュメントの追加
- 2020 年 2 月 13 日:Standard Azure Load Balancer を使用するパブリック エンドポイントへのアクセスについて説明されているドキュメントを示すための、「[Red Hat Enterprise Linux Server 上の Azure VM での IBM Db2 LUW の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)」での変更
- 2020 年 2 月 13 日:「[Microsoft Azure で実行されている SAP の認定と構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)」への新しい VM の種類の追加
- 2020 年 2 月 13 日:「[Azure での SAP ワークロード: 計画とデプロイに関するチェックリスト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)」への新しい SAP サポート ノートの追加
- 2020 年 2 月 13 日:クラスター リソースのタイムアウトを Red Hat のタイムアウト推奨値に合わせるために、[Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)および [Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)を変更しました
- 2020 年 2 月 11 日:[SAP HANA on Azure Large Instance の Azure Virtual Machines への移行](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)をリリースしました
- 2020 年 2 月 7 日:サンプルの NSG スクリーンショットを更新するために、[SAP の HA シナリオにおける Azure ILB を使用した VM のパブリック エンドポイント接続](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)が変更されました
- 2020 年 2 月 3 日:「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)」と「[SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)」に変更があり、SLES でクラスター ノードのホスト名にダッシュを使用することに関する警告が削除されました。
- 2020 年 1 月 28 日:SAP HANA クラスター リソースのタイムアウトを Red Hat のタイムアウトの推奨事項に合わせるために、[RHEL 上の Azure VM での SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)に関する記事が変更されました
- 2020 年 1 月 17 日:「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)」が変更され、既存の VM を近接通信配置グループに移動するセクションが変更されました
- 2020 年 1 月 17 日:「[Azure Availability Zones での SAP ワークロードの構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)」が変更され、Availability Zones 間の待機時間の測定を自動化する手順が示されるようになりました
- 2020 年 1 月 16 日:「[SAP HANA on Azure (L インスタンス) のインストールと構成の方法](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)」が変更され、OS リリースが HANA IaaS ハードウェア ディレクトリに適合するようになりました
- 2020 年 1 月 16 日:[SLES マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)に関する記事が変更され、エンキュー サーバー 2 のアーキテクチャ (ENSA2) を使用した SAP システム用の手順が追加されました
- 01/10/2020:[SLES 上の Azure NetApp Files を使用した Azure VM のスタンバイ ノードによる SAP HANA のスケールアウト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)に関する記事と [RHEL 上の Azure NetApp Files を使用した Azure VM のスタンバイ ノードによる SAP HANA のスケールアウト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)に関する記事が変更され、`nfs4_disable_idmapping` の変更を永続的にする方法の手順が追加されました。
- 01/10/2020:[SAP アプリケーション用の Azure NetApp Files を使用した SLES 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)に関する記事と [SAP アプリケーション用の Azure NetApp Files を使用した RHEL 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)に関する記事が変更され、Azure NetApp Files NFSv4 ボリュームをマウントする手順が追加されました。
- 2019 年 12 月 23 日:「[SLES マルチ SID 上の Azure VM の SAP NetWeaver に対する高可用性に関するガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)」のリリース
- 2019 年 12 月 18 日:「[RHEL 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)」のリリース
- 2019 年 11 月 21 日:[SUSE Linux Enterprise Server 上の Azure NetApp Files を使用した Azure VM 上のスタンバイ ノードでの SAP HANA スケールアウト](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)が変更され、NFS ID マッピングの構成を簡素化されました。また、ルーティングを簡素化するために推奨プライマリ ネットワーク インターフェイスが変更されました。
- 2019 年 11 月 15 日:[SAP アプリケーション向け Azure NetAppファイルを使用した SUSE Linux Enterprise Server 上の SAP NetWeaver の高可用性](high-availability-guide-suse-netapp-files.md)、および [SAP アプリケーション向け Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver の高可用性](high-availability-guide-rhel-netapp-files.md)の軽微な変更。これは、容量プールのサイズ制限を明確にし、NFSv3 バージョンのみがサポートされているというステートメントを削除するためのものです。
- 2019 年 11 月 12 日:[Azure NetApp Files (SMB) を使用した Windows での SAP NetWeaver の高可用性](high-availability-guide-windows-netapp-files-smb.md)のリリース
- 2019 年 11 月 8 日:「[SUSE Linux Enterprise Server 上の Azure VM での SAP HANA の高可用性](sap-hana-high-availability.md)」、「[Azure 仮想マシン (VM) 上で SAP HANA システム レプリケーションを設定する](sap-hana-high-availability-rhel.md)」、「[SUSE Linux Enterprise Server for SAP Applications 上の SAP NetWeaver の Azure Virtual Machines 高可用性](high-availability-guide-suse.md)」、「[Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の SAP NetWeaver の Azure Virtual Machines 高可用性](high-availability-guide-suse-netapp-files.md)」、「[Red Hat Enterprise Linux での SAP NetWeaver のための Azure Virtual Machines 高可用性](high-availability-guide-rhel.md)」、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](high-availability-guide-rhel-netapp-files.md)」、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](high-availability-guide-suse-nfs.md)」、「[Red Hat Enterprise Linux for SAP NetWeaver における Azure VM での GlusterFS](high-availability-guide-rhel-glusterfs.md)」での Azure Standard Load Balancer の使用を勧めるための変更  
- 2019 年 11 月 8 日:「[SAP ワークロードの計画とデプロイ チェックリスト](sap-deployment-checklist.md)」での暗号化の推奨事項を明確にするための変更  
- 2019 年 11 月 4 日:「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](high-availability-guide-suse-pacemaker.md)」で、ユニキャスト構成を使用してクラスターを直接作成するように変更  


