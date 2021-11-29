---
title: Azure VM 上で SAP の使用を開始する | Microsoft Docs
description: Microsoft Azure において仮想マシン (VM) 上で実行される SAP ソリューションについて説明します
services: virtual-machines-sap
ms.service: virtual-machines-sap
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73e1f58073c92ba8a0631d4ee7136de3bac5e114
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549548"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure を使用して SAP ワークロード シナリオをホストして実行する

Microsoft Azure を使用した場合、スケーラブルかつ SAP に準拠した、エンタープライズで実証済みのプラットフォーム上で、ミッション クリティカルな SAP ワークロードとシナリオを確実に実行できます。 Azure によって、スケーラビリティ、柔軟性、およびコスト削減が実現します。 Microsoft と SAP のパートナーシップの拡大により、Azure での開発およびテストから運用までのシナリオを通して SAP アプリケーションを実行でき、そのすべてがサポートされます。 SAP NetWeaver から SAP S/4HANA、Linux から Windows 上の SAP BI、そして SAP HANA から SQL まで対応しています。

SAP NetWeaver シナリオをさまざまな DBMS を使用して Azure 上でホストすることに加え、その他の SAP ワークロード シナリオ (SAP BI など) も Azure 上でホストできます。 

Azure for SAP HANA の独自性は、Azure を一線を画すものにしているオファーにあります。 SAP HANA を利用したより多くのメモリと CPU リソースを要する SAP シナリオのホストを可能にするために、Azure では顧客専用のベアメタル ハードウェアの使用を提供しています。 このソリューションを使用して、S/4HANA またはその他の SAP HANA ワークロードに対して最大 24 TB (120 TB スケールアウト) のメモリを必要とする SAP HANA のデプロイを実行します。 

また、Azure で SAP ワークロード シナリオをホストすると、ID の統合およびシングル サインオンの要件を作成できます。 この状況は、さまざまな SAP コンポーネントおよび SAP のサービスとしてのソフトウェア (SaaS) またはサービスとしてのプラットフォーム (PaaS) のオファーに接続するために Azure Active Directory (Azure AD) を使用した場合に、発生する可能性があります。 Azure AD と SAP のエンティティを使用したこのような統合とシングル サインオンのシナリオについては、Azure AD SAP ID 統合およびシングル サインオンに関するセクションに一覧と説明があります。

## <a name="changes-to-the-sap-workload-section"></a>SAP ワークロード セクションの変更
この記事の最後には、SAP on Azure ワークロード セクションでのドキュメントへの変更が記載されています。 変更ログのエントリは、約 180 日間保持されます。

## <a name="you-want-to-know"></a>お知りになりたい情報について
ご不明な点がございましたら、スタート ページのこのセクションに示されている特定のドキュメントまたはフローを参照してください。 以下についてお知りになりたい場合は、次のようにしてください。

- SAP ソフトウェアの各リリースとオペレーティング システムの各バージョンでサポートされる、Azure VM および HANA Large Instances ユニット。 回答と情報を検索するプロセスについては、「[Azure デプロイでサポートされている SAP ソフトウェア](./sap-supported-product-on-azure.md)」というドキュメントをお読みください
- Azure VM および HANA Large Instances でサポートされる SAP デプロイ シナリオ。 サポートされるシナリオについては、次のドキュメントを参照してください。
    - [Azure 仮想マシンの SAP ワークロードでサポートされるシナリオ](./sap-planning-supported-configurations.md)
    - [HANA L インスタンスのサポートされるシナリオ](./hana-supported-scenario.md)
- さまざまな Azure リージョンで使用できる Azure サービス、Azure VM の種類、Azure Storage サービスについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」のサイトを参照してください。 
- Windows と Pacemaker でサポートされるもの以外に、サードパーティの HA フレームは動作するか。 [SAP サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) の下部を確認してください
- 自分のシナリオに最適な Azure ストレージ 「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」を参照してください。
- Oracle Enterprise Linux の Red Hat カーネルは SAP でサポートされているか。 SAP の [SAP サポート ノート #1565179](https://launchpad.support.sap.com/#/notes/1565179) をお読みください。
- Azure [Da(s)v4](../../dav4-dasv4-series.md)/[Ea(s)](../../eav4-easv4-series.md) VM ファミリが SAP HANA で認定されていないのはなぜか。 Azure Das および Eas VM ファミリは、AMD プロセッサ駆動型ハードウェアに基づいています。 SAP HANA では、仮想化されたシナリオであっても AMD プロセッサはサポートされません
- 最新の Linux カーネルを実行していても、SAP HANA で "RDTSCP 命令の cpu フラグまたは constant_tsc もしくは nonstop_tsc の cpu フラグが設定されていないか、current_clocksource と available_clocksource が正しく構成されていません" というメッセージが表示されるのはなぜか。 回答については、[SAP サポート ノート #2791572](https://launchpad.support.sap.com/#/notes/2791572) を確認してください
- SAP Fiori を Azure にデプロイするためのアーキテクチャはどこで入手できるか。 ブログ「[SAP on Azure: Application Gateway Web Application Firewall (WAF) v2 Setup for Internet facing SAP Fiori Apps](https://blogs.sap.com/2020/12/03/sap-on-azure-application-gateway-web-application-firewall-waf-v2-setup-for-internet-facing-sap-fiori-apps/)」(SAP on Azure: インターネットに接続する SAP Fiori アプリ用の Application Gateway Web アプリケーション ファイアウォール (WAF)) を確認してください 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス)

一連のドキュメントでは、SAP HANA on Azure (Large Instances)、略して HANA Large Instances について説明します。 HANA Large Instances については、まず、[SAP HANA on Azure (Large Instances) の概要とアーキテクチャ](./hana-overview-architecture.md)に関するドキュメントを参照してから、HANA Large Instance セクションの関連ドキュメントを参照してください



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 仮想マシン上の SAP HANA
ドキュメントのこのセクションでは、SAP HANA のさまざまな側面について説明します。 前提条件として、Azure IaaS の基本的なサービスを提供する Azure のプリンシパル サービスをよく理解している必要があります。 そのため、Azure の計算、ストレージ、およびネットワークに関する知識が必要になります。 これらの主題の多くは、SAP NetWeaver に関連する [Azure 計画ガイド](./planning-guide.md)で取り扱っています。 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 仮想マシン上にデプロイされた SAP NetWeaver
このセクションでは、SAP NetWeaver、SAP LaMa、および Business One on Azure の計画とデプロイに関するドキュメントを紹介します。 ドキュメントでは、Azure 上で SAP ワークロードを利用する非 HANA データベースの基礎と使用に注目しています。 また、高可用性に関するドキュメントや記事は、Azure における SAP HANA の高可用性の基礎にもなります。

## <a name="sap-netweaver-and-s4hana-high-availability"></a>SAP NetWeaver と S/4HANA の高可用性
SAP アプリケーション レイヤーと DBMS の高可用性に関する詳細は、「[SAP NetWeaver のための Azure Virtual Machines 高可用性](./sap-high-availability-guide-start.md)」ドキュメントから始まる文書で説明されています。



## <a name="integrate-azure-ad-with-sap-services"></a>Azure AD と SAP サービスの統合
このセクションでは、ほとんどの SAP SaaS および PaaS サービス、NetWeaver、Fiori で SSO を構成する方法に関する情報を確認できます 



## <a name="documentation-on-integration-of-azure-services-into-sap-components"></a>SAP コンポーネントへの Azure サービスの統合に関するドキュメント
このセクションでは、SAP データ ソースへの Microsoft Power BI の統合と、SAP BW への Azure Data Factory の統合に関するドキュメントを紹介します。

## <a name="change-log"></a>変更履歴
- 2021 年 11 月 16 日: Azure Files の NFS を使用する SAP ASCS/ERS[Azure Files の NFS を使用する SLES 上の SAP NW 用 HA](./high-availability-guide-suse-nfs-azure-files.md)、[Azure Files の NFS を使用する RHEL 上の SAP NW 用 HA](./high-availability-guide-rhel-nfs-azure-files.md) に向けた高可用性ガイドをリリースしました。 
- 2021 年 11 月 2 日:「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」と「[SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ](./dbms_guide_sapase.md)」を変更し、Azure NetApp Files で NFS に対する SAP ASE サポートを宣言しました。
- 2021 年 11 月 2 日:「[Azure Availability Zones での SAP ワークロードの構成](./sap-ha-availability-zones.md)」を変更し、シンガポール南東部をアクティブ/アクティブ構成のリージョンに移しました
- 2021 年 11 月 2 日:「[Red Hat Enterprise Linux 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability-rhel.md)」を変更し、HANA スケールアップアクティブ/アクティブ (読み取り有効) 構成の手順を更新しました。
- 2021 年 10 月 26 日: [RHEL 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウト HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md) に関するページに変更を加え、HANA スケールアウト アクティブ/アクティブ (読み取りが有効) 構成のリソース名を更新
- 2021 年 10 月 19 日: [RHEL 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウト HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md) に関するページに変更を加え、HANA スケールアウト アクティブ/アクティブ (読み取りが有効) 構成の手順を追加
- 2021 年 10 月 11 日: 「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](./sap-high-availability-guide-wsfc-shared-disk.md)」、「[SAP ASCS/SCS 用の Windows フェールオーバー クラスターと共有ディスクを使用して SAP HA 向けに Azure インフラストラクチャを準備する](./sap-high-availability-infrastructure-wsfc-shared-disk.md)」、「[Windows Server フェールオーバー クラスタリングと Azure 共有ディスクを使用した SAP ASCS/SCS インスタンスのマルチ SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md)」に変更を加え、サポートされる Azure 共有ディスクでのゾーン冗長ストレージ (ZRS) の説明を追加。
- 2021 年 10 月 08 日: [SLES 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウト HSR](./sap-hana-high-availability-scale-out-hsr-suse.md)、[RHEL 上の ANF を使用した SAP HANA スケールアップの HA](./sap-hana-high-availability-netapp-files-red-hat.md)、および [RHEL 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウト HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md) に関する記事を変更し、sudoers ファイルの既定値を追加し、HANA スケールアウトについて更新 (HANA srHook 用)
- 2021 年 10 月 1 日: SAP HANA バックアップの新しい Azure Backup アーキテクチャに関するドキュメントへのリンクを目次に追加。 「[SAP ワークロードのための Azure Virtual Machines Oracle DBMS のデプロイ](./dbms_guide_oracle.md)」に、Oracle DBMS 用の Azure Backup サービスへのリンクを追加
- 2021 年 9 月 24 日: [SLES 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウト HSR](./sap-hana-high-availability-scale-out-hsr-suse.md)、[RHEL で ANF を使用した SAP HANA スケールアップの HA](./sap-hana-high-availability-netapp-files-red-hat.md)、および [RHEL 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウト HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md) に関する記事を変更し、sudoers ファイルの作成手順を調整 (HANA srHook 用)
- 2021 年 9 月 15 日: 「[SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ](./dbms_guide_sapase.md)」に SAP ASE の新しい HADR 構成を導入
- 2021 年 9 月 08 日: 「[SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)」に手動の QoS 容量プールを追加
- 2021 年 8 月 26 日: Azure Fence Agent 用のロール定義 JSON を修正するために、[Azure の RHEL での Pacemaker のセットアップ](./high-availability-guide-rhel-pacemaker.md)、および [Azure の SLES での Pacemaker のセットアップ](./high-availability-guide-suse-pacemaker.md)に関するページを変更
- 2021 年 8 月 17 日: ANF でホストされる NFS ボリュームを使用した IBM Db2 のサポートを導入するために、「[SAP ワークロードのための IBM Db2 Azure Virtual Machines DBMS のデプロイ](./dbms_guide_ibm.md)」、「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」、「[Azure 仮想マシンの SAP ワークロードでサポートされるシナリオ](./sap-planning-supported-configurations.md)」の各ページを変更
- 2021 年 8 月 2 日: エンキュー サーバーの障害をシミュレートするテスト シナリオの動作 (ENSA1/ENSA2) を明確にするために、[SAP アプリケーション用の SLES 上の Azure VM での SAP NW の高可用性](./high-availability-guide-suse.md)、[ANF を使用した SLES 上の Azure VM での SAP NW の高可用性](./high-availability-guide-suse-netapp-files.md)、[SAP アプリケーション用の RHEL 上の Azure VM での SAP NW の高可用性](./high-availability-guide-rhel.md)、[ANF を使用した RHEL 上の Azure VM での SAP NW の高可用性](./high-availability-guide-rhel-netapp-files.md)に関するページを変更     
- 2021 年 8 月 11 日: クラスター リソースの持続性、移行のしきい値、および順序の制約を調整するために、[SAP アプリケーション用の RHEL 上の Azure VM での SAP NW の高可用性](./high-availability-guide-rhel.md)、[ANF を使用した RHEL 上の Azure VM での SAP NW の高可用性](./high-availability-guide-rhel-netapp-files.md)、[RHEL マルチ SID 上の Azure VM での SAP NW の高可用性ガイド](./high-availability-guide-rhel-multi-sid.md)に関するページを変更  
- 2021 年 8 月 11 日: [Azure 上の SAP IQ を使用した SAP BW-Near Line Storage (NLS) 実装ガイド](./sap-iq-deployment-guide.md)のリリース 
- 2021 年 7 月 29 日: 「[SAP アプリケーション用の Azure NetApp Files (SMB) を使用した Windows 上の Azure VM における SAP NetWeaver の高可用性](./high-availability-guide-windows-netapp-files-smb.md)」および「[Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](./sap-high-availability-guide-wsfc-shared-disk.md)」に、ASCS/SCS および DBMS 用の結合された 2 ノード Windows クラスターを導入 
- 2021 年 7 月 26 日: [Azure の RHEL での Pacemaker の設定](./high-availability-guide-rhel-pacemaker.md)と [Azure の SLES での Pacemaker の設定](./high-availability-guide-suse-pacemaker.md)に関するページを変更し、ロールの割り当て手順を、Azure Fence Agent の設定について説明するセクションの RBAC ドキュメントへのリンクに置き換える
- 2021 年 7 月 22 日: ASCS クラスター リソース (ENSA2 のみ) の `failure-timeout` を削除するために、[SAP アプリケーション用の RHEL 上の Azure VM での SAP NW の高可用性](./high-availability-guide-rhel.md)、[ANF を使用した RHEL 上の Azure VM での SAP NW の高可用性](./high-availability-guide-rhel-netapp-files.md)、[RHEL マルチ SID 上の Azure VM での SAP NW の高可用性ガイド](./high-availability-guide-rhel-multi-sid.md)に関するページを変更
- 2021 年 7 月 16 日: より合理化されたナビゲーションのために SAP on Azure のドキュメントの目次 (TOC) を再構築
- 2021 年 7 月 2 日: azacsnap ツールと HANA Large Instances のバックアップおよび復元の重複コンテンツを削除するために、「[HANA Large Instances での SAP HANA のバックアップと復元](./hana-backup-restore.md)」を変更
- 2021 年 7 月 2 日: 2 つのノード Pacemaker クラスターでのフェンスの競争を回避する方法と KB へのリンクを追加し、`fence_kdump` によるオプションの STONITH 構成を使用する場合のフェールオーバーの遅延を減らす方法を説明するために、[Azure の RHEL での Pacemaker の設定](./high-availability-guide-rhel-pacemaker.md)に関するページを変更
- 2021 年 7 月 1 日: [HLI で利用可能な SKU](./hana-available-skus.md)に関するページに、新しく認定された HANA Large Instances SKU を追加 
- 2021 年 6 月 30 日: 推奨 SAP プロファイル パラメーターのセクションを追加するために、[WSFC と Azure NetApp Files (SMB) を使用した SAP ASCS/SCS の HA ガイド](./high-availability-guide-windows-netapp-files-smb.md)に関するページを変更 
- 2021 年 6 月 29 日: fence_kdump を使用したオプションの stonith 構成を追加するために、[Azure の RHEL での Pacemaker の設定](./high-availability-guide-rhel-pacemaker.md)に関するページを変更
- 2021 年 6 月 28 日: SAP ホスト名の長さの制限を回避するために SMB サーバー (コンピューター アカウント) プレフィックスを 8 文字以下にする必要があるというステートメントを追加するように、[WSFC と Azure NetApp Files (SMB) を使用した SAP ASCS/SCS の HA ガイド](./high-availability-guide-windows-netapp-files-smb.md)に関するページを変更
- 2020 年 6 月 17 日: HANA リソース作成コマンド (RHEL 8.x) から meta キーワードを削除するように、「[RHEL 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability-rhel.md)」を変更
- 2021 年 6 月 9 日: 「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」の M192 ---_ v2 の VM SKU 名を修正
- 2021 年 5 月 26 日: [SLES 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウト HSR](./sap-hana-high-availability-scale-out-hsr-suse.md)、[RHEL で ANF を使用した SAP HANA スケールアップの HA](./sap-hana-high-availability-netapp-files-red-hat.md)、および [RHEL 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウト HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md) に関する記事を変更し、ANF 上で HANA を実行するために OS を準備するための構成を追加  
- 2021 年 5 月 13 日: [Azure の SLES での Pacemaker のセットアップ](./high-availability-guide-suse-pacemaker.md)に関する記事を変更し、リソース エージェント azure-events の動作を明確化 
- 2021 年 4 月 30 日: パッケージ python3-azure-mgmt-compute のバージョンに Azure Fence Agent との互換性のない変更が加えられた事に関する警告を含めるよう [Azure の SUSE に Pacemaker をセットアップする](./high-availability-guide-suse-pacemaker.md)方法に関する記事を変更 (SLES 15)  
- 2021 年 4 月 27 日: [WSFC とファイル共有を使用した SAP ASCS/SCS インスタンス](./sap-high-availability-guide-wsfc-file-share.md)に関するページを変更し、重要な SAP ノートへのリンクを前提条件のセクションに追加
- 2021 年 4 月 27 日: 「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」で、新しい Msv2 および Mdsv2 VM を HANA ストレージ構成に追加
- 2021 年 4 月 27 日: 「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」で、HSR 構成のすべての VM 間で同じストレージの種類を HANA System Replication に使用するための要件を追加
- 2021 年 4 月 27 日: 「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」で、DBMS 高可用性レプリケーション構成のすべて VM 間で同じストレージの種類を DBMS レプリケーションのシナリオに使用するための要件を追加
- 2021 年 4 月 23 日: 「[Azure の Linux 向け SAP BusinessObjects BI プラットフォーム デプロイ ガイド](businessobjects-deployment-guide-linux.md)」で、Azure Database for MySQL のプライベート リンクを構成するためのセクションを追加し、数か所の記述をわずかに変更
- 2021 年 4 月 22 日: Azure 上の Windows 向け SAP BusinessObjects BI プラットフォームのドキュメントとして、[Azure 上の Windows への SAP BusinessObjects BI プラットフォーム デプロイ ガイド](businessobjects-deployment-guide-windows.md)をリリース
- 2021 年 4 月 21 日: 「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」の記事で、Premium ストレージ ディスクの読み取りキャッシュを有効にすると M32ts および M32ls に対する HCMT/HWCCT ストレージ テストで HANA KPI が基準に達しない場合がある理由の説明を追加
- 2021 年 4 月 20 日: 「[SAP ワークロードのための IBM Db2 Azure Virtual Machines DBMS のデプロイ](./dbms_guide_ibm.md)」の記事で、Azure ブロック ストレージが異なる場合の IBM Db2 のストレージ ブロック サイズを明確化
- 2021 年 4 月 12 日: SAP HANA システム レプリケーションの Python フックの構成手順を追加するために、[SLES 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability.md)、[RHEL 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability-rhel.md)、[RHEL での ANF を使用した SAP HANA スケールアップの高可用性](./sap-hana-high-availability-netapp-files-red-hat.md)に関するページを変更  
- 2021 年 4 月 12 日: [Azure Backup サービスを使用した SAP HANA のバックアップまたは復元](../../../backup/sap-hana-db-about.md)のドキュメントによる SAP HANA のバックアップ ドキュメントの置き換え 
- 2021 年 4 月 12 日: [SLES 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウト HSR](./sap-hana-high-availability-scale-out-hsr-suse.md) に関する構成ガイドのリリース
- 2021 年 4 月 7 日: 「[SAP NetWeaver のための SQL Server Azure Virtual Machines DBMS のデプロイ](./dbms_guide_sqlserver.md)」で、SQL Server の複数インスタンスと複数データベースのサポートを明確化
- 2021 年 4 月 7 日: 「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)」にセカンダリ IP アドレスに関連する情報を追加
- 2021 年 4 月 7 日: 「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」に ANF での Oracle DBMS サポートのサポートを追加
- 2021 年 3 月 17 日: [SLES 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability.md)、[RHEL 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability-rhel.md)、[RHEL での ANF を使用した SAP HANA スケールアップの高可用性](./sap-hana-high-availability-netapp-files-red-hat.md)に関するページの変更により、Pacemaker クラスターでの HANA アクティブ/読み取り可能のシステム レプリケーションの手順を追加
- 2021 年 3 月 15 日: [WSFC とファイル共有を使用した SAP ASCS/SCS インスタンス](./sap-high-availability-guide-wsfc-file-share.md)、[WSFC とファイル共有を使用した SAP ASCS/SCS インスタンスのインストール](./sap-high-availability-installation-wsfc-file-share.md)、[WSFC とファイル共有を使用した SAP ASCS/SCS マルチ SID](./sap-ascs-ha-multi-sid-wsfc-file-share.md) に関するページの変更により、SAP ASCS/SCS インスタンスと SOFS 共有を別々のクラスターにデプロイする必要があることを明確化
- 2021 年 3 月 3 日: SAP システムのインストール時、SWPM を実行しているユーザーには昇格された特権が必要になるという注意書きを追加するために、[WSFC と Azure NetApp Files (SMB) を使用する SAP ASCS/SCS のための HA ガイド](./high-availability-guide-windows-netapp-files-smb.md)に関するページを変更
- 2021 年 2 月 11 日: RHEL 8.x 用の pacemaker クラスター コマンドを修正するために、「[Red Hat Enterprise Linux Server 上の Azure VM での IBM Db2 LUW の高可用性](./high-availability-guide-rhel-ibm-db2-luw.md)」を変更
- 2021 年 2 月 3 日: stonith create コマンドの pcmk_host_map を更新するために、[Azure の RHEL への Pacemaker のセットアップ](./high-availability-guide-rhel-pacemaker.md)に関するページを変更
- 2021 年 2 月 3 日: stonith create コマンドの pcmk_host_map を追加するために、[Azure の SLES への Pacemaker のセットアップ](./high-availability-guide-suse-pacemaker.md)に関するページを変更 
- 2021 年 2 月 3 日: 記事「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」で SUSE 用の I/O スケジューラ設定をより詳細に記述
- 2021 年 2 月 1 日: [SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)へのリンクを追加するために、[RHEL での ANF を使用した SAP HANA スケールアップの高可用性](./sap-hana-high-availability-netapp-files-red-hat.md)、[RHEL 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウト HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md)、[SLES でのスタンバイ ノードと ANF を使用した Azure VM への SAP HANA スケールアウト](./sap-hana-scale-out-standby-netapp-files-suse.md)、[RHEL でのスタンバイ ノードと ANF を使用した Azure VM への SAP HANA スケールアウト](./sap-hana-scale-out-standby-netapp-files-rhel.md)に関するページを変更
- 2021 年 1 月 23 日: 「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」および「[SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)」の記事で、ディスク ボリューム マネージャーを使用せずに、異なる Azure ディスクまたは NFS 共有間で HANA データ ファイルに対する I/O 操作をストライピングする機能である HANA データ ボリューム パーティション分割機能の説明を追加
- 2021 年 1 月 18 日: 「[SAP ワークロードのための Azure Virtual Machines Oracle DBMS のデプロイ](./dbms_guide_oracle.md)」で、Oracle 向け Azure NetApp Files ベースの NFS のサポートを追加。ドキュメント「[SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)」の表内の小数の調整
- 2021 年 1 月 11 日: RHEL8 と RHEL7 および ENSA1 と ENSA2 の両方で動作するようにコマンドを調整するための、[RHEL 上の Azure VM での SAP アプリケーションのための SAP NW の高可用性](./high-availability-guide-rhel.md)、[RHEL 上の Azure VM での ANF を使用した SAP NW の高可用性](./high-availability-guide-rhel-netapp-files.md)、[RHEL 上の Azure VM での SAP NW の高可用性 (マルチ SID) のガイド](./high-availability-guide-rhel-multi-sid.md) に関するページのわずかな変更
- 2021 年 1 月 5 日: [SLES で ANF を使用した Azure VM のスタンバイ ノードでの SAP HANA スケールアウト](./sap-hana-scale-out-standby-netapp-files-suse.md)および [RHEL で ANF を使用した Azure VM のスタンバイ ノードでの SAP HANA スケールアウト](./sap-hana-scale-out-standby-netapp-files-rhel.md)に関するページに変更が加えられ、SAP Host Agent でローカル ポート範囲を管理できるように推奨構成を修正  
- 2021 年 1 月 4 日: 「[SAP HANA on Azure (L インスタンス) とは](./hana-overview-architecture.md)」に、HLI でサポートされる新しい Azure リージョンを追加
- 2020 年 12 月 29 日: 「[Azure Availability Zones での SAP ワークロードの構成](./sap-ha-availability-zones.md)」で、特定の Azure リージョンのアーキテクチャに関する推奨事項を追加
-  2020 年 12 月 21 日: [HLI で利用可能な SKU](./hana-available-skus.md)に関するページで、HANA Large Instances の SKU に新しい認定資格を追加
- 2020 年 12 月 12 日: 「[Azure デプロイでサポートされている SAP ソフトウェア](./sap-supported-product-on-azure.md#oracle-dbms-support)」に、SAP による Oracle Enterprise Linux のサポートに関する詳細を明確にする SAP ノートへのポインターを追加
- 2020 年 11 月 26 日: 変更後のシングル [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) に合わせて、「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」および「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」の内容を調整
- 2020 年 11 月 5 日: 「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」で、HANA でサポートされるファイル システムの種類に関する新しい SAP ノートへのリンクを変更 
- 2020 年 10 月 26 日: 「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」で、Azure Premium Storage 構成に関するいくつかの表を変更し、プロビジョニングされたスループットとバースト スループットの対比を明確化
- 2020 年 10 月 22 日: net.ipv4.tcp_keepalive_time の推奨事項を調整するために、[SLES 上の Azure VM での SAP アプリケーションのための SAP NW の高可用性](./high-availability-guide-suse.md)、[SLES 上の Azure VM での ANF を使用した SAP NW の高可用性](./high-availability-guide-suse-netapp-files.md)、[RHEL 上の Azure VM での SAP アプリケーションのための SAP NW の高可用性](./high-availability-guide-rhel.md)、[RHEL 上の Azure VM での ANF を使用した SAP NW の高可用性](./high-availability-guide-rhel-netapp-files.md)に関するページを変更  
- 2020 年 10 月 16 日: [SLES 上の Azure VM での Pacemaker を使用した IBM Db2 LUW の高可用性](./dbms-guide-ha-ibm.md)、[RHEL 上の Azure VM での SAP アプリケーションのための SAP NW の高可用性](./high-availability-guide-rhel.md)、[RHEL 上の Azure VM での IBM Db2 LUW の 高可用性](./high-availability-guide-rhel-ibm-db2-luw.md)、[RHEL 上の Azure VM での SAP NW の高可用性 (マルチ SID) のガイド](./high-availability-guide-rhel-multi-sid.md)、[RHEL 上の Azure VM での ANF を使用した SAP NW の高可用性](./high-availability-guide-rhel-netapp-files.md)、[SLES 上の Azure VM での SAP アプリケーションのための SAP NW の高可用性](./high-availability-guide-suse.md)、[SLES 上の Azure VM での SAP NNW の高可用性 (マルチ SID) のガイド](./high-availability-guide-suse-multi-sid.md)、[SLES 上の Azure VM での ANF を使用した SAP アプリケーションのための SAP NW の高可用性](./high-availability-guide-suse-netapp-files.md)、[SLES 上の Azure VM での NFS の高可用性](./high-availability-guide-suse-nfs.md)、[SLES 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability.md)、[RHEL での ANF を使用した SAP HANA スケールアップの高可用性](./sap-hana-high-availability-netapp-files-red-hat.md)、[RHEL 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability-rhel.md)、[RHEL 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウトの HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md)、[WSFC と共有ディスクを使用した SAP ASCS/SCS 向けの Azure インフラストラクチャの準備](./sap-high-availability-infrastructure-wsfc-shared-disk.md)、[WSFC と Azure 共有ディスクを使用した SAP ASCS/SCS 向けのマルチ SID 高可用性のガイド](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md)、および [WSFC と共有ディスクを使用した SAP ASCS/SCS 向けのマルチ SID 高可用性のガイド](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)に関するページを変更し、セカンダリ IP での負荷分散シナリオではフローティング IP がサポートされないという説明を追加 
- 2020 年 10 月 16 日: 「[HANA Large Instances での SAP HANA のバックアップと復元](./hana-backup-restore.md)」に、HANA Large Instances のストレージ スナップショットを制御するためのドキュメントを追加
- 2020 年 10 月 15 日: Azure 上の SAP BusinessObjects BI プラットフォームに関するドキュメント、「[Azure の SAP BusinessObjects BI プラットフォームの計画および実装ガイド](businessobjects-deployment-guide.md)」、「[Azure の Linux 向け SAP BusinessObjects BI プラットフォーム デプロイ ガイド](businessobjects-deployment-guide-linux.md)」のリリース
- 2020 年 10 月 5 日: [RHEL 上の Azure VM での Pacemaker を使用した SAP HANA スケールアウト HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md) に関する構成ガイドのリリース
- 2020 年 9 月 30 日: RHEL 8.1 向けの手順に合わせて、[RHEL 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability-rhel.md)、[RHEL での ANF を使用した SAP HANA スケールアップの高可用性](./sap-hana-high-availability-netapp-files-red-hat.md)、[Azure の RHEL への Pacemaker のセットアップ](./high-availability-guide-rhel-pacemaker.md)に関するページを変更
- 2020 年 9 月 29 日: 「[SAP アプリケーションで最適なネットワーク待ち時間を実現するための Azure 近接通信配置グループ](./sap-proximity-placement-scenarios.md)」の記事で、PPG の使用に関する制限事項と推奨事項をさらに明確化 
- 2020 年 9 月 28 日: 「[SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)」の記事で、Azure NetApp Files を使用した SAP HANA 用の新しいストレージ操作ガイドを追加
- 2020 年 9 月 23 日: [HLI で利用可能な SKU](./hana-available-skus.md) に関するページで、HLI 用の新しい認定 SKU を追加 
- 2020 年 9 月 20 日: DBMS バイナリと SAP バイナリを別々の Azure ディスクに分離することを推奨する新しい構成提案に合わせて、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](./dbms_guide_general.md)」、「[SAP NetWeaver のための SQL Server Azure Virtual Machines DBMS のデプロイ](./dbms_guide_sqlserver.md)」、「[SAP ワークロードのための Azure Virtual Machines Oracle DBMS のデプロイ](./dbms_guide_oracle.md)」、「[SAP ワークロードのための IBM Db2 Azure Virtual Machines DBMS のデプロイ](./dbms_guide_ibm.md)」の各ドキュメントの内容を変更。 また、さまざまなガイドに Ultra Disk の推奨事項を追加しました。
- 2020 年 9 月 8 日: stonith の定義を明確にするために、[SLES 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability.md)に関するページを変更
- 2020 年 9 月 3 日: Ultra Disk の容量 1 GB あたり最小 2 IOPS の基準に合わせて「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」の内容を変更
- 2020 年 9 月 2 日: どの SKU が HANA 認定であるかをより明確にするために、[HLI で利用可能な SKU](./hana-available-skus.md) に関するページを変更
- 2020 年 8 月 25 日:誤植を修正するため、[ANF のある SLES 上の Azure VM において SAP NW の HA](./high-availability-guide-suse-netapp-files.md) が変更されました
- 2020 年 8 月 25 日:[WSFC と共有ディスクを使用した SAP ASCS/SCS の HA ガイド](./sap-high-availability-guide-wsfc-shared-disk.md)、[WSFC と共有ディスクを使用して SAP ASCS/SCS 用に Azure インフラストラクチャを準備](./sap-high-availability-infrastructure-wsfc-shared-disk.md)に関するページ、[WSFC と共有ディスクを使用した SAP NW HA のインストール](./sap-high-availability-guide-wsfc-shared-disk.md)に関するページでの変更。Azure 共有ディスクを使用するオプションを紹介し、SAP ERS2 アーキテクチャをドキュメント化するためのものです。
- 2020 年 8 月 25 日:[WSFC と Azure 共有ディスクを使用した SAP ASCS/SCS のマルチ SID HA ガイド](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md)のリリース
- 2020 年 8 月 25 日:[WSFC および Azure NetApp Files (SMB) を使用した SAP ASCS/SCS の HA ガイド](./high-availability-guide-windows-netapp-files-smb.md)、[WSFC とファイル共有を使用して SAP ASCS/SCS 用に Azure インフラストラクチャを準備](./sap-high-availability-infrastructure-wsfc-file-share.md)に関するページ、[WSFC と共有ディスクを使用した SAP ASCS/SCS のマルチ SID HA ガイド](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)、[WSFC および SOFS ファイル共有を使用した SAP ASCS/SCS のマルチ SID HA ガイド](./sap-ascs-ha-multi-sid-wsfc-file-share.md)での変更。コンテンツの更新と、WFC と共有ディスクを使用した SAP ASCS/SCS の HA ガイドの再構成の結果として行われました。 
- 2020 年 8 月 21 日:タイプ I および II の HLI ユニットで使用可能なオペレーティング システムとして、新しい OS リリースを [HANA Large Instances と互換性のあるオペレーティング システム](./os-compatibility-matrix-hana-large-instance.md)に追加
- 2020 年 8 月 18 日:[RHEL での ANF を使用した SAP HANA の高可用性のスケールアップ](./sap-hana-high-availability-netapp-files-red-hat.md)のリリース
- 2020 年 8 月 17 日:Azure Site Recovery を使用して SAP NetWeaver システムをオンプレミスから Azure に移行するための情報を「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)」に追加しました
- 2020 年 8 月 14 日:Db2 のディスク構成に関するアドバイスを「[SAP ワークロードのための IBM Db2 Azure Virtual Machines DBMS のデプロイ](./dbms_guide_ibm.md)」に関する記事に追加しました
- 2020 年 8 月 11 日:RHEL 7.6 を、タイプ I の HLI ユニットで使用可能なオペレーティング システムである [HANA Large Instances と互換性のあるオペレーティング システム](./os-compatibility-matrix-hana-large-instance.md)に追加
- 2020 年 8 月 10 日:コスト志向の SAP HANA ストレージ構成を [SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)に導入し、[Azure での SAP ワークロード: 計画とデプロイに関するチェックリスト](./sap-deployment-checklist.md)を更新
- 2020 年 8 月 4 日:Pacemaker クラスターにおける信頼性の高い名前解決の重要性を強調するために、[Azure での SLES に対する Pacemaker の設定](./high-availability-guide-suse-pacemaker.md)および [Azure での RHEL に対する Pacemaker の設定](./high-availability-guide-rhel-pacemaker.md)に関するページを変更
- 2020 年 8 月 4 日:パラメーター `enque/encni/set_so_keepalive` の使用を明確にするために、[WFCS とファイル共有における SAP NW HA](./sap-high-availability-installation-wsfc-file-share.md)、[WFCS と共有ディスクにおける SAP NW HA](./sap-high-availability-installation-wsfc-shared-disk.md)、[Azure VM における SAP NW の HA](./high-availability-guide.md)、[SLES 上の Azure VM での SAP NW の HA](./high-availability-guide-suse.md)、[ANF を使用した SLES 上の Azure VM での SAP NW の HA](./high-availability-guide-suse-netapp-files.md)、[SLES マルチ SID 上の Azure VM での SAP NW の HA ガイド](./high-availability-guide-suse-multi-sid.md)、[RHEL 上の Azure VM での SAP NetWeaver の高可用性](./high-availability-guide-rhel.md)、[ANF を使用した RHEL 上の Azure VM での SAP NW の HA](./high-availability-guide-rhel-netapp-files.md)、[RHEL マルチ SID 上の Azure VM での SAP NW の HA ガイド](./high-availability-guide-rhel-multi-sid.md)に関するページを更新
- 2020 年 7 月 23 日:SAP HANA Large Instances 予約を購入する前に知っておくべきことと、その購入方法について説明する記事「[Azure 予約を使用して SAP HANA Large Instances に保存する](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md)」を追加
- 2020 年 7 月 16 日:[展開ガイド](deployment-guide.md)で Azure PowerShell を使用して SAP 用の新しい VM 拡張機能をインストールする方法を説明
- 2020 年 7 月 4 日: [Azure Monitor for SAP Solutions (プレビュー)](./monitor-sap-on-azure.md) のリリース
- 2020 年 7 月 1 日:ドキュメント「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」での、Azure Premium Storage バースト機能に基づく低コストのストレージ構成の提案 
- 2020 年 6 月 24 日:新しい改善された Azure フェンス エージェントと、Azure フェンス エージェントに基づく、デバイス向けの回復性の高い STONITH 構成のリリースに関する、「[Azure での SLES に対する Pacemaker の設定](./high-availability-guide-suse-pacemaker.md)」での変更。 
- 2020 年 6 月 24 日:回復性の高い STONITH 構成のリリースに関する、「[Azure での RHEL に対する Pacemaker の設定](./high-availability-guide-rhel-pacemaker.md)」での変更。
- 2020 年 6 月 23 日:「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)」ガイドおよび「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」ガイドの概要に対する変更
- 2020 年 6 月 22 日: [デプロイ ガイド](deployment-guide.md)に関する記事への、SAP 用の新しい VM 拡張機能のインストール手順の追加
- 2020 年 6 月 16 日:SUSE パブリック クラウド インフラストラクチャ 101 ドキュメントへのリンクの追加に関する[SAP の HA シナリオにおける Azure Standard ILB を使用した VM のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)に関するページでの変更 
- 2020 年 6 月 10 日:「[HLI で利用可能な SKU](./hana-available-skus.md)」および「[SAP HANA (Large Instances) のストレージ アーキテクチャ](./hana-storage-architecture.md)」への新しい HLI SKU の追加
- 2020 年 5 月 21 日:[SAP の HA シナリオにおける Azure 標準 ILB を使用した VM のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)へのリンクを追加するために、[Azure での SLES に対する Pacemaker の設定](./high-availability-guide-suse-pacemaker.md)および [Azure での RHEL に対する Pacemaker の設定](./high-availability-guide-rhel-pacemaker.md)に関するページを変更  
- 2020 年 5 月 19 日:HANA 関連ボリュームに LVM を使用する場合はルート ボリューム グループを使用できないという重要なメッセージを「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」に追加
- 2020 年 5 月 19 日:HANA L インスタンス タイプ II でサポートされる新しい OS を「[HANA L インスタンスと互換性があるオペレーティング システム](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance)」に追加
- 2020 年 5 月 12 日:リンクを更新するため、およびサード パーティ製ファイアウォールの構成に関する情報を追加するために、[SAP の HA シナリオにおける Azure 標準 ILB を使用した VM のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)に関するページを変更
- 2020 年 5 月 11 日: [SLES 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability.md)に関するページを変更して、`netcat` リソースのリソースの持続性を 0 に設定。これにより、フェールオーバーが効率化されます 
- 2020 年 5 月 5 日:Mv1 VM ファミリで Gen2 デプロイを使用できることを示すために、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)」を変更
- 2020 年 4 月 24 日:ANF ボリュームが自動的に割り当てられることをより明確に示す、「[SUSE Linux Enterprise Server 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](./sap-hana-scale-out-standby-netapp-files-suse.md)」、「[Red Hat Enterprise Linux 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](./sap-hana-scale-out-standby-netapp-files-rhel.md)」、「[SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](./high-availability-guide-suse-netapp-files.md)」および「[SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](./high-availability-guide-rhel-netapp-files.md)」への変更
- 2020 年 4 月 22 日:クラスターのメンテナンス モードの切り替えと競合するため、属性 `is-managed` を手順から削除して「[SUSE Linux Enterprise Server 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability.md)」を変更
- 2020 年 4 月 21 日:「[Azure デプロイでサポートされている SAP ソフトウェア](./sap-supported-product-on-azure.md)」および「[Microsoft Azure で実行されているSAP の認定と構成](./sap-certifications.md)」の記事に、SAP (Hybris) Commerce Platform 1811 以降用のサポートされている DBMS として、SQL Azure DB を追加
- 2020 年 4 月 16 日:「[Azure デプロイでサポートされている SAP ソフトウェア](./sap-supported-product-on-azure.md)」および「[Microsoft Azure で実行されているSAP の認定と構成](./sap-certifications.md)」の記事に、SAP (Hybris) Commerce Platform 用のサポートされている DBMS として、SAP HANA を追加
- 2020 年 4 月 13 日:「[SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ](./dbms_guide_sapase.md)」での正確な SAP ASE リリース番号への修正
- 2020 年 4 月 7 日:「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](./high-availability-guide-suse-pacemaker.md)」での、cloud-netconfig-azure の手順を明確にするための変更
- 2020 年 4 月 6 日:「[SUSE Linux Enterprise Server 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](./sap-hana-scale-out-standby-netapp-files-suse.md)」および「[Red Hat Enterprise Linux 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](./sap-hana-scale-out-standby-netapp-files-rhel.md)」での、NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) への参照を削除するための変更 ([TR-4746](https://www.netapp.com/us/media/tr-4746.pdf) に置き換えられました)
- 2020 年 3 月 31 日: 「[SUSE Linux Enterprise Server 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability.md)」および「[Red Hat Enterprise Linux 上の Azure VM での SAP HANA の高可用性](./sap-hana-high-availability-rhel.md)」での、ストライプ ボリュームを作成するときにストライプ サイズを指定する方法の手順を追加するための変更
- 2020 年 3 月 27 日: 「[SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](./high-availability-guide-suse-netapp-files.md)」での、ファイル システム マウント オプションを NetApp TR-4746 (同期マウント オプションを削除する) に合わせるための変更
- 2020 年 3 月 26 日: 「[SUSE Linux Enterprise Server for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](./high-availability-guide-suse-multi-sid.md)」での、NetApp TR-4746 に参照を追加するための変更
- 2020 年 3 月 26 日: 図を更新し、Azure Load Balancer バックエンド プールの作成手順を明確にするために、[SLES for SAP アプリケーション上の Azure VM での SAP NetWeaver の高可用性](./high-availability-guide-suse.md)、[SAP アプリケーション用の Azure NetApp Files を使用した SLES 上の Azure VM での SAP NetWeaver の高可用性](./high-availability-guide-suse-netapp-files.md)、[SLES 上の Azure VM での NFS の高可用性](./high-availability-guide-suse-nfs.md)、[RHEL マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](./high-availability-guide-suse-multi-sid.md)、[RHEL for SAP アプリケーション上の Azure VM での SAP NetWeaver の高可用性](./high-availability-guide-rhel.md)、[SAP アプリケーション用の Azure NetApp Files を使用した RHEL 上の SAP NetWeaver 用の Azure VM の高可用性](./high-availability-guide-rhel-netapp-files.md)に関するページを変更
- 2020 年 3 月 19 日: ドキュメント「[クイックスタート: Azure Virtual Machines への単一インスタンスの SAP HANA の手動インストール](./hana-get-started.md)」から「[Azure Virtual Machines への SAP HANA のインストール](./hana-get-started.md)」へのメジャー リビジョン
- 2020 年 3 月 17 日: 「[Azure の SUSE Linux Enterprise Server に Pacemaker をセットアップする](./high-availability-guide-suse-pacemaker.md)」での、必要なくなった SBD 構成設定を削除するための変更
- 2020 年 3 月 16 日: 「[Azure デプロイでサポートされている SAP ソフトウェア](./sap-supported-product-on-azure.md)」での、SAP HANA IaaS 認定プラットフォームの列の認定シナリオの明確化
- 2020 年 3 月 11 日:「[Azure 仮想マシンの SAP ワークロードでサポートされるシナリオ](./sap-planning-supported-configurations.md)」での、DBMS インスタンスあたり複数のデータベースのサポートを明確にするための変更
- 2020 年 3 月 11 日: 「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)」での、第 1 世代および第 2 世代 VM を説明するための変更
- 2020 年 3 月 10 日: ANF の実際の既存スループット制限を明確にするための、「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」の変更
